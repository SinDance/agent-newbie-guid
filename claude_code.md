# Claude Code 下载与安装目录（终端版 / App 版 × 平台）

> 出品方：Anthropic　整理日期：2026-10-10
> 返回总目录：[`agent下载目录.md`](./agent下载目录.md)（那 5 款国内/海外双包产品）　命令行汇总风格见 [`download_myself.md`](./download_myself.md)
> 本文按你的要求**只下载了 App 版 Windows x64 一个包**（已放进 [`安装包/`](./安装包/)）；终端版一律只记安装命令，不下载二进制。
> 来源标注口径：
> **实测 200** = 我用 HTTP 校验过状态码与 `Content-Length`；**文档给出** = 官方文档原文照抄，未单独发包验证；**官方仓库** = GitHub Releases / apt 源 / npm registry 的机器可读数据。
> **校验值口径**：`latest` 类地址随发版换对象，写死的哈希下次下载必然校验失败，所以本文不记这类数值。有官方实时校验值的（终端版 CLI 走 `manifest.json`、App 的 `.deb` 走 apt `Packages` 索引）给取法命令；官方只给 MD5 的（App 的 Windows / macOS 包）说明清楚；跨文档汇总见 [`download_myself.md`](./download_myself.md) 第 8 节。
> 官方来源：<https://code.claude.com/docs/en/overview.md>、[/docs/en/setup.md](https://code.claude.com/docs/en/setup.md)、[/docs/en/desktop-quickstart.md](https://code.claude.com/docs/en/desktop-quickstart.md)、[/docs/en/desktop-linux.md](https://code.claude.com/docs/en/desktop-linux.md)

---

## 速览：终端版和 App 版是两个东西

| | **终端版 Claude Code CLI** | **App 版 Claude 桌面 App** |
| --- | --- | --- |
| 是什么 | `claude` 命令，纯终端 TUI | Claude 客户端，三个标签页 Chat / Cowork / **Code** |
| 内含 Claude Code | 本体就是 | **已内置**，装 App 不用再装 CLI（文档原话：*you don't need to install the CLI separately*） |
| 版本线 | **2.1.296**（latest）/ 2.1.287（stable） | **2.31226.1** —— 两条线不同步，别看错 |
| 平台 | macOS / Windows（原生 + WSL）/ Linux（含 musl）/ 两架构 | macOS、Windows x64、Windows ARM64、Linux（beta，仅 Debian 系） |
| 安装方式 | 官方脚本 / brew / winget / npm / apt-dnf-apk | 官网下载 dmg / exe / .deb，Linux 走 apt 源 |
| 账号要求 | Pro / Max / Team / Enterprise 订阅或 Console（**免费 claude.ai 计划不含**） | 同左，且 Code 标签页需付费订阅 |
| 国内版 | 无。Anthropic 只发国际版，需海外网络环境 | 无，同左 |
| 本文是否下载 | ❌ 只记命令 | ✅ 只下了 Windows x64 |

> 和总目录里那 5 款最大的区别：**这里没有「国内版 / 海外版」两选一**。同一二进制面向全球，差别只在你登录的账号地区是否受支持（<https://www.anthropic.com/supported-countries>，文档给出）。

---

## 1. 终端版 Claude Code CLI

版本号取自官方发布清单（实测 200）：`latest` 通道当前 **2.1.296**，`buildDate 2026-10-09T14:46:35Z`，commit `fdb6c17a`。npm registry 上 `@anthropic-ai/claude-code` 同为 `2.1.296`（实测 200），两处一致。
终端版按要求**没有下载安装**，所以本机没有跑过 `claude --version` 复核；装好后用它自检，正常输出形如 `2.1.211 (Claude Code)`（文档给出）。

### 1.1 安装命令（按平台，全部来自官方文档 · 文档给出）

