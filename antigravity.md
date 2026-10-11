# Google Antigravity 下载与安装目录（终端版 / App 版 × 平台）

> 出品方：Google（签名主体 Google LLC）　整理日期：2026-10-10
> 返回总目录：[`agent下载目录.md`](./agent下载目录.md)　同结构文档：[`claude_code.md`](./claude_code.md)（Anthropic）、[`codex.md`](./codex.md)（OpenAI）　命令行汇总风格见 [`download_myself.md`](./download_myself.md)
> 本文**只下载了 App 版（Antigravity 2.0）Windows x64 一个包**（已放进 [`安装包/`](./安装包/)）；终端版一律只记安装命令，macOS / Linux / Android 与旧版 IDE 线只留链接。
> 来源标注口径：**实测 200** = 用 HTTP 校验过状态码与 `Content-Length`；**文档给出** = 官方文档原文照抄，未单独发包验证；**官方清单** = CLI 更新服务 `manifests/<平台>.json` 与 winget 仓库清单（机器可读，随发版更新）。
> **校验值口径（本文不写死任何哈希数值）**：
> - **终端版有官方实时 SHA512**：每平台一份 `manifest.json`，字段 `version` / `url` / `sha512`，地址长期稳定，现取现用（命令见 1.3）。
> - **App 版 Google 自己不公布校验值**：同一只对象的 `.sha256` / `.sha512` 侧车、`manifest.json`、`latest.yml`、`version.json`、`antigravity-hub/latest/...` 实测**全部 404**；GCS 响应头对这几只复合上传对象**只给 `crc32c`，连 `md5` 头都没有**（`ETag` 形如 `-CNqCmv6rqpcDEAE=`，带前导横线即复合对象标志）。实操核对退回三项：代码签名 `Valid` + `Content-Length` 逐字节 + 包内 `ProductVersion`（见 2.2）。
> - **可用的替代校验源（Windows 两只包）**：微软 winget 仓库清单里逐架构记了 `InstallerSha256`，取法见 2.3 末。**注意这是社区/机器人提交的第三方记录，不是 Google 官方发布的校验值**，只覆盖 Windows x64 / ARM64；macOS 与 Linux 包没有任何哈希可取。
> - 本机已下包的完整性记在 `安装包/SHA256SUMS.local`（`sha256sum -c` 自检），数值不抄进文档。跨产品汇总见 [`download_myself.md`](./download_myself.md) 第 8 节。
> 官方来源：<https://antigravity.google/docs/getting-started.md>（本文 App 直链与 CLI 命令的原始出处）、`/docs/overview.md`、`/docs/plans.md`、`/docs/faq.md`、`/docs/cli/install.md`、`/docs/cli/reference.md`、`/docs/ide/overview.md`、`/docs/enterprise.md`
> **抓取技巧**：`antigravity.google/docs/<页>.md` 同样支持末尾加 `.md` 取纯文本（与 Claude / OpenAI 那两家一致），**但必须带 `--compressed`**：这些响应是 gzip 编码的，`curl -s` 不带该参数拿回来的是二进制流（实测），管道里的 `grep` 会当成 binary file 跳过。`/docs/llms.txt` 实测 404，没有索引文件。`/download` 页面是 JS 渲染，直链由前端注入，`curl` 抓 HTML 里搜不到任何 `antigravity-hub/`，要解析 App 版本就走上面那个 `.md` 页。

---

## 速览：Google 一家有**三条**在发版的版本线

