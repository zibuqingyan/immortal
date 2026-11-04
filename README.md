# immortal

> 影视元数据 / 订阅 / 刮削管理面板。Docker 一键部署，前端已内置，无需编译。
>
> 维护者：**子不轻言** · 当前版本 **2.0.2**
>
> Docker Hub：**[zibuqingyan/immortal](https://hub.docker.com/r/zibuqingyan/immortal)**
> [![Docker Pulls](https://img.shields.io/docker/pulls/zibuqingyan/immortal)](https://hub.docker.com/r/zibuqingyan/immortal)
> [![Docker Version](https://img.shields.io/docker/v/zibuqingyan/immortal?sort=semver)](https://hub.docker.com/r/zibuqingyan/immortal/tags)
> [![Image Size](https://img.shields.io/docker/image-size/zibuqingyan/immortal/2.0.2)](https://hub.docker.com/r/zibuqingyan/immortal/tags)

---

## 这是什么

immortal 不负责播放，负责「片子进库之前和之后的所有麻烦事」：

- 从多个站点抓取番号、标题、简介、演员、海报、背景图
- 把元数据写成 Emby / Jellyfin / Plex 能识别的 `.nfo` 与图片（按 2:3 裁切、与视频同名）
- 按规则自动订阅新片、自动下载、自动整理归档（移动 / 硬链接 / 软链接）
- 把媒体库情况与观影习惯统计成图表，一键生成可分享的观影海报

界面支持**仙侠 / 通俗双命名**，一键切换全站文案。

首次启动有**六步配置向导**（媒体目录 / 媒体服务器 / 下载器 / 网络与代理 / 抓取源 / 连通自检），每一步都能跳过、配置随时可改；配置不全时观星台会显示引导条。

## 下载

### 方式一：直接拉镜像（推荐）

```bash
docker pull zibuqingyan/immortal:2.0.2
```

然后 `deploy/docker-compose.yml` 里把 `image:` 改成 `zibuqingyan/immortal:2.0.2`，或本地打 tag：

```bash
docker tag zibuqingyan/immortal:2.0.2 immortal:2.0.2
```

### 方式二：离线包

到 [Releases](../../releases) 取对应版本的 `*-compose.zip`（镜像 + compose + 文档）、`*-image.tar.gz`（只要镜像）或 `*-compose.tar.gz`（只要 compose 与文档）。

## 三步启动

```bash
docker pull zibuqingyan/immortal:2.0.2
#   或： gunzip -c immortal-2.0.2-image.tar.gz | docker load
cp .env.example .env && vi .env   # 改 MEDIA_DIR 与 POSTGRES_PASSWORD
docker compose up -d && docker compose ps
```

浏览器打开 `http://<IP>:8000/sign-up` 注册第一个账号，按引导条完成首次配置。完整步骤见 **[WIKI.md](WIKI.md)**。

## 能力一览

| 模块 | 能力 |
|---|---|
| 刮削 | 目录监听 / 批量任务 / 手动补刮；多源并发 + 字段级融合 |
| 元数据 | NFO 生成、海报竖版裁切、演员资料补全与同步回 Emby |
| 订阅 | 影片 / 演员 / 综合订阅；过滤器 + 优先级 + 偏好三层规则 |
| 下载 | qBittorrent / Transmission / CloudDrive / 迅雷 |
| 整理 | 目录浏览、扫描识别番号、空目录清理（先出计划再执行） |
| 统计 | Emby 观影统计、排行、观影海报生成 |
| 运维 | 定时任务（cron）+ 执行日志 + API Token + 三渠道通知 |
| 配置 | 六步首次配置向导、连通自检 |
| 可观测 | 数据源健康度（成功 / 请求失败 / 解析失败 / 真空未命中 / 不可用）、数据覆盖率 |

## 环境要求

| 项目 | 要求 |
|---|---|
| Docker | 20.10+ |
| Docker Compose | v2 |
| 磁盘 | 约 3 GB |
| 内存 | 512 MB 起步，1 GB 更稳 |

## 常见问题

**Q：刮削全是「未命中」**
多数是出网问题。在 `.env` 里配 `SCRAPE_PROXY` 指向自己的代理后重启。天机台「灵源健康度」会区分请求失败、解析失败与真空未命中，先看清是哪一类。

**Q：NFO 生成了但 Emby 不认**
NFO 必须与视频同名（`ABC-123.mp4` ↔ `ABC-123.nfo`）。

**Q：改了数据库密码就连不上**
密码只在 `pgdata` 首次初始化时写入，要么改回去，要么清库重来（会丢数据）。

**Q：从 2.0.0 升级要注意什么**
2.0.1 起 JWT 密钥改为随机持久化，升级后**所有登录态与旧 API Token 失效，重新登录一次即可**；数据在 `/data` 卷内不受影响。

## 声明

本项目仅供个人自建媒体库的元数据管理使用，不提供、不托管、不分发任何音视频内容。

## 版本

| 版本 | 要点 |
|---|---|
| 2.0.2 | 六步首次配置向导 + 连通自检；数据源失败归因（四态健康度 / 单源自测）；数据覆盖率可视化与一键回填；PWA 冷启缓存防回归；CORS 默认同源 |
| 2.0.1 | 安全加固：JWT 密钥随机持久化、登录态默认过期、敏感接口鉴权；天机台补「硬链接」选项 |
| 2.0.0 | 双命名全站贯通、版本页加维护者、镜像去掉私有内网默认值 |