| 平台 / 通道 | 命令 | 自动更新 | 备注 |
| --- | --- | --- | --- |
| macOS、Linux、WSL | `curl -fsSL https://claude.ai/install.sh \| bash` | ✅ 后台自动 | 脚本可访问（实测 302→200）；下载过程**不显示进度条**，别以为卡住了 |
| Windows PowerShell | `irm https://claude.ai/install.ps1 \| iex` | ✅ 后台自动 | 实测 200；不需要管理员权限 |
| Windows CMD | `curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd` | ✅ 后台自动 | 实测 200。若报 `The token '&&' is not a valid statement separator`，说明你在 PowerShell 而不是 CMD |
| Homebrew（macOS / Linux） | `brew install --cask claude-code` | ❌ 手动 `brew upgrade claude-code` | 两个 cask：`claude-code`=stable 通道，`claude-code@latest`=latest 通道 |
| WinGet（Windows） | `winget install Anthropic.ClaudeCode` | ❌ 手动 `winget upgrade Anthropic.ClaudeCode` | 本机 `winget show` 实测源内版本 **2.1.292**，比 latest 慢几个版本 |
| npm（跨平台） | `npm install -g @anthropic-ai/claude-code` | ✅ 默认开启 | 需 Node 22+；官方明确 **不要用 sudo**；包体只是拉同一个原生二进制 |
| apt（Debian/Ubuntu） | 见 1.4 | ❌ 手动 | stable / latest 两个仓库分支 |
| dnf（Fedora/RHEL）、apk（Alpine） | 见 1.4 | ❌ 手动 | 同一套 `downloads.claude.ai` 仓库 |

装完在目标项目目录里运行 `claude` 即可；首次会弹浏览器登录。设了 `ANTHROPIC_API_KEY` 并在提示里同意使用该 key，可跳过登录。

### 1.2 系统要求（文档给出）

| 项 | 要求 |
| --- | --- |
| macOS | 13.0+ |
| Windows | Windows 10 1809+ 或 Windows Server 2019+，可原生运行也可 WSL 1/2 |
| Linux | Ubuntu 20.04+、Debian 10+、Alpine 3.19+ |
| 硬件 | 4 GB+ 内存，x64 或 ARM64 |
| Shell | Bash / Zsh / PowerShell / CMD 任一 |
| 其他 | 需联网；`ripgrep` 一般已内置 |

