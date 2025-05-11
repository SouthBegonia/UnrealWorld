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



# GamePlay框架的 Runtime启动流程

![image-20250510155149664](Pic/UE-GamePlay框架的Runtime流程.png)

![image-20250510155149664](Pic/UE-GamePlay框架的Runtime流程2.png)

## 参考文章

- [Unreal Engine 的启动流程 - 放牛的星星 - 知乎](https://zhuanlan.zhihu.com/p/610523485)
- [UE4 Gameplay之GameMode流程分析(一) - 孤傲雕 - 知乎](https://zhuanlan.zhihu.com/p/70045930)
