UE内的AI，可概述为 AI单位的**智能行为** + 行为的**环境准备**，旨在于 **实现非玩家单位（AI单位）的智能交互行为**


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
  - [参考文章](#参考文章-2)
- [感知系统](#感知系统)
  - [感知组件（AI Perception Component）](#感知组件ai-perception-component)
    - [感知属性（AI Perception Properties）](#感知属性ai-perception-properties)
      - [感知类型（AI Sense）](#感知类型ai-sense)




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



