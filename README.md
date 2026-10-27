# OpenSekai

OpenSekai 是一个用于学习和研究目的的 Project Sekai 音乐游戏玩法复刻项目，目前提供可用的**谱面编辑器**、**第三方歌曲包支持**，以及**从编辑器进入 live 测试游玩**的完整流程。

## 开发环境

- Unity: 2022.3.62f2
- 渲染管线: URP 14
- 主要依赖: TextMesh Pro、UniTask、MessagePack、uPalette、UI Particle、SoftMaskForUGUI

## 目录说明

- `Assets/Scripts/Assembly-CSharp/Sekai`：主要游戏逻辑。
- `Assets/Resources`：运行时通过 `Resources` 加载的 Prefab、文本、字体、特效和界面数据。
- `Assets/Sekai/assetbundle/resources`：原样放置的 AssetBundle 源资源，资源本身应保留 AssetBundle 名称，之后通过构建流程打包。
- `Assets/StreamingAssets`：打包后的 AssetBundle 与本地运行数据输出位置，主要用于构建后的运行环境。
- `Assets/Editor/OpenSekaiAssetBundleBuildPipeline.cs`：本地 AssetBundle 构建流程。

## 资源说明

本仓库中的代码以 MIT 协议发布。第三方库、字体、音频、贴图、prefab、shader 参考资源以及任何可能来源于原作或其他权利方的资源，不自动包含在 MIT 授权范围内，请分别遵循其原始许可和权利归属。

本项目与任何原作游戏、发行方或权利方没有官方关联。

------

## 关于本仓库

本仓库修改自 [kamcdev/OpenSekai_Community](https://github.com/kamcdev/OpenSekai_Community)，并在其基础上**开放了高帧率**支持。

上游仓库已归档停止维护，此处的改动仅供学习和研究使用。

### 新增：高帧率（120 / 144 / 165 / 185 / 240 Hz）

原版设置里虽然有一个 `Use120FPS` 开关，但没有任何代码读取它；运行时只是把 `Application.targetFrameRate` 设成屏幕当前刷新率，而 `QualitySettings` 各档位的垂直同步是开着的（当前使用的 Ultra 档 `vSyncCount: 1`），这个值根本不会生效。同时也没有主动请求高刷新率的显示模式，跑多少帧完全取决于系统分给应用的显示模式，玩家没有任何选择余地。

现在设置面板新增「帧率」一项，可选 **跟随屏幕 / 120 / 144 / 165 / 185 / 240 Hz**：

- 选定档位后关闭垂直同步（`QualitySettings.vSyncCount = 0`），改由 `Application.targetFrameRate` 驱动，帧率才真正解锁到所选档位。
- Android 平台会通过 `WindowManager.LayoutParams.preferredDisplayModeId` 请求与所选档位匹配的显示模式：先按物理分辨率筛出同一块屏的模式，再取不超过所选档位的最高刷新率，让面板真正切到 120 / 144 / 165 / 185 / 240。
- 设备拿不到可用的显示模式时会自动回退，只设置 `Application.targetFrameRate`，不会黑屏或报错退出。
- 「跟随屏幕」保持原版行为（跟随屏幕当前刷新率），不主动请求高刷模式。

音游对延迟稳定性要求很高，所以帧率不是「能多高就多高」，而是由玩家挑一个固定档位：帧率频繁抖动会让判定手感漂移，固定档位反而更稳。

### 改动位置

- `Assets/Scripts/Assembly-CSharp/CP/FramerateUtility.cs`：帧率档位的定义与应用（`ApplyFrameRateLimit`），包含 Android 显示模式请求逻辑。
- `Assets/Scripts/Assembly-CSharp/Sekai/LiveSettingData.cs`：新增 `FrameRateLimit` 字段保存所选档位，旧的 `Use120FPS` 设置会迁移到 120 档。
- `Assets/Scripts/Assembly-CSharp/Sekai/Core/Live/BaseLiveController.cs`：进入 live 时按当前设置应用帧率。
- `Assets/CustomMusicScoreManager/Runtime/UI/ScreenLayerCustomMusicScoreManager.cs`：设置面板中的帧率选择项，以及顶栏标题。

### 环境说明

- 本仓库实际使用的 Unity 版本为 **2022.3.62f3**（上面的开发环境小节沿用上游文档）。
- 上游文档中的 `Assets/Editor/OpenSekaiAssetBundleBuildPipeline.cs`，在本仓库中名为 `Assets/Editor/OjskCommunityAssetBundleBuildPipeline.cs`。
