# Unreal Engine - GamePlay框架

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/UE_GamePlay.png)



## UObject、Actor、Component

### UObject

> UObject是所有UE内物体的基类，其提供了系列基础功能（对象管理、序列化、反射、GC、BP支持、编辑器支持等）供派生类使用

![image-20250507145353403](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250507145353403.png)

#### UObject的核心功能

- 对象管理：能被UE识别、追踪、管理

- 序列化：能动态加载（如LoadObject()）和序列化到本地（如 `.uasset`文件）
- 反射：允许程序运行时动态查询和操作类和对象信息
- GC：被UE追踪后的对象，才能进入垃圾回收系统
- BP支持：与蓝图对象交互
- 编辑器支持：在编辑器界面显示类的成员

#### UObject的结构

例如 创建一个UObject的派生类 UMyObject 后，其代码结构如下：

![image-20250507154822558](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250507154822558.png)

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

![image-20250507170836682](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250507170836682.png)

- 组件搭载：不同功能的Component可附着到Actor上
  - Actor在场景内的位置、缩放等，就是由 `USceneComponent`类型的`RootComponent`成员实现

![image-20250507171131596](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250507171131596.png)

![image-20250507171519058](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250507171519058.png)

- 各类交互：
  - 碰撞、触发事件
  - 玩家输入
  - 蓝图控制

![image-20250507171839934](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250507171839934.png)

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

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/UE_GamePlay_ActorLifeCycle1.png)



### Component

> UActorComponent 派生自 UObject，用途是 作为特定功能单元，附着到Actor上

#### UActorComponent的核心功能

- 附着到Actor：

![image-20250507173530029](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250507173530029.png)

- 生命周期：

![image-20250507173608498](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250507173608498.png)

#### UActorComponent的派生类

`UActorComponent` 是所有组件的基类，根据使用场景，常分为3类组件：

- **Actor组件**（`UActorComponent`类）：处理 非物理相关、不需要几何表现的 抽象行为
  - 使用示例：移动、物品栏或属性管理等
- **场景组件**（`USceneComponent`、`UActorComponent` 的子类）：处理 物理相关、不需要几何表现的 行为
  - 使用示例：弹簧臂、摄像机、物理力、约束、音频等
- **Primitive组件**（`UPrimitiveComponent`、`USceneComponent` 的子类）：处理 几何表现相关的 行为
  - 使用示例：静态/骨骼网格体、球体碰撞体积、粒子系统等



常见的组件有：

