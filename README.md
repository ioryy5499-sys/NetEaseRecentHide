# 网易云最近任务隐藏（LSPosed）

注意！此模块为chatgpt制作在hyperos4上验证通过

一个最小化 Java / Android Studio 项目：在 `android`（system_server）作用域中 Hook
`com.android.server.wm.RecentTasks.isVisibleRecentTask(...)`，仅将网易云音乐
`com.netease.cloudmusic` 的最近任务可见性判定为 `false`。

## 重要说明

- 这是“隐藏最近任务卡片”的实验性实现，不是后台保活工具。
- 不 Hook 网易云播放器、媒体会话、通知栏或锁屏控制；这些控制应由网易云自己的媒体服务继续提供。
- HyperOS 可能修改系统框架类、方法签名或桌面最近任务实现。项目在未取得你的实际 `framework.jar` / 系统构建信息前，不能保证适配所有 HyperOS 版本。
- 如果系统日志显示找不到 `RecentTasks.isVisibleRecentTask`，本版本不会生效；请关闭模块并提交日志，不要盲目添加更多 system_server Hook。
- `xposed_scope.list` 将默认作用域设为 `android`。在 LSPosed 管理器中确认只给系统框架启用，不要把该模块作用域勾到网易云本身。
- 建议先保留可用的 KernelSU/LSPosed 禁用方式。若开机循环崩溃，进入 LSPosed 禁用模块或通过安全模式恢复。

## 构建环境

- Android Studio（支持 Android Gradle Plugin 8.6.1）
- JDK 17
- Android SDK Platform 35
- Gradle 8.7（Android Studio 可按项目配置下载）
- 网络可访问 Google Maven、Maven Central 与 `https://api.xposed.info/`

打开项目目录 `NetEaseRecentHide/`，等待 Gradle Sync，然后选择 `app` 的 `assembleDebug` 任务。
APK 预期输出位置：`app/build/outputs/apk/debug/app-debug.apk`。

## 安装与启用

1. 编译并安装 APK。
2. 在 LSPosed 管理器中启用“网易云最近任务隐藏”。
3. 作用域只选择 `系统框架 / android`（如果管理器显示 `system`，以设备上实际系统框架进程标识为准）。
4. 重启设备。
5. 打开网易云播放音乐，切到后台；检查通知栏和锁屏媒体控制，再打开最近任务确认卡片是否隐藏。
6. 如果模块不生效，先在 LSPosed 日志中查看 `[NetEaseRecentHide]`，不要先修改播放器或省电策略。

## 回滚

在 LSPosed 管理器中禁用模块并重启。该模块不改写系统分区，也不修改网易云 APK。

## 兼容性限制

该实现针对 AOSP 风格的 `RecentTasks` 可见性路径。部分 OEM 桌面还会在 Launcher/SystemUI 侧二次过滤任务；如果 HyperOS 使用不同逻辑，可能需要额外适配，不能仅凭 Android 版本推断。

注意：该流程当前使用 Android SDK 35 编译，目的是验证项目可构建；它并不意味着已针对 Android 17 / HyperOS 的内部实现完成兼容性验证。
