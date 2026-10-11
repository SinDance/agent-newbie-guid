# Agent 客户端下载目录（国内版 / 海外版）

> 整理日期：2026-10-09，2026-10-10 增补终端型 Agent，同日再增 Google Antigravity　平台：Windows 10/11 x64（另附 macOS 地址）
> 本文所有直链均用 HTTP 实测校验（状态码 200），大小取服务端返回的 `Content-Length`。
> **本文不写死任何校验码（2026-10-10 修订）。** `latest` 类地址的内容随发版变化，写死的哈希在下次下载必然校验失败，留着只会误导。改成三档处理，每档的具体命令见 [`download_myself.md` 第 8 节](./download_myself.md)：
>
> - **官方有实时校验值** → 记下取法，现取现用：Qoder（OSS 响应头 `x-oss-meta-sha256`）、WorkBuddy macOS（更新接口 `sha256hash`）、Claude Code CLI（版本指针 + `manifest.json`）、Claude App 的 `.deb`（apt `Packages` 索引）、Codex CLI（GitHub API `assets[].digest`）、**Antigravity CLI**（`manifests/<平台>.json` 里的 `sha512`，官方安装脚本自己就按它校验）。
> - **官方只给 MD5** → 用对象 URL 的响应头现取，别抄数值：Trae（`ETag` / `Content-MD5`）、WorkBuddy Windows（`x-cos-meta-md5`）、DeepSeek Harness（`ETag`）、Claude App 安装器（`x-goog-hash: md5`）。MD5 只能证明传输没损坏，来源可信性仍以代码签名为准。
> - **官方完全不公布** → 删掉校验码，改用「代码签名 Valid + `Content-Length` + 包内版本号」三项核对：ChatGPT App（MSIX / dmg / deb / rpm）、MiniMax Design（清单里的 SHA256 属于内部 `.nupkg`，不是官网那只 `-x64-Setup.exe`）、**Antigravity 2.0 App 与旧版 Antigravity IDE**（GCS 侧车 `.sha256`/`.sha512`、`manifest.json`、`latest.yml` 实测全 404，对象头只有 `crc32c`；Windows 两只包可用微软 winget 仓库清单的 `InstallerSha256` 交叉核，但那是第三方提交记录，不是 Google 官方值）。
>
> 安装包存放：`入门agent部署/安装包/`（清单见其 `README.md`）。**安装原则**：只有在本地目录下确实存在对应安装包的情况下，才使用本地安装程序；否则提示用户下载整合安装包，或者从网络自行下载。
> **取最新地址 + 下载的全部命令行，单独放在 [`download_myself.md`](./download_myself.md)**，按 Agent 名归类、国内海外分列。
> **终端型 / 国际 Agent 另立三份**，与本文那 5 款图形客户端分开放，各自内含「终端版 / App 版」两节并按平台展开：
> [**`claude_code.md`**](./claude_code.md)（Anthropic：Claude Code CLI + Claude 桌面 App）、
> [**`codex.md`**](./codex.md)（OpenAI：Codex CLI + ChatGPT 桌面 App）、
> [**`antigravity.md`**](./antigravity.md)（Google：Antigravity CLI `agy` + Antigravity 2.0 桌面 App + 旧版 Antigravity IDE）。

---

## 速览

| 产品 | 出品方 | 国内版入口 | 海外版入口 | 国内版当前版本 | 海外版当前版本 | 安装包是否已下载 |
| --- | --- | --- | --- | --- | --- | --- |
| **Qoder** | 阿里巴巴 | <https://qoder.com/zh> | <https://qoder.com> | 0.4.4 | 0.4.4 | ✅ 两版都在 |
| **Trae** | 字节跳动 | <https://www.trae.com.cn> | <https://www.trae.ai> | 3.4.1 | 3.6.1 | ✅ 两版都在 |
| **WorkBuddy** | 腾讯云 | <https://www.workbuddy.cn> | <https://www.workbuddy.ai> | 5.7.7 | 5.7.6 | ✅ 两版都在 |
| **DeepSeek Harness（dsh）** | 深度求索 | <https://www.deepseek.com/zh/harness/> | <https://www.deepseek.com/en/harness/> | 0.2.0-rc.2（两版同一个包） | 0.2.0-rc.2（两版同一个包） | ✅ 已下载 |
| **MiniMax Design** | MiniMax（海螺） | <https://design.minimaxi.com/> | <https://design.minimax.io/> | 3.0.21 | 3.0.21 | ✅ 两版都在 |