- [`USceneComponent`](https://dev.epicgames.com/documentation/en-us/unreal-engine/components-in-unreal-engine#scenecomponents)：具备 空间变换（位置、缩放、旋转）、SceneComponent嵌套功能
- [`UPrimitiveComponent`](https://dev.epicgames.com/documentation/en-us/unreal-engine/components-in-unreal-engine#primitivecomponents)：基元组件，包含或生成某类几何的场景组件，通常用于渲染或碰撞

- `UAudioComponent`：播放音效

- `UPhysicsComponent`：实现物理行为

- `UTimelineComponent`：实现动画时间轴

- `UPostProcessComponent`：实现后期处理效果

- `UStaticMeshComponent`：显示静态网络模型

- `USkeletalMeshComponent`：显示骨骼网络模型
- `UCameraComponent`：相机
- `USpringArmComponent`：弹簧臂
- `UBoxComponent` / `USphereComponent `/ `UCapsuleComponent`：碰撞/触发器



### 参考文章

- [UEGamePlay框架：UObject，Actor，Component ](https://zhuanlan.zhihu.com/p/15846253240)

- [《InsideUE4》GamePlay架构（一）Actor和Component ](https://zhuanlan.zhihu.com/p/22833151)

- [[浅析UE4 Actor&Actor生命周期](https://www.cnblogs.com/CatSevenMillion/p/16653763.html)](https://www.cnblogs.com/CatSevenMillion/p/16653763.html)

- [Actor组件 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/components-in-unreal-engine)


## Pawn

> [Pawn](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/pawn-in-unreal-engine) 是被玩家或AI所控制的 所有Actor的基类，是玩家或AI在游戏场景中的具化体现（表现+行为）

### Pawn的核心功能

1. 可被 `AController` 控制
   - `APlayerController`：玩家控制
   - `AAIController`：AI控制

![image-20250510154033663](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250510154033663.png)

2. 具有比 AActor 更强大的输入管理

![image-20250510155149664](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250510155149664.png)

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

![image-20250618161709640](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250618161709640.png)

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

![image-20250618163340737](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250618163340737.png)

在实现自定义的GameMode后，可将其指定为 **默认的GameMode**。作用是 Level未指定GameMode下 默认使用此GameMode

![image-20250618163614379](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250618163614379.png)

或将其指定为 **Level的 GameMode**。但注意 GameMode在World中仅存在唯一实例，在 Persistent Level+Streaming Levels 的情况下，仅会采用 Persistent Level 所指定的GameMode

![image-20250618164352434](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250618164352434.png)


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



## GameInstance

**GameInstance** 负责 管理游戏的生命周期、全局状态、跨Level数据的数据保存。其在整个游戏中 **全程唯一存在**，当游戏结束时才销毁

主要功能：

- 全局数据管理：例如 全局设置（分辨率、音量）、跨Level的数据（分数、存档）
- 与 主菜单、游戏模式的交互：加载主菜单、初始化网络连接、保存设置等
- 资源管理：管理全局资源（音频设置、网络设置、玩家设置等）

![](https://pica.zhimg.com/v2-e3572a2d46608304109104e6174688d8_1440w.png)

### 参考文章

- [《InsideUE4》GamePlay架构（九）GameInstance](https://zhuanlan.zhihu.com/p/24005952)
- [UEGamePlay框架：GameInstance - 知乎](https://zhuanlan.zhihu.com/p/16089742496)



## Subsystems

Subsystems是一套 可定义的类的框架，其能被UE 自动实例化 并 自动托管生命周期

目前可从UE预定义的5个父类派生：

- `UEngineSubsystem`

- `UEditorSubsystem`

- `UGameInstanceSubsystem`

- `UWorldSubsystem`

- `ULocalPlayerSubsystem`

Subsystems的作用：

- 实现类似 单例模式 的效果，同时 避免重复编写类似代码、避免自行管理生命周期
- 模块化：例如全局性的游戏系统，以往可在`UGameInstance`的派生类内写，现可以转到 各`UGameInstanceSubsystem`里写

### 示例

以 `UGameInstanceSubsystem` 为例，派生出 `UMyScoreSubsystem`：

```c++
//UMyScoreSubsystem.h
#include "CoreMinimal.h"
#include "Subsystems/GameInstanceSubsystem.h"
#include "MyScoreSubsystem.generated.h"

UCLASS()
class [PROJECTNAME]_API UMyScoreSubsystem : public UGameInstanceSubsystem
{
	GENERATED_BODY()

public:
	virtual bool ShouldCreateSubsystem(UObject* Outer) const override { return true; };
	virtual void Initialize(FSubsystemCollectionBase& Collection) override;
	virtual void Deinitialize() override;

	UFUNCTION(BlueprintCallable)
	void AddScore(float delta);

	UPROPERTY(EditAnywhere, BlueprintReadWrite)
	float Score;
};
```

可通过C++或蓝图进行访问

```c++
// 方法1：
UGameInstance* GameInstance = UGameplayStatics::GetGameInstance(GetWorld());
UMyScoreSubsystem* MyScoreSubsystem = GameInstance->GetSubsystem<UMyScoreSubsystem>();
MyScoreSubsystem->AddScore(1);
UE_LOG(LogTemp, Log, TEXT("MyScore = %f"), MyScoreSubsystem->Score);

// 方法2：通过USubsystemBlueprintLibrary内的API
UGameInstanceSubsystem* GameInstanceSySubsystem = USubsystemBlueprintLibrary::GetGameInstanceSubsystem(GetWorld(), UMyScoreSubsystem::StaticClass());
MyScoreSubsystem = Cast<UMyScoreSubsystem>(GameInstanceSySubsystem);
if (MyScoreSubsystem != nullptr)
{
    MyScoreSubsystem->AddScore(5);
    UE_LOG(LogTemp, Log, TEXT("MyScore = %f"), MyScoreSubsystem->Score);
}
```

![image-20250624153523499](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250624153523499.png)

其他几类Subsystem的访问例如下：

```c++
// UMyEngineSubsystem
UMyEngineSubsystem* MySubsystem = GEngine->GetEngineSubsystem<UMyEngineSubsystem>();

// UMyEditorSubsystem
UMyEditorSubsystem* MySubsystem = GEditor->GetEditorSubsystem<UMyEditorSubsystem>();

// UMyGameInstanceSubsystem
UGameInstance* GameInstance = UGameplayStatics::GetGameInstance(...);
UMyGameInstanceSubsystem* MySubsystem = GameInstance->GetSubsystem<UMyGameInstanceSubsystem>();

// UMyWorldSubsystem
UWorld* World = MyActor->GetWorld();
UMyWorldSubsystem* MySubsystem=World->GetSubsystem<UMyWorldSubsystem>();

// UMyLocalPlayerSubsystem
ULocalPlayer* LocalPlayer = Cast<ULocalPlayer>(PlayerController->Player)
UMyLocalPlayerSubsystem * MySubsystem = LocalPlayer->GetSubsystem<UMyLocalPlayerSubsystem>();
```

![image-20250624154215497](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250624154215497.png)

### 参考文章

- [《InsideUE4》GamePlay架构（十一）Subsystems](https://zhuanlan.zhihu.com/p/158717151)



## SaveGame、LoadGame

通过对 `USaveGame` 或其派生类进行 序列化/反序列化 到本地，实现存档/读档 机制

存档文件 `XXX.sav`，默认位于 项目根目录/Saved/SaveGames 内

|      类型       |              适用情景              | 备注 |
| :-------------: | :--------------------------------: | :--: |
|  同步保存/加载  |        数据量小、游戏暂停时        |      |
|  异步保存/加载  | 数据量大、游戏非暂停、需要避免卡顿 |      |
| 二进制保存/加载 |                                    |      |

以 [UGameplayStatics::SaveGameToSlot](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/Runtime/Engine/Kismet/UGameplayStatics/SaveGameToSlot?application_version=5.5) 为例，存读档相关API的核心参数说明：

- `FString & SlotName`：存档名，辨别此份存档的唯一名称，且本地存档文件`.sav`的前缀也同此。建议一个存档一个名字
- `int32 UserIndex`：用户平台ID。可自行设置参数 以标识不同平台，但通常不用刻意设定

```c++
UFUNCTION (BlueprintCallable, Category="SaveGame")  
static bool SaveGameToSlot  
(  
    USaveGame * SaveGameObject,  
    const FString & SlotName,  
    const int32 UserIndex  
)
```

### 示例

以 同步保存 为例，其在C++层或蓝图层的实现：

```c++
//UMySaveGame.h
UCLASS()
class [PROJECTNAME]_API UMySaveGame : public USaveGame
{
	GENERATED_BODY()

public:
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Basic)
	FString PlayerName;

	UPROPERTY(VisibleAnywhere, BlueprintReadWrite, Category = Basic)
	FString SaveSlotName;

	UPROPERTY(VisibleAnywhere, Category = Basic)
	uint32 UserIndex;

	UMySaveGame();
};

//同步存档
if (UMySaveGame* SaveGameInstance = Cast<UMySaveGame>(UGameplayStatics::CreateSaveGameObject(UMySaveGame::StaticClass())))
{
    // 修改存档实例的 数据
    SaveGameInstance->PlayerName = TEXT("Player");
    SaveGameInstance->SaveSlotName = TEXT("SaveSlot_00");
    SaveGameInstance->UserIndex = 0;

    // 保存
    FString SlotNameString = TEXT("SaveSlot_00");
    int32 UserIndexInt32 = 0;
    if (UGameplayStatics::SaveGameToSlot(SaveGameInstance, SlotNameString, UserIndexInt32))
    {
        // 保存成功
    }
}
```

![image-20250623170726088](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250623170726088.png)

### 参考文章

- [保存和加载游戏 - UnRealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/saving-and-loading-your-game-in-unreal-engine?application_version=5.5)
- [UGameplayStatics::SaveGameToSlot - UnrealEngineAPI](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/Runtime/Engine/Kismet/UGameplayStatics/SaveGameToSlot?application_version=5.5)
- [Save Game - what User Index is for? - UnrealEngineDev](https://forums.unrealengine.com/t/save-game-what-user-index-is-for/319803)
- [保存和加载游戏 - 博客园](https://www.cnblogs.com/CodeWithMe/p/13201689.html)



## 常用工具、函数、库

### [UGamePlayStatic](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/Runtime/Engine/Kismet/UGameplayStatics)

适用于C++与蓝图的 静态类，含有诸多 **GamePlay实用方法**：

- **全局相关**：
  - `GetGameMode()`、`GetGameState()`、`GetGameInstance()`、`GetPlayerController()`、`GetPlayerPawn()`、`GetPlayerCharacter()`
  - `SetGamePaused()`、`IsGamePaused()`

- **关卡逻辑**：
  - `OpenLevel()`、`OpenLevelBySoftObjectPtr()`
  - `LoadStreamLevel()`、`UnloadSteamLevel()`、`GetStreamingLevel()`
- **生成相关**：
  - `SpawnObject()`
- **Actor相关**：
  - `GetActorOfClass()`
  - `GetAllActorsOfClass()`、`GetAllActorsWithInterface()`、`GetAllActorsWithTag()`
- **粒子相关**：
  - `SpawnEmitterAtLocation()`、`SpawnEmitterAttached()`
- **音效相关**：
  - `PlaySoundAtLocation()`、`PlaySound2D()`
- **射线检测相关**：
  - `MakeHitResult()`、`BreakHitResult()`
- **SaveGame相关**：
  - `CreateSaveGameObject()`
  - `SaveGameToMemory()`、`SaveGameToSlot()`、`DoesSaveGameExist()`
  - `LoadGameFromMomory()`、`LoadGameFromSlot()`
- **时间获取相关**：
  - `GetWorldDeltaSeconds()`、`GetTimeSeconds()`、`GetRealTimeSeconds()`



参考文章：

- [UGameplayStatics类实用分析 - 知乎](https://zhuanlan.zhihu.com/p/71254108)



# GamePlay框架的 Runtime启动流程

![image-20250510155149664](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/UE-GamePlay框架的Runtime流程.png)

![image-20250510155149664](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/UE-GamePlay框架的Runtime流程2.png)

## 参考文章

- [Unreal Engine 的启动流程 - 放牛的星星 - 知乎](https://zhuanlan.zhihu.com/p/610523485)
- [UE4 Gameplay之GameMode流程分析(一) - 孤傲雕 - 知乎](https://zhuanlan.zhihu.com/p/70045930)
- [UE5 -- 引擎运行流程（从main到BeginPlay） - 易米八一 - 知乎](https://zhuanlan.zhihu.com/p/577433224)



# GAS

>  **Gameplay技能系统（Gameplay Ability System）** 是一种框架，用于编译`Actor`可以拥有和触发的属性、技能和交互
>
> 该系统可适应各种各样的[Gameplay驱动型](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/data-driven-gameplay-elements-in-unreal-engine?application_version=5.4)项目，例如 **角色扮演游戏** （RPG）、 **动作冒险（Action-Adventure）** 游戏和 **多玩家在线战术竞技** 游戏（MOBA）
>
> 通过GAS，可以快速制作游戏内的 主动/被动技能、Buff效果、伤害计算、战斗状态逻辑等

GAS核心模块：

- Ability System Component 技能系统组件
- GameplayTags 游戏标签
- Gameplay Ability
- Gameplay Attributes
- Gameplay Effect
- Gameplay Cue



## Ability System Component（ASC）

ASC本质是一个 `UActorComponent`，用于处理 GAS框架下的 交互逻辑：

- 使用技能（GamePlayAbility）
- 属性设置（AttributeSet）
- 效果处理（GamePlayEffect）

任何需要使用GAS的Actor对象，都必须拥有ASC组件（可将其放到Actor或PlayerState上）

ASC所附着的Actor称为 ASC的`OwnerActor`，ASC实际作用的Actor称为 ASC的`AvatarActor`

### 用法-基本配置

首先确保项目启用了GAS插件，后即可配置ASC组件：例如 在目标Actor上添加ASC组件，并继承实现`IAbilitySystemInterface`接口

```c++
// AGASSampleCharacter.h
class AGASSampleCharacter : public ACharacter, public IAbilitySystemInterface
{
    // 申明ASC
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = GameplayAbilities, meta = (AllowPrivateAccess = "true"))
	class UAbilitySystemComponent* AbilitySystem;
    
    // 实现IAbilitySystemInterface接口
	UAbilitySystemComponent* GetAbilitySystemComponent() const override;
}
```

```c++
// AGASSampleCharacter.cpp
AGASSampleCharacter::AGASSampleCharacter()
{
	// 实例化ASC
	AbilitySystem = CreateDefaultSubobject<UAbilitySystemComponent>(TEXT("AbilitySystem"));
}

UAbilitySystemComponent* AGASSampleCharacter::GetAbilitySystemComponent() const
{
    // 实现IAbilitySystemInterface接口，返回ASC实例
	return AbilitySystem;
}
```

![image-20250628172718541](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250628172718541.png)

## Gameplay Tags

`FGameplayTags` 是一种 **树状层级标签**，用于表示GAS内的 **某一种或某一类 状态/效果**

其基本结构例如 `Parent.Child.GrandChild`，其每一层都是一种标签，可粗略可细化，替代了常规的 `Bool`、`Enum`等多重嵌套、结合的结构

GameplayTags的管理位于 项目设置->项目->GameplayTags 内，对应配置亦被保存在 DefaultGameplayTags.ini 文件内

![image-20250629154748694](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250629154748694.png)

### Tags的定义

Tags可以通过如下方式进行定义、修改：

- [直接 项目设置->项目->GameplayTags 内手动进行修改](https://dev.epicgames.com/documentation/en-us/unreal-engine/using-gameplay-tags-in-unreal-engine?application_version=5.4#addingtagsinprojectsettings)
- [从 DataTable 导入](https://dev.epicgames.com/documentation/en-us/unreal-engine/using-gameplay-tags-in-unreal-engine?application_version=5.4#importingtagsfromdatatableassets)
- [C++内定义](https://dev.epicgames.com/documentation/en-us/unreal-engine/using-gameplay-tags-in-unreal-engine?application_version=5.4#definingtagswithc++)

#### 从DataTable导入Tags

使用数据表进行Tags导入，更方便 重复利用、项目移植。基本用法为：

在 项目设置->项目->GameplayTags->Gameplay标签表列表（Gameplay Tag Table List）内 创建、指定数据表（行类型为 `GameplayTagTableRow`）

![image-20250807144218835](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250807144218835.png)

后即可进行Tags配置，配置后生效的Tags就能在 Gameplay标签列表 内看到

![image-20250807144326996](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250807144326996.png)

### Tags的规划

因Tags可能会用于不同的业务模块（例如 技能功能、数值计算、特效表现、冷却等），因此需要Tags的规划尽量 分层合理、维护成本低、可移植性好

#### ARPG类的Tags规划

以 [ActionRPG - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/action-rpg-game?application_version=4.27) 为例，适用于ARPG类游戏的Tags规划可例如：

```
Ability（能力类型：使用物品、技能等）
|   |──Item
|   |──Jump
|   |──Sprint
|   |──Melee（普攻）
|   |	|──Close
|   |	└──Far
|   └──Skill（技能）
|   	└──Skill_A
|
Cooldown（技能冷却：存在即代表技能处于冷却状态）
|   └──Skill
|   	└──Skill_A
|
Event（事件）
|   └──Montage 
|        |──Player
|        |   |──Combo
|        |   |──BurstPound
|        |   |──ChestKick
|        |   |──FrontalAttack
|        |   |──GroundPound
|        |   └──JumpSlam
|        └──Shared
|            |──UseItem
|            |──UseSkill
|            └──WeaponHit
|
GameplayCue（表现处理：特效、音效、震动等）
|   |──Player
|   |	└──Sprint
|   └──Shared
|   	└──Stun
|
Status（状态）
    |──Dead
    |──Sprinting
    |──DamageImmune
    └──Debuff
        └──Stun
```

#### 参考文章

- [UE4 Gameplay Tags怎么划分合理？ - 知乎](https://www.zhihu.com/question/377026014/answer/1068575405)
- [GASDocumentation - Github](https://github.com/tranek/GASDocumentation)



## Gameplay Ability（GA）

`UGameplayAbility` 表示 **一段可被 激活触发的 游戏逻辑**

GA可用于表示 攻击、被攻击、技能等，甚至于 角色跳跃、物品交互等

![image-20250629164241509](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250629164241509.png)

### 用法

#### 1. 创建GA

以从 `UGamePlayAbility` 派生GA蓝图为例，GA蓝图的基本结构如下：

![image-20250629171246560](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250629171246560.png)

GA的职责：

- 生命周期事件：

  - `事件ActivateAbility`：成功激活此GA后调用，无传入参数。适用于 任意激活途径（`TryActivateAbilityByClass`或`SendGameplayEventToActor`）

  - `事件ActivateAbilityFromEvent`：在未使用 `事件ActivateAbility` 且 通过 `SendGameplayEventToActor`形式 成功激活此GA后调用，携带传入了`Event Data`。源码逻辑位于 `void UGameplayAbility::ActivateAbility`

  - `事件OnEndAbility`：GA结束事件
  - `EndAbility`：结束GA的方法
  - `CommitAbility`：执行应用Cost、Cooldown（如果GA有配置Cost或Cooldown、且使用了 `事件ActivateAbility` 或 `事件ActivateAbilityFromEvent` 则必须主动调用一次，源码逻辑位于 `void UGameplayAbility::ActivateAbility`）

- 配置 GameplayTags、Cost、CoolDowns等属性

- 业务逻辑：视觉表现、应用GE等

#### 2. 添加GA到ASC

常规方法是 在C++层 把GA添加到ASC组件内、初始化ASC，则后续ASC就可使用此GA

```c++
// AGASSampleCharacter.h
class AGASSampleCharacter : public ACharacter, public IAbilitySystemInterface
{
    // ASC
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = GameplayAbilities, meta = (AllowPrivateAccess = "true"))
	class UAbilitySystemComponent* AbilitySystem;

    // 声明Ability数组
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Abilities)
	TArray<TSubclassOf<UGameplayAbility>> MyAbilities;
}
```

```c++
// AGASSampleCharacter.cpp
void AGASSampleCharacter::BeginPlay()
{
	Super::BeginPlay();

	if (nullptr != AbilitySystem)
	{
		// 把GA添加到ASC
		if (MyAbilities.Num() > 0)
		{
			for (auto i = 0; i < MyAbilities.Num(); i++)
			{
				if (MyAbilities[i] == nullptr)
					continue;

				AbilitySystem->GiveAbility(FGameplayAbilitySpec(MyAbilities[i].GetDefaultObject(), 1, 0));
			}
		}

		// 初始化ASC
		AbilitySystem->InitAbilityActorInfo(this, this);
	}
}
```

![image-20250629172722366](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250629172722366.png)

#### 3.1 GA的激活

激活某个GA的方式有：

A. 主动调用ASC组件提供的 `TryActivateAbilityByClass`、`TryActivateAbilitiesByTag` 方法

B. GA自身配置Trigger条件，当ASC收到Trigger后将触发激活 其拥有的、满足Trigger条件的 GA。常用方法是 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor`

![image-20250629174728912](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250629174728912.png)

#### 3-2. GA的激活 - Input触发

GA的激活，还可以通过绑定Input输入进行触发，触发将直接进入GA的 `事件ActivateAbility`。Input方式则可以用 **常规Input输入** 或 **EnhancedInput增强输入**

##### 常规Input输入以激活GA

以 [GASDocumentation - Github](https://github.com/tranek/GASDocumentation#462-binding-input-to-the-asc) 为例子，其基本用法为：

1. 项目设置内添加操作映射

![image-20250815221500561](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250815221500561.png)

2. 声明 Ability对应触发的InputID的 枚举列表（注意 枚举名和上面的操作映射名字 必须一致）

```c++
// GASDocumentation.h
UENUM(BlueprintType)
enum class EGDAbilityInputID : uint8
{
	// 0 None
	None			UMETA(DisplayName = "None"),
	// 1 Confirm
	Confirm			UMETA(DisplayName = "Confirm"),
	// 2 Cancel
	Cancel			UMETA(DisplayName = "Cancel"),
	// 3 LMB
	Ability1		UMETA(DisplayName = "Ability1"),
	// 4 RMB
	Ability2		UMETA(DisplayName = "Ability2"),
	// 5 Q
	Ability3		UMETA(DisplayName = "Ability3"),
	// 6 E
	Ability4		UMETA(DisplayName = "Ability4"),
	// 7 R
	Ability5		UMETA(DisplayName = "Ability5"),
	// 8 Sprint
	Sprint			UMETA(DisplayName = "Sprint"),
	// 9 Jump
	Jump			UMETA(DisplayName = "Jump")
};
```
后 建立 `UGameplayAbility` 和上述 `EGDAbilityInputID` 的关联。示例为 从 `UGameplayerAbility` 派生子类，添加成员`AbilityInputID : EGDAbilityInputID`，则各功能GA 就可唯一对应各自的 `EGDAbilityInputID`

```c++
UCLASS()
class GASDOCUMENTATION_API UGDGameplayAbility : public UGameplayAbility
{
	GENERATED_BODY()
	
public:
	UGDGameplayAbility();

	// Abilities with this set will automatically activate when the input is pressed
	UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Ability")
	EGDAbilityInputID AbilityInputID = EGDAbilityInputID::None;

	UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Ability")
	EGDAbilityInputID AbilityID = EGDAbilityInputID::None;
};
```

![image-20250815222837342](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250815222837342.png)


3. 运行态时 对 ASC组件执行 绑定Input激活Ability方法：`UGDAbilitySystemComponent::BindAbilityActivationToInputComponent(UInputComponent* InputComponent, FGameplayAbilityInputBinds BindInfo)`。这使得 `EGDAbilityInputID` 枚举内的InputID在触发时 可响应到ASC组件内

```c++
// AGDHeroCharacter.cpp
void AGDHeroCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
	Super::SetupPlayerInputComponent(PlayerInputComponent);

	// Bind player input to the AbilitySystemComponent.
	BindASCInput();
}

void AGDHeroCharacter::BindASCInput()
{
	if (!ASCInputBound && AbilitySystemComponent.IsValid() && IsValid(InputComponent))
	{
		FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
		AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
			FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));

		ASCInputBound = true;
	}
}
```

4. 运行态时 在添加GA时 绑定激活此GA的InputID，以最终实现 触发InputAction->相应到ASC->ASC激活符合绑定的GA 整个流程

```c++
// AGDCharacterBase.cpp
void AGDCharacterBase::AddCharacterAbilities()
{
	for (TSubclassOf<UGDGameplayAbility>& StartupAbility : CharacterAbilities)
	{
		int32 AbilityInputID = static_cast<int32>(StartupAbility.GetDefaultObject()->AbilityInputID);
		int32 AbilityLevel = GetAbilityLevel(StartupAbility.GetDefaultObject()->AbilityID);

		FGameplayAbilitySpec GameplayAbilitySpec = FGameplayAbilitySpec(StartupAbility, AbilityLevel, AbilityInputID, this);

		AbilitySystemComponent->GiveAbility(GameplayAbilitySpec);
	}
}

// GameplayAbilityTypes.cpp
FGameplayAbilitySpec::FGameplayAbilitySpec(UGameplayAbility* InAbility, int32 InLevel, int32 InInputID, UObject* InSourceObject)
	: Ability(InAbility)
	, Level(InLevel)
	, InputID(InInputID)
	, SourceObject(InSourceObject)
	, ActiveCount(0)
	, InputPressed(false)
	, RemoveAfterActivation(false)
	, PendingRemove(false)
	, bActivateOnce(false) 
{
	Handle.GenerateNewHandle();
}
```

##### EnhancedInput增强输入以激活GA

1. 按常规EnhancedInput用法创建IMC、IA、接入使用
2. 复用上例1、2内的 `EGDAbilityInputID`枚举 和 自行派生的`UGDGameplayAbility : UGameplayAbility`。后需新创建 `EGDAbilityInputID` 与 `UInputAction` 的映射关系，例如 创建一个 `TMap<EGDAbilityInputID, TObjectPtr<UInputAction>> AbilityEnumToAction` 

```c++
// AGDCharacterBase.cpp
class GASDOCUMENTATION_API AGDCharacterBase : public ACharacter, public IAbilitySystemInterface
{
    UPROPERTY(BlueprintReadWrite, EditAnywhere, Category = "GASDocumentation|Abilities")
	TMap<EGDAbilityInputID, TObjectPtr<UInputAction>> AbilityEnumToAction;

	virtual TMap<EGDAbilityInputID, TObjectPtr<UInputAction>> GetAbilityEnumMap();
}
```

![image-20250815230413771](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250815230413771.png)

3. 重写改造 ASC组件执行 绑定Input激活Ability方法：`UGDAbilitySystemComponent::BindAbilityActivationToInputComponent(UInputComponent* InputComponent, FGameplayAbilityInputBinds BindInfo)` 。因为其内部绑定逻辑是针对 常规Input输入，即 `UInputComponent`，而我们则是需要对 `UEnhancedInputComponent` 进行绑定

```c++
// UGDAbilitySystemComponent.cpp
void UGDAbilitySystemComponent::BindAbilityActivationToInputComponent(UInputComponent* InputComponent, FGameplayAbilityInputBinds BindInfo)
{
	Super::BindAbilityActivationToInputComponent(InputComponent, BindInfo);

    AGDCharacterBase* Character = Cast<AGDCharacterBase>(GetAvatarActor());
	if(!Character) { return; }
    
    // 换为处理 EnhancedInput
	UEnhancedInputComponent* EnhancedInputComponent = Cast<UEnhancedInputComponent>(InputComponent);
	if(EnhancedInputComponent)
	{
        // 获取 InputID与InputAction的映射表
        TMap<EGDAbilityInputID, TObjectPtr<UInputAction>> CurrentMap = Character->GetAbilityEnumMap();
        
		UEnum* EnumBinds = BindInfo.GetBindEnum();
		SetBlockAbilityBindingsArray(BindInfo);
		for(int32 idx=0; idx < EnumBinds->NumEnums(); ++idx)
		{
			UInputAction* InputAction = CurrentMap.FindRef(static_cast<EGDAbilityInputID>(idx));
			if(InputAction)
			{
				// Pressed event
				{
					EnhancedInputComponent->BindAction(InputAction, ETriggerEvent::Started,this, &UAbilitySystemComponent::AbilityLocalInputPressed, idx);
				}

				// Released event
				{
					EnhancedInputComponent->BindAction(InputAction, ETriggerEvent::Completed,this, &UAbilitySystemComponent::AbilityLocalInputReleased, idx);
				}
			}
		}
	}
}

// AbilitySystemComponent_Abilities.cpp
void UAbilitySystemComponent::BindAbilityActivationToInputComponent(UInputComponent* InputComponent, FGameplayAbilityInputBinds BindInfo)
{
	UEnum* EnumBinds = BindInfo.GetBindEnum();

	SetBlockAbilityBindingsArray(BindInfo);

	for(int32 idx=0; idx < EnumBinds->NumEnums(); ++idx)
	{
		const FString FullStr = EnumBinds->GetNameStringByIndex(idx);
		
		// Pressed event
		{
			FInputActionBinding AB(FName(*FullStr), IE_Pressed);
			AB.ActionDelegate.GetDelegateForManualSet().BindUObject(this, &UAbilitySystemComponent::AbilityLocalInputPressed, idx);
			InputComponent->AddActionBinding(AB);
		}

		// Released event
		{
			FInputActionBinding AB(FName(*FullStr), IE_Released);
			AB.ActionDelegate.GetDelegateForManualSet().BindUObject(this, &UAbilitySystemComponent::AbilityLocalInputReleased, idx);
			InputComponent->AddActionBinding(AB);
		}
	}

	// Bind Confirm/Cancel. Note: these have to come last!
	if (BindInfo.ConfirmTargetCommand.IsEmpty() == false)
	{
		FInputActionBinding AB(FName(*BindInfo.ConfirmTargetCommand), IE_Pressed);
		AB.ActionDelegate.GetDelegateForManualSet().BindUObject(this, &UAbilitySystemComponent::LocalInputConfirm);
		InputComponent->AddActionBinding(AB);
	}
	
	if (BindInfo.CancelTargetCommand.IsEmpty() == false)
	{
		FInputActionBinding AB(FName(*BindInfo.CancelTargetCommand), IE_Pressed);
		AB.ActionDelegate.GetDelegateForManualSet().BindUObject(this, &UAbilitySystemComponent::LocalInputCancel);
		InputComponent->AddActionBinding(AB);
	}

	if (BindInfo.CancelTargetInputID >= 0)
	{
		GenericCancelInputID = BindInfo.CancelTargetInputID;
	}
	if (BindInfo.ConfirmTargetInputID >= 0)
	{
		GenericConfirmInputID = BindInfo.ConfirmTargetInputID;
	}
}
```

4. 同上例步骤4：运行态时 在添加GA时 绑定激活此GA的InputID

##### 参考文章

- [GASDocumentation - Github](https://github.com/tranek/GASDocumentation)
- [UE5中GAS接入增强输入(EnhancedInput)](https://mytechplayer.com/archives/ue5中gas接入增强输入enhancedinput)



### GA细节面板 - Tags标签

GA上可配置系列 GameplayTags，使其进行 预设的逻辑处理

![image-20250701160404864](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250701160404864.png)

|           名称            |                             描述                             |                        备注                        |
| :-----------------------: | :----------------------------------------------------------: | :------------------------------------------------: |
|       Ability Tags        |              此GA 的 Tags（即 描述GA用的标签）               |                                                    |
| Cancel Abilities with Tag |      当此GA激活时 打断其他所有 拥有配置AbilityTags的GA       |           此类Tag是 某个GA的AbilityTags            |
| Block Abilities with Tag  | 当此GA激活时 其他所有 拥有配置AbilityTags的GA 禁止激活（已激活了的不会被打断） |           此类Tag是 某个GA的AbilityTags            |
|   Activation Owned Tags   | 当此GA激活时 GA的 `AvatarActor` 的ASC 将获取 配置的Tags，反之 当GA结束时 将对应移除 | 此类Tag是附属于ASC上的，而不是 某个GA的AbilityTags |
| Activation Required Tags  | 当GA的 `AvatarActor` 的ASC上 拥有全部 配置的Tags，则此GA才允许被激活 | 此类Tag是附属于ASC上的，而不是 某个GA的AbilityTags |
|  Activation Blocked Tags  | 若此GA的 `AvatarActor` 的ASC上 拥有任意个 配置的Tags，则此GA禁止激活 | 此类Tag是附属于ASC上的，而不是 某个GA的AbilityTags |
|   Source Required Tags    | 当`FGameplayEventData.InstigatorTags` 内 包含全部 配置的Tags，则此GA才允许被激活 |                                                    |
|    Source Blocked Tags    | 当`FGameplayEventData.InstigatorTags` 内 拥有任意个 配置的Tags，则此GA禁止激活 |                                                    |
|   Target Required Tags    | 当`FGameplayEventData.TargetTags` 内 包含全部 配置的Tags，则此GA才允许被激活 |                                                    |
|    Target Blocked Tags    | 当`FGameplayEventData.TargetTags` 内 拥有任意个 配置的Tags，则此GA禁止激活 |                                                    |

#### 关于 Source XXX Tags、Target XXX Tags

当我们采用 **[GameplayEvent的形式激活GA](https://dev.epicgames.com/documentation/en-us/unreal-engine/using-gameplay-abilities-in-unreal-engine?application_version=5.4#triggeringwithgameplayevents)**（例如调用方法 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`，其中的形参 `FGameplayEventData Payload` 其内的下列2成员参数，即对应 Source XXX Tags、Target XXX Tags

```c++
/** Metadata for a tag-based Gameplay Event, that can activate other abilities or run ability-specific logic */
USTRUCT(BlueprintType)
struct GAMEPLAYABILITIES_API FGameplayEventData
{
    // ...
    
	/** Tags that the instigator has */
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = GameplayAbilityTriggerPayload)
	FGameplayTagContainer InstigatorTags;

    /** Tags that the target has */
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = GameplayAbilityTriggerPayload)
	FGameplayTagContainer TargetTags;
    
    // ...
}
```

而当 **非GameplayEvent的形式激活GA的情况**，例如常用的 `UAbilitySystemComponent::TryActivateAbilityByClass()` 其内部流程中 未创建使过`FGameplayEventData`类型的数据，因此这类方法 **会跳过对 Source XXX Tags、Target XXX Tags 的判断**，核心代码位于：

```c++
//AbilitySystemComponent_Abilities.cpp
bool UAbilitySystemComponent::InternalTryActivateAbility(FGameplayAbilitySpecHandle Handle, FPredictionKey InPredictionKey, UGameplayAbility** OutInstancedAbility, FOnGameplayAbilityEnded::FDelegate* OnGameplayAbilityEndedDelegate, const FGameplayEventData* TriggerEventData)
{
    // Core ...
}
```

一个例子：

1. 已有一个目标GA：TargetBlockedTags=`Ability.Test.A`，其带有GameplayEvent的Trigger（TriggerTag=`Ability.Test.C`）
2. 现通过 GameplayEvent的形式 尝试激活目标GA：激活的EventTag对应为`Ability.Test.C`，其将传递 `FGameplayEventData Payload` 参数（其TargetTags配置为`Ability.Test.B`）
3. 执行结果为 因TargetTags内配置的Tags，均不在 GA的TargetBlockedTags列表内，故 成功激活GA
4. 若将 TargetTags从`Ability.Test.B`改配置为`Ability.Test.A`，则执行结果为 因TargetTags内配置的Tags，有存在于 GA的TargetBlockedTags列表内，故 将被阻止激活GA。其余同理

![image-20250701225905311](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250701225905311.png)

总结：

- Source XXX Tags、Target XXX Tags 仅在 通过GameplayEvent激活GA 且有对应非空`Payload`配置下 才可能生效，否则GA的配置无需关心这2项目的内容

### GA细节面板 - Triggers触发器

触发器用于 配置系列触发条件、当达成触发条件后 就激活此GA

**TriggerTag**：即 触发激活此GA的 标识GameplayTag

**Triggers Source**：

- Gameplay Event：当`OwnerActor`收到 配置TriggerTag的Gameplay Event时（例如 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`）激活一次此GA
- Owned Tag Added：当`OwnerActor`获得 配置TriggerTag的时候 激活一次此GA，当`OwnerActor`失去此Tag时 不会移除此GA，需要自行移除
- Owned Tag Present：当`OwnerActor`获得 配置TriggerTag的时候 激活一次此GA，当`OwnerActor`失去此Tag时 会自动移除此GA（GA若执行未完成会被Cancel）

### GA细节面板 - Cooldowns

Cooldown 用于 限制GA的触发间隔，配置项为 GE。其本质为 Cooldown的GE激活期间 添加Tag到ASC，通过此Tag进行限制、实现冷却

Cooldown GE的配置要求：

- Duration Policy = Has Duration类型，并对应配置有效时长
- Granted Tags->Added 配置 Cooldown相关的Tag

![image-20250706175722358](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250706175722358.png)

### GA细节面板 - Costs

Costs 用于 消耗目标Attribute以激活GA，配置项为 GE

Costs GE的配置要求：

- Duration Policy = Instant
- 一个或多个Modifier

![image-20250706180450910](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250706180450910.png)

### GA - AbilityTask

[UAbilityTask](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/gameplay-ability-tasks-in-unreal-engine?application_version=5.4) 是指GA蓝图专用的 **一段异步处理逻辑**

UE已内置诸多常用节点（节点功能可参考 [AbilityTask原生类盘点 - 知乎](https://zhuanlan.zhihu.com/p/431081292)），亦可自行从 `UAbilityTask` 派生实现（可参考 [UE5 GAS RPG创建自定义的Ability Task - CSDN](https://blog.csdn.net/qq_30100043/article/details/137859611)

![image-20250702113549438](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250702113549438.png)



## Gameplay Attributes

`FGameplayAttributeData ` 即 **Attribute**，是 **描述 一项属性 的结构体**，用于表示游戏的某项数值属性（例如 HP、MP等）。其核心成员为：

- `float BaseValue`：基础值。例如 升级后MP永久性的提升
- `float CurrentValue`：当前值。较常用。例如 使用技能会消耗MP，MP也可用药品恢复或随时间自动恢复等

`UAttributeSet` 即 **AS**，负责 **定义和持有 Attribute**，并 **管理Attribute的变化**，包括网络同步。挂载到ASC组件上

### AS用法

#### 1. 创建Attribute及AS

先从 `UAttributeSet` 派生出所需AS，头部添加 [Defining Attributes](https://github.com/tranek/GASDocumentation/tree/master?tab=readme-ov-file#concepts-as-attributes)，并声明所需Attribute：

```c++
// SampleAttributeSet.h
#include "CoreMinimal.h"
#include "AttributeSet.h"
#include "AbilitySystemComponent.h"
#include "SampleAttributeSet.generated.h"

// Defining Attributes : 增加各种Getter和Setter方法的宏（必要）
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)

UCLASS()
class [PROJECTNAME]_API USampleAttributeSet : public UAttributeSet
{
	GENERATED_BODY()

public:
	USampleAttributeSet();

public:
	UPROPERTY(BlueprintReadOnly, Category = "Health")
	FGameplayAttributeData Health;
	ATTRIBUTE_ACCESSORS(USampleAttributeSet, Health);

	UPROPERTY(BlueprintReadOnly, Category = "Health")
	FGameplayAttributeData MaxHealth;
	ATTRIBUTE_ACCESSORS(USampleAttributeSet, MaxHealth)

public:
	// 属性修改前回调
	virtual void PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue) override;

	// GE执行后属性回调
	virtual void PostGameplayEffectExecute(const FGameplayEffectModCallbackData& Data) override;
};
```

```c++
// SampleAttributeSet.cpp
void USampleAttributeSet::PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)
{
    // CurrentValue 被改变前调用
	if (Attribute == GetHealthAttribute())
	{
        // NewValue的边界处理
		NewValue = FMath::Clamp(NewValue, 0.f, GetMaxHealth());
	}
}

void USampleAttributeSet::PostGameplayEffectExecute(const FGameplayEffectModCallbackData& Data)
{
	// 注意：仅在instant GameplayEffect使Attribute的 BaseValue改变时触发
	Super::PostGameplayEffectExecute(Data);

    if (Data.EvaluatedData.Attribute == GetHealthAttribute())
    {
        SetHealth(FMath::Clamp(GetHealth(), 0.0f, GetMaxHealth()));
    }
}
```

若想单纯 **监听 Attribute的变化**，则可以用 ASC注册监听（常用于 通知表现层等外部业务）：

```c++
// AGASSampleCharacter.cpp
void AGASSampleCharacter::BeginPlay()
{
	Super::BeginPlay();

	if (nullptr != AbilitySystem)
	{
        // ASC 初始化
        
        // 向ASC注册Attribute变化事件
		AbilitySystem->GetGameplayAttributeValueChangeDelegate(USampleAttributeSet::GetHealthAttribute()).AddUObject(this, &AGASSampleCharacter::OnHealthChanged);
	}
}

void AGASSampleCharacter::OnHealthChanged(const FOnAttributeChangeData& Data)
{
    // 获取最新值 Data.NewValue
	// 自行Notify
}
```

后需要建立 AttributeSet 与 ASC的联系：即让**ASC注册持有AS**。方法为：在与ASC同一OwnerActor上的 构造方法中 就创建AttributeSet，则当 ASC执行 `UAbilitySystemComponent::InitializeComponent()` 时，就会自动对此AS并进行相关处理：

```c++
// AGDPlayerState.cpp
AGDPlayerState::AGDPlayerState()
{
	// 实例化ASC
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));

    // 在与ASC同OwnerActor的构造方法中创建AttributeSet，则将会在 UAbilitySystemComponent::InitializeComponent() 时被注册到ASC
	AttributeSetBase = CreateDefaultSubobject<UGDAttributeSetBase>(TEXT("AttributeSetBase"));
}

// UAbilitySystemComponent_Abilities.cpp
void UAbilitySystemComponent::InitializeComponent()
{
	Super::InitializeComponent();

	// Look for DSO AttributeSets (note we are currently requiring all attribute sets to be subobjects of the same owner. This doesn't *have* to be the case forever.
	AActor *Owner = GetOwner();
	InitAbilityActorInfo(Owner, Owner);	// Default init to our outer owner

	// cleanup any bad data that may have gotten into SpawnedAttributes
	for (int32 Idx = SpawnedAttributes.Num()-1; Idx >= 0; --Idx)
	{
		if (SpawnedAttributes[Idx] == nullptr)
		{
			SpawnedAttributes.RemoveAt(Idx);
		}
	}

	TArray<UObject*> ChildObjects;
	GetObjectsWithOuter(Owner, ChildObjects, false, RF_NoFlags, EInternalObjectFlags::Garbage);

	for (UObject* Obj : ChildObjects)
	{
		UAttributeSet* Set = Cast<UAttributeSet>(Obj);
		if (Set)  
		{
			SpawnedAttributes.AddUnique(Set);
		}
	}

	SetSpawnedAttributesListDirty();
}
```

#### 2. AS的初始化

方法1（不推荐）：为目标AS配置对应的 DataTable，挂载到ASC上的Default Starting Table项：

![image-20250709195207309](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250709195207309.png)

方法2（官方推荐）：新建GE专用于初始化目标AS，运行态时自行Apply以进行初始化：

![image-20250709195748391](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250709195748391.png)

参考文章：

- [【UE】记录GAS中AttributeSet初始化流程 - 知乎](https://zhuanlan.zhihu.com/p/687390022)

#### 3. AS的使用

![image-20250703174705572](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250703174705572.png)



## Gameplay Effect（GE）

`UGameplayEffect` 即 GE，是**Ability对自己或他人产生影响的途径**。可理解为游戏内的 一段伤害、一个Buff等 **一项游戏效果**，其注重于 效果的结果表现，而不是 效果的实现流程（这是GA的职责），因此其 **不可添加逻辑、相当于一个配置各类效果的配置表**

![image-20250704204529992](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704204529992.png)

### GE的功能简介

在 UE 5.3 版本后，GE稍有改动，改动内容可参阅 [UE5-GAS插件UE5.3改动 - 知乎](https://zhuanlan.zhihu.com/p/657035214)

#### 修改Attribute

通过配置GE细节面板->GameplayEffect->Modifier，可修改Attribute：

![image-20250704174546020](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704174546020.png)

#### 赋予GameplayTag

当此GE Apply成功时，会赋予目标ASC 配置的GameplayTag：

![image-20250704180912409](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704180912409.png)

#### 赋予GA

当此GE Apply成功时，会赋予目标ASC 配置的GA：

- Removal Policy：GE移除时 目标GA的处理

  - Cancel Ability Immediately：移除，并触发事件EndAbility

  - Remove Ability on End：移除，但是不触发EndAbility

  - Do Nothing：GA不会被移除

![image-20250704181210460](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704181210460.png)

#### 嵌套应用GE

当此GE Apply成功时，Apply配置的GE：

![image-20250704174447542](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704174447542.png)

当此GE 的Duration被打断或结束时，Apply配置的GE：

（备注：适用情况为 DurationPolicy=HasDuration）

![image-20250704180213438](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704180213438.png)

当此GE 的Stacking溢出时，Apply配置的GE：

（备注：Stacking溢出仅适用于 DurationPolicy=Infinite/HasDuration）

![image-20250704175840562](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704175840562.png)

#### 调用GC

当此GE Apply成功时，触发配置的GC：

![image-20250704181859613](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704181859613.png)

### GE功能 - 修改Attribute

作为GE的核心功能，修改Attribute的功能主要由 GE->GameplayEffect页签->Modifiers、Executions实现

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250817003752686.png)

#### Modifiers

一项 Modifiers 可修改一项 Attribute

- Attribute：要修改的Attribute
- Modifier Op：运算符。包含 加、乘、除、覆盖。配合 Magnitude值 对Attribute进行修改
- Modifier Magnitude：运算值。也就是 将要和 Attribute进行 Modifier Op运算的值
  - Scalable Float：直接使用 浮点数 作为Magnitude值
  - Attribute Based：使用 从Source或Target上 快照捕获的某个Attribute 作为Magnitude值
  - Custom Calculation Class：使用 自定义计算类的返值 作为Magnitude值。可自行实现复杂计算逻辑、捕获所需的Attribute
  - Set By Caller：使用 蓝图Caller的传值 作为Magnitude值

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250816225154917.png)

##### Attribute Based

核心是 从目标对象上获取Attribute 作为Magnitude值 以供 ModifierOp 进行最终运算

例如：配置实现了 扣除目标身上的HealthAttribute值（值为 目标身上的 当前HealthAttribute值），GE执行表现为 目标Health扣光

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250817210511175.png)

##### Custom Calculation Class

核心是 从[`UGameplayModMagnitudeCalculation`](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation) 派生子类、自行按需捕获Attribute、计算返回Magnitude值 以供 ModifierOp 进行最终运算

例如：实现了 `UMMC_Test : UGameplayModMagnitudeCalculation`，GE的执行表现为 扣除Source身上当前HealthAttribute值的一半

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250817002729020.png)

```c++
// UMMC_Test.h
UCLASS()
class [PROJECTNAME]_API UMMC_Test : public UGameplayModMagnitudeCalculation
{
	GENERATED_BODY()

public:
	UMMC_Test();

	virtual float CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec& Spec) const override;

private:
	// 声明捕获属性
	FGameplayEffectAttributeCaptureDefinition CaptureHealthDef;
};
```

```c++
// UMMC_Test.cpp

// 构造函数内 初始化捕获信息
UMMC_Test::UMMC_Test()
{
	// 设置 需要捕获的Attribute 及捕获设置参数
	CaptureHealthDef.AttributeToCapture = UGDAttributeSetBase::GetHealthAttribute();
	CaptureHealthDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Source;
	CaptureHealthDef.bSnapshot = false;		// true = 捕获ApplyGE时刻的值, false = 捕获每次计算时刻的值

	// 添加到捕获列表
	RelevantAttributesToCapture.Add(CaptureHealthDef);
}

// Magnitude计算逻辑
float UMMC_Test::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec& Spec) const
{
	// 获取 捕获值
	FAggregatorEvaluateParameters EvaluateParams;
	float CaptureHealthValue = 0.0f;
	GetCapturedAttributeMagnitude(CaptureHealthDef, Spec, EvaluateParams, CaptureHealthValue);

    // 返回计算完成的 Magnitude值
	return CaptureHealthValue / 2.0f;
}
```

##### Set By Caller

核心为 蓝图函数（`AssignTagSetByCallerMagnitude`）传入自定义Magnitude值，并以 DataTag（GameplayTag）或DataName（FName） 为唯一标识，GE在Apply时刻 根据此唯一标识获取到 Magnitude值 以供 ModifierOp 进行最终运算

例如：使用DataTag=Data.Damage、Magnitude=-50，GE的执行表现为 扣除目标身上当前HealthAttribute值（50）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250817212113127.png)

#### Executions

相比于 Modifier，Executions则更为灵活：可自定义计算逻辑和Attribute修改

##### Execution Calculation Class

与 Custom Calculation Class方法类似，Execution Calculation Class也可以 捕获Attribute、自行计算Magnitude值。其从 [`UGameplayEffectExecutionCalculation`](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-) 派生计算类

优点：

- 多Attribute修改应用：Execution Calculation Class 可直接计算Magnitude值并应用修改到Attribute，而不再是 计算Magnitude值并返回 以供ModifierOp 进行最终运算
- 可从传入参数（`ExecutionParams`）内获取到source、target双方诸多信息（ASC、Actor、Tags...）
- 快照值预修改：可使用 CalculationModifiers 对快照Attribute值 预先进行ModifierOp修改、才传递给业务层

缺点：

- 不可预测性，仅能在C++层实现
- 不支持 未设置Period的Infinite
- 不会触发属性的 `PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)`，需要自行实现属性限制

例如：实现了 `UTestExecutionCalculation : UGameplayEffectExecutionCalculation`，GE的执行表现为 扣除身上当前HealthAttribute值（MaxHealthAttribute/2）、并扣除了ManaAttribute值（10）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250817231043917.png)

```c++
// UTestExecutionCalculation.h
UCLASS()
class [PROJECTNAME]_API UTestExecutionCalculation : public UGameplayEffectExecutionCalculation
{
	GENERATED_BODY()

public:
	UTestExecutionCalculation();

	virtual void Execute_Implementation(const FGameplayEffectCustomExecutionParameters& ExecutionParams, FGameplayEffectCustomExecutionOutput& OutExecutionOutput) const override;
};
```

```c++
// UTestExecutionCalculation.cpp

struct STestCaptureDefStatics
{
	DECLARE_ATTRIBUTE_CAPTUREDEF(Health)
	DECLARE_ATTRIBUTE_CAPTUREDEF(MaxHealth)

	STestCaptureDefStatics()
	{
		// 设置 需要捕获的Attribute、捕获源、是否快照
		DEFINE_ATTRIBUTE_CAPTUREDEF(UGDAttributeSetBase, Health, Target, false);
		DEFINE_ATTRIBUTE_CAPTUREDEF(UGDAttributeSetBase, MaxHealth, Target, true);
	}
};

static const STestCaptureDefStatics& TestCaptureDefStatics()
{
	static STestCaptureDefStatics DStatics;
	return DStatics;
}

// 构造函数内 初始化捕获信息
UTestExecutionCalculation::UTestExecutionCalculation()
{
	// 添加到捕获列表
	RelevantAttributesToCapture.Add(TestCaptureDefStatics().HealthDef);
	RelevantAttributesToCapture.Add(TestCaptureDefStatics().MaxHealthDef);
}

// 计算处理逻辑
void UTestExecutionCalculation::Execute_Implementation(const FGameplayEffectCustomExecutionParameters& ExecutionParams, FGameplayEffectCustomExecutionOutput& OutExecutionOutput) const
{
	// 获取 source、target相关信息
	UAbilitySystemComponent* TargetAbilitySystem = ExecutionParams.GetTargetAbilitySystemComponent();
	UAbilitySystemComponent* SourceAbilitySystem = ExecutionParams.GetSourceAbilitySystemComponent();

	AActor* TargetActor = TargetAbilitySystem ? TargetAbilitySystem->GetAvatarActor() : nullptr;
	AActor* SourceActor = SourceAbilitySystem ? SourceAbilitySystem->GetAvatarActor() : nullptr;

	const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();

	const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();
	const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();

	FAggregatorEvaluateParameters EvaluationParameters;
	EvaluationParameters.SourceTags = SourceTags;
	EvaluationParameters.TargetTags = TargetTags;


	// 获取 捕获值
	float HealthVal = 0.0f;
	ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(TestCaptureDefStatics().HealthDef, EvaluationParameters, HealthVal);
	float MaxHealthVal = 0.0f;
	ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(TestCaptureDefStatics().MaxHealthDef, EvaluationParameters, MaxHealthVal);

	// 传入计算后的Magnitude值，生成一个Modifier，以达到修改Attribute的效果
	float MagnitudeVal_Health = -MaxHealthVal;
	auto HealthMod = FGameplayModifierEvaluatedData(TestCaptureDefStatics().HealthProperty, EGameplayModOp::Additive, MagnitudeVal_Health);
	OutExecutionOutput.AddOutputModifier(HealthMod);

	// 同理，可修改其他Attribute
	float MagnitudeVal_Mana = -10;
	auto ManaMod = FGameplayModifierEvaluatedData(UGDAttributeSetBase::GetManaAttribute(), EGameplayModOp::Additive, MagnitudeVal_Mana);
	OutExecutionOutput.AddOutputModifier(ManaMod);
}
```

#### 参考文章

- [GASDocumentation - Github](https://github.com/tranek/GASDocumentation)
- [UE GAS框架中GameplayEffect/Attribute Based Modifier详解 - CSDN](https://blog.csdn.net/m0_45371381/article/details/146031577)
- [虚幻四Gameplay Ability System入门(7)-Gameplay Effect详解(2)自定义Calculation Class - 知乎](https://zhuanlan.zhihu.com/p/368112930)
- [UE5 GAS RPG使用MMC根据等级设置血量和蓝量（下） - CSDN](https://blog.csdn.net/qq_30100043/article/details/136884265)
- [UE5 GAS RPG 使用Execution Calculations处理对目标造成的最终伤害 - CSDN](https://blog.csdn.net/qq_30100043/article/details/138673957)
- [UE5 Gameplay Ability System(GAS) 简单记录 - cnblog](https://www.cnblogs.com/rkexy/p/17961311)
- [GAS GE 自定义计算过程 - Zerol](https://kisspread.github.io/notes/GAS/6GE_Calculation.html)

### GE细节面板

#### GE细节面板 - Duration

**Duration Policy**：描述GE的 持续类型

| Duration Policy  |                         描述                          |                 修改对象                  | Period | 应用示例 |
| :--------------: | :---------------------------------------------------: | :---------------------------------------: | :----: | :------: |
|   **Instant**    |                立即触发、立即自动结束                 |                 BaseValue                 | 不可用 | 常规扣血 |
|   **Infinite**   |                立即触发、人为手动结束                 | 无Period=CurrentValue；有Period=BaseValue |  可用  | 长按奔跑 |
| **Has Duration** | 立即触发、到期自动结束（持续时间=Duration Magnitude） | 无Period=CurrentValue；有Period=BaseValue |  可用  | 临时Buff |

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250820160625227.png)

#### GE细节面板 - Period

**Period**：设置GE的触发周期（仅 Infinite、Has Duration 可用），时间单位为 秒

**Execute Periodic Effect on Application**：GE首次触发、开始计时Time=0时 是否就触发一次

**Periodic Inhibition Policy**：GE被阻断后的处理方式

- Never Reset：从被打断时的位置 继续执行
- Reset Period：从0开始计算周期
- Execute and Reset Period：打断时立即执行一次，下次从0开始计算周期

|      Period配置       |                   表现效果                    |                  备注                   |
| :-------------------: | :-------------------------------------------: | :-------------------------------------: |
|   Infinite + Period   |         GE以 每Period秒 永远触发下去          | 相当于 以每Period秒 执行一次Instant触发 |
| Has Duration + Period | GE在 DurationMagnitude秒内 以 每Period秒 触发 |                                         |

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250820161047369.png)

### GE的使用

从 `UGameplayEffect` 即可派生出GE蓝图，后就可 **从ASC 或 GA 内Apply指定类型的GE**：

![ASC内应用GE](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704204038864.png)

![GA内应用GE](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704204158308.png)

## Gameplay Cue（GC）

GameplayCue（GC）常用于处理 GAS系统内 **非游戏流程逻辑相关的业务**，例如 粒子特效、音效、震动等 **交互反馈表现**

目前GC分为2类：

- `UGameplayCueNotify_Static`：适用于 一次性播放的特效。其不会在Level内生成实例。对应Instant和Periodic的GE
- `AGameplayCueNotify_Actor`：适用于 持久性/不定时长的特效。每次触发会在Level内生成对应实例。对应Infinity和Has Duration的GE

### 用法

#### 1. 创建GC

打开GameplayCue编辑器（UE5位于 工具栏内），为所需GC创建一个Tag，后点击 处理器->新增，就显示为此Tag创建GC的蓝图的界面：

![image-20250705113603476](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250705113603476.png)

#### 2. GC蓝图设置

若是 Static类型的GC，则需要在蓝图内 重载`OnExecute`方法，并对 类默认值->细节面板->Gameplay Cue进行相关设置：

![image-20250705174816951](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250705174816951.png)

若是 Actor类型的GC，则需要在蓝图内 重载`OnActive`、`OnRemove`方法，并对 细节面板->Gameplay Cue、Clean Up 进行相关设置：

![image-20250705171754483](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250705171754483.png)

#### 3. GC的使用

可通过GE触发 目标Gameplay Cue Tags 的GC：

![image-20250705172452344](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250705172452344.png)

也可在GA内调用触发GC：

![image-20250705173205714](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250705173205714.png)

## GAS的调试方法

### [showdebug abilitysystem](https://dev.epicgames.com/community/learning/tutorials/Y477/unreal-engine-gameplay-ability-system-debugging-tools#showdebugabilitysystem)

运行中通过`~`键打开控制台，输入 `showdebug abilitysystem` 即可显示当前控制的Character的GAS相关数据（PageUp、PageDown切换查看对象）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250818202442845.png)

### [AbilitySystem.DebugBasicHUD](https://dev.epicgames.com/community/learning/tutorials/Y477/unreal-engine-gameplay-ability-system-debugging-tools#abilitysystemdebugbasichud)

运行中通过`~`键打开控制台，输入 `AbilitySystem.DebugBasicHUD` 即可显示，主要内容有：当前AttributeSet的信息、Attribute的监听对象、当前GE信息

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250818213114903.png)

### [Debug Widgets](https://dev.epicgames.com/community/learning/tutorials/Y477/unreal-engine-gameplay-ability-system-debugging-tools#debugwidgets)

运行中通过`~`键打开控制台，输入下列指令，即能可视化查看特定数据：

|                         commands                         |                             描述                             |                           使用示例                           |
| :------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| `AbilitySystem.DebugAttribute [Attribute1] [Attribute2]` | 在所有带有 目标Attribute的Actor上，绘制目标Attribute的数据信息 | `AbilitySystem.DebugAttribute Health MaxHealth WeaponDamage` |
|      `AbilitySystem.DebugAbilityTags [Tag1] [Tag2]`      | 在所有带有 目标GamePlayTag的Actor，绘制带有Tag及层数信息（无Tag就不绘制） | 显示=`AbilitySystem.DebugAbilityTags Damage.Conditions.DamageImmune`；清除=`AbilitySystem.DebugAbilityTags` |
|           `AbilitySystem.ClearDebugAttributes`           |       清除 `AbilitySystem.DebugAttribute` 所绘制的信息       |                                                              |

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250819203922424.png)

### [Gameplay Debugger](https://dev.epicgames.com/community/learning/tutorials/Y477/unreal-engine-gameplay-ability-system-debugging-tools#gasingameplaydebugger)

UE内置的调试器，能在 运行态时 可视化查看 目标Actor上的 AI、BehaviorTree、Abilities、EQS、Perception相关的数据

在Outliner大纲视图内，选中想要查看的Actor，回到Game中 点击键盘`'`键，即可显示窗口。通过快捷键（修改快捷键在 项目设置->引擎->Gameplay调试器）切换页签到 3.Abilities 后，即可查看GAS相关数据：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250818201841408.png)

### [GAS in Visual Logger](https://dev.epicgames.com/community/learning/tutorials/Y477/unreal-engine-gameplay-ability-system-debugging-tools#gasinvisuallogger)

可视化记录器（Visual Logger），位于 工具->调试->可视化记录器。可将AbilitySystem相关日志 以时间轴形式展现：
![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250819203951301.png)

## 参考文章

- [Gameplay技能系统 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/gameplay-ability-system-for-unreal-engine?application_version=5.4)
- [虚幻引擎游戏技能系统文档 - CSDN](https://blog.csdn.net/pirate310/article/details/106311256)
- [【Unreal】虚幻GAS系统快速入门 - 知乎](https://zhuanlan.zhihu.com/p/486808688)
- [UE5 GAS Base - CSDN](https://blog.csdn.net/qq_52179126/article/details/131860252)
- [UE4 GAS/ActionRPG学习导图——AttributeSet（Gameplay Ability System）- CSDN](https://blog.csdn.net/jk_chen_acmer/article/details/115309073)
- [Gameplay Ability System - Debugging Tools - UnrealEngineDev](https://dev.epicgames.com/community/learning/tutorials/Y477/unreal-engine-gameplay-ability-system-debugging-tools)



# Behavior Tree

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250819204016556.png)

[行为树](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/behavior-trees-in-unreal-engine?application_version=5.5) 是UE内 **非玩家角色单位的 行为指导**

根据 黑板（BlackBoard）存储、提供的必要数据，行为树蓝图内部 **[通过事件驱动](https://dev.epicgames.com/documentation/en-us/unreal-engine/behavior-tree-in-unreal-engine---overview?application_version=5.5#behaviortreesareevent-driven)** 进行系列 **流程决策、执行**

![20250809_184606](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250809_184606.gif)

## 黑板（BlackBoard）

**黑板** 是一项独立资源，用于存储记录 行为树所需的信息（名为 **黑板键**）

![image-20250809183540927](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250809183540927.png)

## 行为树

行为树与蓝图相似，皆是以一种**可视化**方式创建，将一系列具备功能的节点添加并连接至行为树图表

执行逻辑时，行为树会使用 **黑板** 来存储它需要知道的信息（名为 **黑板键**），从而做出有根据的决策：

![image-20250809183824642](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250809183824642.png)

### 行为树 - 基本用法

在创建完一个行为树后，可在 AIController内的 `OnPossess`事件中，选择执行目标行为树：

![image-20250809184255645](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250809184255645.png)

### 行为树节点

行为树节点（`UBTNode`）执行行为树的主要工作，实现各类特定业务，例如 任务、逻辑流程控制、数据更新等

除 [根节点](https://dev.epicgames.com/documentation/en-us/unreal-engine/behavior-tree-node-reference-in-unreal-engine?application_version=5.5#behaviortreenodetypes) 外，行为树节点可分为 4种类型：

|                           节点类型                           |                             说明                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| [合成节点](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-behavior-tree-node-reference-composites?application_version=5.5) |            定义分支的根以及执行该分支的 基本规则             |
| [装饰器节点](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-behavior-tree-node-reference-decorators?application_version=5.5) | 也称为**条件**。它们连接到另一节点，并**决定树中的分支、甚至单个节点能否被执行** |
| [任务节点](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-behavior-tree-node-reference-tasks?application_version=5.5) |                   是某段 **可执行的操作**                    |
| [服务节点](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-behavior-tree-node-reference-services?application_version=5.5) | 此类节点附接至合成节点，而且只要其分支正在执行，它们就会按照定义的频率执行。它们通常用于**检查和更新黑板**。它们取代了其他行为树系统中的传统并行节点 |

#### 根节点

即 **行为树的起始点**，特殊节点（不可挂载 装饰器或服务节点）。点选后可 查看、修改 此行为树使用的 黑板资源（Blackboard Asset）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250819202925567.png)

#### 合成节点

[合成节点（Composite）](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-behavior-tree-node-reference-composites?application_version=5.5)：是 定义分支的根，确定了**执行该分支的基本规则**

合成节点仅有官方提供的3种：

1. [选择器（Selector）](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-behavior-tree-node-reference-composites?application_version=5.5#selector)：**子节点按 从左到右顺序执行**，当某个子节点执行成功 则选择器执行成功 且 停止执行，而当全部子节点执行失败，则选择器执行失败。即 **顺序执行，直到子节点成功 才停止**

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250819202941209.png)

2. [序列（Sequence）](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-behavior-tree-node-reference-composites?application_version=5.5#sequence)：**子节点按 从左到右顺序执行**，当全部子节点执行成功，则序列执行成功，而当某个子节点实行失败 则序列执行失败 且 停止执行。即 **顺序执行，有子节点失败 就停止**

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250819203003628.png)

3. [简单平行节点（SimpleParallel）](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-behavior-tree-node-reference-composites?application_version=5.5#simpleparallel)：左侧为 任务节点（称为 主任务），右侧为 后台树，**两边 并发执行**。当 主任务执行完成时，根据节点设置的结束模式（FinishMode）不同，后台树可能 被立即终止 或 等待执行完成

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250819203023732.png)

#### 装饰器节点

[装饰器节点（Decorator）](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-behavior-tree-node-reference-decorators?application_version=5.5)：在其他行为树系统中也称为 **条件语句**，附着于另一个节点，**决定了树中的 分支或节点 是否能够被执行**

装饰器节点含诸多 [预设类型](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-behavior-tree-node-reference-decorators?application_version=5.5)，也可以[自定义装饰器](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-behavior-tree-node-reference-decorators?application_version=5.5#customdecorators)：

![image-20250809172418520](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250809172418520.png)

以 [黑板节点](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-behavior-tree-node-reference-decorators?application_version=5.5#blackboard) （检查给定的 **黑板键（Blackboard Key）** 上是否设置了值）为例：

1. 有 **"Hash Line of Sight？"**装饰器节点：
   - 当 黑板键 **HasLineOfSight** 的数值为 **已设置** （或为true）时，条件达成，才允许进入 **追逐玩家（Chase Player）** 分支
   - 通知观察者=结果改变时+观察者终止=Both，则表示 当黑板键 **HasLineOfSight** 布尔值相互变化时（即 条件判断结果变化），自身分支（**Chase Player**）及 同层右侧分支 都将停止。最终回到 **AI Root**根节点
2. 例如 初始时 **HasLineOfSight**=false，不进入**Chase Player**分支，按顺序进入 **Patrol**分支。而当 **HasLineOfSight**->true 时，则中断**Chase Player**及右侧全部分支（正在执行**Patrol**分支的话 也将被中断）。最终回到 **AI Root**根节点，又顺序进入 **Chase Player**分支、又进行  **HasLineOfSight** 的条件判断为达成，成功进入 **Chase Player**分支

![image-20250809172827877](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250809172827877.png)

#### 任务节点

[任务节点（Tasks）](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-behavior-tree-node-reference-tasks?application_version=5.5)：**实现某项功能操作，并最终产生执行结果**，例如 移动AI到目标位置、修改黑板值、自定义功能等

任务节点含诸多 [预设类型](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-behavior-tree-node-reference-tasks?application_version=5.5)，也可以[自定义任务节点](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-behavior-tree-node-reference-tasks?application_version=5.5#customtasks)：

![image-20250810203051750](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250810203051750.png)

以 [移动至（MoveTo）节点](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-behavior-tree-node-reference-tasks?application_version=5.5#moveto)为例：设定 黑板键 为 Actor类型的关键帧 `EnemyActor`，则执行MoveTo节点时，所属的 AI Pawn会移动到目标位置

![image-20250810203905169](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250810203905169.png)

##### 基本用法

任务节点的基本使用流程为：开始触发、自定义逻辑处理、产生执行结果

以 [官方案例](https://dev.epicgames.com/documentation/en-us/unreal-engine/behavior-tree-in-unreal-engine---quick-start-guide?application_version=5.5)内的 [Task-ChasePlayer节点](https://dev.epicgames.com/documentation/en-us/unreal-engine/behavior-tree-in-unreal-engine---quick-start-guide?application_version=5.5#4-tasksetup-chaseplayer)为例：

1. 新建自定义任务节点，接收到 `Event Receive Execute AI` 时即代表 开始执行任务节点
2. 后执行自定义逻辑处理（修改AIPawn的追逐速度）
3. 最终 产生执行结果（`Finish Execute(bool isSuccess)`），即代表 此节点的执行结果

![image-20250810221030083](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250810221030083.png)

C++层的实现也类似，例如 官方任务节点 `UBTTask_Wait : UBTTaskNode` ：

![image-20250810223152216](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250810223152216.png)

#### 服务节点

[服务节点（Service）](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-behavior-tree-node-reference-services?application_version=5.5)：通常挂载到 合成节点或任务节点上，只要其分支被执行，它们就会 **以定义的频率执行**，没有返回值 且 不直接影响执行流程。常用于 **检查和更新黑板**

预设的服务节点有 [默认聚焦](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-behavior-tree-node-reference-services?application_version=5.5#defaultfocus) 和 [运行EQS](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-behavior-tree-node-reference-services?application_version=5.5#runeqs)，也可以 [自定义服务节点](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-behavior-tree-node-reference-services?application_version=5.5#customservices)

##### 基本用法

基于 [官方案例](https://dev.epicgames.com/documentation/en-us/unreal-engine/behavior-tree-in-unreal-engine---quick-start-guide?application_version=5.5) ，新建一个 自定义服务节点，挂载到 合成节点Patrol上，则

- 当 进入Patrol子树后，触发一次 `Event Receive Search Start`（备注：仅限 挂载于合成节点上 的情况）
- 当 Patrol子树 成功运行，触发一次 `Event Receive Activation`，此服务节点 处于激活态。激活态期间 根据配置间隔和随机偏差 周期性触发 `Event Receive Tick`
- 当 Patrol子树全部完成运行或被中断 且 服务节点处于激活态，触发一次 `Event Receive Deactivation`

![image-20250810231821702](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250810231821702.png)

##### 参考文章

- [[UE4][C++][AI]自定义行为树节点——Service(服务节点) - 知乎](https://zhuanlan.zhihu.com/p/296462878)



# 寻路系统

UE的[寻路系统](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/basic-navigation-in-unreal-engine)，主要用于 **为 AI Agent 提供寻路功能**

基本使用流程为：

1. 在场景生成 寻路网格体 
2. （非必要）运行态时 寻路网格体的重构
3. 寻路代理 使用 寻路网格体 进行寻路移动

## 寻路网格体

寻路网格体（Nav Mesh），是 **针对Level生成、并存储于Level的数据**，其在运行态时 随Level被加载、使用，亦可在运行态时 被重构

### 寻路网格体 运行态时的生成规则

寻路网格体虽然是在 编辑器态（离线下）生成、存储到Level的，但在运行态时，针对 **运行态下 是否可重构、修改寻路网格体** 问题，将寻路网格体的 **[运行态生成规则](https://dev.epicgames.com/documentation/en-us/unreal-engine/overview-of-how-to-modify-the-navigation-mesh-in-unreal-engine#4-generatingthenavigationmeshatruntime)** 分为了3类：

（可在 *项目设置->引擎->导航网格体->运行时生成* 进行设置）

|               生成规则               |                             描述                             | 备注 |
| :----------------------------------: | :----------------------------------------------------------: | :--: |
|            静态（Static）            |                    运行加载后，无法再更改                    |      |
|           动态（Dynamic）            | 运行时将重新构建。期间 寻路网格使用的数据可被更新、网格体可重构、新生成 |      |
| 限动态修饰（Dynamic Modifiers Only） | 运行加载后，期间仅有 寻路区域/寻路链接/动态对象 上的寻路修饰 可修改网格体数据（影响寻路结果），但不会新生成网格体 |      |

### [寻路网格体的生成](https://dev.epicgames.com/documentation/en-us/unreal-engine/basic-navigation-in-unreal-engine#2-buildingthenavigationmesh)

在目标Level下，放置 **寻路网格体边界体积（Navigation Mesh Bounds Volume）** 的Actor对象到场景内、调整其覆盖范围，即可生成寻路网格体。按下 `P` 键，能可视化看到网格体：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250823171144851.png)

此外，还会附带生成一个 **RecastNavMesh-Default** 对象到场景，其功能有 **调整网格体生成选项、显示设置等**：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250823171824640.png)

## 参考文章

- [寻路系统 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/basic-navigation-in-unreal-engine)
