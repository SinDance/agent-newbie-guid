# download_myself —— 取最新地址 & 下载命令行

按 Agent 名字分节，每节内**国内版 / 海外版**分开列。每条命令都已在 2026-10-09 实测通过，2026-10-10 补验了校验值入口。
**本文一律不写死校验码** —— `latest` 类地址换版后哈希必变。每只包「官方有没有当期校验值、怎么现取」集中在本文第 8 节。
对应直链表格、macOS 地址与选型说明见 [`agent下载目录.md`](./agent下载目录.md)；下载结果存放在 `安装包/`。
**终端型 / 国际 Agent 不在本文正文**：Claude Code 见 [`claude_code.md`](./claude_code.md)，OpenAI（Codex CLI / ChatGPT App）见 [`codex.md`](./codex.md)，Google（Antigravity CLI / 2.0 App / 旧版 IDE）见 [`antigravity.md`](./antigravity.md)，那三份里各自带取最新地址与下载命令；本文只在第 9 节留这三家**已下载的 App 包**的重下命令。

**运行环境**：Git Bash（Windows 自带 curl 即可）、macOS Terminal、Linux 均可。Windows 若用 PowerShell，见文末第 7 节。

命名规则统一为：`产品-地区-Windows-64位-安装级别-v版本号.exe`。

```bash
mkdir -p 安装包 && cd 安装包   # 先建目录，下面所有命令都在此目录内执行
```

---

## 1. Qoder（阿里巴巴）

链接是固定 `latest` 路径，**不需要解析版本号**，重新下载即最新版。SHA256 也挂在这只对象的响应头里，一并现取（见 8.2），不要写死。

### 1.1 国内版

```bash
# 取当前版本号、大小与官方 SHA256（可选，下载前后各跑一次即可）
curl -sI "https://qoder-app.oss-cn-beijing.aliyuncs.com/qoder-app/releases/latest/Qoder-CN-win-x64-user.exe" \
  | tr -d '\r' | grep -iE 'x-oss-meta-(version|sha256)|content-length'

# 下载
curl -fL --retry 3 -o "Qoder-国内版-Windows-64位-用户级-v0.4.4.exe" \
  "https://qoder-app.oss-cn-beijing.aliyuncs.com/qoder-app/releases/latest/Qoder-CN-win-x64-user.exe"
```

### 1.2 海外版

```bash
# 取当前版本号、大小与官方 SHA256（可选）
curl -sI "https://download.qoder.com/qoder-app/releases/latest/Qoder-win-x64-user.exe" \
  | tr -d '\r' | grep -iE 'x-oss-meta-(version|sha256)|content-length'

# 下载
curl -fL --retry 3 -o "Qoder-国际版-Windows-64位-用户级-v0.4.4.exe" \
  "https://download.qoder.com/qoder-app/releases/latest/Qoder-win-x64-user.exe"
```

---

## 2. Trae（字节跳动）

直链里写死了构建号，**每次先解析再生效**。默认取新加坡镜像 `trae-ai-sg`，实测比国内官方节点快 6 倍且内容一致。版本接口里没有任何哈希字段（全文无 sha/md5/hash），完整性只能取对象 URL 的 `ETag`/`Content-MD5`，命令见 8.3。

### 2.1 国内版

```bash
TRAE_CN_URL=$(curl -s "https://api.trae.com.cn/icube/api/v1/native/version/trae/cn/latest" \
  | grep -oE 'https://[^"]*win32/TRAE_CN-Setup-x64\.exe' | grep 'trae-ai-sg' | head -1)
echo "$TRAE_CN_URL"   # 形如 .../stable/2.3.90452/win32/TRAE_CN-Setup-x64.exe

TRAE_CN_VER=$(echo "$TRAE_CN_URL" | grep -oE 'stable/[0-9.]+' | cut -d/ -f2)
curl -fL --retry 3 -o "Trae-国内版-Windows-64位-v${TRAE_CN_VER}.exe" "$TRAE_CN_URL"
```

> 想换节点：把上面的 `grep 'trae-ai-sg'` 换成 `grep 'trae-com-cn'`（国内官方，慢）或 `grep 'trae-ai-us'`（美东）。

### 2.2 海外版

```bash
TRAE_URL=$(curl -s "https://api.trae.ai/icube/api/v1/native/version/trae/latest" \
  | grep -oE 'https://[^"]*win32/TRAE-Setup-x64\.exe' | grep 'trae-ai-sg' | head -1)
echo "$TRAE_URL"      # 形如 .../stable/2.3.90453/win32/TRAE-Setup-x64.exe

TRAE_VER=$(echo "$TRAE_URL" | grep -oE 'stable/[0-9.]+' | cut -d/ -f2)
curl -fL --retry 3 -o "Trae-国际版-Windows-64位-v${TRAE_VER}.exe" "$TRAE_URL"
```

