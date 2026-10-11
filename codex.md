# OpenAI 侧下载与安装目录（ChatGPT 桌面 App / Codex CLI × 平台）

> 出品方：OpenAI　整理日期：2026-10-10
> 返回总目录：[`agent下载目录.md`](./agent下载目录.md)　Claude 侧同结构文档见 [`claude_code.md`](./claude_code.md)
> 本文按你的要求**只下载了 ChatGPT App 的 Windows x64 包**（在 [`安装包/`](./安装包/)）；Codex CLI 一律只记安装命令，其他平台只留链接。
> 来源标注口径：**实测 200** = 已用 HTTP 校验状态码与 `Content-Length`；**文档给出** = 官方文档原文照抄；**官方仓库** = GitHub Releases / npm registry。
> **校验值口径**：本文不写死校验码。`ChatGPT-x64.msix` 那类不带版本号的固定地址，每次发版对象就换，写死的哈希必然报错，且 OpenAI 侧**根本不公布校验值**（只有 Azure 的 blob 版本令牌）；终端版走 GitHub，Release API 里每个资产都带 `digest: sha256:...`，属长期稳定地址，命令现取即可（见 2.2）。汇总见 [`download_myself.md`](./download_myself.md) 第 8 节。
> 官方来源：<https://learn.chatgpt.com/docs/quickstart?setup=app>、[/docs/app.md](https://learn.chatgpt.com/docs/app.md)、[/docs/codex/cli.md](https://learn.chatgpt.com/docs/codex/cli.md)、[/docs/windows/windows-app.md](https://learn.chatgpt.com/docs/windows/windows-app.md)、[/docs/enterprise/windows-deployment.md](https://learn.chatgpt.com/docs/enterprise/windows-deployment.md)、[/docs/linux/linux-app.md](https://learn.chatgpt.com/docs/linux/linux-app.md)、<https://github.com/openai/codex>

---

## 速览：ChatGPT App 和 Codex CLI 是两个东西

| | **App 版 ChatGPT 桌面 App** | **终端版 Codex CLI** |
| --- | --- | --- |
| 是什么 | ChatGPT 客户端，可切 Chat / Work，并能选 **Codex**；支持项目、文件、浏览器与电脑操作、定时任务 | `codex` 命令，终端里对本地仓库读写代码、跑命令、`codex exec` 做脚本与 CI |
| 关系 | App 里的 Codex 用的就是 Codex 那套 agent，但**不需要另装 CLI** | 独立分发，跟 App 不同版本号 |
| 当前版本 | **26.1007.2314.0**（读自 Windows MSIX 包清单，实测） | **0.162.1**（npm 与 GitHub Release tag `rust-v0.162.1` 一致，实测） |
| 平台 | Windows x64 / ARM64、macOS、Linux（preview） | Windows（x64/ARM64）、macOS（x64/arm64）、Linux（x64/arm64，musl） |
| 包形态 | MSIX / dmg / deb / rpm | exe / dmg / tar.gz / npm 包 |
| 是否已下载 | ✅ 只下 Windows x64 | ❌ 只记命令 |
| 国内版 | 无，同 Claude 侧：只发国际版，需海外网络 | 无 |

> Windows 上有个容易踩的坑：App 的 MSIX **包标识仍叫 `OpenAI.Codex`**，显示名才是 ChatGPT（实测读自 `AppxManifest.xml`）。企业部署的检测规则、`Get-AppxProvisionedPackage` 过滤都得按 `OpenAI.Codex` 写。

---

## 1. App 版 ChatGPT 桌面 App

### 1.1 下载地址（按平台）

| 平台 | 包 | 大小 | 地址 | 校验 |
| --- | --- | --- | --- | --- |
| **Windows x64** | `ChatGPT-x64.msix`（离线整包） | 900.9 MB | <https://persistent.oaistatic.com/codex-app-prod/ChatGPT-x64.msix> | 实测 200，✅ **已下载** |
| Windows x64 | Microsoft Store 安装器（在线，双击走 Store） | 小 | <https://get.microsoft.com/installer/download/9PLM9XGG6VKS?cid=website_cta_psi> | 文档给出（HEAD 返回 405，只能浏览器 GET） |
| Windows ARM64 | `ChatGPT-arm64.msix` | 970.2 MB | <https://persistent.oaistatic.com/codex-app-prod/ChatGPT-arm64.msix> | 实测 200 |
| macOS | `ChatGPT.dmg` | 746.9 MB | <https://persistent.oaistatic.com/codex-app-prod/ChatGPT.dmg> | 实测 200 |
| macOS（旧版） | `ChatGPT_Classic.dmg`（ChatGPT Classic，非本文主体） | 75.0 MB | <https://persistent.oaistatic.com/classic/public/ChatGPT_Classic.dmg> | 实测 200 |
| Linux | 见 1.3 | 453–531 MB | <https://learn.chatgpt.com/docs/linux/linux-app.md> | 文档给出 + 直链实测 200 |