| | **终端版 Antigravity CLI** | **App 版 Antigravity 2.0** | **旧 App 线 Antigravity IDE** |
| --- | --- | --- | --- |
| 是什么 | `agy` 命令，终端 TUI；agent 读写本地仓库、跑命令，`agy -p` 做无头脚本与 CI | **独立桌面 App**，官方定位「AI agent 的指挥中心」，可同步/异步编排多个 agent，不依赖 IDE | 早前那款 agentic **IDE**（编辑器 + Tab 补全 + 浏览器 agent），单工作区 |
| 当前版本 | **1.3.3**（构建 `5524738307653632`，实测取自 manifest；对象 `Last-Modified` 2026-10-10） | **2.22.0**（构建 `5446056071266304`，实测读自包内 `VersionInfo`；对象 2026-10-08） | **2.5.5**（构建 `4923483625488384`，实测读自直链路径；对象 2026-10-08） |
| 平台 | Windows x64/ARM64、macOS x64/arm64、Linux x64/arm64（**各含 musl**）、Android arm64 —— manifest 共 9 个键 | Windows x64/ARM64、macOS Apple Silicon/Intel、Linux x64/ARM64、Googlebook（Play） | 同左（路径段写法不同，见 3.1 的坑） |
| 包形态 | 裸二进制 `.exe` / `.tar.gz` | NSIS `.exe` / `.dmg` / `.tar.gz` | 同名 NSIS `.exe` / `.dmg` / `.tar.gz`，走另一只 CDN |
| 官方校验值 | ✅ SHA512（manifest，实时可取） | ❌ 不公布；⚠️ Windows 两只可用 winget 仓库的 `InstallerSha256` 交叉核 | ❌ 不公布，也没有任何侧车或索引 |
| 企业支持 | ✅ | ✅ | ❌ 文档原话：*not supported for enterprise customers*，让企业用 2.0 或 CLI |
| 本文是否下载 | ❌ 只记命令 | ✅ **只下了 Windows x64** | ❌ 只留链接 |

> 三条版本号**互不相干，谁也不能拿来推谁**：CLI `1.3.3`、App `2.22.0`、IDE `2.5.5`。尤其别看数字大小 —— IDE 的 `2.5.5` 比 App 的 `2.22.0` 字面「小」，实际是另一条独立产品线，`2.x` 前缀撞车纯属巧合。
> 和 Claude / OpenAI 那两份文档最大的结构差异：那两家各是「CLI + App」两条线，**Google 多了一条仍在发版的旧 IDE 线**，而且终端版与 App 版连包体所在 CDN 都不同（CLI/App 都在 `storage.googleapis.com/antigravity-public`，但桶内前缀分别是 `antigravity-cli/` 与 `antigravity-hub/`；旧 IDE 在 `edgedl.me.gvt1.com`）。
> 和那两家相同的一点：**没有国内版 / 海外版之分**，只发国际版，需海外网络环境，账号要求见第 4 节。

---

## 1. 终端版 Antigravity CLI（`agy`）

### 1.1 安装命令（文档给出；三个脚本可达性全部实测 200）

| 平台 / 通道 | 命令 | 装到哪 | 备注 |
| --- | --- | --- | --- |
| macOS、Linux、Googlebook | `curl -fsSL https://antigravity.google/cli/install.sh \| bash` | `~/.local/bin/agy` | 脚本实测 200，7,643 B |
| Windows PowerShell | `irm https://antigravity.google/cli/install.ps1 \| iex` | `%LOCALAPPDATA%\agy\bin\agy.exe` | 实测 200，7,165 B；不需要管理员权限 |
| Windows CMD | `curl -fsSL https://antigravity.google/cli/install.cmd -o install.cmd && install.cmd && del install.cmd` | 同上 | 实测 200，6,006 B；报 `&&` 不是有效分隔符说明你在 PowerShell |
| WinGet（Windows） | `winget install Google.AntigravityCLI` | portable 裸二进制，命令名 `agy` | 本机 `winget show` 实测源内版本 **1.3.3**，与官方 manifest 同版；x64 与 arm64 两个架构都在清单里 |
| Homebrew / npm | —— | —— | **官方文档没有给这两条通道**（本文未实测 brew，本机为 Windows）；要用包管理就只认 winget |

脚本自身的行为（实测读脚本原文，`install.sh` 7,643 B）：

- 先按 `uname -s` / `uname -m` 探测平台，Linux 还会判 musl（读 `/lib/libc.musl-*.so.1` 或 `ldd /bin/ls`），Android（Termux）走 bionic 构建 —— 所以**一条命令覆盖全部类 Unix 平台，不用手挑架构**。
- 取 `$DOWNLOAD_BASE_URL/manifests/<平台键>.json` 拿 `version` / `url` / `sha512`，下载到 staging 目录后**先算 SHA512 比对，不匹配直接 `Security Halt` 中止安装**。也就是说官方脚本自带校验，正常装完不需要你手工核哈希。
- staging 位置：macOS/Linux `~/.cache/antigravity/staging`，Windows `%LOCALAPPDATA%\antigravity\staging`，退出时 trap 清理。
- 已装过就直接退出并提示：`agy` 每次运行会在后台自更新；要重装先 `rm ~/.local/bin/agy`（Windows 删 `%LOCALAPPDATA%\agy\bin\agy.exe`）。
- 会改 shell profile 追加 `PATH` 并清掉历史遗留的 `agy` / `antigravity` 别名；不想动就加 `--skip-path` / `--skip-aliases`（文档给出），或用 `-d <目录>` 自定义安装目录。