> 注意两个版本号的含义差异：接口返回的 `version` 字段是产品版本（国内 3.4.1 / 海外 3.6.1），URL 里的 `2.3.9xxxx` 是构建号。上面文件名用的是构建号，若要产品版本号，直接解析 JSON 里的 `"version":"x.y.z"`。

---

## 3. 腾讯 WorkBuddy

`v2/update` 接口直接返回 JSON，含 `version` 与 `url` 两个字段，解析最简单。返回体里另有 `sha256hash`：**macOS 平台有值，Windows 平台是空串**，所以 Windows 包只能靠对象的 `x-cos-meta-md5` 核对传输（8.2 / 8.3）。

### 3.1 国内版

```bash
WB_CN=$(curl -s "https://www.workbuddy.cn/v2/update?platform=workbuddy-win32-x64-user")
WB_CN_VER=$(echo "$WB_CN" | grep -oE '"version":"[^"]+"' | cut -d'"' -f4)   # 5.7.7.40774747
WB_CN_URL=$(echo "$WB_CN" | grep -oE '"url":"[^"]+"'   | cut -d'"' -f4)
echo "$WB_CN_VER"; echo "$WB_CN_URL"

curl -fL --retry 3 -o "腾讯WorkBuddy-国内版-Windows-64位-用户级-v${WB_CN_VER}.exe" "$WB_CN_URL"
```

### 3.2 海外版

```bash
WB_INTL=$(curl -s "https://www.workbuddy.ai/v2/update?platform=workbuddy-win32-x64-user")
WB_INTL_VER=$(echo "$WB_INTL" | grep -oE '"version":"[^"]+"' | cut -d'"' -f4)   # 5.7.6.40488862
WB_INTL_URL=$(echo "$WB_INTL" | grep -oE '"url":"[^"]+"'   | cut -d'"' -f4)
echo "$WB_INTL_VER"; echo "$WB_INTL_URL"

curl -fL --retry 3 -o "腾讯WorkBuddy-国际版-Windows-64位-用户级-v${WB_INTL_VER}.exe" "$WB_INTL_URL"
```

> 可改 `platform`：`workbuddy-darwin-arm64`、`workbuddy-darwin-x64`（macOS）。
> **别用** `workbuddy-win32-x64-system`：接口会返回地址但对象实际 404。

---

## 4. DeepSeek Harness（dsh）

国内版与海外版**共用同一安装包**（`/zh/harness/` 与 `/en/harness/` 给的是同一个下载地址），且链接是固定 `latest`，无需解析。

### 4.1 通用版（国内 = 海外）

```bash
# 确认当前包大小 + 官方 MD5 + 换版时间（可选；这只 latest 没有官方 SHA256，见 8.3）
curl -sI "https://download.deepseek.com/desktop/dsh-latest-windows-x64.exe" | tr -d '\r' | grep -iE '^HTTP/|content-length|^etag|last-modified'

# 下载
curl -fL --retry 3 -o "DeepSeekHarness-通用版-Windows-64位.exe" \
  "https://download.deepseek.com/desktop/dsh-latest-windows-x64.exe"
```

> 没有 Windows ARM64 与 Linux 包，对应路径实测 404。当前包内版本 `0.2.0-rc.2`，可从 exe 属性里读。

---

## 5. MiniMax Design（海螺 AI 动画视频 / vibe Animation）

版本靠区域目录下的 `releases.win.json` 清单，注意 **JSON 是带缩进的美化格式**，正则必须先去掉空白，否则匹配不到。两区包 ID 不同（国内 `com.minimax.hub`，海外 `com.minimax.hub.global`），是两个不同二进制。清单里的 `SHA256` / `SHA1` 属于包内组件 `com.minimax.hub-<版本>-hilo-desktop-85-full.nupkg`，**不是**这只 `-x64-Setup.exe`（大小就差着 4 MB），别拿来校验，见 8.1。

### 5.1 国内版

```bash
MM_CN_BASE="https://filecdn.minimax.chat/public/minimax-hub/release/domestic/"
MM_CN_VER=$(curl -s "${MM_CN_BASE}releases.win.json" | tr -d ' \n\r' \
  | grep -oE '"Version":"[^"]+"' | head -1 | cut -d'"' -f4)          # 3.0.21
echo "$MM_CN_VER"

curl -fL --retry 3 -o "MiniMaxDesign-国内版-Windows-64位-v${MM_CN_VER}.exe" \
  "${MM_CN_BASE}MiniMax%20Design-${MM_CN_VER}-x64-Setup.exe"
```

### 5.2 海外版