> [!IMPORTANT]
> **本地安装与网络下载原则**：
> - 上表「安装包是否已下载」基于完整离线资源库状态。
> - **只有在本地 `入门agent部署/安装包/` 目录下实际存在安装包时，才使用本地安装程序。**
> - 若本地未检测到安装包，必须提示用户**下载整合安装包**，或者按本文提供的直链 / 参照 [`download_myself.md`](./download_myself.md) **从网络自行下载**。

链接稳定性分三种，取最新地址的方式见 `download_myself.md`：

- **固定 `latest` 链接**（Qoder、DeepSeek Harness）：URL 本身长期有效，重新下载即拿到最新版。
- **版本化直链**（Trae、WorkBuddy）：URL 里写死版本号，发新版后旧链接失效，需先调各家的版本接口解析。
- **清单文件驱动**（MiniMax Design）：先读 `releases.win.json` 拿版本号，再拼出安装包地址。

上表 5 款都是**只有图形客户端**的国内/海外双包产品。**终端型 / 国际 Agent（Claude Code、OpenAI Codex / ChatGPT App、Google Antigravity）不在上表**：它们没有国内版与海外版之分，且同一产品分成「终端 CLI」和「桌面 App」两条独立版本线（Google 还多一条仍在发版的旧 IDE 线），地址与命令见第 6 节链接的三份文档。

---

## 1. Qoder（阿里巴巴 AI IDE）

版本 0.4.4，国内版与国际版同版本发布。国际版走 `download.qoder.com`，国内版走阿里云 OSS 北京桶；国内包的文件名多一个 `Qoder-CN-` 前缀，两者签名主体也不同（国内为阿里云，国际为新加坡 BRIGHT ZENITH）。

### 1.1 国内版（qoder.com/zh · docs.qoder.cn）

| 平台 | 大小 | 下载直链 |
| --- | --- | --- |
| Windows 10/11 x64（用户级，推荐） | 208.3 MB | <https://qoder-app.oss-cn-beijing.aliyuncs.com/qoder-app/releases/latest/Qoder-CN-win-x64-user.exe> |
| Windows x64（系统级，全机安装） | 208.3 MB | <https://qoder-app.oss-cn-beijing.aliyuncs.com/qoder-app/releases/latest/Qoder-CN-win-x64-system.exe> |
| macOS Apple Silicon | 240.1 MB | <https://qoder-app.oss-cn-beijing.aliyuncs.com/qoder-app/releases/latest/Qoder-CN-Installer-mac-arm64.zip> |
| macOS Intel | 266.2 MB | <https://qoder-app.oss-cn-beijing.aliyuncs.com/qoder-app/releases/latest/Qoder-CN-Installer-mac-x64.zip> |

校验值：官方就在同一个 `latest` 对象的响应头里发布 SHA256（`x-oss-meta-sha256`，随版本自动更新，所以不要抄数值）。**上表每条链接都带这个头**（system 包、两个 macOS zip 实测都有，换 URL 即可）。HEAD 一条命令现取，和 `sha256sum` 的结果直接比对：

```bash
curl -sI "https://qoder-app.oss-cn-beijing.aliyuncs.com/qoder-app/releases/latest/Qoder-CN-win-x64-user.exe" \
  | tr -d '\r' | grep -iE 'x-oss-meta-(sha256|version)|content-length'
```

2026-10-10 实测：该头返回的是当前 latest 对象（仍是 0.4.4）的哈希，与 `安装包/` 里那只 0.4.4 包 `sha256sum` 逐字符相同。

### 1.2 海外版（qoder.com）

| 平台 | 大小 | 下载直链 |
| --- | --- | --- |
| Windows x64（用户级，推荐） | 208.1 MB | <https://download.qoder.com/qoder-app/releases/latest/Qoder-win-x64-user.exe> |
| Windows x64（系统级） | 208.0 MB | <https://download.qoder.com/qoder-app/releases/latest/Qoder-win-x64-system.exe> |
| macOS Apple Silicon | 241.1 MB | <https://download.qoder.com/qoder-app/releases/latest/Qoder-Installer-mac-arm64.zip> |
| macOS Intel | 265.4 MB | <https://download.qoder.com/qoder-app/releases/latest/Qoder-Installer-mac-x64.zip> |
| Linux amd64 (.deb) | 208.4 MB | <https://download.qoder.com/qoder-app/releases/latest/Qoder-linux-amd64.deb> |

