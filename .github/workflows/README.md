# GitHub Actions Workflows

## Docker Build and Push Workflow

### 概述
此工作流自动构建 Docker 镜像并推送到 Docker Hub 和 GitHub Container Registry (GHCR)。

### 触发条件
- **推送到 main 分支**: 构建并推送镜像，标记为 `main` 和 `latest`
- **发布标签 (v*)**: 构建并推送镜像，使用语义化版本标签 (例如: `v1.0.0`, `1.0`, `1`)
- **Pull Request**: 仅构建镜像，不推送（用于验证）
- **手动触发**: 可通过 GitHub Actions 页面手动运行

### 配置要求

#### Docker Hub（可选）
如需推送到 Docker Hub，请在仓库设置中添加以下 Secrets：
1. 进入仓库 Settings → Secrets and variables → Actions
2. 添加以下 secrets:
   - `DOCKER_USERNAME`: 您的 Docker Hub 用户名
   - `DOCKER_PASSWORD`: Docker Hub 密码或访问令牌（推荐使用访问令牌）

**创建 Docker Hub 访问令牌**:
1. 登录 [Docker Hub](https://hub.docker.com/)
2. 进入 Account Settings → Security → New Access Token
3. 创建只读或读写令牌
4. 将令牌保存到 `DOCKER_PASSWORD` secret

#### GitHub Container Registry (GHCR)
GHCR 使用内置的 `GITHUB_TOKEN`，无需额外配置。镜像将自动推送到 `ghcr.io/huanlinoto/aitradegame`。

### 镜像标签策略
- `main`: 最新的 main 分支构建
- `latest`: main 分支的别名（仅默认分支）
- `v1.0.0`: 发布标签的完整版本
- `1.0`: 发布标签的主版本.次版本
- `1`: 发布标签的主版本
- `pr-123`: Pull request #123 的构建

### 支持的平台
- `linux/amd64` (x86_64)
- `linux/arm64` (ARM64/aarch64)

### 使用构建的镜像

```bash
# 从 Docker Hub 拉取
docker pull huanlinoto/aitradegame:latest

# 从 GitHub Container Registry 拉取
docker pull ghcr.io/huanlinoto/aitradegame:latest

# 运行容器
docker run -d -p 5000:5000 -v ./data:/app/data huanlinoto/aitradegame:latest
```

### 缓存
工作流使用 GitHub Actions 缓存来加速构建过程。首次构建可能较慢，但后续构建会显著加快。

### 故障排除
- **推送失败**: 检查 Docker Hub secrets 是否正确配置
- **构建失败**: 检查 Dockerfile 语法和依赖项
- **权限错误**: 确保仓库有 `packages: write` 权限（GHCR）
