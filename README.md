
# AstrBot 安装详解（Windows + Linux）

> 照着抄就能跑起来的一站式部署指南 · Windows 与 Linux 各 4 种装法 · 含第一次登录、接模型、接 QQ、升级备份与排错手册
>
> 适用版本：AstrBot v4.x（本文以 **v4.29.3** 为例，版本号请以 [Releases](https://github.com/AstrBotDevs/AstrBot/releases) 最新为准）

![platform](https://img.shields.io/badge/platform-Windows%20%7C%20Linux-2f81f7)
![version](https://img.shields.io/badge/AstrBot-v4.x-8a2be2)
![lang](https://img.shields.io/badge/docs-中文-3fb950)

---

## 目录

- [0. 一分钟选型：你该用哪种装法](#0-一分钟选型你该用哪种装法)
- [1. 装之前先记住这 3 个数字](#1-装之前先记住这-3-个数字)
- [2. Windows 部署（4 种）](#2-windows-部署4-种)
- [3. Linux 部署（5 种）](#3-linux-部署5-种)
- [4. 装完必须做的 3 件事](#4-装完必须做的-3-件事)
- [5. 升级、备份与迁移](#5-升级备份与迁移)
- [6. 排错手册（13 个高频坑）](#6-排错手册13-个高频坑)
- [7. 命令速查表](#7-命令速查表)
- [8. 参考链接](#8-参考链接)

---

## 0. 一分钟选型：你该用哪种装法

AstrBot 是一个开源的一站式 Agent 聊天机器人平台，可以接入 QQ、微信、飞书、钉钉、Telegram、Discord、KOOK 等平台，接上大模型后就是一个"能聊天、能查资料、能调插件"的机器人。

装法很多，但**不要纠结**，按你的场景对号入座：

| 你的场景 | 推荐装法 | 难度 | 数据目录 |
| --- | --- | --- | --- |
| Windows 个人电脑，想开箱即用 | **桌面客户端**（AstrBot-desktop） | ⭐ | `C:\Users\你的用户名\.astrbot` |
| Windows，想要一个带界面的启动器管理 | **一键启动器**（astrbot-launcher） | ⭐⭐ | `C:\Users\你的用户名\.astrbot_launcher` |
| Windows，喜欢命令行 / 想跟教程敲命令 | **PowerShell 脚本安装** | ⭐⭐ | 由 `astrbot init` 决定 |
| Windows 老机器，已有 Python 环境 | 源码部署（uv / venv） | ⭐⭐⭐ | `AstrBot\data` |
| Linux 云服务器，长期在线 | **Docker**（强烈推荐） | ⭐⭐ | 宿主机的 `./data` |
| Linux，不想碰 Docker | **一键脚本**（uv tool） | ⭐⭐ | 由 `astrbot init` 决定 |
| Linux，要改代码 / 装自己写的插件 | 源码部署 | ⭐⭐⭐ | `AstrBot/data` |
| Arch Linux | AUR（`astrbot-git`） | ⭐ | `~/.local/share/astrbot` |

> 💡 **一句话原则**：本地体验用桌面客户端；服务器长期跑用 Docker；要折腾代码用源码。

---

## 1. 装之前先记住这 3 个数字

| 项目 | 值 | 说明 |
| --- | --- | --- |
| WebUI 端口 | **6185** | 浏览器打开 `http://localhost:6185` 就是管理面板 |
| 机器人端口 | **6199** | OneBot v11 / NapCat 等实现端连过来的 WebSocket 端口 |
| 初始账号 | **astrbot** | 密码是**随机的**，只在启动日志里打印一次 |

启动成功后终端会打印类似这样一段：

```text
✨✨✨
AstrBot v4.29.3 WebUI is ready

➜  Local: http://localhost:6185
➜  Initial username: astrbot
➜  Initial password: UiYVpZxnW8k22IWqf0ru5pOy
➜  Change it after logging in
✨✨✨
```

**请立刻把这段日志里的密码复制下来**，登录后会强制你改密码。日志刷过去了没关系，第 6 节有找回密码的办法。

> ⚠️ 密码**不是**固定的 `astrbot`/`astrbot`，网上很多老教程写死了默认密码，那是过时信息。

---

## 2. Windows 部署（4 种）

### 2.1 桌面客户端（最推荐，零命令行）

**适合**：不想装 Python、不想开终端、只想双击就能用的人。

1. 打开 [AstrBot-desktop Releases](https://github.com/AstrBotDevs/AstrBot-desktop/releases/latest)。
2. 在 Assets 里下载：
   - 普通 Intel/AMD 电脑：`AstrBot_x.y.z_windows_amd64_setup.exe`
   - ARM 设备（骁龙本等）：`AstrBot_x.y.z_windows_arm64_setup.exe`
   - 不想安装、要绿色版：`..._windows_amd64_portable.zip`
3. 双击安装 → 启动 → 按向导完成初始化。
4. 主界面出现后，点顶部提示或直接打开面板地址即可使用。

**验证**：客户端主界面正常显示、能看到"Bot 模式 / 聊天模式"。

**注意**：
- 桌面客户端适合**个人本地使用**，不适合服务器 7×24 生产环境（要生产请用 Docker）。
- 它的数据目录在 `C:\Users\你的用户名\.astrbot`，备份就备份这个文件夹。
- 客户端会自己检查新版本，顶部出现"AstrBot 有新版本！"点一下即可更新。

---

### 2.2 一键启动器（图形化 + 自动拉源码）

**适合**：想要一个可视化界面来管 AstrBot 的安装/启动/停止。

1. 打开 [astrbot-launcher Releases](https://github.com/AstrBotDevs/astrbot-launcher/releases/latest)。
2. 下载对应安装包：
   - `AstrBot.Launcher_x.y.z_x64-setup.exe`（绝大多数 Windows）
   - `AstrBot.Launcher_x.y.z_arm64-setup.exe`（ARM）
   - 或 `.msi` 版本
3. 安装并启动，在启动器里点"安装 / 启动"。
4. 启动器会自动准备运行环境、拉取 AstrBot 源码并运行。

**数据目录**：`C:\Users\你的用户名\.astrbot_launcher`（新版可视化启动器）。

**常见坑**：
- Windows 提示"已保护你的电脑"→ 点"更多信息"→"仍要运行"（开源脚本没签名，属正常现象）。
- 旧版 PowerShell 启动器（`launcher_astrbot_en.bat`）报 `Python is not installed`：说明 Python 没进环境变量。要么在 bat 里把 `set PYTHON_CMD=python` 改成你的 python.exe 完整路径（引号别删），要么重装 Python 并勾选 **Add Python to PATH** 后重启电脑。

---

### 2.3 PowerShell 脚本安装（一条命令）

**适合**：喜欢命令行、想跟教程"复制粘贴"的人。脚本会自动装好 **uv + Python 3.12 + AstrBot**。

**交互式安装**（推荐新手，全程有中文提示）：

```powershell
irm https://docs.astrbot.app/install.ps1 | iex
```

**无人值守安装**（一条命令跑完，适合远程/批量）：

```powershell
Invoke-WebRequest https://docs.astrbot.app/install.ps1 -OutFile install.ps1 -UseBasicParsing
.\install.ps1 -Language zh-CN -DownloadSource china -NonInteractive
```

参数说明：
- `-Language`：语言（`zh-CN` / `en-US`），省略默认英文。
- `-DownloadSource`：`china`（国内加速）/ `official`（官方源）/ `custom`。中文环境不指定时默认走国内加速镜像。
- `-NonInteractive`：不交互，全默认。

**安装完成后**（脚本会执行 `uv tool update-shell`，必须重载环境变量，否则会提示"命令不存在"）：

```powershell
. $PROFILE
astrbot init
astrbot run
```

**验证**：终端打印出 `WebUI is ready` 与 6185 地址。

> ⚠️ 脚本部署的 AstrBot **不支持在 WebUI 里点升级**，升级用：`uv tool upgrade astrbot --python 3.12`。

---

### 2.4 Docker Desktop（Windows 上跑容器）

**适合**：想和服务器保持一致环境、准备以后迁到 Linux 的人。

**前置**：装好 [Docker Desktop](https://www.docker.com/products/docker-desktop/) 并启动（WSL2 后端）。

**方式 A：命令行直接跑（CMD）**

```bat
mkdir astrbot
cd astrbot
docker run -itd -p 6185:6185 -p 6199:6199 -e TZ=Asia/Shanghai -v "%cd%\data:/AstrBot/data" --name astrbot soulter/astrbot:latest
```

**方式 B：命令行直接跑（PowerShell）**

```powershell
mkdir astrbot
cd astrbot
docker run -itd -p 6185:6185 -p 6199:6199 -e TZ=Asia/Shanghai -v "${PWD}\data:/AstrBot/data" --name astrbot soulter/astrbot:latest
```

**方式 C：Docker Compose（更好管理，推荐）**

新建 `compose.yml`（等价于官方 docker run 的最小写法）：

```yaml
services:
  astrbot:
    image: soulter/astrbot:latest
    container_name: astrbot
    restart: always
    ports:
      - "6185:6185"   # WebUI 管理面板
      - "6199:6199"   # 机器人 WebSocket（OneBot v11）
    environment:
      - TZ=Asia/Shanghai
    volumes:
      - ./data:/AstrBot/data
```

```powershell
docker compose up -d
docker logs -f astrbot
```

**国内拉不动镜像？** 把镜像名换成 DaoCloud 镜像：

```
soulter/astrbot:latest  →  m.daocloud.io/docker.io/soulter/astrbot:latest
```

**注意**：
- Windows 下 `docker` 命令**不要**加 `sudo`。
- `TZ` 必须写 IANA 格式（`Asia/Shanghai`），别写 `GMT+8`。
- `-v "%cd%\data:/AstrBot/data"` 里的 `data` 就是全部数据，备份它等于备份整个机器人。

---

### 2.5 源码部署（Windows）

**适合**：要改代码、写插件、看清内部实现。

**前置**：安装 **Python 3.12+**，安装时勾选 **Add Python to PATH**；装好 Git。

```powershell
git clone https://github.com/AstrBotDevs/AstrBot
cd AstrBot

# 方案一：uv 管理（官方推荐）
uv sync
uv run main.py

# 方案二：内置 venv
python -m venv ./venv
venv\Scripts\activate
python -m pip install -r requirements.txt -i https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
python main.py
```

> 💡 装了插件后，启动建议加 `--no-sync`，避免每次启动都重装插件依赖：
> ```powershell
> uv run --no-sync main.py
> ```

**验证**：浏览器打开 `http://localhost:6185`。

---

## 3. Linux 部署（5 种）

### 3.1 一键脚本（最省事，uv 托管）

**适合**：Debian/Ubuntu/CentOS/Alpine 等主流发行版，想几分钟搞定。

```bash
# 交互式
curl -fsSL https://docs.astrbot.app/install.sh | bash

# 无人值守（国内加速）
curl -fsSL https://docs.astrbot.app/install.sh -o install.sh
bash install.sh --language zh-CN --download-source china --non-interactive
```

脚本会自动安装 **uv、Python 3.12、AstrBot**。

**装完后按你的 shell 重载环境变量**（这步不做，`astrbot` 命令会找不到）：

```bash
source ~/.bashrc        # Bash
source ~/.zshrc         # Zsh
source ~/.config/fish/config.fish   # Fish
```

然后：

```bash
astrbot init
astrbot run
```

**升级**（脚本部署不支持 WebUI 内升级）：

```bash
uv tool upgrade astrbot --python 3.12
```

**注意**：云服务器上要放行 6185 端口（见 3.6），否则外网打不开面板。

---

### 3.2 Docker 部署（服务器首选 ⭐）

**前置**：装好 Docker；Compose 版本建议装 docker compose 插件。

**方式 A：compose（推荐）**

```bash
git clone https://github.com/AstrBotDevs/AstrBot
cd AstrBot
sudo docker compose up -d
```

**方式 B：不想 clone，手写最小 compose**

```bash
mkdir -p ~/astrbot && cd ~/astrbot
cat > compose.yml <<'EOF'
services:
  astrbot:
    image: soulter/astrbot:latest
    container_name: astrbot
    restart: always
    ports:
      - "6185:6185"
      - "6199:6199"
    environment:
      - TZ=Asia/Shanghai
    volumes:
      - ./data:/AstrBot/data
EOF

sudo docker compose up -d
sudo docker logs -f astrbot
```

**方式 C：docker run 一条命令**

```bash
mkdir astrbot && cd astrbot
sudo docker run -itd -p 6185:6185 -p 6199:6199 \
  -v $PWD/data:/AstrBot/data \
  -v /etc/localtime:/etc/localtime:ro \
  -v /etc/timezone:/etc/timezone:ro \
  --name astrbot soulter/astrbot:latest
```

**国内加速**：

```bash
sudo docker pull m.daocloud.io/docker.io/soulter/astrbot:latest
# 或把 compose.yml 里的 image 改成 m.daocloud.io/docker.io/soulter/astrbot:latest
```

**注意**：
- 官方文档提示：Docker 隔离了网络环境，**用宿主机 IP 访问面板**，不要用 `localhost`（尤其是服务器）。
- 需要同时跑 NapCat 的话，用官方提供的 `astrbot.yml`：
  ```bash
  mkdir astrbot && cd astrbot
  wget https://raw.githubusercontent.com/NapNeko/NapCat-Docker/main/compose/astrbot.yml
  sudo docker compose -f astrbot.yml up -d
  ```
- 带 Agent 沙箱（可执行 Python/Shell）的部署用 `compose-with-shipyard.yml`。

---

### 3.3 源码部署（Linux，uv）

**前置**：Python ≥ 3.12、Git、`curl`。

```bash
# 装 uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# 拉源码（默认最新提交；要最新稳定版见下方注释）
git clone https://github.com/AstrBotDevs/AstrBot
cd AstrBot

uv sync
uv run main.py
# 装了插件后建议：
# uv run --no-sync main.py
```

只拉最新稳定 tag 的写法：

```bash
git clone --depth=1 --branch $(git ls-remote --tags --sort='-v:refname' https://github.com/AstrBotDevs/AstrBot.git | head -n1 | awk -F/ '{print $3}') https://github.com/AstrBotDevs/AstrBot.git
```

---

### 3.4 源码部署（Linux，venv）

适合不想引入 uv 的人：

```bash
python3 -m venv ./venv
source venv/bin/activate
python -m pip install -r requirements.txt -i https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
python main.py
```

---

### 3.5 Arch Linux（AUR）

```bash
paru -S astrbot-git
# yay -S astrbot-git 同理

astrbot init
systemctl --user start astrbot.service     # 推荐用 systemd 托管
systemctl --user enable astrbot.service    # 开机自启（如要立即启动加 --now）
```

数据目录固定在 `~/.local/share/astrbot`。

---

### 3.6 服务器部署补的三件事

**① 放行防火墙/安全组端口**

```bash
# ufw（Ubuntu/Debian）
sudo ufw allow 6185/tcp
sudo ufw allow 6199/tcp

# firewalld（CentOS/RHEL/Rocky）
sudo firewall-cmd --permanent --add-port=6185/tcp
sudo firewall-cmd --permanent --add-port=6199/tcp
sudo firewall-cmd --reload
```

> 云服务器（阿里云/腾讯云/雨云等）还要去**控制台安全组**里放行 6185、6199，这一步最容易被忘。

**② 允许远程访问面板**

默认面板只监听 `127.0.0.1`。如果外网打不开，编辑 `data/cmd_config.json`：

```json
"dashboard": {
  "enable": true,
  "host": "0.0.0.0",
  "port": 6185
}
```

改完重启 AstrBot。

> 🔒 暴露到公网务必：改掉初始密码、必要时上 Nginx + HTTPS 反代，并考虑只放行自己的 IP。

**③ systemd 开机自启**（非官方，通用写法，路径按自己情况改）

```bash
mkdir -p ~/.config/systemd/user
cat > ~/.config/systemd/user/astrbot.service <<'EOF'
[Unit]
Description=AstrBot
After=network-online.target

[Service]
Type=simple
WorkingDirectory=%h/astrbot
ExecStart=%h/.local/bin/astrbot run
Restart=always
RestartSec=5

[Install]
WantedBy=default.target
EOF

systemctl --user daemon-reload
systemctl --user enable --now astrbot.service

# 让服务在未登录时也常驻（关键！否则退出 ssh 会停）
loginctl enable-linger $USER
```

查看日志：`journalctl --user -u astrbot -f`

---

## 4. 装完必须做的 3 件事

装好 ≠ 能用。面板能打开，说明服务活了；但机器人要能聊天，还得接**模型**和**消息平台**。

### 4.1 改密码

首次用日志里的随机密码登录 → 自动进入设置账户环节 → 改一个强密码。

### 4.2 接入大模型

面板左侧 **模型提供商 → 新增**，按你的渠道选：

| 渠道 | 需要填 | 备注 |
| --- | --- | --- |
| DeepSeek | API Key + Base URL | 便宜、中文好 |
| 硅基流动 SiliconFlow | API Key | 国内直连、模型多 |
| OpenAI 兼容接口 | Base URL + Key + 模型名 | 中转站/自建 NewAPI 都选这个 |
| Ollama / LM Studio | 本地地址 | 完全离线，吃显存 |

填完保存，再去 **配置文件 → 选择对话模型**，把这个提供商设成默认。

### 4.3 接入 QQ（NapCat + OneBot v11）

1. 部署 [NapCat](https://github.com/NapNeko/NapCatQQ)（或采用 Docker 版）。
2. NapCat WebUI → **网络配置 → 新建 WebSocket 客户端**：
   ```
   ws://127.0.0.1:6199/ws
   ```
   如果 AstrBot 面板里配了 token，这里填**同一个 token**。
3. AstrBot 面板 → **消息平台**，确认 OneBot v11（aiocqhttp）适配器已启用、端口 6199 对得上。
4. QQ 小号加机器人好友，发一句"你好"。

**ws 地址怎么写（血泪经验）**：

| 场景 | NapCat 里填的地址 |
| --- | --- |
| AstrBot 与 NapCat 都在同一台机器的宿主机 | `ws://127.0.0.1:6199/ws` |
| 两个都是 Docker 容器 | `ws://astrbot:6199/ws`（需先 `docker network connect` 到同一网络） |
| 只有 NapCat 是 Docker | `ws://宿主机IP:6199/ws` |
| 跨机器 | `ws://AstrBot所在IP:6199/ws` |

**群聊不回复？** 正常。群聊里默认需要 **@ 机器人** 或用唤醒词（默认 `/`，比如 `/你好`）才会响应，防止刷屏。

---

## 5. 升级、备份与迁移

### 5.1 升级

| 部署方式 | 升级命令 |
| --- | --- |
| 脚本 / uv tool | `uv tool upgrade astrbot --python 3.12` |
| Docker | `docker pull soulter/astrbot:latest` 后 `docker compose up -d`（或删容器重建，数据在 `./data` 不丢） |
| 源码 | `git pull` + `uv sync` 后重启 |
| 桌面客户端 | 顶部提示"有新版本"点一下即可 |
| AUR | `paru -Syu astrbot-git` |

> ⚠️ 脚本部署**不能**在 WebUI 里点升级，会失败/回退。

### 5.2 备份

AstrBot 的所有配置、插件、会话、数据库都在 **`data` 目录**里。备份它 = 备份整个机器人。

```bash
# Docker 部署
tar -czf astrbot-data-$(date +%F).tar.gz ./data

# 源码部署
 tar -czf astrbot-data-$(date +%F).tar.gz AstrBot/data

# 桌面客户端（Windows）
Compress-Archive -Path "$env:USERPROFILE\.astrbot" -DestinationPath "$env:USERPROFILE\Desktop\astrbot-backup.zip"
```

> 备份前先停一下服务，避免数据库写入到一半。

### 5.3 迁移到新机器

1. 新机器按本文装好 AstrBot（**版本尽量一致**）。
2. 停服务，把老的 `data` 目录整个覆盖过去。
3. 启动。面板账号密码、插件、配置全部原样保留。

---

## 6. 排错手册（13 个高频坑）

### ① 面板打开是 404 / 白屏

WebUI 静态文件没下全。去 [Releases](https://github.com/AstrBotDevs/AstrBot/releases/latest) 下载 `AstrBot-vX.Y.Z-dashboard.zip`，解压后把内容丢进 `data` 目录，重启。还不行就重启电脑（社区反馈确实有效）。

### ② 忘记面板密码

**Docker：**

```bash
sudo docker exec -it astrbot astrbot password
sudo docker restart astrbot       # 必须重启才生效
# 连用户名一起改：
# sudo docker exec -it astrbot astrbot password --username 新用户名
```

**uv / pip 脚本部署**（在 `astrbot init` 的那个目录里）：

```bash
astrbot password
astrbot password --username 新用户名
```

**源码 / 其他没有 CLI 的方式**：编辑 `data/cmd_config.json`，在 `dashboard` 里**删掉**这 6 个字段（连同值）：
`username`、`password`、`pbkdf2_password`、`password_storage_upgraded`、`password_change_required`、`jwt_secret`。
重启后会重新生成随机密码（用户名固定 `astrbot`），去日志里看。

### ③ 升级后密码明明正确，就是登不进去

旧版 WebUI 静态文件缓存与新版后端不匹配。
1. 停 AstrBot。
2. 删掉 `data/dist` 文件夹。
3. 重启。
4. 浏览器 `Ctrl+Shift+R`（Mac：`Cmd+Shift+R`）强刷。

### ④ 插件装不上 / 一直转圈

插件从 GitHub 拉取，国内常连不上。进 **设置 → 网络 → 代理与依赖源**：
- 填 HTTP 代理；
- 或设置 **GitHub 加速地址**；
- 或直接下载插件压缩包，在插件页"从文件安装"。

### ⑤ 插件报 `No module named 'xxx'`

插件依赖没装上。面板 **数据与日志 → 日志 → 安装 pip 库**，填库名（可填 PyPI 镜像）点安装。也可对照插件 README 手动 `pip install`。

### ⑥ NapCat 连不上 AstrBot

见 [4.3 的 ws 地址对照表](#43-接入-qqnapcat--onebot-v11)。两个都是 Docker 时：

```bash
sudo docker network create newnet
sudo docker network connect newnet astrbot
sudo docker network connect newnet napcat
sudo docker restart astrbot && sudo docker restart napcat
```

再把地址改成 `ws://astrbot:6199/ws`。

### ⑦ 群聊里机器人不理人

群里默认不逐条回复。**@ 它**或发 `/你好` 这种带唤醒词的。

### ⑧ 管理员指令提示没权限

`/name`、`/provider`、`/op`、`/persona`、`/plugin` 等都需要管理员身份：
发 `/sid` 拿到你的 ID → 面板 **配置文件 → 访问控制 → 管理员 → 管理员 ID** 里加上 → 右下角"保存配置"。

### ⑨ 云服务器上外网打不开 6185

三层都要通：安全组放行 → 系统防火墙放行 → `dashboard.host` 为 `0.0.0.0`。缺一不可。

### ⑩ Docker 拉镜像超时

用 `m.daocloud.io/docker.io/soulter/astrbot:latest`，或给 Docker 配镜像加速器。

### ⑪ 终端提示 `astrbot: command not found`

脚本装完没重载环境变量。Bash 跑 `source ~/.bashrc`，PowerShell 跑 `. $PROFILE`，或直接新开一个终端。

### ⑫ 文转图（t2i）中文变方块/乱码

默认字体不覆盖中文。在配置里指定中文字体文件（社区推荐 Maple Mono 等）。

### ⑬ 桌面上找不到数据目录

- 桌面客户端：`C:\Users\你的用户名\.astrbot`
- 可视化启动器：`C:\Users\你的用户名\.astrbot_launcher`
- 旧版 bat 启动器：bat 脚本同级的 `AstrBot` 目录

---

## 7. 命令速查表

![AstrBot 命令速查卡](./assets/douyin-cheatsheet.png)

```text
# ── Windows（PowerShell）────────────────────────────
irm https://docs.astrbot.app/install.ps1 | iex        # 一键脚本安装
. $PROFILE                                            # 重载环境
astrbot init                                          # 初始化
astrbot run                                           # 启动
docker logs -f astrbot                                # 看日志
docker exec -it astrbot astrbot password              # 重置密码

# ── Linux ─────────────────────────────────────────
curl -fsSL https://docs.astrbot.app/install.sh | bash # 一键脚本安装
source ~/.bashrc                                      # 重载环境
astrbot init && astrbot run                           # 初始化并启动
uv tool upgrade astrbot --python 3.12                 # 升级
sudo docker compose up -d                             # 启动容器
sudo docker logs -f astrbot                           # 看日志
sudo docker exec -it astrbot astrbot password         # 重置密码
journalctl --user -u astrbot -f                       # systemd 日志
tar -czf backup.tar.gz ./data                         # 备份数据

# ── 通用地址 ───────────────────────────────────────
http://localhost:6185     # 管理面板
ws://127.0.0.1:6199/ws    # 机器人 WebSocket
```

---

## 8. 参考链接

| 内容 | 链接 |
| --- | --- |
| 项目主页 | https://github.com/AstrBotDevs/AstrBot |
| 官方文档 | https://docs.astrbot.app/ |
| 桌面客户端 | https://github.com/AstrBotDevs/AstrBot-desktop |
| 一键启动器 | https://github.com/AstrBotDevs/astrbot-launcher |
| Docker 部署文档 | https://docs.astrbot.app/deploy/astrbot/docker.html |
| 源码部署文档 | https://docs.astrbot.app/deploy/astrbot/cli.html |
| 脚本部署文档 | https://docs.astrbot.app/deploy/astrbot/package.html |
| 常见问题 | https://docs.astrbot.app/faq.html |
| NapCat（QQ 实现端） | https://github.com/NapNeko/NapCatQQ |

---

## 免责声明

- 本文为个人整理的实践笔记，**非官方文档**；命令与参数以官方文档及 Releases 为准。
- 文中版本号（v4.29.3）为撰写时的最新稳定版，更新后界面/参数可能微调。
- 请勿将机器人用于骚扰、批量营销、绕过平台风控等违规用途；使用 QQ/微信等平台账号请遵守其用户协议，**风险自负**。

如果这篇文档帮你省下了几个小时，欢迎给 [AstrBot](https://github.com/AstrBotDevs/AstrBot) 和本项目点个 ⭐。