校验值：国际版 CDN 同样透出 `x-oss-meta-sha256`（实测 200，取法与国内版一致，换 URL 即可）：

```bash
curl -sI "https://download.qoder.com/qoder-app/releases/latest/Qoder-win-x64-user.exe" \
  | tr -d '\r' | grep -iE 'x-oss-meta-(sha256|version)|content-length'
```

> 注意：`qoder.com/download` 页面上另有 `QoderWork-*`（Qoder Work，另一条产品线）与 `Qoder-IDE-darwin-*.dmg`，和上表的 Qoder 桌面客户端不是同一个包。

---

## 2. Trae（字节跳动 AI IDE）

国内版与海外版是**两个独立产品**：国内版包名 `TRAE_CN`（v3.4.1，登录用手机号/抖音，模型为豆包、DeepSeek、GLM 等国内模型）；海外版包名 `TRAE`（v3.6.1，登录用 Google/GitHub，模型为 GPT、Claude、Gemini 等）。

### 2.1 国内版（trae.com.cn · v3.4.1 · 构建 2.3.90452）

| 平台 | 大小 | 下载直链 |
| --- | --- | --- |
| Windows x64 | 454.3 MB | <https://lf-cdn.trae.com.cn/obj/trae-com-cn/pkg/app/releases/stable/2.3.90452/win32/TRAE_CN-Setup-x64.exe> |
| Windows x64（新加坡镜像，实测快 6 倍） | 454.3 MB | <https://lf-cdn.trae.ai/obj/trae-ai-sg/pkg/app/releases/stable/2.3.90452/win32/TRAE_CN-Setup-x64.exe> |
| macOS Apple Silicon | 455.7 MB | <https://lf-cdn.trae.com.cn/obj/trae-com-cn/pkg/app/releases/stable/2.3.90452/darwin/TRAE_CN-darwin-arm64.dmg> |
| macOS Intel | — | <https://lf-cdn.trae.com.cn/obj/trae-com-cn/pkg/app/releases/stable/2.3.90452/darwin/TRAE_CN-darwin-x64.dmg> |

签名主体：北京引力弹弓科技有限公司（字节跳动旗下）。

### 2.2 海外版（trae.ai · v3.6.1 · 构建 2.3.90453）

| 平台 | 大小 | 下载直链 |
| --- | --- | --- |
| Windows x64（新加坡节点） | 418.1 MB | <https://lf-cdn.trae.ai/obj/trae-ai-sg/pkg/app/releases/stable/2.3.90453/win32/TRAE-Setup-x64.exe> |
| Windows x64（美东节点） | 418.1 MB | <https://lf-cdn.trae.ai/obj/trae-ai-us/pkg/app/releases/stable/2.3.90453/win32/TRAE-Setup-x64.exe> |
| Windows x64（腾讯云美西节点） | 418.1 MB | <https://lf-static.traecdn.us/obj/trae-ai-tx/pkg/app/releases/stable/2.3.90453/win32/TRAE-Setup-x64.exe> |
| macOS Apple Silicon | 429.7 MB | <https://lf-cdn.trae.ai/obj/trae-ai-sg/pkg/app/releases/stable/2.3.90453/darwin/TRAE-darwin-arm64.dmg> |

签名主体：SPRING (SG) PTE. LTD（新加坡）。

> 实测提示：`TRAE_CN-Setup-x64.exe` 从 `lf-cdn.trae.com.cn` 拉取会被限速（本机实测 0.9 MB/s，长连接掉到约 12 KB/s）；同一文件走 `lf-cdn.trae.ai/obj/trae-ai-sg` 镜像约 5.5 MB/s，两节点对象的 MD5 与代码签名一致。批量部署建议直接用 sg 镜像。

校验值：`api.trae.com.cn` / `api.trae.ai` 的版本接口**不返回任何哈希字段**（全文无 sha/md5/hash/digest，实测），官方也没有 `.sha256` 侧车文件，所以这里没有可用的 SHA256。对象 URL 的响应头带 MD5，可用来确认下载没坏（TOS 单段上传时 `ETag` 即内容 MD5，跨区镜像另给 `Content-MD5`）：