```bash
MM_INTL_BASE="https://file.cdn.minimax.io/public/minimax-hub/release/overseas/"
MM_INTL_VER=$(curl -s "${MM_INTL_BASE}releases.win.json" | tr -d ' \n\r' \
  | grep -oE '"Version":"[^"]+"' | head -1 | cut -d'"' -f4)          # 3.0.21
echo "$MM_INTL_VER"

curl -fL --retry 3 -o "MiniMaxDesign-海外版-Windows-64位-v${MM_INTL_VER}.exe" \
  "${MM_INTL_BASE}MiniMax%20Design-${MM_INTL_VER}-x64-Setup.exe"
```

> 别用 `latest.yml` 取版本：那是历史 Electron 通道，里面还写着 2.0.13，它带的 `sha512` 自然也是 2.0.13 那只包的，对不上当前 exe。macOS 用 `releases.osx-arm64.json` / `releases.osx-x64.json`，文件名后缀换成 `-arm64.dmg` / `-x64.dmg`。

---

## 6. 一次把 9 个包全下齐（仅上表 5 款图形客户端；Anthropic / OpenAI / Google 三家的 App 包见第 9 节）

```bash
mkdir -p 安装包 && cd 安装包

# Qoder —— 固定链接
curl -fL --retry 3 -o "Qoder-国内版-Windows-64位-用户级-v0.4.4.exe" \
  "https://qoder-app.oss-cn-beijing.aliyuncs.com/qoder-app/releases/latest/Qoder-CN-win-x64-user.exe"
curl -fL --retry 3 -o "Qoder-国际版-Windows-64位-用户级-v0.4.4.exe" \
  "https://download.qoder.com/qoder-app/releases/latest/Qoder-win-x64-user.exe"

# Trae —— 先解析再下载
TC=$(curl -s "https://api.trae.com.cn/icube/api/v1/native/version/trae/cn/latest" | grep -oE 'https://[^"]*win32/TRAE_CN-Setup-x64\.exe' | grep trae-ai-sg | head -1)
TI=$(curl -s "https://api.trae.ai/icube/api/v1/native/version/trae/latest"        | grep -oE 'https://[^"]*win32/TRAE-Setup-x64\.exe'  | grep trae-ai-sg | head -1)
curl -fL --retry 3 -o "Trae-国内版-Windows-64位-$(echo "$TC" | grep -oE 'stable/[^/]+' | cut -d/ -f2).exe" "$TC"
curl -fL --retry 3 -o "Trae-国际版-Windows-64位-$(echo "$TI" | grep -oE 'stable/[^/]+' | cut -d/ -f2).exe" "$TI"

# WorkBuddy —— 先解析再下载
WC=$(curl -s "https://www.workbuddy.cn/v2/update?platform=workbuddy-win32-x64-user")
WI=$(curl -s "https://www.workbuddy.ai/v2/update?platform=workbuddy-win32-x64-user")
curl -fL --retry 3 -o "腾讯WorkBuddy-国内版-Windows-64位-用户级-v$(echo "$WC" | grep -oE '"version":"[^"]+"' | cut -d'"' -f4).exe" \
  "$(echo "$WC" | grep -oE '"url":"[^"]+"' | cut -d'"' -f4)"
curl -fL --retry 3 -o "腾讯WorkBuddy-国际版-Windows-64位-用户级-v$(echo "$WI" | grep -oE '"version":"[^"]+"' | cut -d'"' -f4).exe" \
  "$(echo "$WI" | grep -oE '"url":"[^"]+"' | cut -d'"' -f4)"

# DeepSeek Harness —— 固定链接，国内外同一包
curl -fL --retry 3 -o "DeepSeekHarness-通用版-Windows-64位.exe" \
  "https://download.deepseek.com/desktop/dsh-latest-windows-x64.exe"

# MiniMax Design —— 先解析版本再下载
MB_CN="https://filecdn.minimax.chat/public/minimax-hub/release/domestic/"
MB_IN="https://file.cdn.minimax.io/public/minimax-hub/release/overseas/"
VC=$(curl -s "${MB_CN}releases.win.json" | tr -d ' \n\r' | grep -oE '"Version":"[^"]+"' | head -1 | cut -d'"' -f4)
VI=$(curl -s "${MB_IN}releases.win.json" | tr -d ' \n\r' | grep -oE '"Version":"[^"]+"' | head -1 | cut -d'"' -f4)
curl -fL --retry 3 -o "MiniMaxDesign-国内版-Windows-64位-v${VC}.exe" "${MB_CN}MiniMax%20Design-${VC}-x64-Setup.exe"
curl -fL --retry 3 -o "MiniMaxDesign-海外版-Windows-64位-v${VI}.exe" "${MB_IN}MiniMax%20Design-${VI}-x64-Setup.exe"
```