其他入口：官网下载页 <https://chatgpt.com/download/>（GET 实测 200，HEAD 会被拦成 403），文档里的「Install the ChatGPT desktop app」也指向它。
Windows 命令行安装（文档给出）：`winget install --id 9PLM9XGG6VKS -s msstore`（Store 产品 ID 就是那串 `9PLM9XGG6VKS`）。

### 1.2 已下载文件（Windows x64）

| 项 | 值 |
| --- | --- |
| 文件名 | `安装包/ChatGPT-Windows-64位-Appx-v26.1007.2314.0.msix` |
| 大小 | 944,693,176 B（900.9 MB），与官方 `Content-Length` 逐字节一致（实测） |
| 校验值 | OpenAI **不公布任何官方校验值**：该 CDN 只给 `etag: "0x8DF265427C674A5"`，那是 Azure blob 的版本令牌，不是内容哈希（拿去和内容比恒不相等），`<文件>.sha256` / `SHA256SUMS` 之类侧车也没有。而 `ChatGPT-x64.msix` 这个地址不带版本号、发版即换对象，所以任何写死的哈希到下次下载都会失败 —— 本文删除了原先记录的 SHA256。核对方式改为三件套：代码签名（下两行）+ `Content-Length` 逐字节比对 + 包内 `AppxManifest.xml` 的 `Version`；实时取法见 [`download_myself.md`](./download_myself.md) 第 8.4 节 |
| 包标识 | `OpenAI.Codex`，显示名 ChatGPT，版本 `26.1007.2314.0`，最低系统 `10.0.19041.0`（Win10 1903+）—— 实测读自包内 `AppxManifest.xml` |
| 代码签名 | `Status = Valid`，主体 `CN=50BDFD77-8903-4850-9FFE-6E8522F64D5B`，签发方 **Microsoft Marketplace CA G 028**（即 Microsoft Store 签名，不是 OpenAI 自建企业证书），证书有效期到 2026-10-11。签名带时间戳，过期后已下的包仍可安装；届时重下会拿到新证书签的包。本机 `Get-AuthenticodeSignature` 实测 |
| 包内构建时间 | 2026-10-09（`AppxManifest.xml` 时间戳，实测） |

### 1.3 Linux（preview，文档给出 + 直链实测 200）

支持 Ubuntu 24.04 / 26.04 LTS、Debian 13、Fedora 43 / 44、Arch（滚动更新版），每档都有 x64 与 ARM64。

| 发行版 | 架构 | 包 | 大小 | 直链 |
| --- | --- | --- | --- | --- |
| Ubuntu / Debian | x64 | `chatgpt_amd64.deb` | 474.1 MB | <https://persistent.oaistatic.com/codex-app-prod/linux/deb/latest/chatgpt_amd64.deb> |
| Ubuntu / Debian | ARM64 | `chatgpt_arm64.deb` | 453.1 MB | <https://persistent.oaistatic.com/codex-app-prod/linux/deb/latest/chatgpt_arm64.deb> |
| Fedora | x64 | `chatgpt.x86_64.rpm` | 530.7 MB | <https://persistent.oaistatic.com/codex-app-prod/linux/rpm/latest/chatgpt.x86_64.rpm> |
| Fedora | ARM64 | `chatgpt.aarch64.rpm` | 508.2 MB | <https://persistent.oaistatic.com/codex-app-prod/linux/rpm/latest/chatgpt.aarch64.rpm> |
| Arch | 两者 | 脚本安装 | 6 KB | <https://persistent.oaistatic.com/codex-app-prod/linux/install-arch.sh>（实测 200） |

```bash
# Ubuntu / Debian
sudo apt install ./chatgpt_amd64.deb
# Fedora
sudo dnf install ./chatgpt.x86_64.rpm
# Arch
curl -fsSL https://persistent.oaistatic.com/codex-app-prod/linux/install-arch.sh | sh
```

路径里的 `latest` 是固定目录，重新下载即拿到当前版（与 Claude 侧 `latest` 指针同理，但这里是目录不是版本号）。正因为它不带版本，**deb / rpm / dmg / msix 全套 OpenAI 自托管包都没有可用校验值**，只核 `Content-Length` 与安装后的发布者信息；离线 `.deb` 想校验，只有发行版自己的包管理器链（装进 apt 源后由 `Packages` 索引哈希把关），官方目录里没有 `Packages` 索引可取。

### 1.4 离线 / 企业部署（文档给出）