```bash
curl -sI "$TRAE_URL" | tr -d '\r' | grep -iE '^(etag|content-md5|content-length)'
md5sum Trae-国内版-Windows-64位-*.exe     # 与 ETag 十六进制值比对；Content-MD5 是 base64，需先转换
```

2026-10-10 实测：国内包的 `ETag` 与海外包的 `ETag`/`Content-MD5` 解码后，分别与 `安装包/` 里对应文件的 `md5sum` 完全相同。来源可信性看签名主体（北京引力弹弓 / SPRING (SG) PTE. LTD）。

---

## 3. 腾讯 WorkBuddy（AI 办公工作台）

国内版 v5.7.7，海外版 v5.7.6（海外更新慢一拍）。CDN 域名跟随同团队的 CodeBuddy。海外版产品名在包内写作 `WorkBuddy AI`，两版签名主体都是腾讯科技（深圳）。

### 3.1 国内版（workbuddy.cn · v5.7.7.40774747）

| 平台 | 大小 | 下载直链 |
| --- | --- | --- |
| Windows x64（用户级，免管理员权限） | 415.6 MB | <https://download.codebuddy.cn/workbuddy/saas/win32-x64-user/WorkBuddy-win32-x64-user-5.7.7.40774747-2e8619da.exe> |
| macOS Apple Silicon (.zip) | 407.2 MB | <https://download.codebuddy.cn/workbuddy/saas/darwin-arm64/WorkBuddy-darwin-arm64-5.7.7.40774747-2e8619da.zip> |
| macOS Intel (.zip) | — | <https://download.codebuddy.cn/workbuddy/saas/darwin-x64/WorkBuddy-darwin-x64-5.7.7.40774747-2e8619da.zip> |

### 3.2 海外版（workbuddy.ai · v5.7.6.40488862）

| 平台 | 大小 | 下载直链 |
| --- | --- | --- |
| Windows x64（用户级） | 475.5 MB | <https://codebuddy-1328495429.cos.accelerate.myqcloud.com/workbuddy/saas/win32-x64-user/WorkBuddy-win32-x64-user-5.7.6.40488862-3e43260f.exe> |
| macOS Apple Silicon (.zip) | 441.3 MB | <https://codebuddy-1328495429.cos.accelerate.myqcloud.com/workbuddy/saas/darwin-arm64/WorkBuddy-darwin-arm64-5.7.6.40488862-3e43260f.zip> |
| macOS Intel (.zip) | — | <https://codebuddy-1328495429.cos.accelerate.myqcloud.com/workbuddy/saas/darwin-x64/WorkBuddy-darwin-arm64-5.7.6.40488862-3e43260f.zip> |

> 已知坑：`/v2/update?platform=workbuddy-win32-x64-system` 接口会正常返回一个 `win32-x64-system/*.exe` 地址，但实测国内外两个存储桶该对象都是 **404**（未上传）。请用用户级包，或从官网按钮下载。

校验值：更新接口里**有 `sha256hash` 字段，但 Windows 平台返回空串**，macOS 平台才有值（实测国内 5.7.7 / 海外 5.7.6 两条 `win32-x64-user` 都是 `"sha256hash":""`；`workbuddy-darwin-arm64` 有完整 64 位哈希）。所以 Windows 包没有官方 SHA256，只能用 COS 对象的 `x-cos-meta-md5` 响应头核对传输完整性：

```bash
# macOS：官方 SHA256（随版本更新，现取）
curl -s "https://www.workbuddy.cn/v2/update?platform=workbuddy-darwin-arm64" \
  | tr -d ' \n' | grep -oE '"(version|sha256hash)":"[^"]*"'

# Windows：官方 MD5 元数据（注意该对象的 ETag 带 -416 后缀，是分段上传，不等于内容 MD5，要用下面这个头）
curl -sI "$WB_CN_URL" | tr -d '\r' | grep -iE '^(etag|x-cos-meta-md5|content-length)'
```

2026-10-10 实测：国内与海外 Windows 包的 `x-cos-meta-md5` 分别与 `安装包/` 内对应文件的 `md5sum` 相同；两版签名主体都是腾讯科技（深圳）。

---

## 4. DeepSeek Harness（dsh，带 UI 的桌面客户端）

