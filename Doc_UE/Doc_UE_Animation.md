- [Sequencer](#sequencer)
    - [Sequencer的功能](#sequencer的功能)
    - [Sequencer的分类](#sequencer的分类)
    - [Sequencer的基本用法](#sequencer的基本用法)
    - [Sequencer的功能介绍](#sequencer的功能介绍)
      - [轨道内的Actor对象](#轨道内的actor对象)
      - [轨道内Actor对象的重绑定](#轨道内actor对象的重绑定)
      - [轨道 - 镜头切换轨道](#轨道---镜头切换轨道)
      - [轨道 - 镜头轨道](#轨道---镜头轨道)
      - [轨道 - 事件轨道](#轨道---事件轨道)
    - [参考文章](#参考文章)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# Sequencer

[Sequencer](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/how-to-make-movies-in-unreal-engine) 是虚幻引擎的过场动画编辑器，允许用户为动画角色、摄像机、各种属性以及其他Actor制作动画。它提供了一个非线性的编辑环境，允许你按时间轴创建、修改轨道和关键帧

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/UE_Sequencer.gif)

### Sequencer的功能

可以在一个Sequencer资产内 创建不同特定功能的 [轨道](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/sequencer-track-list-in-unreal-engine) 或是 添加成员到轨道Key动画。常用的功能有：

- 相机的创建、切换、表现处理
- [SkeletalMesh](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/skeletal-mesh-assets-in-unreal-engine)的动画播放
- Actor对象（Level内既有/新生成/重绑定）的关键帧动画

### Sequencer的分类

UE内常见的Sequencer资产有：

- **Level Sequence**：是Sequencer的基本文件。Level Sequence 的内部由 **轨道(Tracks)** 构成，各轨道具备独立功能
- **Master Sequence**：实质也是LevelSequence（默认自动创建了镜头轨道），主要用于 拼接组合多个Level Sequence

![](https://pic1.zhimg.com/v2-a0ce5c61aff418b2a737c8830da09d4a_1440w.jpg)

![LevelSequence轨道](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250801211212359.png)

### Sequencer的基本用法

首先创建Sequencer资产，以 Level Sequence为例：

在内容浏览器中点击右键空白区域，选择 **过场动画（Cinematics） > 关卡序列（Level Sequence）** 即可创建，双击后就可打开Sequencer编辑器进行编辑，注意：Play时建议把Sequencer编辑窗口关闭、避免运行时显示异常

![image-20250801193535590](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250801193535590.png)

后就可以在逻辑层对这个Sequence进行Play播放了：核心API是 `ULevelSequencePlayer::CreateLevelSequencePlayer()`、`UMovieSceneSequencePlayer::Play()`，同时也可以 设置播放参数、监听播放事件等

![image-20250801194038593](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250801194038593.png)

![image-20250801193952902](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250801193952902.png)

![20250801_163229](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250801_163229.gif)

### Sequencer的功能介绍

#### 轨道内的Actor对象

我们可以在轨道内 添加Actor对象并对其Key动画等，针对此Actor对象的来源，可将其分为2类：

- [可持有对象（Possessable）](https://dev.epicgames.com/documentation/en-us/unreal-engine/spawn-temporary-actors-in-unreal-engine-cinematics#possessable)：
  - 含义：即 把**Level内已有的对象** 添加到轨道的的Actor。其原本Actor的生命周期 不受Sequencer影响
  - 适用范围：针对Level内持久性Actor的过场动画
- [可生成对象（Spawnable）](https://dev.epicgames.com/documentation/en-us/unreal-engine/spawn-temporary-actors-in-unreal-engine-cinematics#spawnable)：
  - 含义：即 **因Sequencer所需而生成的临时性Actor**。其Actor的创建、显示、销毁等生命周期 受到Sequencer控制，关闭Sequencer编辑窗口后将连带消失、不存在于Level
  - 适用范围：不会在Level内持久存在、仅需Sequencer临时Actor的过场动画
  - 辨别：Actor图标上带黄色闪电的

![PossessableActor](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250803163452038.png)

![SpawnableActor](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250803163738104.png)

#### 轨道内Actor对象的重绑定

一种常见情况例如：CharacterActor 不是在Level内预先放置、而是运行时人为放置的，而制作过场动画Sequence时，用的是 美术Map下的同CharacterActor，那这样的情况下就需要 **设置Sequence内CharacterActor重绑定为运行态时的CharacterActor**，即为 **Sequence内Actor的重绑定**


Sequence内Actor重绑定的基本流程为：

1. Actor放置于所需轨道，设定其绑定方式：
   - Sequencer编辑窗口内，直接设置Actor的绑定端点：即 蓝图内创建绑定函数，返值重绑定对象
   - Sequence播放前，执行`ALevelSequenceActor::AddBinding()`等方法，直接对 目标Sequence资产内的目标可绑端点，进行绑定操作，才播放Sequence。具体可参阅 [用Sequencer在蓝图中重新绑定Actor - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/change-cinematic-track-bindings-in-unreal-engine)
2. 绑定完成后方可正常播放，后续亦可 移除绑定、重设绑定等


一个重绑定CharacterActor的示例如：Sequence内制作CharacterActor的序列帧动画，设置其为 **可持有对象**，且设置 其绑定端点为 PlayerPawn（[UE内设方法](https://dev.epicgames.com/documentation/en-us/unreal-engine/dynamic-binding-in-sequencer#quickbind)）。则当 运行游戏、播放Sequence时，根据方法获取到 PlayerPawn、进行重绑定节点、正式开播Sequence

![image-20250803180941620](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250803180941620.png)

![20250803_185034](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250803_185034.gif)

除此之外，针对普通的Actor，也可进行重绑定：[用Sequencer在蓝图中重新绑定Actor - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/change-cinematic-track-bindings-in-unreal-engine)（原理同上）

TODO：针对 可生成对象类型的Actor的重绑定？是否能绑定到指定Asset

#### 轨道 - [镜头切换轨道](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/cinematic-camera-cut-track-in-unreal-engine)

主要功能为 处理多个相机下、相互切换混合 **最终呈现镜头**。直白点就是 多个相机能拍多个各自的画面，可以是筛选所需画面、陈列于所需时间轴节点上。官方文档：[创建摄像机动画 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/how-to-animate-cinematic-cameras-in-unreal-engine)

首先需要创建 相机剪切轨道：可人为在Sequencer编辑窗口 添加->相机切换轨道，或是 首次新建相机或把现有相机拖入，会自动创建 相机切换轨道

![image-20250801195523238](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250801195523238.png)

我们可以对目标相机Key动画等，但若是想要把此相机的画面呈现于 最终画面，则需要将其添加到 相机切换轨道内，那此段相机的镜头才能被呈现、剪辑、混合

![image-20250801201459204](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250801201459204.png)

![20250801_202433](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250801_202433.gif)

当然也可以把Character身上的相机加入轨道进行编辑：

（同时建议设置此相机的 属性->完成时->保持状态，以便在Sequence播放完后 恢复此相机的原有状态）

![20250801_205521](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250801_205521.gif)

![image-20250801210200179](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250801210200179.png)

#### 轨道 - [镜头轨道](https://dev.epicgames.com/documentation/en-us/unreal-engine/sequences-shots-and-takes-in-unreal-engine?application_version=5.5#shots)

主要功能为 承载多个Level Sequence片段，并可再次进行剪辑、混合

![20250802_213531](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250802_213531.gif)

#### 轨道 - [事件轨道](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/cinematic-event-track-in-unreal-engine)

主要功能为 在轨道上触发绑定的事件。针对事件的触发方式 又分为2类事件轨道：

- [触发器 Trigger](https://dev.epicgames.com/documentation/en-us/unreal-engine/cinematic-event-track-in-unreal-engine#triggerevents)：绑定事件后，在目标 **关键帧节点 单次触发事件**。轨道内各节点可绑定各自事件
- [重复器 Repeater](https://dev.epicgames.com/documentation/en-us/unreal-engine/cinematic-event-track-in-unreal-engine#repeaterevents)：绑定事件后，在目标 **轨道片段内 每帧触发事件**。轨道内各片段可绑定各自事件

![触发器](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250803153723424.png)

![重复器](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250803154843398.png)

对于绑定的事件，有UE提供的 快速绑定事件，或自行在 Sequence的蓝图（即 [导演蓝图](https://dev.epicgames.com/documentation/en-us/unreal-engine/cinematic-event-track-in-unreal-engine#directorblueprint)）内创建Event提供绑定

![image-20250803155512545](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250803155512545.png)

### 参考文章

- [过场动画和Sequencer - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/cinematics-and-movie-making-in-unreal-engine)
- [UE4中的动画编辑器 —— Sequencer in UE4 - 知乎](https://zhuanlan.zhihu.com/p/386200585)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)