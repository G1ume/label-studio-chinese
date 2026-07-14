---
title: Label Studio 故障排除
short: Label Studio 故障排除
tier: opensource
type: guide
order: 460
order_enterprise: 0
hide_menu: true
meta_title: Label Studio 故障排除
meta_description: Label Studio 社区版的常见问题排查
date: 2024-09-03 09:57:28
---

!!! error 企业版
    本页面涵盖 Label Studio 社区版的常见用户故障排除场景。有关 Label Studio Enterprise 的特定信息，请参阅我们的[支持中心文章](https://support.humansignal.com/hc/en-us)。


## 安装

请参阅[安装问题排查](install_troubleshoot)。


## 项目

### 加载项目时出现空白页面

启动 Label Studio 并打开项目后，您看到空白页面。可能有多种原因导致此问题。

如果在启动 Label Studio 时指定的主机缺少协议（如 `http://` 或 `https://`），Label Studio 可能无法找到正确的文件来加载项目页面。

要解决此问题，请更新作为环境变量或在启动 Label Studio 时指定的主机。请参阅[启动 Label Studio](start)。

## 标注

### 标注时速度缓慢

* 如果您使用的是 SQLite 数据库，并且有其他用户导入了大量数据，由于数据库负载，服务器上的其他用户可能会感觉到标注速度变慢。

* 如果您想上传大量数据（数千个项目），请考虑在无人标注时进行，或使用其他数据库后端（如 PostgreSQL 或 Redis）。您可以从 Label Studio 根目录运行 Docker Compose 来使用 PostgreSQL：`docker-compose up -d`，或参阅[从云存储或数据库同步数据](storage)。

* 如果您使用的标注模式有成千上万个标签，请考虑改用[外部分类法](/tags/taxonomy.html)。

### 标注时出现图片/音频/资源加载错误

资源加载中最常见的错误是 <b>CORS</b>（跨域资源共享）问题或跨域问题。当您尝试从外部托管获取图片时，可能会因安全原因被阻止。

打开浏览器控制台并检查其中的错误。通常，此问题通过外部主机设置来解决。

- 如果您作为管理员有权访问托管服务器，则需要为 Web 服务器允许 CORS。例如，在 nginx 上，您可以尝试在 `/etc/nginx/nginx.conf` 的 `location` 部分中添加以下行：

{% details <b>点击查看详情</b> %}
 ```conf
  location <YOUR_LOCATION> {
       if ($request_method = 'OPTIONS') {
          add_header 'Access-Control-Allow-Origin' '*';
          add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
          #
          # 自定义请求头以及各种浏览器*应该*支持但实际上不支持的头
          #
          add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
          #
          # 告知客户端此预检信息有效期为 20 天
          #
          add_header 'Access-Control-Max-Age' 1728000;
          add_header 'Content-Type' 'text/plain; charset=utf-8';
          add_header 'Content-Length' 0;
          return 204;
       }
       if ($request_method = 'POST') {
          add_header 'Access-Control-Allow-Origin' '*';
          add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
          add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
          add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range';
       }
       if ($request_method = 'GET') {
          add_header 'Access-Control-Allow-Origin' '*';
          add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
          add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
          add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range';
       }
  }
  ```
{% enddetails %}
* 对于 Amazon S3，请参阅 Amazon S3 用户指南中的[配置和使用跨域资源共享 (CORS)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/cors.html)。
* 对于 GCS，请参阅 Google Cloud Storage 文档中的[配置跨域资源共享 (CORS)](https://cloud.google.com/storage/docs/configuring-cors)。
* 对于 Microsoft Azure，请参阅 Microsoft Azure 文档中的[Azure 存储的跨域资源共享 (CORS) 支持](https://docs.microsoft.com/en-us/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)。
* 如果您从类似以下方式创建的 HTTP 服务器提供数据：`python -m http.server 8081 -d`，请从命令行运行以下命令：
```bash
npm install http-server -g
http-server -p 3000 --cors
```

并非所有主机都支持 CORS 设置，但您可以尝试在主机配置的管理区域中找到 CORS 设置。

### 音频波形与标注不匹配

如果您发现标注音频数据后，可见的音频波形与时间戳和声音不匹配，请尝试将音频转换为其他格式。例如，如果您正在标注 mp3 文件，请尝试将它们转换为 wav 文件。

```bash
ffmpeg -y -i audio.mp3 -ar 8k -ac 1 audio.wav
```

### 标注者看不到预测结果

请参阅下方的[预标注](#预标注)。

## 云存储和本地存储

在使用外部云存储连接（S3、GCS、Azure）时，请注意以下几点：

* 对于源存储：
   * 当选择**文件**导入方法时，Label Studio 不会导入存储在存储桶中的数据，而是创建对对象的*引用*。因此，您可以完全控制要同步并在标注屏幕上显示的数据。
   * 当选择**任务**导入方法时，存储桶文件被视为不可变；将更新后的文件状态推送到 Label Studio 的唯一方法是使用新文件名上传到存储，或删除与该文件关联的所有任务并重新同步。
* 与外部存储桶的同步操作是单向的。它要么从存储桶中的对象创建任务（源存储），要么将标注推送到输出存储桶（目标存储）。在存储桶端更改内容不能保证结果的一致性。
* 我们建议为每个 Label Studio 项目使用单独的存储桶文件夹。

### CORS 错误

如果您没有设置 CORS，则无法从 Label Studio 查看云存储数据。您可能会看到指向数据的链接而不是数据预览，或者可能在 Web 浏览器控制台中看到 CORS 错误：

* 对于 Amazon S3，请参阅 Amazon S3 用户指南中的[配置和使用跨域资源共享 (CORS)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/cors.html)。
* 对于 GCS，请参阅 Google Cloud Storage 文档中的[配置跨域资源共享 (CORS)](https://cloud.google.com/storage/docs/configuring-cors)。
* 对于 Microsoft Azure，请参阅 Microsoft Azure 文档中的[Azure 存储的跨域资源共享 (CORS) 支持](https://docs.microsoft.com/en-us/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)。

!!! note
    1. 确保为您的服务账号应用正确的角色和权限。例如，为服务账号分配服务账号角色 "roles/iam.serviceAccountTokenCreator"。

    2. 如果使用服务账号名称 `labelstudio` 在 DEBUG 日志中显示错误，则可以使用 `label-studio start` 命令中的 `--log-level DEBUG` 标志启用它们。

### 403 错误

如果您在 Web 浏览器控制台中看到 403 错误，请确保您配置了正确的凭据。

{% details <b>Google Cloud Storage 凭据</b> %}

请参阅 Google Cloud Storage 文档中的[设置身份验证](https://cloud.google.com/storage/docs/reference/libraries#setting_up_authentication)和[Cloud Storage 的 IAM 权限](https://cloud.google.com/storage/docs/access-control/iam-permissions)。

您的账号必须具有**Service Account Token Creator**角色、**Storage Object Viewer**角色以及**storage.buckets.get**访问权限。

此外，如果您使用服务账号授权访问 Google Cloud Platform，请确保激活它。请参阅 Google Cloud SDK 命令行界面文档中的 [gcloud auth activate-service-account](https://cloud.google.com/sdk/gcloud/reference/auth/activate-service-account)。

{% enddetails %}

{% details <b>Amazon S3 凭据</b> %}

对于 Amazon S3，请参阅 Amazon AWS 命令行界面用户指南中的[配置和凭证文件设置](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)。还要检查您的凭据是否能通过 [aws 客户端](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)正常工作。

 * 确保您在创建存储桶时指定了正确的区域。如有需要，请在源存储或目标存储设置中或 `.aws/config` 文件中更改区域，否则可能在访问存储桶对象时遇到问题。
    例如，更新以下内容：`~/.aws/config`

    ```
    [default]
    region=us-east-2  # 更改为您存储桶所在的区域
    ```
- 确保您用于设置源存储或目标存储连接的凭据仍然有效。如果您在浏览器控制台中看到 403 错误，并且您为存储桶设置了正确的权限，则可能需要更新 Access Key ID、Secret Access Key 和 Session ID。请参阅 AWS Identity and Access Management 文档中的[请求临时安全凭据](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_request.html)。

{% enddetails %}

### 点击同步后数据未更新

有时同步过程不会立即开始。这是因为同步过程基于内部作业调度器。如果一段时间后仍无反应，请按照以下步骤操作。

首先，检查您是否指定了正确的凭据（参见上述部分）。

然后前往云存储设置页面，点击云连接旁边的**编辑**。在此处，您可以检查以下内容：

* **文件过滤正则表达式**是否已设置且正确。当未指定过滤器时，所有找到的项目都会被跳过。过滤器应为有效的正则表达式，而不是通配符（例如 `.*` 是有效的，`*.` 是无效的）
* 如果您处理的是存储在存储桶中的图像、音频、文本文件或任何其他二进制内容，**导入方法**应设置为`文件`。

    这将指示 Label Studio 使用 URI 链接（如 `s3://bucket/1.jpg`）自动创建任务，并在打开标注屏幕时将其解析为预签名 `https` URL。

    如果您在存储桶中存储的是 Label Studio 格式的 JSON/JSONL 任务或 Parquet 文件，请将此选项设置为"任务"。

* 检查 rq worker 是否有故障。检查 rq worker 的一个简单方法是完成一次导出操作。

    从数据管理器点击**导出**，创建新快照并下载 JSON 文件。如果看到错误，很可能是您的 rq worker 出现了问题。另一种检查 rq worker 的方法是以超级用户身份登录并前往 `/django-rq` 页面。您应该看到 `workers` 列。如果值为 `0` 或该列为空，这可能表明存在故障。

### 云存储中的 JSON 文件未同步且数据管理器为空

1. 编辑存储设置。如果您在数据管理器中看到任务，请继续执行步骤 2。
2. 将**导入方法**设置为"任务"。

    如果您在数据管理器中看不到任务，则说明您的存储桶没有 GET 权限，只有 LIST 权限。

如果只有 LIST 权限，Label Studio 可以扫描存储桶中是否存在对象，而无需实际读取它们。有了 GET 权限，Label Studio 就可以读取数据并正确提取您的 JSON 文件。


### 任务加载不符合预期

如果任务同步到了 Label Studio 但显示不符合预期，例如显示 URL 而不是图片，或者一个任务中显示了您期望的多个任务，请检查以下内容：
- 如果您在[云存储](storage.html)中放置 JSON 文件，请确保如果同一文件中有多个任务，它们都采用相同的格式（例如，您不能在同一个文件中有一个包含原始 `data` 字段的任务和另一个包含标注和预测的任务）。
- 如果您同步的是图片或音频文件，请确保**导入方法**设置为"文件"。

### 在 Windows 上无法访问本地存储

如果您使用的是 Windows：

1. 确保在设置环境变量 `LABEL_STUDIO_LOCAL_FILES_DOCUMENT_ROOT` 时使用双反斜杠（`\\`）。这是必要的，因为您必须转义反斜杠（\）。
2. 确保在配置项目的本地存储时，在**绝对本地路径**中使用单反斜杠（`\`）。
3. 不要在 `LABEL_STUDIO_LOCAL_FILES_DOCUMENT_ROOT` 或**绝对本地路径**中使用空格或非拉丁字符。

示例：

```bash
LABEL_STUDIO_LOCAL_FILES_DOCUMENT_ROOT=c:\\data\\media
Absolute local path from Local Storage settings = c:\data\media\subpath
```

## 预标注

检查您是否使用了正确的标注单位。

{% details <b>图像标注单位</b> %}

{% insertmd includes/image_units.md %}

{% enddetails %}

### 标注者看不到预测结果

如果标注者看不到预测结果，或者在[将预标注导入 Label Studio](predictions) 后遇到意外行为，请查看以下指南来解决问题。

首先，在项目的**设置 > 标注**部分，确保**使用预测结果预标注任务**已启用。

#### 检查标注配置和任务的配置值

预标注任务 JSON 的 `from_name` 必须与标注配置中 `<Labels name="label" toName="text">` 部分的 `name` 值匹配。`to_name` 必须与 `toName` 值匹配。

例如，以下 XML：
  ```xml
  ...
  <Choices name="choice" toName="image" showInLine="true">`
  ...
  <RectangleLabels name="label" toName="image">
  ...
  ```

应对应以下示例 JSON 的相应部分：
```json
...
"type": "rectanglelabels",        
"from_name": "label", "to_name": "image",
...
type": "choices",
"from_name": "choice", "to_name": "image",
...
```

#### 检查配置和任务中的标签
确保您已为标注界面设置了标注配置，并且 JSON 文件中的标签与配置中的标签完全匹配。如果您使用[工具来转换模型输出](https://github.com/HumanSignal/label-studio-transformers)，请确保标签没有被该工具更改。

#### 检查 ID 和 toName 值
如果您正在执行嵌套标注，例如为特定的 Label 或 Choice 值显示 TextArea 标签，则这些结果的 ID 必须匹配。

例如，如果您想在命名实体识别任务旁边转录文本，您可能有以下标注配置：
```xml
  <View>
    <Labels name="label" toName="text">
      <Label value="PER" background="red"/>
      <Label value="ORG" background="darkorange"/>
      <Label value="LOC" background="orange"/>
      <Label value="MISC" background="green"/>
    </Labels>
    <Text name="text" value="$text"/>
    <TextArea name="entity" toName="text" perRegion="true"/>
  </View>
```

如果您想为此标注配置添加预测文本和建议的转录，可以使用以下示例 JSON。

{% details <b>点击查看详情</b> %}
```json
{
"data":{
         "text":"The world that we live in is a broad expanse of nothingness, said the existential philosopher, before he rode away with his cat on his motorbike. "
      },
   "predictions":[
      {
            "result":[
               {
                  "value":{
                     "start":135,
                     "end":144,
                     "text":"motorbike",
                     "labels":[
                        "ORG"
                     ]
                  },
                  "id":"def",
                  "from_name":"ner",
                  "to_name":"text",
                  "type":"labels"
               },
               {
                  "value":{
                     "start":135,
                     "end":144,
                     "text":[
                        "yay"
                     ]
                  },
                  "id":"def",
                  "from_name":"entity",
                  "to_name":"text",
                  "type":"textarea"
               }
            ]
      }
   ]
}
```

{% enddetails %}

因为 TextArea 标签应用于每个标注的区域，所以标签结果和 textarea 结果的 ID 必须匹配。


#### 只读和隐藏区域

在某些情况下，隐藏或设为`只读`的边界框、文本片段、音频片段等非常有用。您可以在区域中放置 `"readonly": true` 或 `"hidden": true` 来实现此目的（即 `annotations.result` 列表中的字典）。

## 导出

### HTML 标签偏移位置不正确

如果导出的 HTML 标签偏移量与您期望的输出不匹配，例如在处理 HTML 命名实体识别 (NER) 任务时，最常见的原因是 HTML 压缩。当您将 HTML 文件上传到 Label Studio 进行标注时，HTML 会被压缩以去除空白字符。当您标注这些任务时，标签的偏移量适用于压缩后的 HTML 版本，而不是原始未修改的 HTML 文件。

要防止 HTML 文件被压缩，您可以使用不同的导入方法。有关更多信息，请参阅[导入 HTML 数据](tasks.html#Import-HTML-data)。

如果您想修正现有标注，可以按照 Label Studio 的相同方式压缩源 HTML 文件。压缩操作通过以下脚本执行：

```python
import htmlmin

with open("sample.html", "r") as f:
html_doc = f.read()

minified_html_doc = htmlmin.minify(html_doc, remove_all_empty_space=True)
```

如果压缩似乎并未影响偏移量的位置，则可能是复杂的 CSS 或其他原因导致的。

## ML 后端

您可以通过服务器控制台日志调查大多数问题。机器学习后端作为独立于 Label Studio 的服务器运行，因此在故障排除时请确保检查正确的服务器控制台日志。要查看更详细的日志，请使用 `--debug` 选项启动 ML 后端服务器。

如果您正在运行 ML 后端：
- 生产训练日志位于 `my_backend/logs/rq.log`
- 生产运行时日志位于 `my_backend/logs/uwsgi.log`
在开发模式下，训练日志显示在网页浏览器控制台中。

如果您使用 Docker Compose 运行 ML 后端：
- 训练日志位于 `logs/rq.log`
- 主进程和推理日志位于 `logs/uwsgi.log`

### Label Studio 对 ML 服务器请求的默认超时设置

Label Studio 对所有类型的 ML 服务器请求都有默认超时设置。

Label Studio 对 ML 服务器有几种不同的请求：
1. Health - 添加新 ML 后端时检查 ML 后端健康状态的请求（环境变量 `ML_TIMEOUT_HEALTH`）
2. Setup - 设置 ML 后端、初始化 ML 模型的请求（环境变量 ML_TIMEOUT_SETUP）
3. Predict - Label Studio 从 ML 后端获取预测时的预测请求（环境变量 `ML_TIMEOUT_PREDICT`）
4. Train - 训练 ML 后端的请求（环境变量 `ML_TIMEOUT_PREDICT`）
5. Duplicate model - 向 ML 后端发送的复制模型请求（环境变量 `ML_TIMEOUT_PREDICT`）
6. Delete - 向 ML 后端发送的删除请求（环境变量 `ML_TIMEOUT_PREDICT`）
7. Train job status - 从 ML 后端请求训练作业状态（环境变量 `ML_TIMEOUT_PREDICT`）

您可以通过为每个请求设置环境变量来调整超时时间，或在 Label Studio 变量中进行修改。以下是 Label Studio 中的变量部分（以秒为单位）：

```python
CONNECTION_TIMEOUT = float(get_env('ML_CONNECTION_TIMEOUT', 1))  
TIMEOUT_DEFAULT = float(get_env('ML_TIMEOUT_DEFAULT', 100))  
TIMEOUT_TRAIN = float(get_env('ML_TIMEOUT_TRAIN', 30))
TIMEOUT_PREDICT = float(get_env('ML_TIMEOUT_PREDICT', 100))
TIMEOUT_HEALTH = float(get_env('ML_TIMEOUT_HEALTH', 1))
TIMEOUT_SETUP = float(get_env('ML_TIMEOUT_SETUP', 3))
TIMEOUT_DUPLICATE_MODEL = float(get_env('ML_TIMEOUT_DUPLICATE_MODEL', 1))
TIMEOUT_DELETE = float(get_env('ML_TIMEOUT_DELETE', 1))
TIMEOUT_TRAIN_JOB_STATUS = float(get_env('ML_TIMEOUT_TRAIN_JOB_STATUS', 1))
```

您可以在 [ml/api_connector.py](https://github.com/HumanSignal/label-studio/blob/develop/label_studio/ml/api_connector.py#L22..L31) 中修改它们。

### 我启动了 ML 后端，但在 Label Studio UI 中添加后显示为**已断开**

您的 ML 后端服务器可能没有正确启动。

1. 检查 ML 后端服务器是否正在运行。运行以下健康检查：<br/> `curl -X GET http://localhost:9090/health`
2. 如果健康检查没有响应，或者您看到错误，请检查服务器日志。
3. 如果您使用 Docker Compose 启动 ML 后端，请检查用于设置 Docker 内部环境的 `requirements.txt` 文件中是否缺少依赖项。


### ML 后端似乎已连接，但点击"开始训练"后，我看到"错误。点击此处查看详情。"消息

点击错误消息查看回溯信息。可能出现的常见错误包括：
- 用于开始训练的已完成标注数量不足。
- 服务器上的内存问题。
如果您无法自行解决回溯问题，请<a href="https://slack.labelstud.io/?source=docs-ML">在 Slack 上联系我们</a>。

### 我的预测结果不正确，或者我在标注页面上看不到模型预测结果

您的 ML 后端可能以错误的格式生成预测。

- 检查 ML 后端的预测格式是否与[导入预标注中的预测](predictions.html)结构相同。
- 确认项目的标签配置与 ML 后端产生的输出匹配。例如，使用 `<Choices>` 标签为文本创建预测类别。请参阅更多 [Label Studio 标签](/tags)。

### 模型后端无法启动或正常运行

如果在启动 ML 后端服务器后在终端中看到有关缺少包的错���，或在日志中看到错误，您可能需要在 ML 后端的 `requirements.txt` 文件中指定其他包。

### ML 后端无法访问任务

由于 ML 后端和 Label Studio 是不同的服务，您标注的资源（图像、音频等）必须托管并通过 URL 对机器学习后端可访问，否则可能无法创建预测。

### 添加 ML 后端时出现验证错误

如果在向 Label Studio 项目添加 ML 后端 URL 时出现验证错误，请检查以下内容：
- 标注界面是否使用有效的配置进行了设置？
- 机器学习后端是否正在运行？运行以下健康检查：<br/> `curl -X GET http://localhost:9090/health`
- 您的机器学习后端是否可以从 Label Studio 实例访问？它必须对运行 Label Studio 的实例可用。

如果您在 Docker 中运行 Label Studio，您必须在同一个 Docker 容器中运行机器学习后端，或以其他方式使其对运行 Label Studio 的 Docker 容器可用。您可以使用 `docker exec` 命令在 Docker 容器内运行命令，或使用 `docker exec -it <container_id> /bin/sh` 在容器上下文中启动 shell。请参阅 [docker exec 文档](https://docs.docker.com/engine/reference/commandline/exec/)。


### Windows 上的 "No such file or directory" 错误

如果您在 Windows 上运行 `docker-compose up --build` 时遇到类似以下错误：

```
exec /app/start.sh : No such file or directory
exited with code 1
```

此问题很可能是由 Windows 对文本文件中换行符的处理引起的，这可能会影响 `start.sh` 等脚本。要解决此问题，请按照以下步骤操作：

#### 步骤 1：调整 Git 配置

在克隆仓库之前，请确保您的 Git 配置为在检出文件时不自动将换行符转换为 Windows 风格 (CRLF)。这可以通过将 `core.autocrlf` 设置为 `false` 来实现。打开 Git Bash 或您偏好的终端并执行以下命令：

```
git config --global core.autocrlf false
```

#### 步骤 2：重新克隆仓库

如果您在调整 Git 配置之前已经克隆了仓库，则需要重新克隆以确保换行符被正确保留：

1. **删除现有的本地仓库。** 确保您已备份所有更改或进行中的工作。
2. **重新克隆仓库。** 使用标准 Git clone 命令将仓库克隆到本地机器。

#### 步骤 3：构建并运行 Docker 容器

导航到克隆仓库中包含 Dockerfile 和 `docker-compose.yml` 的相应目录。然后，继续执行 Docker 命令：

1. **构建 Docker 容器：** 运行 `docker-compose build` 以根据 `docker-compose.yml` 中指定的配置构建 Docker 容器。

2. **启动 Docker 容器：** 构建过程完成后，使用 `docker-compose up` 启动容器。

#### 补充说明

- 此解决方案专门解决 Windows 上由于自动转换换行符而导致的问题。如果您使用的是其他操作系统，此解决方案可能不适用。
- 请记得检查项目的 `.gitattributes` 文件（如果存在），因为它也会影响 Git 处理文件换行符的方式。

按照这些步骤操作，您应该能够解决由于换行符转换导致 Docker 无法在 Windows 上识别 `start.sh` 脚本的问题。


### Docker 镜像中的 Pip 缓存重置

有时，您希望重置 pip 缓存以确保安装最新版本的依赖项。
例如，Label Studio ML Backend 库在 requirements.txt 中以
`label-studio-ml @ git+https://github.com/HumanSignal/label-studio-ml-backend.git` 的方式使用。假设它已更新，而您想在带有 ML 模型的 Docker 镜像中使用最新版本。

您可以使用以下命令从头重建 Docker 镜像：

```bash
docker compose build --no-cache
```

### `Bad Gateway` 和 `Service Unavailable` 错误

如果您发送多个并发请求，可能会看到这些错误。

请注意，提供的 ML 后端示例是以开发模式提供的，不支持生产级别的推理服务。

### ML 后端无法进行简单的自动标注或无法看到预测

您必须确保 ML 后端可以访问您的 Label Studio 数据。如果不能，您可能会遇到以下问题：

* 服务器日志中出现 `no such file or directory` 错误。
* 在 Label Studio 中加载任务时无法看到预测。
* 您的 ML 后端似乎已正确连接，但无法完成任务中的任何自动标注。

要解决此问题，请确保您已设置 `LABEL_STUDIO_URL` 和 `LABEL_STUDIO_API_KEY` 环境变量。有关更多信息，请参阅[允许 ML 后端访问 Label Studio 数据](ml#Allow-the-ML-backend-to-access-Label-Studio-data)。