出品方深度求索。**国内版与海外版共用同一个安装包**：官网中文版 `/zh/harness/` 与英文版 `/en/harness/` 给出的是完全相同的下载地址，差异只在登录的 DeepSeek 账号与网页端入口，不在客户端二进制。链接为固定 `latest`，重下即更新。

### 4.1 通用版（国内外同一包 · 实测包内版本 0.2.0-rc.2）

| 平台 | 大小 | 下载直链 |
| --- | --- | --- |
| Windows x64 | 275.9 MB | <https://download.deepseek.com/desktop/dsh-latest-windows-x64.exe> |
| macOS Apple Silicon | 352.6 MB | <https://download.deepseek.com/desktop/dsh-latest-macos-arm64.dmg> |
| macOS Intel | 368.5 MB | <https://download.deepseek.com/desktop/dsh-latest-macos-x64.dmg> |

签名主体：Hangzhou DeepSeek Artificial Intelligence Co., Ltd.（杭州深度求索）。包内 `ProductName = DeepSeek Harness`，`ProductVersion = 0.2.0-rc.2`。

产品入口：

| 用途 | 国内 | 海外 |
| --- | --- | --- |
| 产品介绍页 | <https://www.deepseek.com/zh/harness/> | <https://www.deepseek.com/en/harness/> |
| 网页版下载中心 | <https://chat.deepseek.com/downloads> | 同左 |

> 不提供 Windows ARM64 与 Linux 包（对应路径实测均为 404）。
> 你目录里的 `dsh-our-free-model/` 是给 dsh 用的免费模型插件（适配 dsh 0.1.5–0.1.7-rc.2），与客户端本体是两码事。

校验值：官方**没有发布 SHA256**，`latest` 地址旁边也取不到 —— `.exe.sha256`、`.exe.sha512`、`latest.yml`、`SHA256SUMS` 四种常见侧车路径实测全部 404。可用来核对传输的只有 CDN 响应头里的 MD5（该对象单段上传，`ETag` 就是内容 MD5）：

```bash
curl -sI "https://download.deepseek.com/desktop/dsh-latest-windows-x64.exe" | tr -d '\r' | grep -iE '^(etag|content-length|last-modified)'
md5sum DeepSeekHarness-通用版-Windows-64位-*.exe
```

2026-10-10 实测两者一致。`last-modified` 能告出这只 `latest` 包什么时候换过；签名主体 Hangzhou DeepSeek Artificial Intelligence Co., Ltd. 是判断来源的依据。

---

## 5. MiniMax Design（海螺 AI 动画视频 / vibe Animation · OPUS 5.5）

国内版走 `filecdn.minimax.chat`，包 ID `com.minimax.hub`，签名主体「上海稀宇科技有限公司」；海外版走 `file.cdn.minimax.io`，包 ID `com.minimax.hub.global`，签名主体「NANONOBLE PTE. LTD.」（新加坡）。两版同为 3.0.21，但**是两个不同的二进制**，体积和哈希都不一样，不能混用。

### 5.1 国内版（design.minimaxi.com · v3.0.21）

| 平台 | 大小 | 下载直链 |
| --- | --- | --- |
| Windows x64 | 414.4 MB | <https://filecdn.minimax.chat/public/minimax-hub/release/domestic/MiniMax%20Design-3.0.21-x64-Setup.exe> |
| macOS Apple Silicon | 385.8 MB | <https://filecdn.minimax.chat/public/minimax-hub/release/domestic/MiniMax%20Design-3.0.21-arm64.dmg> |
| macOS Intel | 417.1 MB | <https://filecdn.minimax.chat/public/minimax-hub/release/domestic/MiniMax%20Design-3.0.21-x64.dmg> |

### 5.2 海外版（design.minimax.io · v3.0.21）

| 平台 | 大小 | 下载直链 |
| --- | --- | --- |
| Windows x64 | 414.9 MB | <https://file.cdn.minimax.io/public/minimax-hub/release/overseas/MiniMax%20Design-3.0.21-x64-Setup.exe> |
| macOS Apple Silicon | 386.0 MB | <https://file.cdn.minimax.io/public/minimax-hub/release/overseas/MiniMax%20Design-3.0.21-arm64.dmg> |
| macOS Intel | 416.6 MB | <https://file.cdn.minimax.io/public/minimax-hub/release/overseas/MiniMax%20Design-3.0.21-x64.dmg> |

