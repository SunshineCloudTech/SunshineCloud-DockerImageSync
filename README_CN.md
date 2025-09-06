# SunshineCloud Docker 镜像同步与构建工具

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF?logo=github-actions&logoColor=white)](https://github.com/features/actions)

一个全面的Docker镜像同步和构建工具，帮助您：
- **同步Docker镜像** 在不同注册表之间自动同步
- **下载Docker镜像** 打包为压缩文件供离线使用
- **支持多种架构**：AMD64、ARM64、ARM32
- **自动化工作流** 基于GitHub Actions

## 🚀 功能特性

### 镜像同步
- 自动同步Docker镜像到阿里云容器镜像服务(ACR)
- 定时同步（每日太平洋时间上午7点和下午7点）
- 支持手动触发同步

### 镜像构建与下载
- 拉取并打包Docker镜像用于离线部署
- 支持多种架构：
  - **AMD64** (x86-64) - 最常见的架构
  - **ARM64** (aarch64) - 适用于ARM服务器和Apple Silicon
  - **ARM32** (armv7) - 适用于树莓派和物联网设备
- 两种分发方式：
  - **Release资产** (推荐用于小于2GB的镜像)
  - **Artifacts** (适用于小于5GB的大镜像)

## 📋 使用方法

### 方法一：Fork本项目使用

#### 1、Fork本项目到自己的空间
![1](https://github.com/user-attachments/assets/8bfc9d45-76fb-4cf4-be80-19fb202e8a95)
![2](https://github.com/user-attachments/assets/cf990b0c-a149-4f5b-9be8-d0f909f8f402)
![3](https://github.com/user-attachments/assets/d9560061-4092-4ea2-9b8c-a580c9fdcf2d)

#### 2、配置必要的Secrets
在您Fork的仓库中配置以下Secrets：
- `REPO_TOKEN`: GitHub个人访问令牌，需要repo权限
- `SUNSHINECLOUD_ACR_PASSWORD`: 阿里云容器镜像服务密码（可选，仅同步功能需要）

#### 3、输入要构建的Docker镜像名称和标签
> 如果不输入标签，则默认拉取latest标签，但请确保该镜像有latest标签 <br>
> 如图左侧选项代表要构建的平台。支持AMD64、ARM64、ARM32三种架构。<br>
> 若构建的Docker镜像小于2GB，推荐发布到Release，选择红色框的部分。绝大多数镜像都是小于2GB的<br>
> 若构建的Docker镜像大于2GB小于5GB，推荐使用另外三个工作流<br>
> 若要构建大于5GB的镜像，本项目不适合。<br>

![4](https://github.com/user-attachments/assets/1a28afc2-2af2-47ff-91fa-0d42282a260a)
![5](https://github.com/user-attachments/assets/5fc5e388-ffb7-4242-aa0e-076136e1e974)
![6](https://github.com/user-attachments/assets/06bf9e37-cfbe-48cc-9012-1c7c92783d6c)
![7](https://github.com/user-attachments/assets/2fb1babf-1179-45fe-a686-2946939e1ac9)

### 方法二：使用我们预构建的镜像

从我们的[Releases](../../releases)页面下载预构建的Docker镜像。

## 🔧 配置文件

### `images.yaml`
定义同步的源镜像和目标镜像：
```yaml
"debian:bullseye-slim,bookworm-slim": "registry.cn-hangzhou.aliyuncs.com/sunshinecloud/debian"
```

### `auth.yaml`
注册表认证配置：
```yaml
registry.cn-hangzhou.aliyuncs.com/sunshinecloud:
  password: ${SUNSHINECLOUD_ACR_PASSWORD}
  username: SunshineCloudTech
```

## 🏗️ 可用工作流

| 工作流 | 描述 | 输出大小 | 分发方式 |
|--------|------|----------|----------|
| `amd64-release.yml` | AMD64镜像通过Release | < 2GB | GitHub Releases |
| `amd64.yml` | AMD64镜像通过Artifacts | < 5GB | GitHub Artifacts |
| `arm64-release.yml` | ARM64镜像通过Release | < 2GB | GitHub Releases |
| `arm64.yml` | ARM64镜像通过Artifacts | < 5GB | GitHub Artifacts |
| `arm32-release.yml` | ARM32镜像通过Release | < 2GB | GitHub Releases |
| `arm32.yml` | ARM32镜像通过Artifacts | < 5GB | GitHub Artifacts |
| `sync-images.yml` | 同步镜像到ACR | N/A | 注册表 |

## 💾 加载离线Docker镜像

下载`.tar.gz`文件后：

```bash
# 方法1
docker load -i 镜像名称.tar.gz

# 方法2
docker load < 镜像名称.tar.gz
```

**注意**：如果是从Artifacts下载的压缩文件，需要先解压ZIP文件，解压后得到的`.tar.gz`文件可以直接load，无需再解压。

## 📚 使用示例

### 构建常用镜像
```
alpine:latest,alpine/curl,busybox:stable-glibc
nginx:latest,nginx:alpine
ubuntu:20.04,ubuntu:22.04
node:18-alpine,node:20-alpine
```

### 同步配置示例
```yaml
# 同步同一镜像的多个标签
"nginx:latest,alpine,1.21": "registry.cn-hangzhou.aliyuncs.com/sunshinecloud/nginx"

# 同步不同镜像
"redis:latest": "registry.cn-hangzhou.aliyuncs.com/sunshinecloud/redis:latest"
"mysql:8.0": "registry.cn-hangzhou.aliyuncs.com/sunshinecloud/mysql:8.0"
```

## 🎥 视频教程

[![Bilibili](https://img.shields.io/badge/Bilibili-123456?logo=bilibili&logoColor=fff&labelColor=fb7299)](https://www.bilibili.com/video/BV1yyq6YREdF)
[![Bilibili](https://img.shields.io/badge/视频教程-FC7C0D?logo=bilibili&logoColor=fff&labelColor=000)](https://www.bilibili.com/video/BV1EZ421M7mL)

## 🔗 快速链接

- [🚀 国内加速站下载](https://wkdaily.cpolar.top/archives/1)
- [📦 GitHub Releases](../../releases)
- [🏗️ GitHub Actions](../../actions)

## 💡 使用提示

1. **选择合适的架构**：根据您的目标部署环境选择对应的架构
2. **镜像大小考虑**：小于2GB的镜像建议使用Release版本，下载更便捷
3. **批量下载**：可以一次性输入多个镜像名称，用英文逗号分隔
4. **标签处理**：如不指定标签，系统会自动使用latest标签

## ❓ 常见问题

**Q: 为什么有的镜像构建失败？**
A: 可能原因包括：镜像不存在、标签错误、镜像过大（超过5GB）或网络问题。

**Q: 如何查看构建进度？**
A: 在Actions页面可以实时查看工作流执行状态和日志。

**Q: 下载的文件如何使用？**
A: 使用`docker load`命令加载tar.gz文件即可。

## 📄 许可证

本项目基于GNU通用公共许可证v3.0开源 - 查看[LICENSE](LICENSE)文件了解详情。

## 🤝 贡献

欢迎贡献！请随时提交Pull Request。

## ⭐ 支持

如果这个项目对您有帮助，请给个星标⭐！