装完常用命令（文档给出）：

| 操作 | 命令 |
| --- | --- |
| 自检版本 | `agy --version` |
| 手动升级 | `agy update`（平时每次运行已在后台自更新） |
| 启动 TUI | 进项目目录直接敲 `agy`（首次走浏览器登录，再选主题、渲染模式 Alt-Screen / Inline、确认信任该仓库） |
| 无头 / CI | `agy -p`（FAQ 明确：只用于直接脚本与 CI，见 4.3） |
| 退出登录 | TUI 里 `/logout`（清 keyring 里的凭据与本地缓存；用 API key 时该命令无效） |

鉴权三条路线（文档给出）：本机首次启动调浏览器登录（个人 Google 账号，或选 **Business account** 走 Gemini Enterprise）；token 存操作系统钥匙串（Apple Keychain / Secret Service / Windows 凭据管理器），有有效 token 就静默续用；**SSH 远程**没有浏览器，CLI 打印授权 URL，本地浏览器登录后把授权码粘回终端。也可以在 `~/.gemini/antigravity-cli/settings.json` 里设 `"modelProvider": "gemini"` 并 `export GEMINI_API_KEY=...`（**只认环境变量里的 `GEMINI_API_KEY`，不读 `.env`；单设变量不设 provider 无效**），适合无浏览器环境；还能用 `GOOGLE_GEMINI_BASE_URL` 指向自建 Gemini 兼容端点。

### 1.2 系统要求（文档给出）

| 项 | 要求 |
| --- | --- |
| Windows | Windows 10（64-bit）；x64 与 ARM64 都有构建 |
| macOS | 支持 Apple 安全更新的版本（通常是当前 + 前两个），最低 12 Monterey；文档同时写 *x86 is not supported* —— 见 2.1 的坑 |
| Linux | glibc ≥ 2.28、glibcxx ≥ 3.4.25（例：Ubuntu 20、Debian 10、Fedora 36、RHEL 8）；另发 musl 静态构建 |
| Googlebook / Android | Googlebook OS 预装或从 Play 装；CLI 有 `android_arm64` 构建（Termux 走 bionic），**没有 android_amd64（manifest 实测 404）** |

### 1.3 各平台裸二进制直链（离线分发才需要；本文按要求未下载）

**没有 `latest/` 目录**（`antigravity-cli/latest/...` 实测 404），版本号必须先从 manifest 取出来再拼路径。地址模板（官方清单 + 实测）：

```text
https://storage.googleapis.com/antigravity-public/antigravity-cli/<版本号-构建号>/<平台目录>/<文件名>
```

下表是当期 `1.3.3-5524738307653632` 的 9 个平台，全部实测 200。**`url` 里写死版本号，发版即过期**（CLI 发版很勤：manifest 的 `ReleaseDate` 就是 2026-10-10，对象 `Last-Modified` 当天凌晨）；`sha512` 一栏本文只给取法，不给数值。