版本号来源是各区域目录下的 `releases.win.json` / `releases.osx-arm64.json` 清单文件；`latest.yml` 是历史 Electron 通道，里面仍是 2.0.13，**不要拿它当当前版本**。取最新地址与下载命令行见 `download_myself.md`。

校验值：**这只 `-x64-Setup.exe` 没有可用的官方校验值，别拿清单里的哈希去对。** `releases.win.json` 确实带 `SHA256`/`SHA1`，但那条目的对象是包内组件 `com.minimax.hub-3.0.21-hilo-desktop-85-full.nupkg`（430,468,428 B），与官网下载的 `MiniMax Design-3.0.21-x64-Setup.exe`（434,495,664 B）**不是同一个文件**，硬对必然不一致；海外清单同理（`com.minimax.hub.global-...nupkg`）。`<exe>.sha256` 侧车实测 404，`latest.yml` 里的 `sha512` 属于 2.0.13 旧通道。所以这里只能核：代码签名（上海稀宇 / NANONOBLE PTE. LTD）+ `Content-Length` + 从 `releases.win.json` 读到的版本号。

---

## 6. 终端型 / 国际 Agent（Anthropic / OpenAI / Google，三份独立文档）

这三家的产品都同时有「终端版」和「App 版」，**版本线互不相干**，且只有国际版；为避免和上面 5 款混排，各写一份文档，按 Windows x64 / Windows ARM64 / macOS / Linux 展开。

| 文档 | 覆盖 | 终端版 | App 版 | 只下载了哪个 |
| --- | --- | --- | --- | --- |
| [`claude_code.md`](./claude_code.md) | Anthropic | Claude Code CLI `2.1.296`（stable `2.1.287`） | Claude 桌面 App `2.31226.1`（内含 Claude Code） | ✅ **App 版 Windows x64**（6.8 MB 在线安装器） |
| [`codex.md`](./codex.md) | OpenAI | Codex CLI `0.162.1` | ChatGPT 桌面 App `26.1007.2314.0`（内含 Codex） | ✅ **App 版 Windows x64**（900.9 MB 离线 MSIX） |
| [`antigravity.md`](./antigravity.md) | Google | Antigravity CLI `agy` `1.3.3`（manifest 给官方 SHA512，9 个平台键） | Antigravity 2.0 桌面 App `2.22.0`；另有**旧版线** Antigravity IDE `2.5.5`（企业不支持） | ✅ **App 版 Windows x64**（156.5 MB NSIS 用户级包） |

要点先记在这份总目录里，细节进对应文档查：

- **装了 App 就不必再装 CLI**：Claude 桌面 App 的 Code 标签页、ChatGPT App 里的 Codex 都已内置；反过来装 CLI 不会带图形界面。**Google 是例外** —— `agy` 与 Antigravity 2.0 是两套独立产品、各自自更新，装 App 不带 CLI。
- **终端版只给命令不下载**：Claude Code 用 `curl -fsSL https://claude.ai/install.sh | bash`（macOS/Linux/WSL）或 `irm https://claude.ai/install.ps1 | iex`（Windows），另有 brew / winget / npm / apt-dnf-apk；Codex CLI 用 `curl -fsSL https://chatgpt.com/codex/install.sh | sh` 或 `powershell -c "irm https://chatgpt.com/codex/install.ps1 | iex"`，另有 brew / winget / npm；Antigravity CLI 用 `curl -fsSL https://antigravity.google/cli/install.sh | bash`（macOS/Linux/Googlebook）或 `irm https://antigravity.google/cli/install.ps1 | iex`（Windows），另有 `winget install Google.AntigravityCLI`，官方**不给** brew / npm 通道。
- **macOS / Linux 只留链接**：Claude App macOS 是 364.4 MB 通用 dmg，Linux 走 apt 源（beta，仅 Debian 系）；ChatGPT App macOS 是 746.9 MB dmg，Linux 有 deb/rpm/Arch 脚本（preview）；Antigravity 2.0 macOS 按架构分两只 dmg（Apple Silicon 195.8 MB / Intel 208.7 MB，**没有 Universal 单包**），Linux 只有绿色 `.tar.gz`，官方文档未提供 apt/rpm 源。
- **两类地址都要留意**：`claude.ai/api/desktop/...` 端点对纯 curl 返回 403（浏览器正常，需先解析出版本化直链）；ChatGPT 的 Windows 官方按钮其实是 Microsoft Store 安装器，离线整包要取 `ChatGPT-x64.msix`；Antigravity 的 `getting-started.md` 必须带 `curl --compressed` 才拿得到纯文本，且 `/download` 页面是 JS 渲染、curl 搜不到直链。
- **Claude 的 Windows App 包是在线安装器**（6.8 MB，装时联网拉主体），需要离线分发就换终端版二进制或 macOS dmg；OpenAI 的 MSIX 是完整包；Google 的 156.5 MB NSIS 包体积看是全量载荷（未实测离线安装行为）。
- **校验值分档差异**（详见 `download_myself.md` 第 8 节）：Antigravity CLI 是这三家里唯一「官方自己发布实时哈希」的（manifest 的 `sha512`，安装脚本自带校验）；它的 **App 版官方什么都不公布**（`.sha256`/`.sha512`/`manifest.json`/`latest.yml` 实测全 404，GCS 头连 MD5 都没有，只有 `crc32c`），退回签名 + `Content-Length` + 包内版本号；Windows 两只包可另用微软 winget 仓库清单的 `InstallerSha256` 交叉核，但那是第三方提交记录，不算 Google 官方值。

