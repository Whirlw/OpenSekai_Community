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

本仓库修改自 [kamcdev/OpenSekai_Community](https://github.com/kamcdev/OpenSekai_Community)，并在其基础上添加了**游玩帧率上限**功能。

上游仓库已归档停止维护，此处的改动仅供学习和研究使用。

### 新增：帧率上限

音游对延迟稳定性要求很高，因此帧率不采用「无上限」策略，而是由玩家在设置里手动选择一个上限档位：

- 设置面板新增「帧率」一项，可选 **跟随屏幕 / 120 / 144 / 165 / 185 / 240 Hz**。
- 选中档位后关闭垂直同步（`QualitySettings.vSyncCount = 0`），由 `Application.targetFrameRate` 限帧，避免帧率在负载下大幅波动导致判定手感漂移。
- Android 平台会通过 `WindowManager.LayoutParams.preferredDisplayModeId` 请求面板对应的显示模式（按物理分辨率筛选，取不超过所选档位的最高刷新率）；设备不支持或拿不到显示模式时，自动回退为只设置 `Application.targetFrameRate`。
- 「跟随屏幕」档位保持原有行为，不主动请求高刷新率模式。

### 改动位置

- `Assets/Scripts/Assembly-CSharp/CP/FramerateUtility.cs`：帧率档位定义与 `ApplyFrameRateLimit`，包含 Android 显示模式请求逻辑。
- `Assets/Scripts/Assembly-CSharp/Sekai/LiveSettingData.cs`：新增 `FrameRateLimit` 字段，并将旧的 `Use120FPS` 设置迁移为 120 档。
- `Assets/Scripts/Assembly-CSharp/Sekai/Core/Live/BaseLiveController.cs`：进入 live 时按当前设置应用帧率。
- `Assets/CustomMusicScoreManager/Runtime/UI/ScreenLayerCustomMusicScoreManager.cs`：设置面板中的帧率选择项，以及顶栏标题。

### 环境说明

- 本仓库实际使用的 Unity 版本为 **2022.3.62f3**（上面的开发环境小节沿用上游文档）。
- 上游文档中的 `Assets/Editor/OpenSekaiAssetBundleBuildPipeline.cs`，在本仓库中名为 `Assets/Editor/OjskCommunityAssetBundleBuildPipeline.cs`。