---

## 7. Windows PowerShell 版本（不用 Git Bash 时）

```powershell
# Qoder 国内版 / 海外版（固定链接，直接下）
curl.exe -fL -o "Qoder-国内版-Windows-64位-用户级-v0.4.4.exe" "https://qoder-app.oss-cn-beijing.aliyuncs.com/qoder-app/releases/latest/Qoder-CN-win-x64-user.exe"
curl.exe -fL -o "Qoder-国际版-Windows-64位-用户级-v0.4.4.exe" "https://download.qoder.com/qoder-app/releases/latest/Qoder-win-x64-user.exe"

# DeepSeek Harness（国内外同一包）
curl.exe -fL -o "DeepSeekHarness-通用版-Windows-64位.exe" "https://download.deepseek.com/desktop/dsh-latest-windows-x64.exe"

# Qoder 版本号（HEAD 请求读元数据）
$q = Invoke-WebRequest -Method Head -Uri "https://qoder-app.oss-cn-beijing.aliyuncs.com/qoder-app/releases/latest/Qoder-CN-win-x64-user.exe" -UseBasicParsing
$q.Headers['x-oss-meta-version']    # -> 0.4.4

# WorkBuddy 国内版：解析 + 下载（海外版把域名换成 www.workbuddy.ai）
$cn = Invoke-RestMethod "https://www.workbuddy.cn/v2/update?platform=workbuddy-win32-x64-user"
Invoke-WebRequest $cn.url -OutFile ("腾讯WorkBuddy-国内版-Windows-64位-用户级-v" + $cn.version + ".exe")

# Trae 国内版：解析 + 下载（海外版换 api.trae.ai 与 TRAE-Setup-x64.exe）
$cj  = Invoke-RestMethod "https://api.trae.com.cn/icube/api/v1/native/version/trae/cn/latest"
$sg  = $cj.data.manifest.win32.download | Where-Object { $_.region -eq 'sg' } | Select-Object -First 1
Invoke-WebRequest $sg.x64 -OutFile "Trae-国内版-Windows-64位.exe"

# MiniMax Design 国内版：解析 + 下载（海外版换 base 为 file.cdn.minimax.io/.../overseas/）
$b = "https://filecdn.minimax.chat/public/minimax-hub/release/domestic/"
$v = (Invoke-RestMethod ($b + "releases.win.json")).Assets.Version          # 注意必须用 + 拼接，$b"..." 写法会报错
Invoke-WebRequest ($b + "MiniMax%20Design-" + $v + "-x64-Setup.exe") -OutFile ("MiniMaxDesign-国内版-Windows-64位-v" + $v + ".exe")

# Antigravity 2.0 App（Windows x64）：解析「版本-构建号」+ 下载
# Invoke-WebRequest 自己会解压 gzip，所以 PowerShell 侧不需要 Git Bash 那个 --compressed 开关
$gs  = (Invoke-WebRequest "https://antigravity.google/docs/getting-started.md" -UseBasicParsing).Content
$agy = ([regex]'antigravity-hub/([^/"]+)').Match($gs).Groups[1].Value                 # 2.22.0-5446056071266304
$u   = "https://storage.googleapis.com/antigravity-public/antigravity-hub/$agy/windows-x64/Antigravity-x64.exe"
(Invoke-WebRequest -Method Head $u -UseBasicParsing).Headers['Content-Length']        # 先核大小
curl.exe -fL -o ("Antigravity2.0-Windows-64位-用户级-v" + ($agy -split '-')[0] + ".exe") $u
# macOS 把 windows-x64/Antigravity-x64.exe 换成 darwin-arm/Antigravity.dmg（Intel 用 darwin-x64）；Linux 用 linux-x64/Antigravity.tar.gz
# 终端版 agy 安装：irm https://antigravity.google/cli/install.ps1 | iex
```

---

## 8. 下载后校验（2026-10-10 重写：本文不写死任何校验码）

旧版本这里给的是「官方哈希 + 本地哈希表」，做法不对：`latest` 类地址（Qoder、DeepSeek Harness、ChatGPT 的 `ChatGPT-x64.msix`、MiniMax 与 WorkBuddy 的版本化直链在发新版后也一样）指向的对象会随发版更换，任何写死的校验码都会在下次下载时报错，而报错看起来像是包坏了，其实是文档过期了。现在按**能不能稳定地拿到当期校验值**分三档处理，命令都实测过。

### 8.1 官方校验值可得性（2026-10-10 逐个 HTTP 实测）