## 7. 国内版 / 海外版怎么选

| 维度 | 国内版 | 海外版 |
| --- | --- | --- |
| 账号 | 手机号、微信、抖音、阿里云、MiniMax/DeepSeek 国内站 | Google、GitHub、邮箱、国际站账号 |
| 模型 | 豆包、DeepSeek、GLM、Qwen、海螺等 | GPT、Claude、Gemini 等 |
| 网络 | 国内直连 | 一般需要海外网络环境 |
| 数据存放 | 境内，符合国内合规 | 境外 |
| 付费 | 人民币、支付宝/微信 | 美元、国际信用卡 |
| 是否同一二进制 | Qoder、Trae、WorkBuddy、MiniMax Design 均为**独立包** | — |
| 例外 | **DeepSeek Harness 国内外同一安装包**，只区分账号与网页入口 | — |

给国内同事做入门部署 → 一律用国内版；需要海外模型的同事再装海外版。同产品的两版可共存，安装目录与登录态互相独立。

第 6 节那三款（Claude Code / OpenAI / Google Antigravity）**不参与这张表**：官方只发一个国际版，没有国内/海外双包，差别只在账号地区是否受支持，且都要付费订阅或有配额门槛。

Google 这款另有条款要提前说清（见 [`antigravity.md`](./antigravity.md) 第 4 节）：只面向**已开放地区的个人 Google 账号**、未满 18 岁不可用，Workspace 账号常被拒（官方建议改用 `@gmail.com`）；配额按 Google AI Pro / Ultra 分层；**FAQ 明令禁止把 Antigravity 登录给 Claude Code、OpenCode 这类第三方工具供模型**，属封号行为 —— `dsh-our-free-model` 那种「喂给别的客户端」的做法在这里不能照搬。

---

## 8. 安装方式说明（本地安装 vs 网络下载）

在进行客户端部署或自动化安装引导时，请遵循以下原则：

1. **本地安装（仅在安装包已存在时使用）**：
   - **执行条件**：只有在本地 `入门agent部署/安装包/` 目录下**确实存在目标安装包文件**（`.exe` / `.msix`）的情况下，才直接调用本地安装程序进行安装。
   - 具体安装方式与权限说明（用户级免管理员 / 系统级 / 向导式）见 [`安装包/README.md`](./安装包/README.md)「四、安装步骤」。

2. **缺少安装包时的处理（提示用户下载）**：
   - 若本地目录中**不存在**对应安装包（例如仅同步了文档仓库、未解压大文件资源等）：
     - **严禁**直接尝试调用本地安装程序；
     - **必须明确提示用户**选择以下任一方式获取安装包：
       - **方案 A（下载整合安装包）**：下载并解压官方/内部提供的**整合安装包**，将所有安装包放入 `入门agent部署/安装包/` 目录后再执行本地安装；
       - **方案 B（从网络自行下载）**：按照本文第 1~6 节提供的各产品官方下载直链，或参照 [`download_myself.md`](./download_myself.md) 中的下载脚本**从网络自行下载**所需安装包。
