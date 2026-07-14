---
title: 设置数据库
type: guide
tier: opensource
order: 84
order_enterprise: 0
meta_title: 数据库存储设置
meta_description: 配置 Label Studio 使用的数据库存储，以确保高性能和可扩展的数据及配置存储。
section: "安装与设置"
---

Label Studio 使用数据库来存储项目数据和配置信息。

## 标注性能
SQLite 数据库可能适用于拥有数万个标注任务的项目，只要您不计划在数据管理器中使用复杂的过滤器和其他复杂的多用户流水线。如果您想要标注数百万个任务，或者预计会有大量并发用户，或者计划在真实项目上工作，请使用 PostgreSQL 数据库。有关更多信息，请参阅[安装和升级 Label Studio](install.html#PostgreSQL-database)。

例如，如果您在进行标注的同时导入数据，标注任务的加载可能需要超过 10 秒，标注执行也可能需要超过 10 秒。如果您想要标注超过 100,000 个任务且有 5 个或更多并发用户，请考虑使用 PostgreSQL 或其他数据库与 Label Studio 配合使用。

## SQLite 数据库

Label Studio 默认使用 SQLite。您不需要进行任何配置。Label Studio 将所有数据存储在管理员用户指定目录中的单个文件中。在[启动 Label Studio](start.html) 后，使用的目录会打印在终端中。

## PostgreSQL 数据库

您也可以将任务和标注存储在 [PostgreSQL 数据库](https://www.postgresql.org/)中，而不是默认的 SQLite 数据库。如果您打算频繁导入新的标注任务，或计划跨项目标注数十万或更多的任务，建议使用此方式。

### 在启动时创建连接

运行以下命令启动 Label Studio，配置与 PostgreSQL 数据库的连接，扫描现有任务，并将其加载到应用程序中以供特定项目的标注使用。

```bash
label-studio start my_project --init -db postgresql
```

您必须设置以下环境变量以将 Label Studio 连接到 PostgreSQL：

```
DJANGO_DB=default
POSTGRE_NAME=postgres
POSTGRE_USER=postgres
POSTGRE_PASSWORD=
POSTGRE_PORT=5432
POSTGRE_HOST=db
```

### 使用 Docker Compose 创建连接

当您使用 Docker Compose 启动 Label Studio 时，您将使用 PostgreSQL 数据库启动它：
```bash
docker-compose up -d
```

## Minio Blob 存储
MinIO 是一种与 Amazon S3 兼容的 Blob 存储解决方案。您可以使用 MinIO 来存储您的标注任务。

### 启动容器
对于本地开发，您可以托管一个本地 MinIO 服务器，以更接近地模拟基于 S3 的生产环境。
Label Studio 仓库中提供了此用途的示例 docker-compose 文件：[Label Studio repository](https://github.com/HumanSignal/label-studio)。

要同时运行 MinIO 和您的 Label Studio 实例，请使用以下命令：
````bash
# 如果您不是 docker 组的成员，请在 Linux 上添加 sudo
docker compose -f docker-compose.yml -f docker-compose.minio.yml up -d
````
MinIO 服务器将可通过 http://localhost:9000 访问。
要配置 MinIO 设置，请创建一个 `.env` 文件。请记得覆盖默认凭据。

````.dotenv
MINIO_ROOT_USER=minio_admin_do_not_use_in_production
MINIO_ROOT_PASSWORD=minio_admin_do_not_use_in_production

# 要自动选择正确的 minio compose 文件，您可以添加以下行之一：
# Windows
COMPOSE_FILE=docker-compose.yml;docker-compose.minio.yml
# Linux/Mac
COMPOSE_FILE=docker-compose.yml:docker-compose.minio.yml

# 要使用特定的 minio 版本，您可以设置以下环境变量
# MINIO_VERSION=RELEASE.2025-04-22T22-12-26Z
````

### 将 Label Studio 连接到本地 MinIO

如果您没有静态 IP 地址，请在 hosts 文件中创建一个条目，以便 Label Studio 容器和您的浏览器都能通过相同的主机名找到 MinIO。

以下条目将所有对 MinIO 的请求重定向到您的本地系统：
```text
127.0.0.1 minio
```

在 Windows 上，您可以在 `C:\Windows\System32\drivers\etc\hosts` 找到您的 hosts 文件。
在 Linux 上，您可以在 `/etc/hosts` 找到您的 hosts 文件。
在 macOS 上，您可以在 `/private/etc/hosts` 找到您的 hosts 文件。

修改 hosts 文件后，您可以通过 http://minio:9000 在浏览器中连接到您的 MinIO 服务器。

### 移除 MinIO 数据
您可以通过移除容器和相关卷来移除 MinIO 安装。
此操作具有破坏性，将移除存储在 MinIO 中的所有数据。
```bash
docker-compose -f docker-compose.minio.yml down --volumes
```


## 数据持久化

如果您使用 Docker 容器、Heroku 或其他云提供商，您可能希望在关闭 Label Studio 后数据仍然持久存在。您可以[导出数据](export.html)以持久化您的标注任务数据和标注，但要保留 Label Studio 的状态以及您上传用于标注的文件等资产，请设置数据持久化。

### 使用 Docker 持久化数据

在您的机器上挂载 Docker 卷，以在终止运行 Label Studio 的 Docker 容器后持久化内部 SQLite 数据库和您上传到 Label Studio 的资产。

如果您从命令行启动 Docker 容器，请使用卷来持久化数据。请参阅 Docker 文档中的[使用卷](https://docs.docker.com/storage/volumes/)。例如，将 Docker 命令中现有的卷标志替换为您指定的卷：
```bash
docker run -it -p 8080:8080 -v <yourvolume>:/label-studio/data heartexlabs/label-studio:latest
```

!!! attention "important"
    由于这是一个非 root 容器，挂载的文件和目录必须具有 `UID 1001` 的适当权限。

如果您使用 [Label Studio 仓库中包含的配置](https://github.com/HumanSignal/label-studio/blob/develop/docker-compose.yml)通过 Docker Compose 启动，您可以在 Label Studio 的 `docker-compose.yml` 文件中设置 Docker 卷：
```
version: "3.3"
services:
  label_studio:
    image: heartexlabs/label-studio:latest
    container_name: label_studio
    ports:
      - 8080:8080
    volumes:
      - ./mydata:/label-studio/data

volumes:
  mydata:
```

!!! attention "important"
    由于这是一个非 root 容器，挂载的文件和目录必须具有 `UID 1001` 的适当权限。

有关在 Docker Compose 中指定卷的更多信息，请参阅 [Docker Compose 文件文档](https://docs.docker.com/compose/compose-file/compose-file-v3/#volumes)中的卷部分。

### 使用云提供商持久化数据
托管一个由您管理的 PostgreSQL 服务器，并在 Label Studio 中设置 PostgreSQL 环境变量，以从 Heroku、Amazon Web Services、Google Cloud Services 或 Microsoft Azure 等云提供商持久化数据。
