# Blocky - 快速轻量的 DNS 代理和广告拦截器

本项目将开源的 DNS 代理和广告拦截工具 **Blocky** 移植到 Lazycat 平台,实现一键部署。Blocky 是一个快速、轻量级的 DNS 代理,专为本地网络设计,具有强大的广告拦截和隐私保护功能。

## 项目简介

Blocky 是一个高性能的 DNS 代理服务器,主要用于本地网络环境中拦截广告、追踪器和恶意域名。它采用 Go 语言开发,具有出色的性能和极低的资源占用。通过 Lazycat 平台,您可以快速部署 Blocky,无需手动配置复杂的 Docker 环境。

## 主要功能

- **广告和追踪器拦截** - 基于可自定义的黑名单拦截广告、追踪器和恶意域名
- **多种拦截列表支持** - 支持多种格式的拦截列表,包括 hosts 文件和域名列表
- **客户端分组** - 支持为不同客户端配置不同的拦截规则和上游 DNS 服务器
- **DNS 缓存** - 内置智能缓存机制,提升 DNS 查询速度
- **条件转发** - 支持根据域名条件转发到不同的上游 DNS 服务器
- **DoH 和 DoT 支持** - 支持 DNS over HTTPS (DoH) 和 DNS over TLS (DoT)
- **查询日志** - 可选的查询日志记录,便于监控和调试
- **REST API** - 提供 REST API 接口用于管理和监控
- **Redis 支持** - 可选的 Redis 缓存支持,用于多实例部署
- **Prometheus 监控** - 内置 Prometheus 指标导出

## 快速开始

### 在 Lazycat 平台部署

Lazycat 平台提供了一键部署功能,无需手动配置 Docker 环境:

1. 访问 Lazycat 应用市场
2. 搜索并选择 "Blocky"
3. 点击"一键部署"按钮
4. 根据需要调整配置参数
5. 完成部署后即可使用

### 本地开发

如需在本地进行开发和测试,请参考以下步骤:

#### 前置要求

- Make
- Git
- lzc-cli (使用 `npm install -g @lazycatcloud/lzc-cli` 安装)

#### 常用命令

```bash
make help        # 查看可用命令
make info        # 查看项目信息
make clean       # 清理构建文件
make lpk         # 构建 Lazycat 应用包
make deploy      # 部署到 Lazycat 平台
make uninstall   # 卸载应用
```

## 配置说明

### Docker 本地运行

本项目的 `docker-compose.yml`、`.env` 和 `docker-run.sh` 文件改编自上游 Blocky 仓库的官方安装文档 [`docs/installation.md`](https://github.com/0xERR0R/blocky/blob/master/docs/installation.md),保持了推荐的部署模式。

1. 在项目目录中创建您自己的 `config.yml` 配置文件(参考上游配置指南),并确保路径与 `docker-compose.yml` 中的卷映射匹配
2. 根据需要调整 `.env` 文件中的镜像标签、主机名或端口映射。`BLOCKY_DNS_PORT` 控制主机端口(默认 54,避免冲突),`BLOCKY_CONTAINER_DNS_PORT` 应与 `config.yml` 中的 `ports.dns` 对齐(默认 53)
3. 通过 `./docker-run.sh` 启动或管理服务栈,该脚本封装了原生的 `docker compose` 命令:
   - `./docker-run.sh up -d` - 后台启动 Blocky
   - `./docker-run.sh logs -f` - 查看服务日志
   - 可以传递任何其他 `docker compose` 子命令
4. 脚本会导出 `DOCKER_DEFAULT_PLATFORM=linux/amd64`,确保在 Apple Silicon 主机上拉取兼容的镜像

### 拦截功能演示

默认的 `config.yml` 已经在 `blocking.denylists.ads` 下启用了 StevenBlack 拦截列表,`blocking.clientGroupsBlock.default` 将其应用到所有客户端。服务运行后:

1. 将 DNS 客户端指向 `127.0.0.1#54` (或直接通过 `dig` 运行查询)
2. 解析正常域名以确认通过,例如 `dig +tcp @127.0.0.1 -p 54 example.com`
3. 解析拦截列表中包含的已知广告域名,例如 `dig +tcp @127.0.0.1 -p 54 doubleclick.net`。Blocky 会返回 `0.0.0.0`,表示请求已被拦截
4. 通过 `./docker-run.sh logs -f` 查看日志中的 `response_reason=BLOCKED` 条目

如需添加更细粒度的规则,可以扩展 `config.yml` 中的 `blocking.denylists` 或创建自定义分组,然后通过 `./docker-run.sh restart blocky` 重启服务。

## 致谢

本项目基于 [Blocky](https://github.com/0xERR0R/blocky) 开源项目进行移植和封装。感谢 Blocky 开发团队和开源社区的贡献者们创造了如此优秀的 DNS 代理工具。

特别感谢:
- Blocky 项目维护者 [0xERR0R](https://github.com/0xERR0R) 及所有贡献者
- 提供各种拦截列表的社区项目,如 StevenBlack hosts 项目
- Lazycat 平台提供的应用部署能力

## 版权说明

### 本封装项目

Copyright (c) 2025 Lazycat Apps

本封装项目采用 Apache License 2.0 许可证,详见 [LICENSE](LICENSE) 文件。

### Blocky 原项目

Blocky 原项目同样采用 Apache License 2.0 许可证。更多信息请参阅 [Blocky 官方仓库](https://github.com/0xERR0R/blocky)。

## 相关链接

- **Blocky 官方网站**: https://0xerr0r.github.io/blocky/
- **Blocky GitHub 仓库**: https://github.com/0xERR0R/blocky
- **Blocky 官方文档**: https://0xerr0r.github.io/blocky/latest/
- **Lazycat 平台**: https://lazycat.cloud/
- **问题反馈**: 如有问题或建议,请在本项目的 GitHub Issues 中提交

## 开源协议

本项目采用 Apache License 2.0 开源协议。您可以自由使用、修改和分发本项目,但需要遵守协议条款。详细信息请查看 [LICENSE](LICENSE) 文件。
