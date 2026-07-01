# Fork 与来源说明

本仓库基于原项目 [javaht/claude-desktop-zh-cn](https://github.com/javaht/claude-desktop-zh-cn) 修改，保留原项目历史和作者来源信息。

本 fork 额外新增了 macOS Claude Code Desktop 绕过权限确认补丁：安装时可选择打开桌面端 `bypassPermissions` gate，保留开发/远程模式读取到的 `permissions.defaultMode`，跳过桌面端工具权限 broker，并写入 `~/.claude/settings.json` 的 `permissions.defaultMode = "bypassPermissions"`。

该补丁会修改本机 Claude Desktop 的 `Contents/Resources/app.asar`，会减少工具调用确认弹窗，也会降低权限确认带来的保护。请仅在你完全信任的本机、测试机或隔离环境中使用，不建议在生产环境、公司受管设备、陌生项目或高风险目录中开启。

本项目及本 fork 均为非官方社区补丁，不属于 Anthropic 或 Claude 官方项目，也不代表 Anthropic 的认可、支持或担保。