| 产品 / 包 | 官方给什么 | 稳定获取入口 | 取法 |
| --- | --- | --- | --- |
| Qoder 国内版 / 海外版 | ✅ SHA256 | 就是那只 `latest` 对象的响应头 `x-oss-meta-sha256`，与对象同步更新 | 8.2 |
| WorkBuddy macOS | ✅ SHA256 | `/v2/update` 返回体的 `sha256hash` 字段 | 8.2 |
| Claude Code CLI（8 平台） | ✅ SHA256 | `latest`/`stable` 指针 → 该版本 `manifest.json` 的 `checksum`，清单另有 GPG 签名 | 8.2 |
| Claude 桌面 App 的 `.deb` | ✅ SHA256 | apt 仓库 `Packages` 索引里当期版本的 `SHA256:` 行 | 8.2 |
| Codex CLI（GitHub 资产） | ✅ SHA256 | GitHub Releases API 的 `assets[].digest`（`sha256:` 前缀，176 个资产全带） | 8.2 |
| **Antigravity CLI（9 平台）** | ✅ **SHA512** | `.../manifests/<平台键>.json` 的 `sha512` 字段，地址长期稳定；官方安装脚本本身就按它做安装前校验 | 8.2 |
| Trae 国内版 / 海外版 | ⚠️ 只有 MD5 | 对象 URL 的 `ETag` / `Content-MD5`（版本接口完全不含哈希字段） | 8.3 |
| WorkBuddy Windows | ⚠️ 只有 MD5 | 对象的 `x-cos-meta-md5`（接口的 `sha256hash` 对 Windows 返回空串；`ETag` 带 `-416` 分段后缀，不能用） | 8.3 |
| DeepSeek Harness | ⚠️ 只有 MD5 | `latest` 对象的 `ETag`（单段上传，等于内容 MD5）；`.sha256`/`latest.yml`/`SHA256SUMS` 实测全 404 | 8.3 |
| Claude 桌面 App Windows / macOS | ⚠️ 只有 MD5 | 版本化直链的 `x-goog-hash: md5=` / `ETag` | 8.3 |
| ChatGPT App（MSIX / dmg / deb / rpm） | ❌ 什么都不给 | Azure CDN 的 `etag: "0x8D…"` 是 blob 版本令牌，不是内容哈希 | 8.4 |
| **Antigravity 2.0 App（win / dmg / tar.gz）** | ❌ 什么都不给 | 侧车 `.sha256`/`.sha512`、`manifest.json`、`latest.yml`、`version.json`、`antigravity-hub/latest/…` 实测**全 404**；GCS 头对这几只复合对象只给 `crc32c`（连 `md5` 都没有），`ETag` 带前导横线即复合对象标志 | 8.4 |
| **旧版 Antigravity IDE（6 平台）** | ❌ 什么都不给 | `edgedl.me.gvt1.com` 的 `etag: "691262e"` 是 CDN 对象号不是哈希；路径里 `release2/j0qc3/` 是会轮换的发布令牌 | 8.4 |
| MiniMax Design `-x64-Setup.exe` / `.dmg` | ❌ 不给（清单哈希属于别的文件） | `releases.win.json` 里那条 `SHA256` 对应的是 `com.minimax.hub-3.0.21-hilo-desktop-85-full.nupkg`（430,468,428 B），不是官网那只 exe（434,495,664 B） | 8.4 |

结论：能拿到当期官方哈希的六类（含 Antigravity CLI 的 SHA512），命令化取用；只有 MD5 的四类，只当传输完整性检查；完全拿不到的四类 —— ChatGPT App、MiniMax Design、Antigravity 2.0 App、旧版 Antigravity IDE —— 删掉校验码，改核签名 + 大小 + 包内版本号。Antigravity 的 Windows 两只包另有一个**非官方**交叉源可取 SHA256：微软 winget 仓库清单的 `InstallerSha256`（取法见 8.4 末）。

### 8.2 有官方校验值（SHA256 / SHA512）—— 现取现用

