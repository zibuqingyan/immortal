# immortal

> 影视元数据 / 订阅 / 刮削管理面板。Docker 一键部署，前端已内置，无需编译。
>
> 维护者：**子不轻言** · 当前版本 **2.0.0**
>
> Docker Hub：**[zibuqingyan/immortal](https://hub.docker.com/r/zibuqingyan/immortal)**
> [![Docker Pulls](https://img.shields.io/docker/pulls/zibuqingyan/immortal)](https://hub.docker.com/r/zibuqingyan/immortal)
> [![Image Size](https://img.shields.io/docker/image-size/zibuqingyan/immortal/2.0.0)](https://hub.docker.com/r/zibuqingyan/immortal/tags)

---

## 这是什么

immortal 不负责播放，负责「片子进库之前和之后的所有麻烦事」：

- 从多个站点抓取番号、标题、简介、演员、海报、背景图
- 把元数据写成 Emby / Jellyfin / Plex 能识别的 `.nfo` 与图片（按 2:3 裁切、
  与视频同名）
- 按规则自动订阅新片、自动下载、自动整理归档（移动 / 硬链接 / 软链接）
- 把媒体库情况与观影习惯统计成图表，一键生成可分享的观影海报

界面支持**仙侠 / 通俗双命名**，一键切换全站文案（标题、描述、按钮、提示、
海报文案一起变）。

## 下载

### 方式一：直接拉镜像（推荐）

```bash
docker pull zibuqingyan/immortal:2.0.0
```

然后 `deploy/docker-compose.yml` 里把 `image:` 改成 `zibuqingyan/immortal:2.0.0`，
或者本地打个 tag 省得改：

```bash
docker tag zibuqingyan/immortal:2.0.0 immortal:2.0.0
```

### 方式二：离线包

到 [Releases](../../releases/latest) 取：

| 文件 | 大小 | 用途 |
|---|---|---|
| `immortal-2.0.0-compose.zip` | ~157 MB | 镜像 + compose + 文档，一次到位 |
| `immortal-2.0.0-image.tar.gz` | ~158 MB | 只要镜像（已有 compose 时用这个） |
| `immortal-2.0.0-compose.tar.gz` | ~12 KB | 只要 compose 与文档 |

## 三步启动

```bash
# 1) 拿到镜像（二选一）
docker pull zibuqingyan/immortal:2.0.0
#   或： gunzip -c immortal-2.0.0-image.tar.gz | docker load

# 2) 配置（改 MEDIA_DIR 与 POSTGRES_PASSWORD 两项）
cp .env.example .env && vi .env

# 3) 起服务
docker compose up -d && docker compose ps
```

浏览器打开 `http://<IP>:8000/sign-up` 注册第一个账号即可。

完整步骤、首次配置顺序、每个页面怎么用，见 **[WIKI.md](WIKI.md)**。

## 能力一览

| 模块 | 能力 |
|---|---|
| 刮削 | 目录监听 / 批量任务 / 手动补刮；多源并发 + **字段级融合**（海报来自 A、简介来自 B） |
| 元数据 | NFO 生成、海报竖版裁切、演员资料补全与同步回 Emby |
| 订阅 | 影片 / 演员 / 综合订阅，暂停恢复取消；过滤器 + 优先级 + 偏好三层规则 |
| 下载 | qBittorrent / Transmission / CloudDrive / 迅雷 四类下载器 |
| 整理 | 目录浏览、扫描识别番号、空目录清理（一律先出计划再执行） |
| 统计 | Emby 观影统计、排行、观影海报生成 |
| 运维 | 定时任务（cron）+ 执行日志 + API Token + 三渠道通知推送 |

## 环境要求

| 项目 | 要求 |
|---|---|
| Docker | 20.10+ |
| Docker Compose | v2 |
| 磁盘 | 约 3 GB（镜像 1.2 GB 解压后 + 数据库增长） |
| 内存 | 512 MB 起步，1 GB 更稳 |

## 常见问题

**Q：刮削全是「未命中」**
多数是出网问题。在 `.env` 里配 `SCRAPE_PROXY` 指向你自己的代理，重启即可；
不配则走直连。

**Q：NFO 生成了但 Emby 不认**
NFO 必须与视频同名（`ABC-123.mp4` ↔ `ABC-123.nfo`）。改过文件名的话，
到「文件整理 → 扫描识别」重跑一次。

**Q：改了数据库密码就连不上**
密码只在 `pgdata` 首次初始化时写入。要么改回去，要么清库重来（会丢数据）。

更多问题见 [WIKI.md 常见问题](WIKI.md#8-常见问题)。

## 声明

本项目仅供个人自建媒体库的元数据管理使用，不提供、不托管、不分发任何
音视频内容。使用者需自行确保所管理的内容来源合法，并遵守所在地法律法规。

## 版本

| 版本 | 要点 |
|---|---|
| 2.0.0 | 双命名全站贯通、功能描述逐条校准、版本页加维护者、修复保存目录输入框失焦、镜像去掉私有内网默认值 |
| 1.10.21 | 镜像改名、去作者痕迹、去检查更新 |
