# 网易云最近任务隐藏（LSPosed / 旧版 Xposed API）

让网易云音乐的任务卡片不出现在「最近任务」里，后台播放、通知栏、锁屏控制都不受影响。

## 原理

只在 `com.netease.cloudmusic` 进程内 Hook `Activity.onCreate / onResume`，
调用 `ActivityManager.AppTask.setExcludeFromRecents(true)`，
等同于给网易云的任务加上 `excludeFromRecents`。
不 Hook system_server，不需要重启手机，也不会因为系统类变化导致开机循环。

## 构建（手机用户：GitHub Actions）

1. 把整个项目上传到 GitHub 仓库。
2. 仓库 **Actions** → **Build LSPosed APK** → **Run workflow**。
3. 完成后在页面底部 **Artifacts** 下载 `NetEaseRecentHide-debug-apk`，解压得到 `app-debug.apk`。

本地构建：Android Studio + JDK 17，执行 `assembleDebug`。

## 安装与启用

1. 安装 APK。
2. LSPosed 里启用模块，作用域勾选「网易云音乐」（已推荐，通常自动勾选）。
3. 在系统设置里强行停止网易云，再重新打开（不需要重启手机）。
4. 播放音乐，回桌面，打开最近任务，确认卡片消失；再看通知栏播放控制是否正常。

## 排查

- LSPosed 日志里搜 `[NetEaseRecentHide]`：
  - 有 `hooks installed`：模块已加载。
  - 有 `task excluded from recents`：标记已生效。
- 卡片还在：先强行停止网易云重进一次；仍然在，把日志发出来。
- 想改成其他应用：改 `HideRecentModule.java` 里的 `TARGET_PACKAGE` 和 `res/values/arrays.xml`。

## 已知限制

- 卡片隐藏后，只能从桌面图标或通知栏回到网易云。
- 部分系统的「一键清理」可能仍会杀掉后台进程，这和最近任务卡片是否显示无关，需要在省电策略里给网易云放行。

## 回滚

LSPosed 里禁用模块，再强行停止网易云。
