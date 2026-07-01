# Fork 与来源说明

本仓库基于原项目 [javaht/claude-desktop-zh-cn](https://github.com/javaht/claude-desktop-zh-cn) 修改，保留原项目历史和作者来源信息。

本 fork 额外新增了 macOS Claude Code Desktop 绕过权限确认补丁：安装时可选择打开桌面端 `bypassPermissions` gate，保留开发/远程模式读取到的 `permissions.defaultMode`，跳过桌面端工具权限 broker，并写入 `~/.claude/settings.json` 的 `permissions.defaultMode = "bypassPermissions"`。

该补丁会修改本机 Claude Desktop 的 `Contents/Resources/app.asar`，会减少工具调用确认弹窗，也会降低权限确认带来的保护。请仅在你完全信任的本机、测试机或隔离环境中使用，不建议在生产环境、公司受管设备、陌生项目或高风险目录中开启。

本项目及本 fork 均为非官方社区补丁，不属于 Anthropic 或 Claude 官方项目，也不代表 Anthropic 的认可、支持或担保。

## 使用方式

### macOS

1. 退出 Claude Desktop。
2. 下载或克隆本项目。
3. 双击 `install-mac.command`，选择安装中文补丁、安全模式安装或恢复原样 / 卸载补丁。
4. 选择安装中文补丁时，脚本会先尝试恢复旧备份来清理已有汉化；如果没有旧备份，会提示跳过并继续。
5. 安装时选择要安装的语言（1=简体中文，2=繁体中文（中国台湾），3=繁体中文（中国香港））。安全模式同样支持三种中文，并跳过结构性 `app.asar` 补丁；仅保留等长菜单汉化补丁。普通模式会额外询问是否开启 Claude Code 绕过权限补丁。
6. 按提示输入 Mac 登录密码。
7. Claude 会自动重新打开。
8. 如果没有自动切换，打开左下角账号菜单，选择 `Language` -> 对应的中文选项。

如果需要直接开启 Claude Code 绕过权限补丁，也可以在终端中运行：

```bash
CLAUDE_ENABLE_BYPASS_PERMISSIONS_PATCH=1 ./install-mac.command
```

如果需要调整自动更新，可再次运行 `install-mac.command`，选择 `4`，再输入 `y` 禁止自动更新，或输入 `n` 允许自动更新。

如果需要把 CC Switch skills 同步到 Claude Desktop，可再次运行 `install-mac.command`，选择 `5`，再输入 `y` 同步，或输入 `n` 删除之前的同步。

### Windows

1. 退出 Claude Desktop。
2. 下载或克隆本项目。
3. 右键 `install-windows.bat`，选择以管理员身份运行。
4. 先选择安装模式：
   - `1` 安装中文补丁（Cowork 兼容模式，跳过 `app.asar` 补丁；第三方模型请用网关或 ccswitch 别名映射）
   - `2` 安装中文补丁（官方账号登录模式：Cowork 沙箱/工作区不可用）
   - `3` 恢复原样 / 卸载补丁
   - `4` 自动更新设置（`y` 禁止自动更新，`n` 允许自动更新）
   - `5` 同步 CC Switch skills（`y` 开启同步，`n` 删除同步）
5. 选择安装中文补丁时，脚本会先尝试从旧备份恢复来清理已有汉化；如果没有旧备份，会提示跳过并继续。
6. 安装时再选择语言：
   - `1` 简体中文
   - `2` 繁体中文（中国台湾）
   - `3` 繁体中文（中国香港）
7. 脚本会备份当前 Claude Desktop 资源，写入本仓库 `resources` 目录里的中文 JSON，补齐硬编码界面文本，并重启 Claude Desktop。选择模式 1 时会跳过 `app.asar` 补丁，更适合需要 Cowork/截图工作区的场景。选择模式 2 时会直接修改当前 Claude 的 `app.asar`，卸载时从备份恢复。
8. 如果没有自动切换，打开左下角账号菜单，选择 `Language` -> 对应的中文选项。