MSIX 可以直接双击装，也可脱管分发；**离线装必须配 license 文件**，且要管理员：

```powershell
# 三个文件放同一目录，MSIX 需重命名为 ChatGPT.msix
# https://persistent.oaistatic.com/codex-app-prod/ChatGPT-x64.msix
# https://persistent.oaistatic.com/codex-app-prod/ChatGPT-License.xml
Add-AppxProvisionedPackage -Online `
  -PackagePath "$PSScriptRoot\ChatGPT.msix" `
  -LicensePath "$PSScriptRoot\ChatGPT-License.xml" -Regions all
```

Microsoft Store / Intune / SCCM 走法、检测规则（按 `DisplayName -eq 'OpenAI.Codex'`）见 [/docs/enterprise/windows-deployment.md](https://learn.chatgpt.com/docs/enterprise/windows-deployment.md)。用户自装的 Store 安装器需管理员批准一次，之后自动更新。

---

## 2. 终端版 Codex CLI

### 2.1 安装命令（全部官方文档给出；脚本可达性实测 200）

| 平台 / 通道 | 安装 | 更新 | 备注 |
| --- | --- | --- | --- |
| macOS / Linux | `curl -fsSL https://chatgpt.com/codex/install.sh \| sh` | 同命令重跑 | 脚本实测 200，34,564 B |
| Windows | `powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 \| iex"` | 同命令重跑 | 脚本实测 200，67,845 B；文档要求**新开一个 PowerShell 窗口**执行 |
| npm（跨平台） | `npm install -g @openai/codex` | 同命令重跑 | registry 当前 `0.162.1`（实测） |
| Homebrew（macOS / Linux） | `brew install --cask codex` | `brew upgrade --cask codex` | cask 名为 `codex` |
| WinGet（Windows） | `winget install OpenAI.Codex` | `winget upgrade OpenAI.Codex` | 本机 `winget show` 实测源内 **0.161.0**，落后 latest 一版 |

装完在项目目录里直接运行 `codex`，首次选 **Sign in with ChatGPT** 或其他鉴权方式（见 [/docs/auth.md](https://learn.chatgpt.com/docs/auth.md)）。

### 2.2 GitHub Releases 各平台二进制（官方仓库 + 实测 200）

Release：<https://github.com/openai/codex/releases/tag/rust-v0.162.1>（tag `rust-v0.162.1`，发布 2026-10-09T19:44:51Z，共 176 个资产；实测）

取最新版的机器可读入口：`https://api.github.com/repos/openai/codex/releases/latest` → `tag_name`（实测返回 `rust-v0.162.1`）。下载地址模板：

```text
https://github.com/openai/codex/releases/download/<tag>/codex-<rust目标三元组>[.exe][.tar.gz|.zip|.zst|.dmg]
```

| 平台 | 资产 | 大小 | 直链 |
| --- | --- | --- | --- |
| Windows x64 | `codex-x86_64-pc-windows-msvc.exe` | 318.0 MB | <https://github.com/openai/codex/releases/download/rust-v0.162.1/codex-x86_64-pc-windows-msvc.exe>（实测 200） |
| Windows x64 | 压缩包 `.zip` | 157.0 MB | 同目录 `...exe.zip` |
| Windows ARM64 | `codex-aarch64-pc-windows-msvc.exe` | 269.4 MB | <https://github.com/openai/codex/releases/download/rust-v0.162.1/codex-aarch64-pc-windows-msvc.exe> |
| macOS Intel | `codex-x86_64-apple-darwin.dmg` | 181.5 MB | <https://github.com/openai/codex/releases/download/rust-v0.162.1/codex-x86_64-apple-darwin.dmg>（实测 200） |
| macOS Apple Silicon | `codex-aarch64-apple-darwin.dmg` | 166.1 MB | <https://github.com/openai/codex/releases/download/rust-v0.162.1/codex-aarch64-apple-darwin.dmg> |
| macOS（tar 形式） | `codex-{x86_64,aarch64}-apple-darwin.tar.gz` | 102.1 / 93.6 MB | 同目录 `.tar.gz` |
| Linux x64 | `codex-x86_64-unknown-linux-musl.tar.gz` | 106.2 MB | <https://github.com/openai/codex/releases/download/rust-v0.162.1/codex-x86_64-unknown-linux-musl.tar.gz>（实测 200） |
| Linux ARM64 | `codex-aarch64-unknown-linux-musl.tar.gz` | 98.8 MB | <https://github.com/openai/codex/releases/download/rust-v0.162.1/codex-aarch64-unknown-linux-musl.tar.gz> |
| Linux 沙箱依赖 | `bwrap-x86_64-unknown-linux-musl.tar.gz` | 0.25 MB | 同目录；Linux 上 `codex` 用 bubblewrap 做沙箱 |
| 签名 | `*.sigstore` | 8 KB | 每个 Linux 资产配套，用 `cosign`/`sigstore` 验证（官方仓库） |

> Linux 侧**只发 musl 静态二进制**，没有 glibc 版（官方仓库实测：目标三元组只有 `unknown-linux-musl`）；静态二进制在 Ubuntu/Debian/Fedora 上直接能跑。
> macOS 的 `.dmg` 与 `.tar.gz` 是同一个二进制的两种封装，脚本/CI 用 tar.gz 更省事。

校验值：终端版**有官方实时 SHA256，而且地址长期稳定** —— GitHub 在每个 Release 资产上给出 `digest` 字段（`sha256:` 前缀），2026-10-09 那只 `rust-v0.162.1` 的 176 个资产全带。所以下面这些链接不必在文档里配哈希表，离线分发前按资产名现取（表里写死的 `rust-v0.162.1` 会和 `latest` 一起过期，地址与 `digest` 都从这条 API 拿）：

```bash
curl -fsSL https://api.github.com/repos/openai/codex/releases/latest -o /tmp/last.json        # 先落盘再解析：管道里 grep -m1 提前退出会把 curl 打断报错
TAG=$(grep -oE '"tag_name": "[^"]+"' /tmp/last.json | head -1 | cut -d'"' -f4)               # rust-v0.162.1
curl -fsSL "https://api.github.com/repos/openai/codex/releases/tags/${TAG}" -o /tmp/rel.json
awk '/"name": "codex-x86_64-pc-windows-msvc.exe"/{f=1} f&&/"digest"/{print; exit}' /tmp/rel.json   # "digest": "sha256:dd13bdb1…"
sha256sum CodexCLI-Windows-64位-*.exe                                                          # 去掉 sha256: 前缀后比对
```

Linux / macOS 资产把 `awk` 里的资产名换成 `codex-aarch64-apple-darwin.tar.gz`、`codex-x86_64-unknown-linux-musl.tar.gz` 等即可；`*.sigstore` 那套是另一条链（cosign/sigstore 验证，官方仓库），用它就不必自己算哈希。

### 2.3 手动下载命令（按需，不放进 `安装包/`）

```bash
# 解析最新 tag（落盘再取，避免管道提前退出报错）
curl -fsSL https://api.github.com/repos/openai/codex/releases/latest -o /tmp/last.json
CODEX_TAG=$(grep -oE '"tag_name": "[^"]+"' /tmp/last.json | head -1 | cut -d'"' -f4)     # rust-v0.162.1

# 例：Windows x64 裸二进制
curl -fL --retry 3 -o "CodexCLI-Windows-64位-v${CODEX_TAG#rust-v}.exe" \
  "https://github.com/openai/codex/releases/download/${CODEX_TAG}/codex-x86_64-pc-windows-msvc.exe"

# 例：macOS Apple Silicon 走 tar.gz
curl -fL --retry 3 "https://github.com/openai/codex/releases/download/${CODEX_TAG}/codex-aarch64-apple-darwin.tar.gz" \
  | tar -xz -C ~/.local/bin
```

---

## 3. 给同事部署时怎么选

| 场景 | 建议 |
| --- | --- |
| 只想用图形界面（项目、文件、浏览器操作、定时任务），顺带用 Codex | ChatGPT 桌面 App；Windows 发 1.2 那只 MSIX（900 MB 离线整包，内网也能装），macOS 发 dmg |
| 天天开终端、要 `codex exec` 进 CI | Codex CLI，macOS/Linux 用 `install.sh`，Windows 用 `install.ps1`，公司统一版本就锁 npm/`brew` |
| 电脑不给管理员权限 | CLI 更省事（用户目录即可）；App 的 MSIX 与 Store 安装器都要管理员批准一次 |
| Linux | App 仍是 preview（只支持 Ubuntu 24.04/26.04、Debian 13、Fedora 43/44、Arch），要稳就用 CLI |
| 需要离线整包 | App 满足（MSIX/dmg/deb/rpm 都是完整包）；这点比 Claude 侧好，Claude 的 Windows 包只有 6.8 MB 在线安装器，见 [`claude_code.md`](./claude_code.md) 2.1 |
| 包体积敏感 | CLI 的 tar.gz（100 MB 级）远小于 App（900 MB 级） |

一句话：**App 版和终端版各管各的**，装 App 能直接用 Codex，不必再装 CLI；反之装了 CLI 也没有图形界面。两条版本线互不相干（App `26.1007.2314.0` / CLI `0.162.1`），别互相套用版本号。