```bash
# Qoder 国内版（海外版换成 download.qoder.com 那条 URL，头同名）
curl -sI "https://qoder-app.oss-cn-beijing.aliyuncs.com/qoder-app/releases/latest/Qoder-CN-win-x64-user.exe" \
  | tr -d '\r' | grep -iE 'x-oss-meta-(sha256|version)|content-length'

# WorkBuddy macOS —— Windows 的 sha256hash 是空串，别按空值判成败
curl -s "https://www.workbuddy.cn/v2/update?platform=workbuddy-darwin-arm64" \
  | tr -d ' \n' | grep -oE '"(version|sha256hash)":"[^"]*"'

# Claude Code CLI —— 指针给版本，清单给哈希；平台键换 darwin-arm64 / win32-arm64 / linux-x64-musl 等
CC_VER=$(curl -fsSL https://downloads.claude.ai/claude-code-releases/latest)     # 或 .../stable
curl -fsSL "https://downloads.claude.ai/claude-code-releases/${CC_VER}/manifest.json" \
  | tr -d ' \n' | grep -oE '"win32-x64":\{[^}]*\}'

# Claude 桌面 App .deb —— 从 apt 索引取当期那条（claude_code.md 2.3 同款）
ARCH=$(dpkg --print-architecture)
PKG="https://downloads.claude.ai/claude-desktop/apt/stable/dists/stable/main/binary-${ARCH}/Packages"
DEB=$(curl -s "$PKG" | tr -d '\r' | grep '^Filename: pool/main/c/claude-desktop/claude-desktop_' | sort -V | tail -1 | cut -d' ' -f2)
curl -s "$PKG" | tr -d '\r' | grep -A4 -F "$DEB" | grep -E '^(Size|SHA256):'

# Codex CLI —— GitHub API 的 digest 字段就是 sha256（先落盘再解析，避免 grep 提前退出把 curl 打断）
curl -fsSL https://api.github.com/repos/openai/codex/releases/latest -o /tmp/codex-last.json
TAG=$(grep -oE '"tag_name": "[^"]+"' /tmp/codex-last.json | head -1 | cut -d'"' -f4)
curl -fsSL "https://api.github.com/repos/openai/codex/releases/tags/${TAG}" -o /tmp/codex.json
awk '/"name": "codex-x86_64-pc-windows-msvc.exe"/{f=1} f&&/"digest"/{print; exit}' /tmp/codex.json   # sha256:…

# Antigravity CLI —— 官方给的是 SHA512（不是 256），一条 manifest 同时给版本号、真实 url 与哈希
# 平台键：windows_amd64 / windows_arm64 / darwin_arm64 / darwin_amd64 / linux_amd64 / linux_arm64
#        / linux_amd64_musl / linux_arm64_musl / android_arm64（android_amd64 实测 404）
AGYB="https://antigravity-cli-auto-updater-974169037036.us-central1.run.app"
curl -fsSL "$AGYB/manifests/windows_amd64.json"          # 302 B 的小 JSON：version + url + sha512
sha512sum cli_windows_x64.exe | cut -d' ' -f1            # macOS 用 shasum -a 512 | cut -d' ' -f1
# 注：官方 install.sh / install.ps1 装前就自动做这一步，不匹配直接中止安装，正常装完不必手工核

# 下完自己算，两边一比对
sha256sum *.exe *.msix          # Git Bash / macOS / Linux
```

PowerShell 版（配合第 7 节使用）：

```powershell
# Qoder 官方 SHA256
(Invoke-WebRequest -Method Head "https://download.qoder.com/qoder-app/releases/latest/Qoder-win-x64-user.exe" -UseBasicParsing).Headers['x-oss-meta-sha256']
(Get-FileHash .\Qoder-国际版-Windows-64位-用户级-*.exe -Algorithm SHA256).Hash.ToLower()   # 两行输出应相同
```

### 8.3 只有 MD5 —— 只能证明没下坏，不能证明来源

```bash
# Trae：解析出的 URL 直接 HEAD（ETag 即内容 MD5；sg 镜像另带 base64 的 Content-MD5）
#   国内版用第 2.1 节的 $TRAE_CN_URL，海外版用 2.2 节的 $TRAE_URL
curl -sI "$TRAE_CN_URL" | tr -d '\r' | grep -iE '^(etag|content-md5|content-length)'
md5sum Trae-国内版-Windows-64位-*.exe

# WorkBuddy Windows：用自定义元数据头，别用 ETag（分段上传，形如 "...-416"，不等于内容 MD5）
curl -sI "$WB_CN_URL" | tr -d '\r' | grep -iE '^(x-cos-meta-md5|etag|content-length)'
md5sum 腾讯WorkBuddy-国内版-Windows-64位-用户级-*.exe

# DeepSeek Harness：latest 对象的 ETag 就是内容 MD5，重下后头会变，正好当「是否换版」信号
curl -sI "https://download.deepseek.com/desktop/dsh-latest-windows-x64.exe" | tr -d '\r' | grep -iE '^(etag|last-modified|content-length)'
md5sum DeepSeekHarness-通用版-Windows-64位-*.exe

# Claude 桌面 App 安装器：GCS 同时给 x-goog-hash: md5=... 和同值的 ETag
curl -sI "https://downloads.claude.ai/releases/win32/x64/2.31226.1/ClaudeSetup-8576520051724193e9f74fc538f9ce8125e9ddd2.exe" \
  | tr -d '\r' | grep -iE '^(etag|x-goog-hash|content-length)'
md5sum ClaudeDesktop-Windows-64位-用户级-*.exe
```

