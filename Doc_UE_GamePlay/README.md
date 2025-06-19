# Unreal Engine - GamePlay框架

![](https://d1iv7db44yhgxn.cloudfront.net/documentation/images/e48f12be-54cd-419c-93b0-47715d80e51d/gameplay-schematic.png)



## UObject、Actor、Component

### UObject

> UObject是所有UE内物体的基类，其提供了系列基础功能（对象管理、序列化、反射、GC、BP支持、编辑器支持等）供派生类使用

![image-20250507145353403](Pic/image-20250507145353403.png)

#### UObject的核心功能

- 对象管理：能被UE识别、追踪、管理

- 序列化：能动态加载（如LoadObject()）和序列化到本地（如 `.uasset`文件）
- 反射：允许程序运行时动态查询和操作类和对象信息
- GC：被UE追踪后的对象，才能进入垃圾回收系统
- BP支持：与蓝图对象交互
- 编辑器支持：在编辑器界面显示类的成员

#### UObject的结构

例如 创建一个UObject的派生类 UMyObject 后，其代码结构如下：

![image-20250507154822558](Pic/image-20250507154822558.png)

其中的 `UCLASS()`宏用于标记此为UObject的派生类、划归UE进行管理

而`GENERATED_BODY()`宏则会 展开生成必要代码以支持UE的反射系统、对UE的属性系统（`UFUNCTION`、`UFUNCTION`）提供支持、等


#### 参考文章

- [《InsideUE4》UObject（一）开篇 ](https://zhuanlan.zhihu.com/p/24319968)
- [Unreal Engine 中 GENERATED_BODY() 的作用](https://blog.csdn.net/gameatp/article/details/145934358)



### Actor

>  Actor派生自UObject，是UE内常用的一个基类，提供了 生命周期、组件搭载、各类交互等功能

#### Actor的核心功能

- 生命周期：
  - BeginPlay：游戏开始或Actor生成时调用
  - EndPlay：游戏结束或Actor销毁调用
  - Tick：每帧调用

![image-20250507170836682](Pic/image-20250507170836682.png)

- 组件搭载：不同功能的Component可附着到Actor上
  - Actor在场景内的位置、缩放等，就是由 `USceneComponent`类型的`RootComponent`成员实现

![image-20250507171131596](Pic/image-20250507171131596.png)

![image-20250507171519058](Pic/image-20250507171519058.png)

- 各类交互：
  - 碰撞、触发事件
  - 玩家输入
  - 蓝图控制

![image-20250507171839934](Pic/image-20250507171839934.png)

#### Actor的生命周期

[Actor的生命周期](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-actor-lifecycle) 包含生成及销毁

**Actor生成：**

1. 从磁盘加载（Load From Disk）：已经处在Level中的Actor，发生 [UEngine::LoadMap](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/Runtime/Engine/Engine/UEngine/LoadMap) 发生时，或当 [关卡流送](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/level-streaming-in-unreal-engine) 调用 [UWorld::AddToWorld](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/Runtime/Engine/Engine/UWorld/AddToWorld)
2. 在编辑器中运行（Play in Editor）：也就是编辑器状态下运行
3. 生成（Spawning）：也就是实例化Actor到世界
4. 延迟生成（Deffered Spawn）：将Actor的任意成员的属性设置为 Expose on Spawn，即可延迟Actor的生成

**Actor的销毁：**发生如下情形时将调用 [AActor::EndPlay](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/Runtime/Engine/GameFramework/AActor/EndPlay)，后续进入GC流程

- 显式调用 [AActor::Destroy](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/Runtime/Engine/GameFramework/AActor/Destroy)
- "在编辑器中运行（Play in Editor）"终结
- 关卡过渡（无缝行程或加载地图）
- 包含Actor的流送关卡被卸载
- Actor的生命周期已过
- 应用程序关闭（全部Actor被销毁）

![](https://d1iv7db44yhgxn.cloudfront.net/documentation/images/e63c12cc-0186-4e34-a7bc-5ca774b0bce6/actorlifecycle1.png)



### Component

> UActorComponent 派生自 UObject，用途是 作为特定功能单元，附着到Actor上

#### UActorComponent的核心功能

- 附着到Actor：

![image-20250507173530029](Pic/image-20250507173530029.png)

- 生命周期：

![image-20250507173608498](Pic/image-20250507173608498.png)

#### 常用的Component

##### SceneComponent 场景组件

> SceneComponent 派生自 UActorComponent，具备 空间变换（位置、缩放、旋转）、SceneComponent嵌套功能

常见的 `SceneComponent`的派生类由：

- `UStaticMeshComponent`：显示静态网络模型
- `USkeletalMeshComponent`：显示骨骼网络模型
- `UCameraComponent`：相机
- `USpringArmComponent`：弹簧臂
- `UBoxComponent` / `USphereComponent `/ `UCapsuleComponent`：碰撞/触发器

##### UActorComponent的派生类

- `UAudioComponent`：播放音效

- `UPhysicsComponent`：实现物理行为

- `UTimelineComponent`：实现动画时间轴

- `UPostProcessComponent`：实现后期处理效果

  

### 参考文章

- [UEGamePlay框架：UObject，Actor，Component ](https://zhuanlan.zhihu.com/p/15846253240)

- [《InsideUE4》GamePlay架构（一）Actor和Component ](https://zhuanlan.zhihu.com/p/22833151)

- [[浅析UE4 Actor&Actor生命周期](https://www.cnblogs.com/CatSevenMillion/p/16653763.html)](https://www.cnblogs.com/CatSevenMillion/p/16653763.html)



## Pawn

> [Pawn](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/pawn-in-unreal-engine) 是被玩家或AI所控制的 所有Actor的基类，是玩家或AI在游戏场景中的具化体现（表现+行为）

### Pawn的核心功能

1. 可被 `AController` 控制
   - `APlayerController`：玩家控制
   - `AAIController`：AI控制

![image-20250510154033663](Pic/image-20250510154033663.png)

2. 具有比 AActor 更强大的输入管理

![image-20250510155149664](Pic/image-20250510155149664.png)

### Pawn的派生类

#### DefaultPawn

派生自Pawn，带有 DefaultPawnMovementComponent、CollisionComponent和StaticMeshComponent等

#### SpectatorPawn

派生自DefaultPawn，不带重力并关闭了StaticMesh，常用于观战视角

#### Character

派生自Pawn，带有 CharacterMovementComponent、CapsuleComponent、USkeletalMeshComponent等，常用于人型单位

![](https://pic2.zhimg.com/v2-e3e8606aa67344bf178fd9097d249693_1440w.png)


### 参考文章

- [《InsideUE4》GamePlay架构（四）Pawn](https://zhuanlan.zhihu.com/p/23321666?refer=insideue4)



## Controller

> AController 派生自Actor，负责将 玩家/AI控制 作为系列行为指令 传递到所操控的Pawn

![](https://pic4.zhimg.com/v2-4151952d1f2ab74fcc78d7c3bd215e0d_1440w.jpg)

### Controller的核心功能

1. 接收、管理玩家/AI输入
2. 传递行为指令 到所操控的Pawn上
3. 协调网络同步：多人游戏中正确同步玩家状态

### Controller的派生类

#### APlayerController

**玩家控制** 的控制器，从玩家的输入设备（键盘、鼠标、游戏手柄等）获取输入、转换指令、传递到Pawn

涉及模块：

- Camera管理：通过`PlayerCameraManager`类以控制玩家相机
- Input系统
- UPlayer关联
- HUD显示：在控制器的相机前显示的UI
- Level切换
- Voice：网络语音

![](https://pic3.zhimg.com/v2-88131e55febd8886e0f3c87b92c526e8_1440w.png)

#### AAIController

**AI控制** 的控制器，从决策树、行为树（Behavior Tree）或其他 AI 逻辑来控制Pawn

涉及模块：

- Navigation
- AI组件
- Task系统

![](https://pic1.zhimg.com/v2-a0c2148ff8331da1b70ab4157e19f1c2_1440w.png)

### 参考文章

- [《InsideUE4》GamePlay架构（五）Controller](https://zhuanlan.zhihu.com/p/23480071)

- [《InsideUE4》GamePlay架构（六）PlayerController和AIController](https://zhuanlan.zhihu.com/p/23649987)

- [UEGamePlay框架：Pawn，Controller，APlayerState](https://zhuanlan.zhihu.com/p/16087843925)



## Level、World

**Level** 是Actor的容器

![](https://pic1.zhimg.com/v2-bca44e1f846c37b12f08bc0a6659b4ae_1440w.png)

**World** 由至少一个Level组成，负责Level的管理

Level在World内可分为2类：

- **Persistent Level（持久关卡）**：核心关卡，始终加载，负责管理其他子关卡的动态加载/卸载。通常为 包含基础场景元素（关照、天空盒）的Level
- **Streaming Levels（流送关卡）**：通过流送体积（Level Streaming Volumes）、蓝图或代码动态加载的子关卡，按需加载/卸载
  - 流送体积：当玩家进入体积区域时触发加载，离开时卸载
  - 蓝图/C++控制：使用`Load Stream Level`和`Unload Stream Level`函数控制
  - 固定加载（Always Loaded）：某些子关卡可能与持久关卡同时加载，常用于多人协作开发的分层设计

![](https://pic4.zhimg.com/v2-41963e6f39bcefb2d799d31bec703759_1440w.png)



### 参考文章

- [《InsideUE4》GamePlay架构（二）Level和World - 知乎](https://zhuanlan.zhihu.com/p/22924838)
- [UE4 World, Level, LevelStreaming从入门到深入 - CSDN](https://blog.csdn.net/qq_29523119/article/details/145841523)
- [UE4中Info、LevelScriptActor和Level - 博客园](https://www.cnblogs.com/hxhspace/p/16132483.html)
- [关卡流送概述 - EnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/level-streaming-overview-in-unreal-engine)



## GameMode

**GameMode** 负责处理游戏的 **基础规则、逻辑、流程**

![](https://pic4.zhimg.com/v2-df7c0a12f2b806dbb60e84a95ae9758d_1440w.png)

涉及模块：

1. 核心Class登记：

![image-20250618161709640](Pic/image-20250618161709640.png)

2. 游戏实体的Spawn：
   - 玩家的Pawn，PlayerController，AIController
3. 游戏进度控制：
   - 游戏的暂停、重启
4. Level的切换
5. 多人游戏的同步


### 创建及配置

从 `AGameModeBase` 即可派生出 自定义的GameMode

后可在C++层就登记Class；或是 又可派生出BP子类，在BP子类内登记Class（下图），优势在于 无需调整代码即可编辑

```c++
//AMyGameModeBase.cpp
#include "MyGameModeBase.h"

#include "MyGameStateBase.h"
#include "GameFramework/HUD.h"

AMyGameModeBase::AMyGameModeBase()
{
	GameStateClass = AMyGameStateBase::StaticClass();
	HUDClass = AHUD::StaticClass();
}
```

![image-20250618163340737](Pic/image-20250618163340737.png)

在实现自定义的GameMode后，可将其指定为 **默认的GameMode**。作用是 Level未指定GameMode下 默认使用此GameMode

![image-20250618163614379](Pic/image-20250618163614379.png)

或将其指定为 **Level的 GameMode**。但注意 GameMode在World中仅存在唯一实例，在 Persistent Level+Streaming Levels 的情况下，仅会采用 Persistent Level 所指定的GameMode

![image-20250618164352434](Pic/image-20250618164352434.png)


### 参考文章

- [《InsideUE4》GamePlay架构（七）GameMode和GameState](https://zhuanlan.zhihu.com/p/23707588)
- [Unreal Engine的Gameplay框架和重点 - 放牛的星星 - 知乎](https://zhuanlan.zhihu.com/p/612837045)
- [阿赵UE引擎C++编程学习笔记——GameMode和生命周期 - CSDN](https://blog.csdn.net/liweizhao/article/details/138141220)
- [UE5中GameMode、玩家角色等的设置与获取 - 知乎](https://zhuanlan.zhihu.com/p/560971474)
- [UEGamePlay框架：GameMode，GameState - 知乎](https://zhuanlan.zhihu.com/p/16089018768)
- [Game Mode 和 Game State - EnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/game-mode-and-game-state-in-unreal-engine)



## GameState

**GameState** 负责管理、储存游戏的 **状态数据**。从概念上而言，GameState 应该管理所有已连接客户端已知的信息（特定于 Game Mode 但不特定于任何个体玩家）

![](https://picx.zhimg.com/v2-336fd667fb674bf176fa198741eec129_1440w.png)

### 参考文章

- [《InsideUE4》GamePlay架构（七）GameMode和GameState](https://zhuanlan.zhihu.com/p/23707588)
- [Unreal Engine的Gameplay框架和重点 - 放牛的星星 - 知乎](https://zhuanlan.zhihu.com/p/612837045)
- [UEGamePlay框架：GameMode，GameState - 知乎](https://zhuanlan.zhihu.com/p/16089018768)
- [Game Mode 和 Game State - EnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/game-mode-and-game-state-in-unreal-engine)



## UPlayer、ULocalPlayer

**UPlayer** 是 管理、储存玩家信息的 辅助类，一个UPlayer可代表 一个玩家（本地或远程玩家）

> 本地玩家：通过本地设备输入的玩家
>
> 远程玩家：通过网络连接的玩家

主要功能：

- 管理玩家输入：各玩家的输入都传递到UPlayer
- 管理与玩家的连接：网络游戏内，UPlayer代表和管理各玩家的客户端连接
- 持有PlayerController

![](https://pica.zhimg.com/v2-f1fee0eaf2b5fea87085f71895f4e730_1440w.png)

**ULocalPlayer** 派生自 UPlayer，负责处理 本地玩家 在单机游戏或本地多人游戏中的 输入、控制、会话管理等，一个ULoalPlayer代表 一个本地玩家

主要功能：

- 管理输入设备：从本地设备接收输入
- 管理玩家会话：跟踪玩家的输入、角色和游戏状态
- 区别开本地玩家与远程玩家：远程玩家由 UPlayer+APlayerController管理，本地玩家由ULocalPlayer管理
- 游戏视角控制：控制玩家视角、相机、输入绑定


![](https://pic4.zhimg.com/v2-0cdc26e0b66ec808197a76cb8b09350d_1440w.png)


### 参考文章

- [《InsideUE4》GamePlay架构（八）Player](https://zhuanlan.zhihu.com/p/23826859)
- [UEGamePlay框架：UPlayer，ULocalPlayer，UNetConnection - 知乎](https://zhuanlan.zhihu.com/p/16089379054)



# GamePlay框架的 Runtime启动流程

![image-20250510155149664](Pic/UE-GamePlay框架的Runtime流程.png)

![image-20250510155149664](Pic/UE-GamePlay框架的Runtime流程2.png)

## 参考文章

- [Unreal Engine 的启动流程 - 放牛的星星 - 知乎](https://zhuanlan.zhihu.com/p/610523485)
- [UE4 Gameplay之GameMode流程分析(一) - 孤傲雕 - 知乎](https://zhuanlan.zhihu.com/p/70045930)