| 平台 | manifest 键 | 包 | 大小 | 直链 |
| --- | --- | --- | --- | --- |
| Windows x64 | `windows_amd64` | `cli_windows_x64.exe` | 184.2 MB | <https://storage.googleapis.com/antigravity-public/antigravity-cli/1.3.3-5524738307653632/windows-x64/cli_windows_x64.exe> |
| Windows ARM64 | `windows_arm64` | `cli_windows_arm64.exe` | 174.9 MB | <https://storage.googleapis.com/antigravity-public/antigravity-cli/1.3.3-5524738307653632/windows-arm/cli_windows_arm64.exe> |
| macOS Apple Silicon | `darwin_arm64` | `cli_mac_arm64.tar.gz` | 52.5 MB | <https://storage.googleapis.com/antigravity-public/antigravity-cli/1.3.3-5524738307653632/darwin-arm/cli_mac_arm64.tar.gz> |
| macOS Intel | `darwin_amd64` | `cli_mac_x64.tar.gz` | 57.3 MB | <https://storage.googleapis.com/antigravity-public/antigravity-cli/1.3.3-5524738307653632/darwin-x64/cli_mac_x64.tar.gz> |
| Linux x64 | `linux_amd64` | `cli_linux_x64.tar.gz` | 59.7 MB | <https://storage.googleapis.com/antigravity-public/antigravity-cli/1.3.3-5524738307653632/linux-x64/cli_linux_x64.tar.gz> |
| Linux ARM64 | `linux_arm64` | `cli_linux_arm64.tar.gz` | 56.3 MB | <https://storage.googleapis.com/antigravity-public/antigravity-cli/1.3.3-5524738307653632/linux-arm/cli_linux_arm64.tar.gz> |
| Linux x64 (musl) | `linux_amd64_musl` | `cli_linux_x64_musl.tar.gz` | 59.8 MB | <https://storage.googleapis.com/antigravity-public/antigravity-cli/1.3.3-5524738307653632/linux-x64-musl/cli_linux_x64_musl.tar.gz> |
| Linux ARM64 (musl) | `linux_arm64_musl` | `cli_linux_arm64_musl.tar.gz` | 54.4 MB | <https://storage.googleapis.com/antigravity-public/antigravity-cli/1.3.3-5524738307653632/linux-arm-musl/cli_linux_arm64_musl.tar.gz> |
| Android arm64（Termux） | `android_arm64` | `cli_android_arm64.tar.gz` | 51.6 MB | <https://storage.googleapis.com/antigravity-public/antigravity-cli/1.3.3-5524738307653632/android-arm/cli_android_arm64.tar.gz> |

> 体积差异值得记一笔：CLI 的 **Windows 构建是 184 MB 裸 exe**（内嵌依赖），而 macOS / Linux / Android 是 50–60 MB 的 `.tar.gz`。给 Windows 同事做离线分发时别按 mac 的体量估。
> 平台目录名与 manifest 键不是一一对应的字符串（`windows_amd64` → `windows-x64`，`linux_arm64_musl` → `linux-arm-musl`），**别猜路径，直接取 manifest 里的 `url` 字段**（见下）。
> 对象头哈希情况（实测）：`.tar.gz` 那几只 GCS 有 `x-goog-hash: md5=...`，两只 Windows `.exe` 只有 `crc32c`（复合上传，无 MD5）。这些都不必用 —— 官方 SHA512 就在 manifest 里。

**取当期版本号与官方 SHA512（本文对终端版唯一推荐的校验方式，地址长期稳定、随发版更新）：**

```bash
AGY_CLI_BASE="https://antigravity-cli-auto-updater-974169037036.us-central1.run.app"
PLAT=windows_amd64     # 换成 darwin_arm64 / linux_amd64_musl / android_arm64 等，见上表第二列
curl -fsSL "$AGY_CLI_BASE/manifests/$PLAT.json"          # 当期清单：version + url + sha512，302 B 的小 JSON
# 只要版本号：
curl -fsSL "$AGY_CLI_BASE/manifests/$PLAT.json" | tr -d ' \n' | grep -oE '"version": "[^"]*"' | cut -d'"' -f4
# 下完自检（macOS 用 shasum -a 512）：
sha512sum cli_windows_x64.exe | cut -d' ' -f1           # 与 manifest 的 sha512 逐字符比对
```

2026-10-10 实测：9 个平台键里除 `android_amd64`（404）外全部 200，`version` 一律 `1.3.3`；官方安装脚本正是按这条 `sha512` 做安装前校验的。

---

## 2. App 版 Antigravity 2.0（本文只下载了 Windows x64）

官方定位（文档原话）：*a standalone desktop application tailored for managing AI agents*，独立于 IDE 运行， predecessor 是 Agent Manager。能力面：执行系统命令、读写文件、联网搜索、skills 与 MCP server、子 agent 管理、操作 Chrome、产出 artifacts 与实施计划。

### 2.1 下载地址（按平台，全部实测 200）

