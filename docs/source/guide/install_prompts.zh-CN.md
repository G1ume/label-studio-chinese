---
title: 在本地环境中安装提示功能（可选）
short: 安装提示功能
type: guide
tier: enterprise
order: 0
order_enterprise: 71
meta_title: 安装提示功能
meta_description: 在 Label Studio Enterprise 本地环境中安装提示功能
section: "安装与设置"
parent: "install_k8s"
parent_enterprise: "install_enterprise_k8s"
---

在本地环境中安装提示功能需要安装 Adala，即我们的数据标注代理微服务。

只有在您需要使用提示功能时才需要完成这些步骤。更多信息请参阅[提示功能概述](prompts_overview)。

!!! note
    提示功能仅支持 Kubernetes 部署。如果您使用的是 Docker，则需要迁移到 Kubernetes 部署才能使用提示功能。有关更多信息，请参阅[在 Kubernetes 上部署 Label Studio Enterprise](install_enterprise_k8s.html)。


## 前提条件

- Kubernetes 集群 **v1.24** 或更高版本
- Helm **v3.8.0** 或更高版本
- Docker CLI（用于登录 Docker Hub）

## 资源要求

安装前，请确保您的 Kubernetes 集群可以为 Adala 提供以下最低资源：

| 资源 | 要求 |
| --- | --- |
| CPU | 6 核 |
| 内存 | 12 GB |

## 1. 向 Docker Hub 进行身份验证并验证访问权限

您需要 Docker Hub 用户名和密码。如果没有，请[联系 HumanSignal 团队申请访问权限](mailto:support@humansignal.com)。

登录 DockerHub 以访问私有 OCI 仓库：

```bash

docker login -u CUSTOMER_USERNAME
```

出现提示时，输入您的 Docker Hub 密码。

然后验证您的凭据和访问权限：

```bash
helm pull oci://registry-1.docker.io/heartexlabs/adala
```

预期输出：

```bash
Pulled: registry-1.docker.io/heartexlabs/adala:X.X.X
Digest: sha256:***************************************************
```

## 2. 创建用于拉取镜像的 Kubernetes Secret

创建一个 Kubernetes Secret，以允许您的集群拉取私有的 Adala 镜像：

```bash
kubectl create secret docker-registry heartex-pull-key \
  --docker-server=https://index.docker.io/v2/ \
  --docker-username=CUSTOMER_USERNAME \
  --docker-password=CUSTOMER_PASSWORD
```

## 3. 准备自定义 values 文件

创建一个名为 `custom.values.yaml` 的文件，内容如下：

```yaml
adala-app:
  deployment:
    image:
      tag: 20260409.063233-main-6b619568
      pullSecrets:
        - heartex-pull-key
adala-worker:
  deployment:
    image:
      tag: 20260409.063233-main-6b619568
      pullSecrets:
        - heartex-pull-key
```

!!! note
    如有必要，请将 `image.tag` 替换为适当的版本。


## 4. 为 Adala 创建专用命名空间

为 Adala 创建一个专用的命名空间 `prompt`：

```bash
kubectl create namespace prompt
```

## 5. 安装 Adala Helm Chart

运行以下命令，使用自定义 values 安装 **Adala**：

```bash
helm install lse oci://registry-1.docker.io/heartexlabs/adala --values custom.values.yaml
```

## 6. 验证 Adala 是否正在运行

检查 `prompt` 命名空间中的所有 Pod 是否处于 **Running** 或 **Completed** 状态：

```bash
kubectl get pods -n prompt
```

您应该看到所有 Pod 的 `STATUS` 都设置为 `Running` 的输出，例如：

```
NAME                                  READY   STATUS    RESTARTS       AGE
adala-adala-app-d4564ffd7-gtmhx       1/1     Running   0              100m
adala-adala-kafka-controller-0        1/1     Running   0              110m
adala-adala-kafka-controller-1        1/1     Running   0              111m
adala-adala-kafka-controller-2        1/1     Running   0              113m
adala-adala-redis-master-0            1/1     Running   0              125m
adala-adala-worker-5d87f97f76-mq952   1/1     Running   0              111m

```

如果有任何 Pod 未运行，您可以进一步调查：

```bash

kubectl describe pod <pod-name> -n prompt
```

或

```bash
kubectl logs <pod-name> -n prompt
```

## 7. 更新 Label Studio `values.yaml` 文件

您需要更新 Label Studio Enterprise `values.yaml` 文件的 `global` 部分，以包含以下内容：

* 添加 Adala 端点，使 Label Studio 能够连接到 Adala。
* 添加提示功能开关，以在 Label Studio 中启用提示功能的可见性。


```yaml
global:
  extraEnvironmentVars:
    PROMPTER_ADALA_URL: http://adala-adala-app.prompt:8000
  featureFlags:
    fflag_feat_all_dia_835_prompter_workflow_long: true
```

关于 `PROMPTER_ADALA_URL` 的说明：

- `prompt` 是 Adala 安装所在的命名空间。
- `adala-adala-app` 是 Helm 发布自动创建的 Adala 服务名称。
- 端口 `8000` 是 Adala 监听的默认端口。

更新 values 文件后，重新部署 Label Studio 以应用更改。

## 8. 可选：添加自定义 CA 证书包

如果您的环境需要自定义 CA 证书包（例如，通过带有自签名证书的企业代理与大语言模型提供商通信时），您必须使 CA 证书对 Adala Pod 可用。

由于 Label Studio Enterprise 和 Adala（提示功能）部署在不同的命名空间中，Kubernetes Secret 不能在它们之间共享。即使您在 Label Studio 命名空间中已经有 CA 证书包 Secret，也必须在 `prompt` 命名空间中创建一个新的 Secret：

```bash
kubectl create secret generic custom-ca-bundle \
  --from-file=ca.crt=my.cert \
  -n prompt
```

将 `my.cert` 替换为您 CA 证书文件的路径。密钥名称（`ca.crt`）必须与下面 values 文件中引用的 `key` 匹配。

然后将以下内容添加到您的 `custom.values.yaml` 中，并重新运行第 5 步中的 `helm install`/`helm upgrade` 命令：

```yaml
adala-app:
  deployment:
    extraEnvVars:
      REQUESTS_CA_BUNDLE: /etc/ssl/custom/ca.crt
      SSL_CERT_FILE: /etc/ssl/custom/ca.crt
    extraVolumeMounts:
      - mountPath: /.cache
        name: cache-dir
      - mountPath: /etc/ssl/custom
        name: custom-ca
        readOnly: true
    extraVolumes:
      - name: cache-dir
        emptyDir: {}
      - name: custom-ca
        secret:
          secretName: custom-ca-bundle
          items:
            - key: ca.crt
              path: ca.crt

adala-worker:
  deployment:
    extraEnvVars:
      REQUESTS_CA_BUNDLE: /etc/ssl/custom/ca.crt
      SSL_CERT_FILE: /etc/ssl/custom/ca.crt
    extraVolumeMounts:
      - mountPath: /.cache
        name: cache-dir
      - mountPath: /etc/ssl/custom
        name: custom-ca
        readOnly: true
    extraVolumes:
      - name: cache-dir
        emptyDir: {}
      - name: custom-ca
        secret:
          secretName: custom-ca-bundle
          items:
            - key: ca.crt
              path: ca.crt
```
