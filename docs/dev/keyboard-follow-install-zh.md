# 鼠标跟随模式：双机安装与配置

本说明对应 `feat/keyboard-follow` 分支。MacBook 是服务器，使用自带键盘和触控板；Windows 是客户端，使用直接连接到 Windows 的独立鼠标。两台电脑都安装**同一次工作流运行、同一提交**产出的 Deskflow。不要混用此分支与上游正式版或旧后台服务：鼠标跟随消息使用协议 1.9。

## 获取安装包

将本地工作流 `.github/workflows/keyboard-follow-packages.yml` 提交到你自己的 fork 的 `feat/keyboard-follow` 分支后，在该 fork 的 **Actions → Keyboard Follow packages** 手动运行，或推送该分支触发。成功的运行会提供 `keyboard-follow-macos-arm64`（DMG）和 `keyboard-follow-windows-x64`（MSI、便携 ZIP）两个构建产物。工作流会在两个系统上运行现有 CTest，并检查安装包存在、打包后的 Core 能报告版本。

DMG 使用临时签名，没有 Apple 公证。将 `Deskflow.app` 从 DMG 拖入“应用程序”；如果 macOS 拒绝打开，在“系统设置 → 隐私与安全性”中允许打开来自该构建的应用。Windows 运行 MSI 安装；若安装器要求 Microsoft Visual C++ Redistributable，请按提示安装。安装后确保没有旧版 Deskflow 后台服务仍在运行，并在 Deskflow 设置中使用当前安装包自带的 Core。

## 保留 Tailscale 网络

两台电脑继续登录原有 Tailscale tailnet。确认 Mac 与 Windows 在 Tailscale 中互相可达。Mac 作为服务器，Windows 客户端的“服务器地址”填写 Mac 的 Tailscale IP 或 MagicDNS 名称；保留原有 Deskflow 端口设置（通常为 `24800`）和 TLS 设置。允许 Deskflow Core 通过两台系统的防火墙及 Tailscale 策略。无需更改 Tailscale 路由，也无需使用屏幕边缘切换。

## Deskflow 设置

1. Mac 打开 Deskflow，选择 **Share this computer's input**，将共享模式设为 **Mouse follow mode**。在服务器配置中登记 Windows 客户端显示的电脑名。
2. Windows 打开 Deskflow，选择接收另一台电脑的输入，填入 Mac 的 Tailscale 地址，然后连接。Windows 鼠标始终直接连接 Windows。
3. Mac 授予当前 `Deskflow.app`“辅助功能”和“输入监控”权限。系统已记录旧版应用的授权时，移除旧条目并重新添加当前应用，然后重启 Deskflow。
4. 移动 Mac 触控板，确认状态显示“键盘 → Mac”；移动 Windows 鼠标，确认显示“键盘 → Windows”。分别试打字，检查两边鼠标指针只在本机移动。确认后保持共享模式即可。

这些是现有模式的配置步骤。鼠标点击、滚动和键盘锁定不参与归属切换。

## 验证边界

现有单元测试覆盖协议 1.9 消息、1.7/1.8 客户端不接收新消息、客户端活动采样、服务端键盘路由和断线清理。两套系统分别通过测试，可以检查共同源码的构建和协议处理；真正的 Mac ↔ Windows 输入、Tailscale 连接与系统权限仍需在两台设备上实测。
