# Codex Dream Skin（Windows）

面向 Microsoft Store 官方 Codex Desktop 的非官方 Windows 主题工具。

本项目通过仅监听本机的 CDP 注入 CSS 和装饰层，不修改 WindowsApps、`app.asar` 或官方程序签名。侧栏、建议卡、项目选择器、菜单和输入框仍然是原生可交互控件。

> Windows 版本当前支持安装、启动、验证、截图和恢复；暂时没有 macOS 版的一键选图定制入口。

## 开始前准备

- 已从 Microsoft Store 安装官方 Codex，并至少正常启动过一次。
- `%USERPROFILE%\.codex\config.toml` 已存在。
- 已安装 Node.js，PowerShell 中可以运行 `node`。

还没有安装 Codex？登录 [CODELINK 教程中心](https://api.codelink.chat/tutorial)，打开「Codex 接入」查看客户端安装说明。

检查 Node.js：

```powershell
node --version
```

如果提示找不到命令，请安装当前 Node.js LTS，再重新打开 PowerShell。

## 1. 进入项目目录

```powershell
cd "C:\你的路径\Codex-Dream-Skin\windows"
```

如果当前窗口禁止执行本地脚本，可以只为这一次 PowerShell 会话临时放行：

```powershell
Set-ExecutionPolicy -Scope Process Bypass
```

关闭 PowerShell 后，该设置会自动失效。不要为了本项目修改全系统执行策略。

## 2. 安装

```powershell
.\scripts\install-dream-skin.ps1
```

安装器会备份主题相关配置，并在桌面和开始菜单创建启动入口。

状态和日志目录：

```text
%LOCALAPPDATA%\CodexDreamSkin
```

## 3. 启动

建议先正常退出已经打开的 Codex，然后运行：

```powershell
.\scripts\start-dream-skin.ps1
```

Windows 默认使用本机端口 `9335`。

如果提示 Codex 已经在运行，请保存工作并退出 Codex，再重新运行启动脚本。只有在明确允许脚本重启当前 Codex 时，才使用：

```powershell
.\scripts\start-dream-skin.ps1 -RestartExisting
```

启动器每次都会动态查找最新的 `OpenAI.Codex` Store 包，不会写死某个 WindowsApps 版本目录。Codex 更新后，重新执行安装和启动即可。

## 4. 验证并截图

```powershell
.\scripts\verify-dream-skin.ps1 -ScreenshotPath "$env:USERPROFILE\Desktop\Codex-Dream-Skin-Verification.png"
```

主题显示后，还要实际检查：

1. 首页横幅和侧栏正常显示，没有横向滚动。
2. 点击一个原生建议卡，确认正常响应。
3. 打开真实项目选择器。
4. 在输入框输入并清空文字。
5. 打开一个任务，再返回 New Task。
6. 缩窄窗口，确认核心控件没有被遮挡。

验证脚本应成功退出。只看到背景图，但按钮无法点击或输入框被遮挡，不能算安装成功。

## 5. 恢复官方外观

只移除当前实时主题：

```powershell
.\scripts\restore-dream-skin.ps1
```

同时恢复安装前的基础主题配置：

```powershell
.\scripts\restore-dream-skin.ps1 -RestoreBaseTheme
```

恢复基础主题并删除项目创建的快捷方式：

```powershell
.\scripts\restore-dream-skin.ps1 -RestoreBaseTheme -Uninstall
```

恢复操作不会删除官方 Codex，也不会删除你的任务。

## 端口冲突

如果 `9335` 被其他程序占用，可以改用另一个端口，但安装、启动、验证和恢复必须始终使用相同端口：

```powershell
.\scripts\install-dream-skin.ps1 -Port 9340
.\scripts\start-dream-skin.ps1 -Port 9340
.\scripts\verify-dream-skin.ps1 -Port 9340
.\scripts\restore-dream-skin.ps1 -Port 9340 -RestoreBaseTheme
```

不要把 CDP 暴露到 `0.0.0.0`、局域网或公网。

## 当前能力边界

- Windows 当前没有 macOS 版的交互式一键换图脚本。
- 主题资源位于 `windows\assets\`，直接修改属于开发者操作。
- 不要把完整效果截图当作整窗覆盖层；所有真实 Codex 控件必须保持可点击。
- 装饰层必须保持 `pointer-events: none`。
- 本项目不会修改 API Key 或 Base URL。

## 相关文件

- `scripts/install-dream-skin.ps1`：安装和配置备份。
- `scripts/start-dream-skin.ps1`：启动 Codex 和注入器。
- `scripts/verify-dream-skin.ps1`：实时验证和截图。
- `scripts/restore-dream-skin.ps1`：移除主题和恢复配置。
- `references/qa-inventory.md`：开发者功能与视觉验收清单。
- `references/runtime-notes.md`：运行时和更新行为说明。

## 项目支持

本项目由 [CODELINK AI 中转站](https://api.codelink.chat) 提供支持。CODELINK 的 Codex 教程和 API 服务与本换肤工具独立配置，不是使用主题的必要条件。
