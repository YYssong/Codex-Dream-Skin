# Codex Dream Skin Studio（macOS）

面向官方 **Codex Desktop** 的非官方 macOS 主题工具。

选择一张喜欢的图片，就能把它变成 Codex 首页横幅、任务页背景和磨砂内容层，同时保留原生侧栏、建议卡、项目选择器、任务内容、菜单和输入框的完整交互。

本项目通过仅监听本机的 **CDP** 注入主题，不修改官方 `.app`、`app.asar` 或代码签名。

> 本项目与 OpenAI 无隶属或赞助关系。Codex 及相关商标归其权利人所有。

## 开始前准备

- 一台运行 macOS 的 Mac。
- 已安装官方 Codex Desktop，并至少正常启动过一次。
- `~/.codex/config.toml` 已由 Codex 创建。
- 不需要全局安装 Node.js；安装器会校验并使用 Codex 自带的已签名 Node.js。

还没有安装 Codex？登录 [CODELINK 教程中心](https://api.codelink.chat/tutorial)，打开「Codex 接入」查看客户端安装说明。

## 推荐安装方式

打开本目录，双击：

```text
Install Codex Dream Skin.command
```

安装完成后，桌面会出现四个入口：

- `Codex Dream Skin.command`：启动或重新应用主题。
- `Codex Dream Skin - Customize.command`：选择图片并生成自己的主题。
- `Codex Dream Skin - Verify.command`：验证主题并生成截图。
- `Codex Dream Skin - Restore.command`：恢复安装前的官方外观。

如果 macOS 首次阻止打开 `.command` 文件，请在 Finder 中右键该文件并选择“打开”。不要关闭系统安全功能，也不要运行来源不明的解除隔离命令。

## 命令行安装

在 `macos` 目录中运行：

```bash
# 1. 可选：执行静态测试
# 需要本机已有可用的官方 Codex 应用
./tests/run-tests.sh

# 2. 安装到稳定目录并创建桌面入口，但暂不启动
./scripts/install-dream-skin-macos.sh --no-launch

# 3. 选择图片并生成主题
~/.codex/codex-dream-skin-studio/scripts/customize-theme-macos.sh
```

安装后的主要路径：

| 内容 | 路径 |
| --- | --- |
| 主题引擎 | `~/.codex/codex-dream-skin-studio` |
| 状态、日志和用户图片 | `~/Library/Application Support/CodexDreamSkinStudio` |
| 主题配置备份 | Application Support 下的 `theme-backup.json` |

## 换成自己的图片

双击桌面的：

```text
Codex Dream Skin - Customize.command
```

在 Finder 中选图，再输入主题名称即可。

图片建议：

- 支持 PNG、JPEG、HEIC、TIFF、WebP 等 macOS 可读取格式。
- 原图不超过 50 MB，处理后的文件不超过 16 MB。
- 推荐使用宽度 2000 像素以上的横图。
- 图片左侧尽量简洁，避免首页标题难以阅读。
- 图片只作为横幅和背景，不要使用带假按钮、假输入框的整窗截图。

需要精确设置文字和颜色时，可以运行：

```bash
~/.codex/codex-dream-skin-studio/scripts/customize-theme-macos.sh \
  --image "/图片的绝对路径/theme.png" \
  --name "我的 Codex 主题" \
  --tagline "把喜欢的画面变成工作台" \
  --quote "MAKE SOMETHING WONDERFUL" \
  --accent "#7cff46" \
  --secondary "#36d7e8" \
  --highlight "#642a8c"
```

恢复项目自带演示主题：

```bash
~/.codex/codex-dream-skin-studio/scripts/customize-theme-macos.sh --reset-demo
```

## 验证主题

双击桌面的：

```text
Codex Dream Skin - Verify.command
```

验证器会检查实时界面，并在桌面生成：

```text
Codex Dream Skin Verification.png
```

命令行验证：

```bash
~/.codex/codex-dream-skin-studio/scripts/verify-dream-skin-macos.sh \
  --reload \
  --screenshot "$HOME/Desktop/Codex Dream Skin Verification.png"
```

除了看到主题，还应实际点击建议卡、项目选择器、侧栏和输入框。验证结果应包含 `pass: true`，且页面不能出现横向溢出或装饰层遮挡按钮。

## 暂停与恢复

临时暂停主题，不重启 Codex：

```bash
~/.codex/codex-dream-skin-studio/scripts/pause-dream-skin-macos.sh
```

完整恢复安装前的外观并重启 Codex：

```bash
~/.codex/codex-dream-skin-studio/scripts/restore-dream-skin-macos.sh \
  --restore-base-theme \
  --restart-codex
```

桌面的 `Codex Dream Skin - Restore.command` 执行的就是完整恢复。

## 可选：SwiftBar 菜单栏

双击：

```text
Install Menu Bar.command
```

菜单栏支持应用、暂停、换图、切换已保存主题、打开图片目录和完整恢复。SwiftBar 是可选功能，不安装也不影响桌面入口。

## 给客户打包

需要生成“解压后双击安装”的客户包时运行：

```bash
./scripts/build-client-release.sh "$HOME/Desktop/Codex 主题编辑器.zip"
```

生成的 ZIP 包含可见安装器和隐藏的完整主题引擎。不要只发送 CSS 或图片，否则客户会缺少启动、验证和恢复能力。

## 安全原理

1. 发现官方 `com.openai.codex`，校验签名、Team ID、架构和内置 Node.js。
2. 通过用户级启动流程运行 Codex，并把 CDP 仅绑定到 `127.0.0.1`。
3. 只接受属于 Codex 或其合法子进程的调试端口。
4. 只向预期的 `app://` 渲染页面注入主题。
5. 注入器在刷新和路由变化后重新应用主题。
6. 恢复时核对 PID、路径和启动时间，只停止已记录的注入器。

CDP 在本机回环地址上没有额外认证。主题运行期间不要运行来源不明的本机程序；不再使用时可以执行完整恢复。

## 许可与声明

- MIT License，详见 `LICENSE`。
- 商标、示例素材和运行时说明见 `NOTICE.md`。
- 本项目不是 OpenAI 产品，也不是 Codex 源码分支。
- 本项目不修改官方二进制，不提供 API 中转，也不会改写 API Key 或 Base URL。

## 项目支持

本项目由 [CODELINK AI 中转站](https://api.codelink.chat) 提供支持。CODELINK 的 Codex 教程和 API 服务与本换肤工具独立配置，不是使用主题的必要条件。