| 平台 | 包 | 类型 | 大小 | 直链（当期 2.22.0） |
| --- | --- | --- | --- | --- |
| **Windows x64** | `Antigravity-x64.exe` | NSIS 安装器 | **156.5 MB** | <https://storage.googleapis.com/antigravity-public/antigravity-hub/2.22.0-5446056071266304/windows-x64/Antigravity-x64.exe> ✅ **已下载** |
| Windows ARM64 | `Antigravity-arm64.exe` | NSIS 安装器 | 147.3 MB | <https://storage.googleapis.com/antigravity-public/antigravity-hub/2.22.0-5446056071266304/windows-arm/Antigravity-arm64.exe> |
| macOS Apple Silicon | `Antigravity.dmg` | 完整 `.dmg` | 195.8 MB | <https://storage.googleapis.com/antigravity-public/antigravity-hub/2.22.0-5446056071266304/darwin-arm/Antigravity.dmg> |
| macOS Intel | `Antigravity.dmg` | 完整 `.dmg` | 208.7 MB | <https://storage.googleapis.com/antigravity-public/antigravity-hub/2.22.0-5446056071266304/darwin-x64/Antigravity.dmg> |
| Linux x64 | `Antigravity.tar.gz` | 绿色压缩包 | 171.9 MB | <https://storage.googleapis.com/antigravity-public/antigravity-hub/2.22.0-5446056071266304/linux-x64/Antigravity.tar.gz> |
| Linux ARM64 | `Antigravity.tar.gz` | 绿色压缩包 | 169.9 MB | <https://storage.googleapis.com/antigravity-public/antigravity-hub/2.22.0-5446056071266304/linux-arm/Antigravity.tar.gz> |
| Googlebook / Android | Play 商店 | — | — | <https://play.google.com/store/apps/details?id=com.google.android.apps.antigravity> |

> **四个坑，分发前先读：**
> 1. **macOS 的 x86 声明前后矛盾**：文档 macOS 要求原文是 *Minimum version 12 (Monterey); **x86 is not supported***，但官方下载页与 `getting-started.md` 都还在给 `darwin-x64` 那只 208.7 MB 的 dmg（实测 200）。给 Intel Mac 同事之前先说清「官方声明不支持 x86」，并让对方装完实测；Apple Silicon 走 `darwin-arm` 没有这个问题。
> 2. **Intel Mac 与 Apple Silicon 是两只不同的 dmg**（不像 Claude 那样发 Universal 单包），别拷错架构；Linux 同理分 x64 / arm。
> 3. **地址写死 `2.22.0-5446056071266304`**，`antigravity-hub/latest/...` 实测 404 —— 每次发版整段路径都要换，解析命令见 2.3。
> 4. **Windows ARM64 的路径段是 `windows-arm`**（旧 IDE 线那只写 `windows-arm64`），两条线的平台目录名不一致，不能互套。

安装环节（文档给出）：首次装可能弹「Keep Both / Replace」，**选 Replace**；选了 Replace 会提示重装 IDE。启动并登录时选个人 Google 账号，或选 **Business account** 连 Gemini Enterprise。常用快捷键 `Ctrl+K` 会话选择、`Ctrl+P` 找文件、`Ctrl+L` 聚焦输入、`Ctrl+N` 新会话；斜杠命令 `/goal`（一口气跑完不打断）、`/grill-me`（先反问对齐计划）、`/schedule`（定时任务）、`/browser`（控制 Chrome 调试）、`/plugin`（管插件）。项目（Project）是权限边界：左栏带 `+` 的文件夹图标 → New Project → Add Folder，可一次挂多个目录/仓库拿跨仓上下文；起 agent 时选 **Local mode**（直接在活动目录里改）或 **New worktree mode**（隔离的 Git worktree）。

### 2.2 已下载文件（Windows x64）

