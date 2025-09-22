
- [总览](#总览)
- [Behavior Tree](#behavior-tree)
  - [黑板（BlackBoard）](#黑板blackboard)
  - [行为树](#行为树)
    - [行为树 - 基本用法](#行为树---基本用法)
    - [行为树节点](#行为树节点)
      - [根节点](#根节点)
      - [合成节点](#合成节点)
      - [装饰器节点](#装饰器节点)
        - [基本用法](#基本用法)
        - [面板说明](#面板说明)
        - [参考文章](#参考文章)
      - [任务节点](#任务节点)
        - [基本用法](#基本用法-1)
      - [服务节点](#服务节点)
        - [基本用法](#基本用法-2)
        - [参考文章](#参考文章-1)
- [StateTree](#statetree)
  - [状态树（StateTree）](#状态树statetree)
    - [配置与使用](#配置与使用)
    - [状态树模块](#状态树模块)
      - [输入条件（Enter Conditions）](#输入条件enter-conditions)
        - [自定义实现 输入条件](#自定义实现-输入条件)
      - [任务（Tasks）](#任务tasks)
        - [自定义实现 任务](#自定义实现-任务)
      - [过渡（Transitions）](#过渡transitions)
      - [求值器（Evaluators）](#求值器evaluators)
        - [自定义实现 求值器](#自定义实现-求值器)
      - [全局任务（Global Tasks）](#全局任务global-tasks)
  - [用法示例](#用法示例)
    - [示例1](#示例1)
    - [示例2](#示例2)
  - [参考文章](#参考文章-2)
- [寻路系统](#寻路系统)
  - [寻路网格体](#寻路网格体)
    - [寻路网格体 运行态时的生成规则](#寻路网格体-运行态时的生成规则)
    - [寻路网格体的生成](#寻路网格体的生成)
    - [寻路网格体的修改](#寻路网格体的修改)
      - [寻路修饰体积](#寻路修饰体积)
      - [寻路链接代理](#寻路链接代理)
        - [简单链接](#简单链接)
        - [智能链接](#智能链接)
      - [寻路区域/区域类](#寻路区域区域类)
    - [寻路网格体的优化](#寻路网格体的优化)
  - [寻路代理](#寻路代理)
    - [寻路查询筛选器](#寻路查询筛选器)
  - [寻路行为](#寻路行为)
    - [AIMoveTo](#aimoveto)
    - [UBTTask\_MoveTo](#ubttask_moveto)
  - [参考文章](#参考文章-3)
- [感知系统](#感知系统)
  - [感知组件（AI Perception Component）](#感知组件ai-perception-component)
    - [感知属性（AI Perception Properties）](#感知属性ai-perception-properties)
      - [感知类型（AI Sense）](#感知类型ai-sense)
- [EQS](#eqs)
  - [生成器（Generators）](#生成器generators)
    - [Item的类型](#item的类型)
    - [基本用法](#基本用法-3)
    - [生成器节点](#生成器节点)
      - [Actors of Class](#actors-of-class)
      - [Composite](#composite)
      - [Current Location](#current-location)
      - [Points: XXXX](#points-xxxx)
      - [自定义生成器](#自定义生成器)
        - [蓝图实现](#蓝图实现)
        - [C++实现](#c实现)
  - [情景（Contexts）](#情景contexts)
    - [基本用法](#基本用法-4)
    - [自定义情景](#自定义情景)
      - [蓝图实现](#蓝图实现-1)
      - [C++实现](#c实现-1)
  - [测试（Test）](#测试test)
    - [基本用法](#基本用法-5)
      - [细节面板 - 测试](#细节面板---测试)
    - [测试节点](#测试节点)
      - [Distance](#distance)
      - [Dot](#dot)
      - [Trace](#trace)
      - [Overlap](#overlap)
  - [EQS用法示例](#eqs用法示例)
    - [BehaviorTree 使用EQS](#behaviortree-使用eqs)
    - [StateTree 使用EQS](#statetree-使用eqs)
  - [调试](#调试)
  - [参考文章](#参考文章-4)
- [Smart Objects](#smart-objects)
  - [模块介绍](#模块介绍)
    - [智能对象子系统（Smart Object Subsystem）](#智能对象子系统smart-object-subsystem)
    - [游戏行为配置（Gameplay Behavior Config）](#游戏行为配置gameplay-behavior-config)
    - [游戏行为（Gameplay Behavior）](#游戏行为gameplay-behavior)
    - [智能对象定义（Smart Object Definition）](#智能对象定义smart-object-definition)
    - [智能对象组件（Smart Object Component）](#智能对象组件smart-object-component)
  - [流程介绍](#流程介绍)
  - [基本用法](#基本用法-6)
  - [参考文章](#参考文章-5)
- [参考文章](#参考文章-6)




![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)

# 总览

UE内的AI，可概述为 AI单位的**智能行为** + 行为的**环境准备**，旨在于 **实现非玩家单位（AI单位）的智能交互行为**

- 智能行为：
  - 环境感知
  - 决策/方案
  - 行为/能力
- 环境准备：
  - 导航系统
  - 能力组件


![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



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

##### 基本用法

以 [黑板节点](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-behavior-tree-node-reference-decorators?application_version=5.5#blackboard) （检查给定的 **黑板键（Blackboard Key）** 上是否设置了值）为例：

1. 有 **"Hash Line of Sight？"**装饰器节点：
   - 当 黑板键 **HasLineOfSight** 的数值为 **已设置** （或为true）时，条件达成，才允许进入 **追逐玩家（Chase Player）** 分支
   - 通知观察者=结果改变时+观察者终止=Both，则表示 当黑板键 **HasLineOfSight** 布尔值相互变化时（即 条件判断结果变化），自身分支（**Chase Player**）及 同层右侧分支 都将停止。最终回到 **AI Root**根节点
2. 例如 初始时 **HasLineOfSight**=false，不进入**Chase Player**分支，按顺序进入 **Patrol**分支。而当 **HasLineOfSight**->true 时，则中断**Chase Player**及右侧全部分支（正在执行**Patrol**分支的话 也将被中断）。最终回到 **AI Root**根节点，又顺序进入 **Chase Player**分支、又进行  **HasLineOfSight** 的条件判断为达成，成功进入 **Chase Player**分支

![image-20250809172827877](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250809172827877.png)

##### 面板说明

**黑板键（Blackboard Key）** ：

- 通知观察者（Notify Observer）：
  - 值改变时 = **黑板键的 键值变化（Value1 <=> Value2）** 时触发通知观察者。适用于 值类型的黑板键
  - 结果改变时 = **黑板键的 结果发生变化（Null <=> Actor1/Actor2/...）**时触发通知观察者。适用于 引用类型的黑板键
- 观察者终止（Observer Aborts）：观察器 收到触发通知时的 中断行为
  - None = 不中断
  - Self = 中断其所在子树，并返回失败
  - Low Priority = 中断所有 低优先级的节点、子树（直观上就是 其右侧全部子树）
  - Both = Self + Low Priority

##### 参考文章

- [Notify Observer difference? - UnrealEngineDev](https://forums.unrealengine.com/t/notify-observer-difference/411460)
- [WTF Is? AI: Blackboard Decorator Node in Unreal Engine 4 ( UE4 ) - Youtube](https://www.youtube.com/watch?v=PcBV-X5R9dE&t=366s&ab_channel=MathewWadsteinTutorials)
- [UE5 行为树（1）：运行逻辑 - 知乎](https://zhuanlan.zhihu.com/p/667581758)



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



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# StateTree

[StateTree](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/state-tree-in-unreal-engine)是UE内的 **通用分层状态机**，组合了行为树中的 选择器（Selectors） 与状态机中的 状态（States） 和 过渡（Transitions）

有别于 BehaviorTree可实现的 **高智能、频繁感知、EQS的AI**，StateTree主要作用是实现 **数量庞大、低智能、AI**

> 备注：截至目前 UE5.6，官方还在对StateTree模块进行扩充、修改，因此建议 ST功能预览则用最新UE版本，业务实现则按需选择

## 状态树（StateTree）

### 配置与使用

在 Plugins内添加 **GameplayStateTree** 和 **StateTree** 插件后，即可新建 ST资产：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250905193850568.png)

后为单位添加 `UStateTreeComponent` 或 `UStateTreeAIComponent`，并设置 StateTree资产、在StateTree内设置对应单位的上下文类。运行后将 自动执行进入StateTree

- `UStateTreeComponent`：上下文只关联 Actor类。适用于 不含AIController的单位
- `UStateTreeAIComponent`：上下文关联 Actor+Controller。适用于 带AIController的单位

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250905195935677.png)

**StateTree基本运行规则**为：

1. 自顶向下 逐层检查State 是否可进入
2. 进入State后依次执行 此State的任务队列
3. 依照 此State的过渡规则 可在各类情况下（State完成/成功/失败、事件通知等）过渡到其他State

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250905194803273.png)

### 状态树模块

#### 输入条件（Enter Conditions）

输入条件用于 **判断是否能进入此State**。内置包含 参数比较、Tags持有判断等，亦可 自行继承实现自定义条件判断。对应C++层的 `FStateTreeConditionBase : FStateTreeNodeBase`

但注意 判断对比的参数源 只有上下文Actor的成员参数及 StateTree左侧面板的参数（这块参数全StateTree可读不可写，外部也不可读取/修改，相当于常量）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250905201137265.png)

##### 自定义实现 输入条件

例如下图 新建一个**蓝图形式的输入条件** `STD_TestCondition : UStateTreeConditionBlueprintBase`，重写`Receive Test Condition`，其返回值即为Condition：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250908142822624.png)

若是 **C++形式的输入条件**，以 UE5.6中的[Variant_Combat示例](https://github.com/EpicGames/UnrealEngine/blob/release/Templates/TP_ThirdPerson/Source/TP_ThirdPerson/Variant_Combat/AI/CombatStateTreeUtility.h) 中为例，其创建了一个 判断自身Character是否落地的输入条件 `FStateTreeCharacterGroundedCondition : FStateTreeConditionCommonBase`：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250908144013760.png)

后在 `TestCondition(FStateTreeExecutionContext& Context)`内实现判断逻辑、返回Condition值，并使用 Categoty=Condition的成员参数`bMustBeOnAir : bool` 以设置Condition的双向判断：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250908144600435.png)

#### 任务（Tasks）

任务是 **某个State期间的一段功能逻辑函数，任务的完成性 决定了 此State的完成性**。对应C++层的 `FStateTreeTaskBase : FStateTreeNodeBase`

例如 有一个 `Patrol And Move` 的State，为其创建、添加一个蓝图形式的任务 `STT_MoveToLoaction : UStateTreeTaskBlueprintBase` 功能为移动自身到目标位置，其周期函数有：

- `Event EnterState`：进入此State 开始执行此任务时触发
- `Event ExitState`：此State在 任务EnterState后 退出其状态时触发
- `Event StateCompleted`：此State完成时触发
- `Event Tick`：任务激活期间（EnterState 到 ExitState）的Tick

当我们执行完成移动逻辑后，相当于 `Patrol And Move` **当前State就算完成了，一定要 主动调用 `Finish Task`**，则State才会走 过渡判断（图例是顺序过渡到Wait），否则将一直处于 `Patrol And Move`状态。而 `STT_MoveToLoaction ` 之前还有的 `STT_FindRandomPatrol`、`STT_SetMoveSpeed` 任务内 就可以不调用`Finish Task`

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250905202053007.png)

此外我们可以设置 **任务函数的传入、传出参数**。例如有一个 `STT_FindRandomPatrol : UStateTreeTaskBlueprintBase` 功能为 提供一个范围内的随机可达坐标。则我们可以：

- 传入 上下文参数 `Actor`（添加变量后、输入设置其Categoty=Context。StateTree内会自动绑定参数源为 上下文Actor）
- 传入 限制范围参数 `PatrolRadius: float`（添加变量后、输入设置其Categoty=Input。StateTree内设置参数源为 全局参数 `PatrolRadius=1000`）
- 计算后设置传出参数 `PatrolLocation : Vector`（添加变量后、输入设置其Categoty=Output。StateTree内后续任务就可获取此值作为参数源，例如后面 `STT_MoveToLoaction `任务的`TargetLocation`）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250905205310275.png)

##### 自定义实现 任务

**蓝图形式的任务**，从 `UStateTreeTaskBlueprintBase` 派生后重写 `Event EnterState`、`Event ExitState`、`Event StateCompleted`、`Event Tick`几个事件即可，可参照上文实现

**C++形式的任务**，则从 `FStateTreeTaskCommonBase`派生后重写，以 UE5.6中的[Variant_Combat示例](https://github.com/EpicGames/UnrealEngine/blob/release/Templates/TP_ThirdPerson/Source/TP_ThirdPerson/Variant_Combat/AI/CombatStateTreeUtility.h)，其创建了一个 自身Character发动攻击的任务 `FStateTreeComboAttackTask : FStateTreeTaskCommonBase`：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250908153524102.png)

随后也是同样的重写相关方法：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250908154158808.png)

#### 过渡（Transitions）

过渡是指 **State之间的切换规则**，基本流程为：通过契机**触发过渡判断**时、**判断过渡条件**是否通过、通过则从此State切换到目标State

（下图1）在State细节面板上，添加一条过渡，其参数有：

- **触发（Trigger）**：即 触发过渡判断的契机
  - 状态完成时（On State Conpleted）
  - 状态成功时（On State Succeeded）
  - 状态失败时（On State Failed）
  - 状态Tick期间（On Tick）
  - 状态期收到事件（On Event）
- **过渡到（Transition To）**：
  - 下个状态：即 面板上此State的 **相邻下一条State**。但是 **倘若下条State不满足它的 输入条件，则过渡失败**、仍将处于此State
  - 下个可选状态：即 顺序检查面板上 **此State的后续State**，**倘若 后续某条State满足它的 输入条件，则过渡成功**，否则过渡失败。例如 下图2中，ST_2输入条件恒不满足，ST_1的过渡需配置为 下个可选状态才可顺延过渡到ST_3，倘若配置为 下个状态 则过渡到ST_2失败、仍将处于ST_1
  - 树成功、树失败：标记当前StateTree的完成状态，也将 **终止执行此StateTree**
  - 指定State：同理也将检查目标State的  输入条件
- **条件（Conditions）**：即 过渡条件，满足全部条件后 才允许此过渡。与输入条件一样 可采用 `FStateTreeConditionBase `，其自定义实现方案也相同

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250906152813229.png)

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250906154304940.png)

#### 求值器（Evaluators）

求值器 是StateTree资产详情面板的成员，其主要功能是 **在StateTree起始/结束/Tick 时，计算、提供参数给到 树内各State使用**，其在StateTree开始时 就一并执行

例如下图，新建一个 求值器`STE_Test_Global : StateTreeEvaluatorBlueprintBase`，其核心事件有：

- `Event TreeStart`
- `Event TreeStop`
- `Event Tick`

我们可以通过此求值器，在 `Event TreeStart` 时 计算、缓存所需业务参数（`ActorMoveComp`），后续的State就可以直接 获取、使用此参数（而不是 State自身又通过Task 实现求值器一样的计算逻辑），且 **求值器可被此StateTree内 全部State访问使用**

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250906161259988.png)

##### 自定义实现 求值器

**蓝图形式的求值器**，从 `StateTreeEvaluatorBlueprintBase`派生后重写 `Event TreeStart`、`Event TreeStop`、`Event Tick`事件即可，可参照上文实现

**C++形式的求值器**，则从 `FStateTreeEvaluatorBase :FStateTreeNodeBase`派生，也是同样的重写`TreeStart`、`TreeStop`、`Tick`方法

#### 全局任务（Global Tasks）

区别于上文的任务是 挂在于某个State下的，**全局任务则位于 StateTree资产详情面板内**，其挂载的也 同类型的`FStateTreeNodeBase`（`UStateTreeTaskBlueprintBase`、`FStateTreeTaskCommonBase` 的父类）类型的任务，也就包含有相同的事件（`Event EnterState`、`Event Tick`等）

类似于 求值器，其也是在StateTree开始时 就一并执行，二者功能上有重叠性（Tick、能计算提供参数给到StateTree节点）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250905204016814.png)

## 用法示例

### 示例1

例如 用StateTree实现 [UE官方BehaviorTree示例](https://dev.epicgames.com/documentation/en-us/unreal-engine/behavior-tree-in-unreal-engine---quick-start-guide?application_version=5.5#endresult)，二者基本结构例如：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250905192536172.png)

区别于 BehaviorTree实现（在AIController中 AIPerception感知时 更新黑板键 HasLineOfSight），StateTree则调整为 在AIController中 AIPerception感知时 SendEvent到StateTree、以驱动StateTree切换State：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250905195358232.png)

### 示例2

例如 官方在UE5.6下ThirdPerson模板项目内的[Variant_Combat](https://github.com/EpicGames/UnrealEngine/tree/release/Templates/TP_ThirdPerson/Source/TP_ThirdPerson/Variant_Combat) 为例，其用StateTree实现了AI敌人的行为（待机、侧移对峙、攻击）及特殊状态处理（落地、死亡）：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250908194019948.gif)

其StateTree结构如下图，流程简述就是：非死亡期间，进行 索敌攻击、待机、侧移对峙 3选1随机执行；外加Tick期间判断死亡、落地的状态过渡

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250908194646472.png)

数据源问题：

- 全局任务`FStateTreeGetPlayerInfoTask`在Tick期间持续获取更新`TargrtPlayerCharacter`等数据（也就是被AI单位视为敌人的 玩家的Character），为各类Condition判断或Task提供了核心参数
- 状态树模式为 `UStateTreeAIComponent`，则可持有 上下文Actor和上下文AIController的指针，也就能 获取二者的成员参数以供StateTree各部分使用

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250908193211546.png)

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250908193121013.png)

## 参考文章

- [《游戏AI系统其三：状态树StateTree》 - 知乎](https://zhuanlan.zhihu.com/p/27481809513)
- [Learn All You Need to Know About State Trees In Unreal Engine FOR FREE - Youtube](https://www.youtube.com/watch?v=BAYZgAzs7RM&ab_channel=TheGameDevCave)
- [虚幻的行为树 Behavior Tree (BT)与状态树 State Tree (ST)对比分析 - 知乎](https://zhuanlan.zhihu.com/p/1918237969791288178)




![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



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

### [寻路网格体的修改](https://dev.epicgames.com/documentation/en-us/unreal-engine/modifying-the-navigation-mesh-in-unreal-engine?application_version=4.27)

在基于 寻路网格体边界体积（Navigation Mesh Bounds Volume）生成了寻路网格体后，亦可再 **对寻路网格体进行修改（网格是否生成、路径成本、可行性）**

#### [寻路修饰体积](https://dev.epicgames.com/documentation/en-us/unreal-engine/modifying-the-navigation-mesh-in-unreal-engine?application_version=4.27#2-usingnavigationmodifiervolumes)

**寻路修饰体积（Nav Modifier Volumes）** 用于 **修改寻路网格体**
放置 Nav Modifier Volumes的Actor对象到场景内、调整其覆盖范围，即可 **修改所处范围的寻路网格体**：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250825160902305.png)

设置 Nav Modifier Volumes 上的 **区域类（Area Class）**即代表不同的修改效果：

|    Area Class     |                             说明                             |
| :---------------: | :----------------------------------------------------------: |
|  NavArea_Default  | 将相同的寻路成本指定给体积内的区域和寻路网格体。相当于 **不做修改** |
|   NavArea_Null    | 体积内的区域的寻路成本 无限，相当于 **移除寻路网格体、无法通过** |
| NavArea_Obstacle  | 体积内的区域的寻路成本 较高。相当于 **除非代理找不到更低成本的路径，否则不会走该区域** |
| NavArea_LowHeight |                在高度不足的地方使用，无法通过                |

#### [寻路链接代理](https://dev.epicgames.com/documentation/en-us/unreal-engine/overview-of-how-to-modify-the-navigation-mesh-in-unreal-engine#3-usingnavigationlinkproxies)

**寻路链接代理（Nav Link Proxy）** 由单组/多组 **左右成对的点链接构成**，点链接的左右两端之间 构成一段虚假的寻路网格体（可配置其AreaClass）、以 **构造 跨寻路网格体区域之间的 移动路线**

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250825193251967.png)

寻路链接代理分为2类：

- 简单链接（SimpleLink）：适用于 平地位移、从高台跳下。不可自定义移动逻辑
- 智能链接（SmartLink）：适用于 跳跃到高台等。需自行实现左右端点间的移动逻辑

##### 简单链接

简单链接 即为寻路链接代理最简单直观的表现。放置 寻路链接代理（Nav Link Proxy）的Actor到场景下，调节**左右端点位置**（必须位于 已有的寻路网格体内），设置**可行方向**（单向或双向），设置其区域类（通常=NavArea_Default，表示可行）。当看到 左右端点箭头正常显示、方向符合，即表示此Nav Link Proxy搭放正确，运行后能作为合法路径加入到寻路移动中

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250825191929338.png)

##### 智能链接

相比于简单链接，智能链接的核心区别在于：**可自定义实现 抵达智能链接端点后的逻辑处理**。流程上就是：代理移动到智能链接的端点后、触发`Receive Smart Link Reache事件`、后续自行实现移动逻辑（通常为跳跃或瞬移）

用法上，例如创建一个 `BP_NavProxyLink : NavLinkProxy` 蓝图，实现 `Receive Smart Link Reache事件`，编写代理的跳跃逻辑（跳跃到 智能链接端点的 末端/目标点）：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250825200033780.png)

后将 `BP_NavProxyLink` 放置于场景，先按照简单链接的用法 配置左右端点的位置及可行方向，后 **修改区域类为 None**，点击 **将端点从简单链接复制到智能链接（Copy End Points from Simple Link to Smart Link）** 按钮，再勾选 **智能链接有意义（Smart Link Is Relevant）** 复选框

运行后表现就是 代理移动到智能链接的端点、触发蓝图事件、执行跳跃逻辑到了目标端点位置，至此完成了寻路移动

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250825201021063.png)

#### 寻路区域/区域类

基于上文我们知道 Nav Modifier Volumes 中可以设置其 **区域类（Area Class）** ，以达到修改寻路网格体区域的可行性或移动成本，其核心就是 Area Class内的 **默认成本（Default Cost）**
从 `NavArea` 派生一个子类后，即可看到其设置参数：

- **默认成本（Default Cost）**：描述所其区域的成本，数值越大表示成本越高。默认=1=无额外成本（相当于 NavArea_Default）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250827145238720.png)

例如 创建了3个`NavArea` 的子类，设定到不同Nav Modifier Volumes上。其中蓝色、红色区域的成本为1，而粉色区域的成本为4，则寻路代理在移动时，将会避开粉色区域、选择走蓝色或红色区域：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250827144553845.png)

### 寻路网格体的优化

[优化寻路网格体的生成速度 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/optimizing-navigation-mesh-generation-speed?application_version=4.27)



## 寻路代理

寻路代理，通常指代 AI Agent，即 AIController所控制的Character

### 寻路查询筛选器

基于上文我们知道 创建完寻路网格体后，可以通过 Nav Modifier Volumes 修改网格体的可行性或移动成本，这样做的话相当于 **所有寻路代理 将共用同套寻路网格体 进行寻路、无法特殊处理单个代理**，因此就有了 **寻路查询筛选器（Navigation Query Filter）** ：解决 **单个寻路代理 在网格体上 各区域的移动成本重载**。

就例如：一个由陆地+河流构成的区域，单位可正常寻路移动、穿越河流，但火系单位 不可移动跨越河流，即 对于火系单位来说 河流区域的移动成本无限大

从 `Navigation Query Filter` 派生一个子类后，即可看到寻路查询筛选器的设置参数：

- 区域：
  - 区域类：需要自定义重载的 Area Class
  - 漫游开销重载：即重载 默认成本（Default Cost）
  - 进入开销重载：即重载 固定区域进入开销

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250827152959880.png)

例如 有3块默认成本一致的区域（蓝、粉、红）。新建一个 寻路查询筛选器后，设置筛选器 重载粉色、红色区域的成本较高、蓝色区域不变（不添加也行）、配置筛选器到Character上的 **筛选器类（Filter Class）** 栏。则 当代理开始寻路时，将会走蓝色区域 而不是成本被筛选器重载后成本较高的红、粉色区域：
![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250827154905457.png)



## 寻路行为

基于上文创建完成了 场景的寻路网格（核心是 Navigation Mesh Bounds Volume），现在我们就能 在移动代理（AI Agent）上调用**寻路相关方法**

### AIMoveTo

蓝图常用的AIMoveTo节点，对应C++位于 `UAIBlueprintHelperLibrary::CreateMoveToProxyObject(UObject* WorldContextObject, APawn* Pawn, FVector Destination, AActor* TargetActor = NULL, float AcceptanceRadius = 5.f, bool bStopOnOverlap = false) `

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250827165945169.png)

### UBTTask_MoveTo

行为树的 MoveTo 任务节点，对应C++位于 `UBTTask_MoveTo `
![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250827172627951.png)

## 参考文章

- [寻路系统 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/basic-navigation-in-unreal-engine)
- [虚幻引擎 NavMesh 导航寻路系统原理机制源码剖析 - 知乎](https://zhuanlan.zhihu.com/p/691181077)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# 感知系统

**[AI感知系统（AI Perception System）](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/ai-perception-in-unreal-engine?application_version=5.5)** 为Pawn提供了一种 **从环境中接收数据的方式**，例如 噪音的来源、AI是否遭到破坏、或AI是否看到了什么

实现上是通过 **AI感知组件（AI Perception Component）** 来完成，其相当于 刺激的监听器，收到刺激（视觉/听觉/伤害等）后触发响应事件

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250901164440545.gif)

## [感知组件（AI Perception Component）](https://dev.epicgames.com/documentation/en-us/unreal-engine/ai-perception-in-unreal-engine?application_version=5.5#ai-perception-component)

在一个Pawn单位上添加 `UAIPerceptionComponent : UActorComponent` 组件，其核心参数有：

- 感官配置：配置所需的 **感知属性**，例如 伤害/视觉/听觉等
- 事件：感知变化时 分发的各类事件
  - 目标感知更新时 `OnTargetPerceptionUpdated`

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250901154945220.png)

### [感知属性（AI Perception Properties）](https://dev.epicgames.com/documentation/en-us/unreal-engine/ai-perception-in-unreal-engine?application_version=5.5#ai-perception-properties)

可以添加 **感官配置**（`UAISenseConfig`）并 配置所需的 **感知类型**（`UAISense`），以实现不同的 感知逻辑

#### 感知类型（AI Sense）

感知类型 即为**感知逻辑的实现**，对应为 `AISense : UObject `

UE已有的类型有 视觉（`UAISense_Sight : AISense `）、听觉（`UAISense_Hearing : AISense `）、伤害（`UAISense_Damage : AISense `）等，对应用法可参阅 [AI Perception Properties - UnrealEngine](https://dev.epicgames.com/documentation/en-us/unreal-engine/ai-perception-in-unreal-engine?application_version=5.5#ai-perception-properties)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# EQS

[EQS（Environmental Query System）](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/environment-query-system-in-unreal-engine) 让AI单位能够 **根据查询类型 收集特定环境数据**，以供其做出决策

常用于 行为树、状态树，供 AI单位寻找最近敌人、最佳躲藏位置等



EQS的基本流程可概述为：基于实际场景 通过 [生成器](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/eqs-node-reference-generators-in-unreal-engine) **生成查询单位**，进行系列 [测试](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/eqs-node-reference-tests-in-unreal-engine) 以**计算 各查询单位的 权重结果**，最终提供 **外部使用此查询结果**。此外 生成查询单位或计算权重时 还可指定 [情景](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/eqs-node-reference-contexts-in-unreal-engine) 以限定 逻辑操作对象

## 生成器（Generators）

生成器 用于 **生成 将要测试和加权的 Item（位置或Actor）**，节点代码 `UEnvQueryGenerator : public UEnvQueryNode`

### Item的类型

关于 **Item的类型**（也相当于 **EQS的查询结果数据类型**）可根据 `UEnvQueryGenerator::ItemType` 辨别（派生的生成器节点，在其 构造函数内 会设置ItemType），ItemType可分为以下几类：

- `UEnvQueryItemType_ActorBase : UEnvQueryItemType_VectorBase`：在BT内 Item可对应提供 Actor或FVector类型的 黑板键（代码位于 `UEnvQueryItemType_ActorBase::StoreInBlackboard()`
- `UEnvQueryItemType_Direction : UEnvQueryItemType_VectorBase`：Item可对应 FVector类型的数据
- `UEnvQueryItemType_Point : UEnvQueryItemType_VectorBase`：Item可对应 FVector类型的数据

总结就是 **使用EQS时 需要根据所需结果类型（Actor/FVector）选择匹配的 生成器**

下图即为 UE预设的生成器的 Item类型：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250918160502216.png)

### 基本用法

首先启用 **场景查询编辑器（Environment Query Editor）**插件，后即可通过 内容浏览器->人工智能->场景查询 **创建EQS资产**

创建完成EQS资产后，从Root节点即可拉出 生成器节点。UE已有部分 [预设的生成器节点](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/eqs-node-reference-generators-in-unreal-engine)

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250916205142382.png)

### 生成器节点

#### Actors of Class

在 搜索中心 周围特定的 搜索半径 内查找 给定类的所有Actor，返回的Actor可以用作测试中的Item

例如下图：搜索 查询者自身 300范围内的 类型为`BP_EQS_Box`的Actor 作为Item

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250916203732027.png)

#### Composite

也就是 多个生成器节点 共同生成Item以供测试

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250916213731855.png)

#### Current Location

情景 的 位置坐标 作为Item

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250916214054458.png)

#### Points: XXXX

**Points:Circle**：在 情景周围 生成环状网格 作为Item

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250916215436789.png)

**Points:Cone**：以 Actor情景为中心 放射出锥体网格 作为Item

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250916215657846.png)

**Points:Donut**：以 情景为中心 生成放射状网格 作为Item

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250916220041880.png)

**Points:Grid**：在 情景周围 生成方形网格 作为Item

备注：通过设置 检测模式 以限定网格是否需符合寻路可达

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250916220355199.png)

**Points: Pathing Grid**：在 情景周围 基于寻路网格体范围 生成方形网格 作为Item：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250916214938017.png)

#### 自定义生成器

如以上UE预设的生成器 均无法满足我们需求（例如 使用Actors of Class生成，但又需要对Item进行筛选，一种做法是 Actors of Class+自定义测试，另种做法就是 自定义生成器）

##### 蓝图实现

从 `UEnvQueryGenerator_BlueprintBase`派生出 **自定义生成器蓝图**，后 **根据你的情景（Contexts）的类型** 重写 `DoItemGeneration` 或 `DoItemGenerationFromActors`、在函数体内 **添加生成Item**（主动调用`AddGeneratedVector`或`AddGeneratedActor`方法）

例如下图 创建了`EQS_AllWall`自定义生成器蓝图，其将查找关卡内全部`BP_EQS_Wall`的Actor、但只把`BP_EQS_Wall`对象成员中`IsTall`符合的 添加到Item生成（生成的Item为Actor类型），最终在`EQS_Test`内调用此 `EQS_AllWall`生成器、将会按需生成Item：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250922171607608.png)

##### C++实现

从 `UEnvQueryGenerator`即可派生 **自定义生成器**，重写`UEnvQueryGenerator::GenerateItems()`方法、并在函数体内 **添加生成Item**（调用 `FEnvQueryInstance::AddItemData(TArray<TypeValue>& ItemCollection)`方法）

以官方生成器节点Actors of Class 为例，其核心代码为：

```c++
// UEnvQueryGenerator_ActorsOfClass.h
class UEnvQueryGenerator_ActorsOfClass : public UEnvQueryGenerator
{
	GENERATED_UCLASS_BODY()

	UPROPERTY(EditDefaultsOnly, Category=Generator, meta=(AllowAbstract))
	TSubclassOf<AActor> SearchedActorClass;

	AIMODULE_API virtual void GenerateItems(FEnvQueryInstance& QueryInstance) const override;
    
    // OtherCode
};

// UEnvQueryGenerator_ActorsOfClass.cpp
void UEnvQueryGenerator_ActorsOfClass::GenerateItems(FEnvQueryInstance& QueryInstance) const
{
	// OtherCode

	TArray<AActor*> MatchingActors;

    // OtherCode：从World检索合法的Actor

	QueryInstance.AddItemData<UEnvQueryItemType_Actor>(MatchingActors);
}
```

## 情景（Contexts）

情景 可以作为 生成器或测试 逻辑处理中的 **参考值**，此值可为 Actor/Actor集合/坐标/坐标集合

### 基本用法

以下图为例，在 EQS_Test内，使用了 Points:Grid生成器节点，其细节面板内的 周围生成（Generate Around）即为 情景，当前选择的是 `EnvQueryContext_Querier`情景表示 查询者自身，表现上就是 基于查询者自身 生成Item网格

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250917152034487.png)

UE提供 [预设情景节点](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/eqs-node-reference-contexts-in-unreal-engine) 有：

- `UEnvQueryContext_Item: UEnvQueryContext`
- `UEnvQueryContext_Querier : UEnvQueryContext`：查询的OwnerActor作为参考值
- `UEnvQueryContext_BlueprintBase : UEnvQueryContext`：供蓝图派生实现

### 自定义情景

#### 蓝图实现

从 `UEnvQueryContext_BlueprintBase` 即可派生自定义情景，重写 **4个方法中的任意个即可**（可参阅源码，4方法的调用优先级逐级上升）

- ProvideSingleActor：提供 单一Actor 作为参考值
- ProvideSingleLocation：提供 单一FVector 作为参考值
- ProvideActorsSet：提供 Actor集合 作为参考值
- ProvideLocationsSet：提供 FVector集合 作为参考值

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250917155145786.png)

以下图为例：`EQC_PlayerContext` 情景需要提供 本地玩家的Actor 作为参考值，则 重写`ProvideSingleActor`、返回玩家Actor即可；但此方法在编辑态下无效，则 为了方便测试`AEQSTestingPawn`，我们可以重写`ProvideActorsSet` 返回编辑器关卡内的玩家类 即可在编辑器态下查看效果（但注意：运行态下 将会走 `ProvideActorsSet` 而不是 `ProvideSingleActor`，因此建议测完后删除）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250917160245727.png)

#### C++实现

C++层实现则为 从`UEnvQueryContext` 派生自定义情景类、重写`ProvideContext`方法、设置参考值

例如下列代码 `UEnvQueryContext_Test`情景 即为 提供玩家Actor 作为参考值

```c++
// UEnvQueryContext_Test.h
class [PROJECT_NAME] UEnvQueryContext_Test : public UEnvQueryContext
{
	GENERATED_BODY()

	virtual void ProvideContext(FEnvQueryInstance& QueryInstance, FEnvQueryContextData& ContextData) const override;
};

// UEnvQueryContext_Test.cpp
void UEnvQueryContext_Test::ProvideContext(FEnvQueryInstance& QueryInstance, FEnvQueryContextData& ContextData) const
{
	AActor* QueryOwner = Cast<AActor>(QueryInstance.Owner.Get());

	APawn* Player = UGameplayStatics::GetPlayerPawn(QueryOwner->GetWorld(), 0);
	
    // 提供Actor作为参考值
	UEnvQueryItemType_Actor::SetContextHelper(ContextData, Cast<AActor>(Player));
    // 提供FVector作为参考值
    //UEnvQueryItemType_Point::SetContextHelper(ContextData, FVector(0, 0, 0));
}
```

## 测试（Test）

测试 用于 **对生成器提供的Item 过滤、打分**，生成器 可包含多个测试，测试顺序可调

注意 流程上是 **先过滤 后打分**，以避免对无效的Item再打分计算

### 基本用法

创建完生成器后，即可以为其添加 单/多个测试，UE以包含诸多 [预设测试](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/eqs-node-reference-tests-in-unreal-engine)。当生成器生成Item后 将执行测试以进行过滤、打分

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250917171704767.png)

例如下图示例：生成放射状Item后，执行 距离测试（过滤保留距离200~400的Item，剩余Item 根据与查询者的距离 进行线性插值打分、距离越远分越高）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250917174909571.png)

#### 细节面板 - 测试

- 测试目的（Test Purpose）：
  - 仅过滤（Filter Only）：排除 未通过测试的Item
  - 仅计分（Score Only）：对 Item进行评分（也可以说 赋予Item加权数值）
  - 过滤并计分（Filter and Score）
- 测试注释（Test Comment）

### 测试节点

#### Distance

对 Item 到 目标情景 的距离 进行测试

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250917174909571.png)

#### Dot

对 2向量 的点积 进行测试

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250917182321566.png)

#### Trace

对 Item 到 目标情景 进行追踪测试。相当于二者进行射线检测

通过设置 布尔匹配（Bool Match）=False，以实现典型的用例：Item位置 可以看到目标玩家，EQS最终提供此Item

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250917183405582.png)

#### Overlap

对 Item位置 进行 目标通道的 重叠测试

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250917184743575.png)

## EQS用法示例

### BehaviorTree 使用EQS

在行为树内 调用预设的 `UBTTask_RunEQSQuery`任务节点，即可执行配置的EQS（还可配置 EQS查询参数、结果选择、更新黑板键）

例如下图示例：

1. 在`RunEQSQuery`任务节点内 实行了 `EQS_Test`查询（以玩家为中心生成的辐射状网格，寻路移动到查询者 路径越短的Item分越高）
2. 查询完毕后，若查询成功 则选择单一最佳项目（即分数最高的Item）更新其数据（此处为FVector）到 MoveToLocation黑板键；若 查询失败，则重置MoveToLocation黑板键数据
3. 后行为树继续执行：执行MoveTo任务（移动到MoveToLocation黑板键的坐标）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250918165753317.png)

### StateTree 使用EQS

状态树内 提供了 `FStateTreeRunEnvQueryTask`运行EQS查询任务，即可执行配置的EQS（还可配置 EQS查询参数、结果选择、结果输出）

例如下图示例：

1. TestEQS状态内 为其添加 运行EQS查询任务，指定EQS为`EQS_Test`（查询逻辑同上文行为树内的EQS）
2. 设置EQS的 结果输出到 状态的本地参数MoveToLocation上（查询成功 才更新数据到MoveToLocation）
3. 查询完成后，状态树将继续执行后续任务：STT_MoveToLocation（自身移动到 MoveToLocation坐标）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250918170943587.png)

## 调试

UE提供了 `AEQSTestingPawn : ACharacter` 以便于我们 **在编辑器下 直接查看目标EQS效果**

例如下图 创建一个蓝图对象 `EQS_TestPawn :AEQSTestingPawn`，放置到场景内、挂载 查询模板参数（`QueryTemplate`），即可查看效果：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250916210333442.png)

## 参考文章

- [EQS - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/environment-query-system-in-unreal-engine)
- [UE5 EQS执行流程 源码解析 - 知乎](https://zhuanlan.zhihu.com/p/26110293004)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)





# Smart Objects

[智能对象（Smart Objects）](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/smart-objects-in-unreal-engine) 是 **关卡内 可与AI单位或Player交互的 对象**

智能对象 不包含执行逻辑，而是 提供交互信息到 交互对象上、交互对象再自行实现交互逻辑，旨在于 **将场景交互行为 与 AI单位的行为 解耦**。举例就像：一把椅子是智能对象（只能人形单位使用，使用方法是坐下动画），人形单位想要与椅子交互，就得移动到椅子位置、被动触发播放 坐下动画 即可

## 模块介绍

### 智能对象子系统（Smart Object Subsystem）

智能对象子系统 `USmartObjectSubsystem : public UWorldSubsystem`，负责 **追踪关卡内所有 智能对象**，也提供了大部分业务方法

例如下图 `BTT_FindSmartObject`行为树任务中，使用智能对象子系统 查询了附近可交互的智能对象：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250921165407479.png)

### 游戏行为配置（Gameplay Behavior Config）

游戏行为配置 `UGameplayBehaviorConfig : public UObject`，负责 **指定游戏行为类**，且其可接收自定义参数以待后续呈递到 游戏行为中

例如下图 `BP_SO_BehaviorConfig`游戏行为配置，指定了游戏行为类、且声明了`TargetMontage : UAnimMontage`参数：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250921174541462.png)

### 游戏行为（Gameplay Behavior）

游戏行为 `UGameplayBehavior : public UObject`，负责 **让交互对象 实现具体的交互行为**（类似行为树的Task，触发->执行逻辑->结束任务）

例如下图 `BP_SO_Behavior_PlayMontage`游戏行为，在 `OnTriggeredCharacter`事件内 让交互对象播放了蒙太奇动画（动画对象引用来自于 游戏行为配置）：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250921180136692.png)

### 智能对象定义（Smart Object Definition）

智能对象定义 `USmartObjectDefinition : public UDataAsset` 是一种**数据资产**，负责 **承载交互行为配置**（游戏行为配置、游戏行为）

**交互行为配置的载体为 Slot插槽**，可以在一个智能对象定义内 创建多个Slot、各Slot可以指定交互行为定义（游戏行为配置、游戏行为）、各Slot可通过标签以限制交互条件等。**对象交互 就是占用某个Slot、读取其行为配置、执行其游戏行为**

例如下图 `SO_Definition_PlayMontage`智能对象定义，其创建了2个Slot：

- `Slot_AM_Jump`：（默认行为）基于`BP_SO_BehaviorConfig`行为配置（传入`TargetMontage`=`AM_Jump`）、指定`BP_SO_Behavior_PlayMontage`游戏行为
- `Slot_AM_Gesture`：自行指定了此Slot的 行为配置、游戏行为（传入`TargetMontage`=`AM_Gesture`）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250921181114374.png)

### 智能对象组件（Smart Object Component）

智能对象组件 `USmartObjectComponent : public USceneComponent`，挂载于Actor上以 **标识其为 智能对象** 方可 **被智能对象子系统 追踪、使用**

例如下图 `BP_SO_RunBT`蓝图对象内，手动添加了 SmartObjectComponent、并设置了 智能对象定位为 `SO_Definition_PlayMontage`：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250921182505577.png)

## 流程介绍

1. 运行前准备：
   - 制作 游戏行为配置、游戏行为
   - 制作 智能对象定义：创建Slot、设置Slot的行为定义、限制条件等
   - 制作 智能对象：带有智能对象组件、指定将使用的智能对象定义
2. 运行中流程：
   1. 游戏开始时，智能对象子系统 自动初始化
   2. AI单位 通过智能对象子系统 查阅到附近有 智能对象
   3. 当 此智能对象有 空闲Slot，则宣称占用此Slot，后即可与此Slot进行交互，交互完成后才会释放Slot

## 基本用法

基于 [官方示例](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/smart-objects-in-unreal-engine---quick-start) 后，有以下调整：

- AI行为树 查找智能对象任务`BTT_FindSmartObject`中，宣称占用Slot新版本方法为 `USmartObjectBlueprintFunctionLibrary::MarkSmartObjectSlotAsClaimed`
- AI行为树 执行智能对象交互任务`BTT_UseSmartObject`中，智能对象交互方法 改用 `MoveToAndUseSmartObjectWithGameplayBehavior`，以避免其再自行调用AIMoveTo到Slot位置

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250921185448466.png)

并 在`BP_SO_BehaviorConfig`游戏行为配置内 新加了 `TargetMontage : AnimMontage`参数，就能在`SO_Definition_PlayMontage`智能对象定义内 指定Slot播放哪个蒙太奇动画，实现了 不同Slot播不同蒙太奇动画（官方案例把蒙太奇动画写死到了 `BP_SO_Behavior_PlayMontage`游戏行为内）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250921190222328.png)

最终呈现效果：AI单位 与 圆柱型的智能对象进行交互（随机选择智能对象，随机选择红黄色Slot，Slot行为=在AI单位上播放 不同的蒙太奇动画）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250921191242808.gif)

## 参考文章

- [虚幻引擎 | UE5 Smart Object（智能对象）插件理解与应用 - 知乎](https://zhuanlan.zhihu.com/p/1891993349180880509)
- [UE5 SmartObjects（智能对象）插件 - 知乎](https://zhuanlan.zhihu.com/p/458142070)
- [《游戏AI系统其二：SmartObject》 - 知乎](https://zhuanlan.zhihu.com/p/16492407509)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# 参考文章

- [Artificial Intelligence - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/artificial-intelligence-in-unreal-engine?application_version=5.5)
- [游戏开发中的AI技术Overview - 知乎](https://zhuanlan.zhihu.com/p/15800876841)
- [游戏开发中的各种 AI 决策技术 - 知乎](https://zhuanlan.zhihu.com/p/730977400)
