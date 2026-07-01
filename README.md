# Claude Desktop 中文补丁

一个用于 Claude Desktop 的中文界面补丁，支持简体中文、繁体中文（中国台湾）和繁体中文（中国香港）。macOS 可双击 `install-mac.command`，Windows 可右键管理员运行 `install-windows.bat`。

本项目支持官方订阅和 API 使用方式。第三方 API / 网关模型请先参考相关网关或 ccswitch 配置说明。

## Fork 与来源说明

本仓库基于原项目 [javaht/claude-desktop-zh-cn](https://github.com/javaht/claude-desktop-zh-cn) 修改，保留原项目历史和作者来源信息。

本 fork 额外新增了 macOS Claude Code Desktop 绕过权限确认补丁：安装时可选择打开桌面端 `bypassPermissions` gate，保留开发/远程模式读取到的 `permissions.defaultMode`，跳过桌面端工具权限 broker，并写入 `~/.claude/settings.json` 的 `permissions.defaultMode = "bypassPermissions"`。

该补丁会修改本机 Claude Desktop 的 `Contents/Resources/app.asar`，会减少工具调用确认弹窗，也会降低权限确认带来的保护。请仅在你完全信任的本机、测试机或隔离环境中使用，不建议在生产环境、公司受管设备、陌生项目或高风险目录中开启。

本项目及本 fork 均为非官方社区补丁，不属于 Anthropic 或 Claude 官方项目，也不代表 Anthropic 的认可、支持或担保。

## 界面截图

![Claude Desktop 中文界面截图](docs/images/claude-desktop-zh-cn-home.png) ![Claude Desktop 中文设置界面截图](docs/images/claude-desktop-zh-cn-settings.png)

## 功能完整说明

### 中文界面与语言资源

- 支持三种中文变体：`zh-CN`、`zh-TW`、`zh-HK`。
- 自动把当前选择的中文语言加入 Claude Desktop 前端语言白名单。
- 安装前端中文资源：`frontend-*.json`。
- 安装桌面壳层中文资源：`desktop-*.json`。
- 安装 statsig i18n 兜底资源：`statsig-*.json`。
- 安装 macOS 原生菜单资源：`Localizable*.strings`。
- 合并当前 Claude 版本自带的 `en-US.json` 与本仓库中文翻译：已翻译字段显示中文，新版本新增但尚未翻译的字段保留英文，避免界面缺字段。
- 扫描并替换前端 bundle 中未走 i18n JSON 的硬编码文本，例如侧边栏入口、设置页标签、模型选择项等。
- 修正中文语言显示名称，让语言菜单能正确展示对应中文选项。

### 在线 claude.ai 页面汉化

- 普通安装模式会修改 `app.asar`，在在线账号登录后的 `claude.ai` 页面注入显示层 DOM 翻译。
- 覆盖聊天、项目、Artifacts、设置、菜单等远程页面中的硬编码界面文案。
- 同步并锁定前端语言状态，减少 Claude Desktop 自动把语言写回英文的情况。
- 该逻辑只改界面文本和语言状态，不改第三方 API、网关、模型路由或请求内容。
- 安全模式会跳过在线页面 DOM 翻译，因为该功能需要结构性修改 `app.asar`。

### 第三方模型名兼容

- macOS 普通模式可绕过新版 Claude Desktop 对 3P gateway 模型名的本地 Anthropic 校验。
- 可避免 `deepseek-v4-pro`、`kimi-*` 等非 Anthropic 模型名导致配置整体失效。
- Windows 官方账号模式会直接修改 `app.asar` 并同步改写 `Claude.exe` 内嵌完整性哈希。
- 若需要 Cowork 沙箱 / 截图工作区，Windows 建议选择模式 1，并在网关或 ccswitch 中做模型别名映射。

### macOS Claude Code Desktop 绕过权限确认补丁

- 安装 macOS 普通模式时可选择是否开启。
- 也可通过环境变量直接开启：

```bash
CLAUDE_ENABLE_BYPASS_PERMISSIONS_PATCH=1 ./install-mac.command
```

- 补丁会打开桌面端 `bypassPermissions` gate。
- 保留开发模式或远程配置读取到的 `permissions.defaultMode`，不再过滤 `auto` / `bypassPermissions`。
- 跳过桌面端工具权限 broker，减少工具调用前的确认弹窗。
- 写入 `~/.claude/settings.json`：

```json
{
  "permissions": {
    "defaultMode": "bypassPermissions"
  }
}
```

- 该补丁不能与 macOS 安全模式同时使用，因为它需要结构性修改 `app.asar`。
- 该补丁会降低工具调用确认保护，只建议在受信任或隔离环境中使用。
- Windows 版本当前没有实现绕过权限确认补丁。Windows 如需支持，需要单独适配 `resources/app.asar`、同步更新 `Claude.exe` 内嵌完整性哈希，并额外验证 Windows Claude Desktop 的 bundle 结构；同时会进一步影响签名和 Cowork / 沙箱兼容性，因此本 fork 暂未提供 Windows 绕过权限开关。

### 备份、恢复与安全处理

- macOS 安装前会自动备份当前 `/Applications/Claude.app`。
- macOS 安装前会尝试从旧备份恢复，先清理上一轮汉化，再重新安装。
- macOS 恢复 / 卸载时会选择同目录下最早的备份恢复为 `/Applications/Claude.app`，并清理其他补丁备份。
- macOS 会对修改后的 Claude.app、内部 app/framework/原生二进制做本机 ad-hoc 重签名。
- macOS 会清除 `com.apple.quarantine` 隔离属性。
- Windows 会备份被修改的前端 JS bundle、`app.asar`、`Claude.exe` 到 `resources\.zh-cn-backups`。
- Windows 卸载时会从备份恢复。
- Windows 修改 `app.asar` 后会同步改写 `Claude.exe` 内嵌完整性哈希；这会破坏 Authenticode 签名，可能影响 Cowork VM 服务。

### 自动更新控制

- macOS 和 Windows 都提供菜单项 `4` 控制 Claude Desktop 自动更新。
- 输入 `y` 禁止自动更新。
- 输入 `n` 允许自动更新。
- macOS 会优先写入 Claude-3p `configLibrary`；若不存在有效配置，则写入 Claude Desktop enterprise policy。
- Windows 会优先写入 Claude-3p `configLibrary`；若不存在有效配置，则写入 `HKCU\SOFTWARE\Policies\Claude` policy。

### CC Switch skills 同步

- macOS 和 Windows 都提供菜单项 `5` 同步或删除 CC Switch skills。
- 输入 `y` 会扫描本机 CC Switch skills 目录，把 Claude Desktop 中缺失的 skill 以软链接加入本地 skills 目录，并更新 manifest。
- 输入 `n` 只删除之前同步产生的软链接和 manifest 记录。
- 不删除 CC Switch 源目录。
- 不覆盖 Claude Desktop 里已有的同名 skill。
- 同步或删除后需要重启 Claude Desktop 生效。

## 适用环境

- macOS 或 Windows。
- 已安装 Claude Desktop。
- macOS 需要系统自带 Python 3，通常路径为 `/usr/bin/python3`。
- Windows 需要 PowerShell，建议以管理员权限运行。

## 使用方式

### macOS

1. 退出 Claude Desktop。
2. 下载或克隆本项目。
3. 双击 `install-mac.command`。
4. 选择操作：
   - `1` 安装中文补丁，官方订阅与第三方 API 均可使用；普通模式会应用结构性 `app.asar` 补丁。
   - `2` 安装中文补丁，安全模式会跳过结构性 `app.asar` 补丁；第三方模型需借助 ccswitch 或网关映射。
   - `3` 恢复原样 / 卸载补丁。
   - `4` 自动更新设置。
   - `5` 同步 CC Switch skills。
5. 选择安装中文补丁时，脚本会先尝试恢复旧备份来清理已有汉化；如果没有旧备份，会提示跳过并继续。
6. 选择要安装的语言：
   - `1` 简体中文。
   - `2` 繁体中文（中国台湾）。
   - `3` 繁体中文（中国香港）。
7. 普通模式会询问是否开启 Claude Code 绕过权限补丁。
8. 按提示输入 Mac 登录密码。
9. Claude 会自动重新打开。
10. 如果没有自动切换，打开左下角账号菜单，选择 `Language` -> 对应中文选项。

### macOS 直接开启绕过权限补丁

```bash
cd /path/to/claude-desktop-zh-cn
CLAUDE_ENABLE_BYPASS_PERMISSIONS_PATCH=1 ./install-mac.command
```

### macOS 安全模式

安全模式会跳过结构性 `app.asar` 补丁，仅保留等长菜单汉化补丁。适合只想尽量降低改动范围的场景，但不会启用在线页面 DOM 翻译、3P 模型名绕过和绕过权限确认补丁。

### Windows

1. 退出 Claude Desktop。
2. 下载或克隆本项目。
3. 右键 `install-windows.bat`，选择以管理员身份运行。
4. 选择安装模式：
   - `1` 安装中文补丁，Cowork 兼容模式，跳过 `app.asar` 补丁；第三方模型请用网关或 ccswitch 别名映射。
   - `2` 安装中文补丁，官方账号登录模式；会修改 `app.asar`，Cowork 沙箱 / 工作区不可用。
   - `3` 恢复原样 / 卸载补丁。
   - `4` 自动更新设置。
   - `5` 同步 CC Switch skills。
5. 选择安装中文补丁时，脚本会先尝试从旧备份恢复来清理已有汉化；如果没有旧备份，会提示跳过并继续。
6. 选择语言：
   - `1` 简体中文。
   - `2` 繁体中文（中国台湾）。
   - `3` 繁体中文（中国香港）。
7. 脚本会备份当前 Claude Desktop 资源，写入中文资源，补齐硬编码界面文本，并重启 Claude Desktop。
8. 如果没有自动切换，打开左下角账号菜单，选择 `Language` -> 对应中文选项。

> 注意：Windows 版本当前不支持开启 Claude Code Desktop 绕过权限确认补丁。该功能目前仅在 macOS 普通模式中提供。

## 文件说明

- `install-mac.command`：macOS 双击运行入口。
- `install-windows.bat`：Windows 安装 / 恢复菜单入口。
- `scripts/patch_claude_zh_cn.py`：macOS 补丁主脚本。
- `scripts/install_windows.ps1`：Windows 汉化安装、卸载、备份恢复脚本。
- `resources/manifest*.json`：语言包 manifest。
- `resources/frontend-*.json`：Claude 前端界面中文翻译。
- `resources/frontend-hardcoded-*.json`：前端硬编码文案替换规则。
- `resources/desktop-*.json`：Claude 桌面壳层中文翻译。
- `resources/statsig-*.json`：statsig i18n 兜底资源。
- `resources/Localizable*.strings`：macOS 原生菜单中文资源。
- `resources/release.json`：补丁资源版本信息。

## macOS 脚本会做什么

- 退出正在运行的 Claude Desktop。
- 备份当前 `/Applications/Claude.app`。
- 复制 Claude.app 到临时目录。
- 修改语言白名单。
- 安装中文资源与 statsig 兜底资源。
- 替换前端硬编码中文文案。
- 根据安装模式决定是否修改 `app.asar`。
- 普通模式会注入在线页面 DOM 翻译、锁定 DesktopIntl locale、补丁主进程菜单、绕过 3P 模型名校验。
- 可选开启 Claude Code Desktop 绕过权限确认补丁。
- 写入 `~/Library/Application Support/Claude/config.json`，设置当前语言。
- 开启绕过权限补丁时，写入 `~/.claude/settings.json` 的 `permissions.defaultMode = "bypassPermissions"`。
- 对修改后的 app 做 ad-hoc 重签名并清除 quarantine。
- 替换 `/Applications/Claude.app`。
- 重新启动 Claude Desktop。

## Windows 脚本会做什么

- 查找 Windows 版 Claude Desktop 安装目录。
- 安装前尝试恢复旧备份，清理上一轮汉化。
- 备份即将修改的前端 bundle、`app.asar`、`Claude.exe`。
- 安装中文资源。
- 修改前端语言白名单。
- 替换前端硬编码中文文案。
- Cowork 兼容模式跳过 `app.asar` 补丁。
- 官方账号模式会修改 `app.asar`，注入在线页面 DOM 翻译和主进程补丁。
- 官方账号模式会同步改写 `Claude.exe` 内嵌的 `app.asar` 完整性哈希。
- 写入用户配置，将语言设置为所选中文变体。
- 重启 Claude Desktop。

## 卸载 / 恢复

再次运行安装脚本，选择 `恢复原样 / 卸载补丁` 即可。

macOS 会从 `/Applications` 同目录下最早的 `Claude.backup-before-zh-CN-*.app` 恢复。Windows 会从 `resources\.zh-cn-backups` 恢复之前备份的文件。

## 注意事项

- 修改 `app.asar` 的模式会增加对 Claude Desktop 内部 bundle 结构的依赖；Claude Desktop 更新后如果结构变化，补丁可能需要更新。
- Windows 模式 2 会破坏 Authenticode 签名，可能导致 Cowork VM 服务拒绝客户端并报 `RPC pipe closed`。
- 绕过权限确认补丁会减少工具调用前的人工确认，请只在你明确理解风险的环境中开启。
- 绕过权限确认补丁当前仅支持 macOS；Windows 版本暂未实现该功能。
- 如果安装失败，优先运行恢复 / 卸载，再更新本项目后重新安装。

## 免责声明

本项目为非官方社区补丁，不属于 Anthropic 或 Claude 官方项目，也不代表 Anthropic 的认可、支持或担保。

本项目会修改本机 Claude Desktop 的本地资源文件，部分模式会修改 `Contents/Resources/app.asar` 并重新签名或改写完整性哈希。Claude Desktop 更新后资源结构可能变化，若补丁失败，请先恢复原样，再更新本项目或重新运行安装脚本。

开启 macOS Claude Code Desktop 绕过权限确认补丁后，由工具调用、文件读写、命令执行、浏览器控制或其他自动化行为造成的结果，需要由使用者自行确认并承担风险。

### 🚩 友情链接

[![LinuxDo](https://img.shields.io/badge/社区-LinuxDo-blue?style=for-the-badge)](https://linux.do/)