| 项 | 值 |
| --- | --- |
| 文件名 | `安装包/Antigravity2.0-Windows-64位-用户级-v2.22.0.exe` |
| 大小 | 164,082,024 B（156.5 MB），与官方 `Content-Length` 及 `x-goog-stored-content-length` 逐字节一致（实测） |
| 包类型 | NSIS 安装器（PE 头实测含 `Nullsoft` 与 `requestedExecutionLevel level="asInvoker"`），winget 官方清单同样标 `InstallerType: nullsoft`。156.5 MB 属全量载荷，**据此判断是离线整包而不是几 MB 的在线安装器 —— 这是推断，本文没有在本机执行安装验证离线行为** |
| 安装级别 | **用户级**：PE manifest 实测 `asInvoker`（不申请提权），winget 官方清单亦标 `Scope: user`（实测读自 winget-pkgs 仓库 2.22.0 的 installer 清单）。安装目录本文未实测（未在本机执行安装） |
| 代码签名 | `Status = Valid`，主体 **`CN=Google LLC, O=Google LLC, L=Mountain View, S=California, C=US, SERIALNUMBER=3582691`**（Delaware 注册），由 **DigiCert Trusted G4 Code Signing RSA4096 SHA384 2021 CA1** 签发，证书有效期至 **2027-04-10**；本机 `Get-AuthenticodeSignature` 实测 |
| 包内版本 | `ProductName = Antigravity`、`ProductVersion = 2.22.0`、`FileVersion = 2.22.0`、`CompanyName = Google`、`FileDescription = "Antigravity - Agentic Desktop Application"`（本机 `VersionInfo` 实测） |
| 校验值 | 官方不公布（见开头口径，实测 404 的侧车清单在内）。**2026-10-10 交叉核过**：微软 winget 仓库当期清单里 `Google.Antigravity` / 2.22.0 / x64 的 `InstallerSha256` 与本机这只文件 `sha256sum` **逐字符一致**，说明 winget 与官网直链是同一个对象；该哈希属于第三方提交记录而非 Google 官方发布，且随版本走，所以数值不抄进本文，取法见 2.3。本地完整性另见 `安装包/SHA256SUMS.local` |
| 对象换版时间 | `Last-Modified: Thu, 08 Oct 2026 11:44:06 GMT`（实测响应头，可当「是否换版」信号） |

### 2.3 取最新地址 + 下载命令

```bash
# 1) 解析 App 当前版本-构建号。getting-started.md 是唯一长期稳定的纯文本源（务必带 --compressed）
AGY_APP=$(curl -s --compressed https://antigravity.google/docs/getting-started.md \
  | grep -oE 'antigravity-hub/[^/"]+' | head -1 | cut -d/ -f2)
echo "$AGY_APP"                                     # 当前 2.22.0-5446056071266304

# 2) 核大小与换版时间（官方无哈希；这两项 + 装完看签名就是全部可核信息）
curl -sI "https://storage.googleapis.com/antigravity-public/antigravity-hub/${AGY_APP}/windows-x64/Antigravity-x64.exe" \
  | tr -d '\r' | grep -iE '^(content-length|x-goog-stored-content-length|last-modified|x-goog-hash)'

# 3) 下载（本文那只已下好的包；macOS 换 darwin-arm / darwin-x64 + Antigravity.dmg，Linux 换 linux-x64 + Antigravity.tar.gz）
curl -fL --retry 3 -o "Antigravity2.0-Windows-64位-用户级-v${AGY_APP%%-*}.exe" \
  "https://storage.googleapis.com/antigravity-public/antigravity-hub/${AGY_APP}/windows-x64/Antigravity-x64.exe"

# 4) 替代校验源（仅 Windows 两只）：winget 仓库清单里记的 InstallerSha256，版本号用 winget 报的当期值
AWV=$(winget show Google.Antigravity --accept-source-agreements | tr -d '\r' | awk -F': ''/^Version/{print $2}')
curl -fsSL "https://raw.githubusercontent.com/microsoft/winget-pkgs/master/manifests/g/Google/Antigravity/${AWV}/Google.Antigravity.installer.yaml" \
  | grep -E 'Architecture|InstallerUrl|InstallerSha256'
sha256sum Antigravity2.0-Windows-64位-用户级-v*.exe # 与对应架构的 InstallerSha256 比对（大小写不敏感）

# 5) 命令行装（winget 走的就是上面那只官方 exe，免手工下载；实测源内版本 2.22.0）
winget install --id Google.Antigravity -e
```

