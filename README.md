<div align="center">
  <img src="./apps/readest-app/src-tauri/icons/icon.png" alt="Readest 图标" width="120" />
  <h1>Readest · 个人分支</h1>
  <p>跨平台电子书阅读器，面向沉浸阅读与深度阅读。</p>
</div>

本仓库是 [praxpur/readest](https://github.com/praxpur/readest)，基于上游 [readest/readest](https://github.com/readest/readest) 的个人分支，主要调整是向所有套餐开放第三方云同步。它不是官方发行仓库，也不代表 Readest 官方。

Readest 是对 [Foliate](https://github.com/johnfactotum/foliate) 的现代化重写，使用 Next.js 16 和 Tauri v2，支持 Windows、macOS、Linux、Android、iOS 和 Web。阅读器的主体功能与跨平台架构来自上游。

## 与上游的差别

以下对比以本分支所基于的上游提交 [`86f58564f`](https://github.com/readest/readest/commit/86f58564f938d462625d29e9a423d1758ff504b6)（2026-11-11）为基准，不代表上游最新版本的状态。

| 项目 | 上游基准版本 | 本分支 |
| --- | --- | --- |
| 第三方云同步 | 启用付费权限检查，免费套餐受到限制 | 关闭该检查，所有套餐均可使用，包括免费套餐 |
| 同步服务 | 已实现 WebDAV、Google Drive、S3 等同步接入 | 沿用上游实现，没有新增云服务或存储空间 |
| 同步相关测试 | 按启用付费限制的行为断言 | 调整云同步、书库刷新和传输队列测试，使其符合开放后的行为 |
| Windows 测试兼容 | OG 渲染器测试使用文件路径；部分 TTS 测试依赖零延迟定时器 | 使用文件 URL 导入 OG 渲染器；在 Windows 跳过依赖该定时精度的 TTS 测试，Linux 仍执行 |
| 本地构建临时目录 | 未忽略 `.build-tools/` | 将其加入 Git 忽略规则 |
| 仓库说明 | 英文 README，以官方项目为主 | 中文 README，明确个人分支的修改范围与使用边界 |

核心改动位于 [`apps/readest-app/src/utils/access.ts`](./apps/readest-app/src/utils/access.ts)：

```ts
export const CLOUD_SYNC_REQUIRES_PREMIUM = false;
```

该开关使 `isCloudSyncAllowed` 对所有套餐放行。使用时仍需自行配置第三方服务的地址、账号、授权或密钥，并遵守服务提供方的额度与收费规则。

**开放第三方云同步不等于开放所有付费功能。** 本分支没有增加 Readest 官方云存储额度，也没有开放专属朗读语音、离线 TTS 下载或其他独立控制权限的功能。

### 关于付费专属朗读语音

Readest Voice 通过服务端 GPU 合成音频。客户端与服务端均检查权限，服务端还会检查 Plus / Pro 音色等级及每日用量。仅移除客户端的禁用状态，无法让免费账号获得官方语音服务。

本分支保留这些检查。若要自行开放语音能力，需要部署并配置自己有权使用的语音服务，承担算力或 API 费用；源码中的自定义 TTS 接入也有独立权限检查，不在当前云同步调整范围内。

## 主要功能

以下功能主要继承自上游，不是本分支新增功能；具体可用性取决于平台、配置和对应服务权限。

| 功能 | 说明 |
| --- | --- |
| 多格式阅读 | 支持 EPUB、PDF、MOBI、KF8（AZW3）、FB2、CBZ、TXT、HTML、Markdown |
| 阅读布局 | 支持滚动与分页、字体和主题调整、代码语法高亮 |
| 搜索与批注 | 书内及书架全文搜索、高亮、书签和笔记 |
| 平行阅读 | 分屏同时阅读两本书或两份文档 |
| 词典与翻译 | 词典、维基百科查询，本地词典导入，以及 DeepL、Yandex 等翻译接入 |
| 书库管理 | 书籍整理、排序，以及 OPDS / Calibre 接入 |
| 网页导入 | 网页剪藏及网络小说章节导入 |
| 听书与朗读 | 有声书、文本转语音、EPUB 3 Media Overlays 同步高亮，以及本地电子书与无 DRM 音频配对 |
| 多端同步 | 同步书籍、阅读进度与批注，并支持 KOReader 同步接入 |
| 无障碍与专注阅读 | 键盘导航、屏幕阅读器支持、阅读标尺与逐段阅读等 |

## 界面预览

以下截图来自上游，仅用于展示继承的阅读界面。

![多平台阅读界面](./data/screenshots/landing_all_platforms.png)

![高亮与批注](./data/screenshots/annotations.png)

![朗读界面](./data/screenshots/tts_speak_aloud.png)

![深色主题](./data/screenshots/theming_dark_mode.png)

## 获取与安装

### 本分支

使用本分支改动，请从本仓库源码构建。也可查看[本分支 Releases](https://github.com/praxpur/readest/releases) 是否提供构建产物；不能将上游安装包视为包含本分支改动的版本。

### 官方版本

以下入口均由上游提供，**不包含本分支对第三方云同步权限的调整**：

- [Readest 官方网站](https://readest.com)：官方介绍与下载入口。
- [上游 GitHub Releases](https://github.com/readest/readest/releases)：Windows、Linux、Android 等安装包。
- [App Store](https://apps.apple.com/app/id6738622779)：macOS、iOS 和 iPadOS 版本。
- [Google Play](https://play.google.com/store/apps/details?id=com.bilingify.readest)：Android 版本。
- [Flathub](https://flathub.org/en/apps/com.bilingify.readest)：Linux Flatpak 版本。
- [官方 Web 阅读器](https://web.readest.com)：在线使用官方服务。

## 从源码运行

需要 Node.js、pnpm、Rust 和目标平台的 Tauri 构建依赖。仓库声明使用 `pnpm@11.1.1`；完整环境要求与平台说明见[贡献指南](./CONTRIBUTING.md#getting-started)。

```bash
# 获取本分支及子模块
git clone --recurse-submodules https://github.com/praxpur/readest.git
cd readest

# 安装依赖并准备阅读器资源
pnpm install
pnpm --filter @readest/readest-app setup-vendors

# 检查 Tauri 环境
pnpm tauri info

# 启动桌面开发版
pnpm tauri dev

# 或启动 Web 开发版
pnpm dev-web
```

构建桌面安装包：

```bash
pnpm tauri build
```

Android / iOS 还需相应 SDK、平台初始化和签名配置，参见[贡献指南](./CONTRIBUTING.md)。运行 Web 开发版不需要编译 Rust；云端功能仍需对应的环境配置和服务。

### 检查与测试

在仓库根目录执行：

```bash
# 格式检查
pnpm format:check

# TypeScript 类型检查及代码检查
pnpm lint

# 单次运行单元测试
pnpm --filter @readest/readest-app test --run
```

Windows 下跳过的部分 TTS 定时测试不代表相应功能已通过 Windows 端到端验证。测试结构和更多验证方式见[测试文档](./apps/readest-app/docs/testing.md)。

## 文档与问题反馈

- [官方使用文档](https://readest.com/docs)：安装、使用教程与常见问题，部分内容针对官方版本。
- [项目架构](./apps/readest-app/docs/architecture.md)、[代码布局](./apps/readest-app/docs/code-layout.md)与[测试说明](./apps/readest-app/docs/testing.md)。
- 本分支特有的问题请提交到[本仓库 Issues](https://github.com/praxpur/readest/issues)。
- 确认属于上游的问题，可查询[上游 Issues](https://github.com/readest/readest/issues)。参与上游贡献前，请阅读[贡献指南](./CONTRIBUTING.md)和[行为准则](./CODE_OF_CONDUCT.md)。

### Windows 启动后没有窗口

若程序没有正常启动，请检查 Microsoft Edge WebView2 Runtime 是否安装且可用。可从 [Microsoft 官方页面](https://developer.microsoft.com/en-us/microsoft-edge/webview2)安装或更新运行时。相关背景见[上游问题 #358](https://github.com/readest/readest/issues/358)。

### Linux 在 Wayland / Niri 下无法启动

本源码所用的 Linux CEF 运行时通过 X11 创建窗口，Wayland 会话需要 XWayland，并设置有效的 `DISPLAY`。Niri 用户可参考 [XWayland 配置说明](https://niri-wm.github.io/niri/Xwayland.html)。随意设置 `DISPLAY` 或添加 `--ozone-platform=wayland` 不能替代可用的 XWayland 服务。

## 致谢与许可

本分支基于 Readest 及其贡献者的工作。支持上游开发可访问 [Readest 官方捐赠页面](https://donate.readest.com)。

本项目遵循 **GNU Affero General Public License v3.0 或更新版本（AGPL-3.0-or-later）**，具体条款见 [LICENSE](./LICENSE)。分发或部署修改版本时，应遵守许可证对应的源码提供义务。

使用的主要开源项目包括：

- [foliate-js](https://github.com/johnfactotum/foliate-js)、[fflate](https://github.com/101arrowz/fflate)、[daisyUI](https://github.com/saadeghi/daisyui)、[marked](https://github.com/markedjs/marked)、[Next.js](https://github.com/vercel/next.js)、[React](https://github.com/facebook/react) 和 [Tauri](https://github.com/tauri-apps/tauri)：MIT 许可。
- [zip.js](https://github.com/gildas-lormeau/zip.js)：BSD-3-Clause 许可。
- [PDF.js](https://github.com/mozilla/pdf.js)：Apache-2.0 许可。
- [react-icons](https://github.com/react-icons/react-icons)：各图标集采用各自的开源许可。

应用所用字体包括 Bitter、Fira Code、Inter、Literata、Merriweather、Noto Sans、Roboto、LXGW WenKai、MiSans、Source Han 和 WenQuanYi Micro Hei，各自遵循其字体许可。感谢[中文网字计划](https://chinese-font.netlify.app)提供的开源工具。