2026-10-10 实测：上面四类响应头的值，与 `安装包/` 内对应文件的 `md5sum` 逐一相同（Trae 国内 ETag `b4dbf8bc…`、Trae 海外 ETag `0802c806…`、WorkBuddy 国内 `x-cos-meta-md5` `3fbe736b…`、DeepSeek ETag `7754925f…`、Claude App ETag `f64529e3…`）。来源是否可信仍然看代码签名，MD5 挡不住有人替换文件后重算哈希。

### 8.4 官方完全不公布 —— 签名 + 大小 + 包内版本号

ChatGPT App（MSIX / dmg / deb / rpm）、MiniMax Design、**Antigravity 2.0 App 与旧版 Antigravity IDE** 这三类，**文档里不再保留任何校验码**。下载后用这三项核对，任何一项对不上就别装：

```bash
# 1) 代码签名主体与状态（Windows 唯一权威判据）
powershell -Command "Get-AuthenticodeSignature .\*.exe, .\*.msix | Format-Table Status,SignerCertificate -AutoSize"

# 2) 大小与服务端 Content-Length 逐字节比对
curl -sI "${MM_CN_BASE}MiniMax%20Design-${MM_CN_VER}-x64-Setup.exe" | tr -d '\r' | grep -i content-length
stat -c '%n %s' MiniMaxDesign-国内版-Windows-64位-*.exe        # macOS 用 stat -f '%N %z'
# Antigravity 顺带看换版时间：GCS 给 Last-Modified 与 x-goog-stored-content-length
curl -sI "https://storage.googleapis.com/antigravity-public/antigravity-hub/${AGY_APP}/windows-x64/Antigravity-x64.exe" \
  | tr -d '\r' | grep -iE '^(content-length|x-goog-stored-content-length|last-modified)'

# 3) 版本号从包本身读，别信文件名
unzip -p ChatGPT-Windows-64位-Appx-*.msix AppxManifest.xml | tr '>' '>\n' | grep -oE 'Version="[0-9.]+"' | head -1
powershell -Command "(Get-Item .\Antigravity2.0-Windows-64位-用户级-*.exe).VersionInfo.ProductVersion"   # 应为 2.22.0 一类的当期值
```

`Status` 应全部为 `Valid`；MiniMax Design 国内版签名主体是上海稀宇科技有限公司，海外版 NANONOBLE PTE. LTD.；ChatGPT App 由 Microsoft Marketplace CA 签（商店包）；**Antigravity 两只 Windows 包（App 与旧 IDE）签名主体都是 `CN=Google LLC`（DigiCert Trusted G4 签发）**。MiniMax 想多一道保障，可以比对 `releases.win.json` 里 `.nupkg` 的 `SHA256`，但那校验的是包内组件，不是这只 exe，别写进 exe 的校验表。

Antigravity 的 **Windows 两只 App 包**还能从一个**非官方**来源拿到 SHA256 —— 微软 winget 仓库的当期清单（2026-10-10 实测：其值与 `安装包/` 里那只 Antigravity 包 `sha256sum` 逐字符一致，说明 winget 与官网 GCS 是同一对象；Google 发版到清单合并有时间差，取不到就是 404，退回上面三件套）：

```bash
# 版本号用 winget 报的当期值（App 与 CLI 都有官方条目：Google.Antigravity / Google.AntigravityCLI）
AWV=$(winget show Google.Antigravity --accept-source-agreements | tr -d '\r' | awk -F': ' '/^Version/{print $2}')
curl -fsSL "https://raw.githubusercontent.com/microsoft/winget-pkgs/master/manifests/g/Google/Antigravity/${AWV}/Google.Antigravity.installer.yaml" \
  | grep -E 'Architecture|InstallerUrl|InstallerSha256'
sha256sum Antigravity2.0-Windows-64位-用户级-*.exe        # 与对应架构的 InstallerSha256 比对（大小写不敏感）
```

### 8.5 本地落盘快照（取代旧的写死哈希表）

`安装包/` 里那 12 只包是 2026-10-09/10 抓的快照，官方 `latest` 之后换版会让旧哈希全部失效，所以旧文档第二节里的哈希表已删除，改成本地生成、本地比对的台账：

```bash
cd 安装包
sha256sum *.exe *.msix > SHA256SUMS.local    # 记录本目录这几只文件的字节指纹
sha256sum -c SHA256SUMS.local                # 分发前后各跑一次，全 OK 才算没坏
```

`SHA256SUMS.local` 只描述本目录当前的这 12 只包，**不能**用来校验从官方重下的新版包；重下后要么用 8.2 的官方值，要么重新生成一份。不想用 Git Bash 时：`powershell -Command "Get-FileHash -Algorithm SHA256 .\*.exe,.\*.msix | Format-List Path,Hash"`。