> 第 4 步只在「微软 winget 仓库已经合了当期版本的清单」时可用；Google 发版到清单合并之间有时间差，取不到 404 属正常，退回 `Content-Length` + 签名。第 1 步的 `--compressed` 不能省，否则管道里是 gzip 字节流、`grep` 什么都匹配不到（实测）。

---

## 3. 旧 App 线 Antigravity IDE（Standalone）—— 只留链接，本文未下载

**这条线只在同事仍在用旧版编辑器界面时才装**；新项目一律推荐 2.0 或 CLI。企业环境**不要**装这条线（文档原话：*Antigravity IDE is not supported for enterprise customers*）。

### 3.1 下载地址（按平台，全部实测 200 · 当期 `2.5.5-4923483625488384`）

| 平台 | 包 | 大小 | 直链 |
| --- | --- | --- | --- |
| Windows x64 | `Antigravity%20IDE.exe` | 217.2 MB | <https://edgedl.me.gvt1.com/edgedl/release2/j0qc3/antigravity/stable/2.5.5-4923483625488384/windows-x64/Antigravity%20IDE.exe> |
| Windows ARM64 | `Antigravity%20IDE.exe` | 235.2 MB | <https://edgedl.me.gvt1.com/edgedl/release2/j0qc3/antigravity/stable/2.5.5-4923483625488384/windows-arm64/Antigravity%20IDE.exe> |
| macOS Apple Silicon | `Antigravity%20IDE.dmg` | 202.3 MB | <https://edgedl.me.gvt1.com/edgedl/release2/j0qc3/antigravity/stable/2.5.5-4923483625488384/darwin-arm/Antigravity%20IDE.dmg> |
| macOS Intel | `Antigravity%20IDE.dmg` | 210.5 MB | <https://edgedl.me.gvt1.com/edgedl/release2/j0qc3/antigravity/stable/2.5.5-4923483625488384/darwin-x64/Antigravity%20IDE.dmg> |
| Linux x64 | `Antigravity%20IDE.tar.gz` | 229.7 MB | <https://edgedl.me.gvt1.com/edgedl/release2/j0qc3/antigravity/stable/2.5.5-4923483625488384/linux-x64/Antigravity%20IDE.tar.gz> |
| Linux ARM64 | `Antigravity%20IDE.tar.gz` | 225.9 MB | <https://edgedl.me.gvt1.com/edgedl/release2/j0qc3/antigravity/stable/2.5.5-4923483625488384/linux-arm/Antigravity%20IDE.tar.gz> |

> 三个坑：
> 1. **ARM64 的路径段是 `windows-arm64`，而 2.0 线是 `windows-arm`**（Linux 两条线都写 `linux-arm`）。混抄必 404。
> 2. 域名 `edgedl.me.gvt1.com` 是 Google 的更新分发 CDN，路径里 `release2/j0qc3/` 这段 **`j0qc3` 是会轮换的发布令牌**，不像 `antigravity-public` 桶那样可预测。这条线也**没有 latest 目录**（同实测 404 类问题），且官方 `.md` 文档页里**不写这条线的直链** —— 只有 <https://antigravity.google/download> 页面（JS 渲染，需浏览器）会给，所以本文的上表是 2026-10-10 从该页取得并逐个发包验证的，换版后请重新用浏览器打开该页取地址。
> 3. 对象头**没有任何内容哈希**：`etag: "691262e"` 是 CDN 的对象号，不是 MD5（拿来和内容比恒不相等）。校验只能核 `Content-Length` + 装完看签名（同 Google LLC）。文件名带空格，脚本里务必保留 `%20` 或整体加引号。

---

## 4. 账号、配额与合规（三家国际产品里这条最要留意）

### 4.1 地区与账号（文档给出）

- **只发国际版**，无国内包；官方 FAQ 原文：*currently available for personal Google Accounts in **approved geographies***。
- Workspace（企业域）账号可能进不去，FAQ 建议改用 `@gmail.com` 个人邮箱试。
- **未满 18 岁不可用**，需要走 Google 账号年龄验证。
- 判定「你在哪个地区」看的是 Google 服务条款页面上的国家；地区显示错了可以提交 country-association 申请改。
- 部分地区拿不到 Google One AI / Google AI 付费计划（FAQ 有列表）。

### 4.2 配额与模型（`/docs/plans.md` 文档给出）