Windows 两条路线（文档给出）：**原生 Windows** 无需 WSL，可选装 [Git for Windows](https://git-scm.com/downloads/win) 以启用 Bash 工具（不装则退化为 PowerShell 工具）；**WSL 2** 支持沙箱，Linux 工具链场景用它。原生 Windows 装不上时先查 PATH，见 `/docs/en/troubleshoot-install`。

### 1.3 原生二进制直链（离线分发用，8 个平台全部实测 200）

地址模板（官方仓库 + 实测）：

```text
https://downloads.claude.ai/claude-code-releases/<版本号>/<平台>/<二进制名>
```

版本号从两个指针文件取（实测 200，正文就是一行版本号）：

| 通道 | 指针地址 | 当前值 |
| --- | --- | --- |
| latest | <https://downloads.claude.ai/claude-code-releases/latest> | `2.1.296` |
| stable | <https://downloads.claude.ai/claude-code-releases/stable> | `2.1.287` |

以 `2.1.296` 为例，各平台直链、大小与官方 SHA256（取自 `manifest.json`，实测 200）。**下表哈希只对 `2.1.296` 这一版有效**：`latest` 指针会往前走，你跟它下载拿到的是新版本，用这张表比对必然失败。换版本时跑本节末那条命令，从当期 `manifest.json` 里取 `checksum` 再核对（表里的 `size` 同理，也是清单字段，可一并核对）。

| 平台 | 二进制 | 大小 | 下载直链 | SHA256（官方清单） |
| --- | --- | --- | --- | --- |
| macOS Apple Silicon | `claude` | 229.5 MB | <https://downloads.claude.ai/claude-code-releases/2.1.296/darwin-arm64/claude> | `c9b5341637becbd423ddffc5b254afb645682a3868cb708bbc6cc0e7bb419937` |
| macOS Intel | `claude` | 237.6 MB | <https://downloads.claude.ai/claude-code-releases/2.1.296/darwin-x64/claude> | `a6bf4f30be241053a923f3820ad23c5991d2f5ac2935b3a8dd64da218d9cc603` |
| Windows x64 | `claude.exe` | 248.4 MB | <https://downloads.claude.ai/claude-code-releases/2.1.296/win32-x64/claude.exe> | `76de74f7694a5bdc03378a5f837c9e132827bf004c98c276bc4e9094ff06769a` |
| Windows ARM64 | `claude.exe` | 236.5 MB | <https://downloads.claude.ai/claude-code-releases/2.1.296/win32-arm64/claude.exe> | `a652a17f5a324d2d9ad9995cc047145f8176b9a4b4f28bbd3aa0708a22679e3b` |
| Linux x64 (glibc) | `claude` | 245.2 MB | <https://downloads.claude.ai/claude-code-releases/2.1.296/linux-x64/claude> | `24972e3bc859fab2b46ed4c1e51f7d6130f06d3bd550811a114640de3370d0de` |
| Linux ARM64 (glibc) | `claude` | 244.6 MB | <https://downloads.claude.ai/claude-code-releases/2.1.296/linux-arm64/claude> | `f1f6e96e0d8342b9dbf41d7e88255397a6a52ce3d8736ad6a4c6b59c9b62fefa` |
| Linux x64 (musl) | `claude` | 239.2 MB | <https://downloads.claude.ai/claude-code-releases/2.1.296/linux-x64-musl/claude> | `94a1ccc6ffc9b0889c6b2b80e202885c8a575766775377398daa4db5fd89417f` |
| Linux ARM64 (musl) | `claude` | 237.3 MB | <https://downloads.claude.ai/claude-code-releases/2.1.296/linux-arm64-musl/claude> | `b0bbc936a82930933d7d5645081050d5f827a946cf6abc6575383b777a0feec0` |

> 注意：**没有 `latest/<平台>/claude.exe` 这种写法**，实测 404。`latest` 和 `stable` 只是版本指针文件，必须先把版本号取出来再拼路径（命令行见 1.5）。
> 完整性校验：每个版本目录还发布 `manifest.json` + `manifest.json.sig`（Anthropic GPG 签名，指纹 `31DD DE24 DDFA B679 F42D 7BD2 BAA9 29FF 1A7E CACE`，公钥 <https://downloads.claude.ai/keys/claude-code.asc>）。验签清单即等于验签所有二进制（文档给出）。
> 取当期校验值（**这是本文对终端版唯一推荐的校验方式**，指针 + 清单都是长期稳定地址，跟着发版自动更新）：
>
> ```bash
> CC_VER=$(curl -fsSL https://downloads.claude.ai/claude-code-releases/latest)   # 或 .../stable
> curl -fsSL "https://downloads.claude.ai/claude-code-releases/${CC_VER}/manifest.json" \
>   | tr -d ' \n' | grep -oE '"(win32-x64|win32-arm64|darwin-x64|darwin-arm64|linux-x64|linux-arm64|linux-x64-musl|linux-arm64-musl)":\{[^}]*\}'
> ```
>
> 2026-10-10 实测：`latest` = `2.1.296`，清单里 8 个平台的 `checksum` 与上表逐字符一致。

### 1.4 Linux 包管理器安装（文档给出）

```bash
# apt（Debian/Ubuntu）—— stable 通道
sudo apt install curl gnupg                                    # 全新系统可能缺
sudo install -d -m 0755 /etc/apt/keyrings
sudo curl -fsSL https://downloads.claude.ai/keys/claude-code.asc -o /etc/apt/keyrings/claude-code.asc
gpg --show-keys /etc/apt/keyrings/claude-code.asc              # 指纹应为 31DDDE24DDFAB679F42D7BD2BAA929FF1A7ECACE
echo "deb [signed-by=/etc/apt/keyrings/claude-code.asc] https://downloads.claude.ai/claude-code/apt/stable stable main" \
  | sudo tee /etc/apt/sources.list.d/claude-code.list
sudo apt update && sudo apt install claude-code
# latest 通道：把 URL 里的 apt/stable 换成 apt/latest，发行版代号换成 latest

# dnf（Fedora/RHEL）：baseurl=https://downloads.claude.ai/claude-code/rpm/stable
# apk（Alpine）：echo "https://downloads.claude.ai/claude-code/apk/stable" >> /etc/apk/repositories
```

### 1.5 版本通道、更新与卸载

| 操作 | 命令 / 说明 |
| --- | --- |
| 立即更新 | `claude update` |
| 安装指定通道 | `curl -fsSL https://claude.ai/install.sh \| bash -s stable`；PowerShell：`& ([scriptblock]::Create((irm https://claude.ai/install.ps1))) stable` |
| 安装指定版本 | 同上，把 `stable` 换成 `2.1.89` 这类版本号 |
| 选通道 | `settings.json` 里 `"autoUpdatesChannel": "latest" \| "stable"`（stable 约滞后一周并跳过重大回归版本） |
| 关自动更新 | `settings.json` → `{"env":{"DISABLE_AUTOUPDATER":"1"}}`；彻底禁更（含手动）用 `DISABLE_UPDATES` |
| 体检 | `claude doctor`（含更新结果、launcher 状态、settings 校验） |
| macOS 安装位置 | `~/.local/bin/claude` → 符号链接到 `~/.local/share/claude/versions/<版本>` |
| npm 升级 | `npm install -g @anthropic-ai/claude-code@latest`（别用 `npm update -g`） |

Alpine / musl 系统需额外 `apk add bash curl libgcc libstdc++ ripgrep` 并设 `USE_BUILTIN_RIPGREP=0`（文档给出）。

---

## 2. App 版 Claude 桌面 App（内含 Claude Code）

App 版本 **2.31226.1**，三个平台包的构建号一致：`8576520051724193e9f74fc538f9ce8125e9ddd2`（实测 200 解析得到，2026-10-10）。Linux apt 源里 `claude-desktop` 的包版本同样报 **2.31226.1**（实测取自仓库 `Packages` 索引），可交叉确认三平台是同一次发版。

### 2.1 下载地址（按平台）

| 平台 | 类型 | 大小 | 官方入口（文档给出） | 解析出的版本化直链（实测 200） |
| --- | --- | --- | --- | --- |
| **Windows x64** | 在线安装器 `ClaudeSetup.exe` | **6.8 MB** | <https://claude.ai/api/desktop/win32/x64/setup/latest/redirect> | <https://downloads.claude.ai/releases/win32/x64/2.31226.1/ClaudeSetup-8576520051724193e9f74fc538f9ce8125e9ddd2.exe> ✅ **已下载** |
| Windows ARM64 | 在线安装器 | 6.3 MB | <https://claude.ai/api/desktop/win32/arm64/setup/latest/redirect> | <https://downloads.claude.ai/releases/win32/arm64/2.31226.1/ClaudeSetup-8576520051724193e9f74fc538f9ce8125e9ddd2.exe> |
| macOS（Universal，Intel + Apple Silicon 同包） | 完整 `.dmg` | 364.4 MB | <https://claude.ai/api/desktop/darwin/universal/dmg/latest/redirect> | <https://downloads.claude.ai/releases/darwin/universal/2.31226.1/Claude-8576520051724193e9f74fc538f9ce8125e9ddd2.dmg> |
| Linux（beta） | apt 源 / `.deb` | 173.0 MB (amd64) | [/docs/en/desktop-linux](https://code.claude.com/docs/en/desktop-linux.md) | 见 2.3 |

> **两个坑，先说清：**
> 1. `claude.ai/api/desktop/...` 这类 redirect 端点有 Cloudflare 防护，纯 `curl` 会返回 **403**（实测）。浏览器打开正常；脚本里要带浏览器 UA + Cookie 才拿得到 307。上表最右列的 `downloads.claude.ai` 直链没有这层限制，可直接 curl，但它**写死了版本号**，发新版就换。
> 2. Windows 包只有 6.8 MB，是**在线安装器**（装时才联网拉主体），不是离线整包；macOS 的 364.4 MB 才是完整镜像。给同事拷 U 盘离线装的话，Windows 端要保证有网。

### 2.2 已下载文件（Windows x64）

| 项 | 值 |
| --- | --- |
| 文件名 | `安装包/ClaudeDesktop-Windows-64位-用户级-v2.31226.1.exe` |
| 大小 | 7,178,400 B（6.8 MB），与官方 `Content-Length` 逐字节一致（实测） |
| 校验值 | 官方**不发布 SHA256**（`<exe>.sha256` 实测 404，桌面 App 侧也没有类似 CLI 那套 `manifest.json`），所以这里不记哈希数值 —— 2.4 的解析命令会给出新版本地址，旧校验值跟它一定对不上。能实时取的只有 GCS 的 MD5 头，随对象一起更新：`curl -sI "<版本化直链>" | tr -d '\r' | grep -iE '^(etag|x-goog-hash|content-length)'`。2026-10-10 实测 ETag = `f64529e366fb578e642968522821bd80`，与本机这只文件的 `md5sum` 一致，可确认下载无损；来源可信性看下一行的签名 |
| 代码签名 | `Status = Valid`，主体 **Anthropic, PBC**（DigiCert Trusted G4 签发，证书有效期至 2026-10-20），本机 `Get-AuthenticodeSignature` 实测 |
| 安装级别 | 用户级，写入当前用户目录，不需要管理员权限 |

### 2.3 Linux 安装（beta，文档给出 + 直链实测 200）

支持 Ubuntu 22.04+ / Debian 12+，x86_64 与 arm64；非 Debian 系（Fedora、Arch）官方让用终端版。

```bash
# 1) 装 curl gnupg（全新系统可能没有）
sudo apt install curl gnupg
# 2) 取签名密钥，指纹应为 31DDDE24DDFAB679F42D7BD2BAA929FF1A7ECACE
sudo curl -fsSLo /usr/share/keyrings/claude-desktop-archive-keyring.asc \
  https://downloads.claude.ai/claude-desktop/key.asc          # 实测 200，1,688 B
# 3) 注册仓库
echo "deb [arch=amd64,arm64 signed-by=/usr/share/keyrings/claude-desktop-archive-keyring.asc] https://downloads.claude.ai/claude-desktop/apt/stable stable main" \
  | sudo tee /etc/apt/sources.list.d/claude-desktop.list      # Release 实测 200
# 4) 安装
sudo apt update && sudo apt install claude-desktop
```

不走 apt 源的离线 `.deb`（当前 2.31226.1，SHA256 取自 apt 仓库索引 `Packages`，实测 200）：

| 架构 | 大小 | 直链 | SHA256 |
| --- | --- | --- | --- |
| amd64 | 173.0 MB | <https://downloads.claude.ai/claude-desktop/apt/stable/pool/main/c/claude-desktop/claude-desktop_2.31226.1_amd64.deb> | `5a9bebdfcb1df6ce38767b373f3a7fabf77156a2ddd8a3f5d04961da5bde305e` |
| arm64 | 166.0 MB | <https://downloads.claude.ai/claude-desktop/apt/stable/pool/main/c/claude-desktop/claude-desktop_2.31226.1_arm64.deb> | `d2f57a38ffc73cfda660f8772a5f8bada05e6d74399948a268c6f5d53593ee8f` |

上表两行是当期索引原文，直链里写死了 `2.31226.1`，发新版后两条都要换。**apt 仓库的 `Packages` 索引是长期稳定地址**，且同一份索引里带着每个历史版本的 `SHA256`，所以校验值不用写死在文档里，跟着索引现取（2026-10-10 实测：`SHA256:` 字段确实存在于该索引，amd64 当期值与上表一致）：

```bash
ARCH=$(dpkg --print-architecture)   # amd64 / arm64
PKG="https://downloads.claude.ai/claude-desktop/apt/stable/dists/stable/main/binary-${ARCH}/Packages"
# 索引里当期那只 .deb（Filename 按版本号排序取最后，与 2.4 的取法一致）
DEB=$(curl -s "$PKG" | tr -d '\r' | grep '^Filename: pool/main/c/claude-desktop/claude-desktop_' | sort -V | tail -1 | cut -d' ' -f2)
curl -s "$PKG" | tr -d '\r' | grep -A4 -F "$DEB" | grep -E '^(Size|SHA256):'    # 同一条记录的官方校验值
echo "https://downloads.claude.ai/claude-desktop/apt/stable/$DEB"               # 对应的下载地址
```

Cowork 标签页在 Linux 上要靠 QEMU/KVM 虚拟机，需要硬件虚拟化、`virtiofsd` 与 `kvm` 组权限；不需要 Cowork 可忽略（文档给出）。

### 2.4 App 的取最新地址命令

```bash
# 解析当前 Windows x64 / macOS / Linux 的 App 版本与真实直链
UA="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0 Safari/537.36"
# 先访问一次下载页拿到 Cloudflare cookie，否则 redirect 端点回 403
curl -s -c /tmp/cj.txt -o /dev/null -A "$UA" https://claude.com/download
for p in win32/x64/setup win32/arm64/setup darwin/universal/dmg; do
  curl -s -b /tmp/cj.txt -c /tmp/cj.txt -D - -o /dev/null -A "$UA" -e https://claude.com/download \
    "https://claude.ai/api/desktop/$p/latest/redirect" | tr -d '\r' | awk 'tolower($1)=="location:"{print $2}'
done

# Linux：从 apt 索引里挑本架构最新 .deb 并下载（官方文档给的一条命令）
curl -fLO "https://downloads.claude.ai/claude-desktop/apt/stable/$(curl -s \
  "https://downloads.claude.ai/claude-desktop/apt/stable/dists/stable/main/binary-$(dpkg --print-architecture)/Packages" \
  | grep '^Filename: pool/main/c/claude-desktop/claude-desktop_' | sort -V | tail -n 1 | cut -d' ' -f2)"

# 下载 Windows x64 App 安装器（本文那只已下好的包）
curl -fL --retry 3 -o ClaudeDesktop-Windows-64位-用户级-2.31226.1.exe \
  "https://downloads.claude.ai/releases/win32/x64/2.31226.1/ClaudeSetup-8576520051724193e9f74fc538f9ce8125e9ddd2.exe"
```

---

## 3. 终端版取最新二进制的命令（不下载，仅按需）

```bash
# latest / stable 通道版本号
CC_VER=$(curl -fsSL https://downloads.claude.ai/claude-code-releases/latest)   # 当前 2.1.296
CC_VER=$(curl -fsSL https://downloads.claude.ai/claude-code-releases/stable)   # 当前 2.1.287

# 查某平台包的大小与官方 SHA256（不下载整个包）
curl -sIL "https://downloads.claude.ai/claude-code-releases/$CC_VER/win32-x64/claude.exe" | grep -i content-length
curl -fsSL "https://downloads.claude.ai/claude-code-releases/$CC_VER/manifest.json" \
  | tr -d ' \n' | grep -o '"win32-x64":{[^}]*}'

# 真要离线分发再下
curl -fL -o "ClaudeCodeCLI-Windows-64位-v${CC_VER}.exe" \
  "https://downloads.claude.ai/claude-code-releases/${CC_VER}/win32-x64/claude.exe"
```

---

## 4. 给同事部署时怎么选

| 场景 | 建议 |
| --- | --- |
| 只在图形界面里让 AI 改代码、看 diff | App 版（Windows x64 那只 6.8 MB 安装器，或 macOS dmg） |
| 已经天天用终端、要脚本化 / CI | 终端版，`curl … \| bash` 或 `winget install Anthropic.ClaudeCode` |
| 公司电脑不给管理员权限 | 都可行：CLI 用户级、App Windows 包也是用户级 |
| Linux 服务器 / WSL | 终端版（App 的 Linux 支持仍是 beta，且只认 Debian 系） |
| 要离线包拷进内网 | Windows 端 App 是在线安装器，不满足；改用终端版二进制直链（1.3），或 macOS 的完整 dmg |
| 网络到不了 Anthropic | 两者都不行；先确认能访问 `downloads.claude.ai` 与 `claude.ai`，再参考 [/docs/en/network-config](https://code.claude.com/docs/en/network-config.md)（文档给出） |

一句话：**App 版和终端版不是一个东西，但装 App 就够了**（App 内置 Claude Code）；终端版适合要在自己的 shell / CI 里跑 `claude` 的人。两者版本号体系不同，别拿 2.31226.1 去找 CLI。