> 大文件下载建议加 `-C -` 断点续传，网络抖动时不会从头再来：
> `curl -fL -C - --retry 5 --retry-delay 4 -o 文件名 "URL"`

---

## 9. 终端型 / 国际 Agent（Claude Code / OpenAI / Google）—— 本文只管三个 App 包

按你的要求，这三家**只下载 App 版的 Windows x64**，终端版只记命令。平台表、macOS/Linux 地址与来源标注全在各自的文档里：

- Anthropic → [`claude_code.md`](./claude_code.md)
- OpenAI → [`codex.md`](./codex.md)
- Google → [`antigravity.md`](./antigravity.md)

```bash
# Claude 桌面 App（Windows x64，在线安装器 6.8 MB）
# 直链里的版本号会随发版变化；先按 claude_code.md 2.4 的命令解析，解析出新地址后要连校验值一起重取（官方只有 MD5 响应头，见 8.3）
curl -fL --retry 3 -o "ClaudeDesktop-Windows-64位-用户级-v2.31226.1.exe" \
  "https://downloads.claude.ai/releases/win32/x64/2.31226.1/ClaudeSetup-8576520051724193e9f74fc538f9ce8125e9ddd2.exe"

# ChatGPT 桌面 App（Windows x64，离线 MSIX 900.9 MB）—— 固定 latest 地址，官方不公布任何校验值，下完核签名与大小（8.4）
curl -fL --retry 3 -C - -o "ChatGPT-Windows-64位-Appx-v26.1007.2314.0.msix" \
  "https://persistent.oaistatic.com/codex-app-prod/ChatGPT-x64.msix"

# Antigravity 2.0 App（Windows x64，NSIS 用户级包 156.5 MB）—— 地址写死版本号，必须先解析
# 解析当期「版本-构建号」：getting-started.md 是唯一稳定纯文本源，务必带 --compressed
AGY_APP=$(curl -s --compressed https://antigravity.google/docs/getting-started.md \
  | grep -oE 'antigravity-hub/[^/"]+' | head -1 | cut -d/ -f2)          # 当前 2.22.0-5446056071266304
curl -fL --retry 3 -C - -o "Antigravity2.0-Windows-64位-用户级-v${AGY_APP%%-*}.exe" \
  "https://storage.googleapis.com/antigravity-public/antigravity-hub/${AGY_APP}/windows-x64/Antigravity-x64.exe"
# 平台目录名换法：macOS Apple Silicon 用 darwin-arm/Antigravity.dmg、Intel 用 darwin-x64/Antigravity.dmg；
# Linux 用 linux-x64/Antigravity.tar.gz、linux-arm/Antigravity.tar.gz；Windows ARM64 是 windows-arm/Antigravity-arm64.exe（不是 windows-arm64）

# 终端版：只跑官方安装脚本，不落地安装包（命令均实测可达 200）
#   Claude Code CLI   macOS/Linux/WSL: curl -fsSL https://claude.ai/install.sh | bash
#                     Windows PowerShell: irm https://claude.ai/install.ps1 | iex
#   Codex CLI         macOS/Linux: curl -fsSL https://chatgpt.com/codex/install.sh | sh
#                     Windows: powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"
#   Antigravity CLI   macOS/Linux/Googlebook: curl -fsSL https://antigravity.google/cli/install.sh | bash
#                     Windows PowerShell: irm https://antigravity.google/cli/install.ps1 | iex
#                     Windows CMD: curl -fsSL https://antigravity.google/cli/install.cmd -o install.cmd && install.cmd && del install.cmd
#                     装的是 agy 二进制；脚本自己按 manifest 的 sha512 校验，不匹配即中止（见 8.2）
```

> `ChatGPT-x64.msix` 的地址是固定的不带版本号，重新下载即当前版；正因为地址不带版本，**同一 URL 的字节会随发版更换**，任何写死的哈希都只在当天有效。版本要从包里读（`unzip -p ChatGPT-x64.msix AppxManifest.xml` 里的 `Version`），别凭文件名猜。
> Claude 那条 `claude.ai/api/desktop/...` redirect 端点用 curl 会 403，得带浏览器 UA 和 cookie，或直接用上表 `downloads.claude.ai` 的版本化直链。
> Antigravity 反过来：它**没有 redirect 端点也没有 `latest` 目录**（`antigravity-hub/latest/...` 实测 404），只有写死「版本-构建号」的 GCS 直链，所以每次都要先跑那行 `--compressed` 解析；`--compressed` 不能省，否则 curl 拿回 gzip 字节流，`grep` 一个都匹配不到。这条线官方完全没有哈希，校验退回签名（`CN=Google LLC`）+ 大小 + 包内 `ProductVersion`，见 8.4。