| 计划 | 能拿到什么 |
| --- | --- |
| Google AI **Ultra** | 最高周限 + **第三方模型**访问权 |
| Google AI **Pro** | 较高配额，每 5 小时刷新直到触顶周限 |
| 无 Pro / Ultra | 也能用，但有基线周限（按可用容量定，防滥用） |
| 超额 | Pro / Ultra 用户可用已购买的 AI credits 打超额，受 **AI Credit Overages** 设置控制（Never / 其他两档） |

核心模型是 Gemini 系（文档点名 Gemini 3.1 Pro、3.8 Flash 及 Gemini Enterprise 提供的模型），无限 Tab 补全、定时任务与 CLI 全计划可用；模型清单见 `/docs/models`，速率限制细节见 `/docs/plans`。**配额消耗按 agent 实际工作量算**，不是按 prompt 条数 —— 简单任务给的次数多，复杂任务扣得多。

### 4.3 一条会直接影响我们工作流的限制（FAQ 原文，务必看）

> *Why can't I use third-party software (such as **Claude Code, OpenClaw, or OpenCode**) with my Antigravity login?* —— 用第三方软件/工具/服务间接访问 Antigravity **违反 ToS**，可能导致账号封停。官方给的正路：要在第三方编码 agent 里用 Gemini，走 **Gemini Enterprise** 或 **Google AI Studio API key**。
> `agy -p` 无头模式只面向「直接脚本 / CI」，把 `agy` 包进第三方 agent、或自动轮换账号，消费级计划下都不支持。

也就是说：**Antigravity 的登录态不能当免费模型池给 Claude Code / Codex 用**，`dsh-our-free-model` 那种「给别的客户端喂模型」的做法在这里是违规项，别照搬。

---

## 5. 给同事部署时怎么选

| 场景 | 建议 |
| --- | --- |
| 只在图形界面里编排 agent（多任务、定时、浏览器操作、artifacts） | App 版 Antigravity 2.0；Windows 发 2.2 那只 **156.5 MB 包**（体积看是全量载荷，不像 Claude 那只 6.8 MB 在线安装器；本文未实测离线安装行为），macOS 按架构发对应 dmg |
| 天天开终端、要脚本化 / CI / SSH 远程 | 终端版 CLI，一条官方脚本；离线分发就取 1.3 的裸二进制（Windows 那只 184 MB，别按 mac 的 52 MB 估工作量） |
| 公司统一装、要能升级跟踪 | `winget install Google.Antigravity`（App，用户级）/ `winget install Google.AntigravityCLI`（CLI，portable）；实测源内版本与官方当期一致 |
| 电脑不给管理员权限 | 都可以：App 包实测 `asInvoker` + `Scope: user`，CLI 装进用户目录 |
| Linux | App 只给绿色 `.tar.gz`，官方文档没有 apt / rpm 源这类包管理器通道；要 glibc ≥ 2.28；Alpine/musl 场景用 CLI 的 musl 构建 |
| Intel Mac | 2.0 有 `darwin-x64` dmg（实测 200），但文档明写 x86 不支持 —— 先说清风险，能换 Apple Silicon 就换 |
| 需要接企业账号 | 用 **Antigravity 2.0 或 CLI**（Google Cloud SSO / WIF / ADC）；**旧 IDE 线不支持企业** |
| 要在别的编码 agent 里复用这个登录 | **不行**，见 4.3；改走 Gemini Enterprise 或 AI Studio API key |
| 只要 IDE 里补全 + 编辑器内 agent，且个人账号 | 才考虑第 3 节旧版 Antigravity IDE；新同事默认给 2.0 |

一句话：**CLI（`agy`）与 App（Antigravity 2.0）是两套东西、各自自更新、版本号互不相干**；装 App 不会带 CLI，装 CLI 也没有图形界面。再加一条 —— 官网下载页还挂着**旧版 Antigravity IDE**（`2.5.5`），它是第三条线，别拿它的 2.x 号段去猜 App 或 CLI 的版本。

---

返回总目录：[`agent下载目录.md`](./agent下载目录.md)　安装包清单与本地完整性台账：[`安装包/README.md`](./安装包/README.md)　全部命令行汇总：[`download_myself.md`](./download_myself.md)
