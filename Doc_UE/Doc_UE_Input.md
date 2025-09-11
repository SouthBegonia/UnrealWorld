- [简介](#简介)
- [Enhanced Input 增强输入](#enhanced-input-增强输入)
  - [基本用法](#基本用法)
  - [用法 - 输入优先级问题](#用法---输入优先级问题)
  - [参考文章](#参考文章)
- [按键映射修改](#按键映射修改)
  - [Enhanced Input的方案](#enhanced-input的方案)
- [手柄适配](#手柄适配)
  - [手柄映射](#手柄映射)
- [输入的处理规则](#输入的处理规则)
  - [参考文章](#参考文章-1)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# 简介

UE中的输入可分为 UE5前的旧版输入、UE5后的EnhancedInput增强输入：

- 旧版输入：
  - 特点：输入映射的基本功能（按键、轴绑定），复杂输入机制（双击、联合输入等）则需要业务层自行处理
- Enhanced Input：
  - 特点：
    - 兼容旧版输入映射
    - 将用户输入到事件处理拆分为不同模块处理：**输入动作（UInputAction）**、**输入修改器（UInputModifier）**、**输入触发器（UInputTrigger）**和 **输入映射环境（UInputMappingContext）**，以asset方式进行配置



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# Enhanced Input 增强输入

## 基本用法

基础用法示例：[虚幻5新特性之EnhancedInput - CSDN](https://blog.csdn.net/xcinkey/article/details/124755202)、[虚幻引擎5：增强输入的使用方法 - CSDN](https://blog.csdn.net/chai_tian/article/details/133818478)

通过配置目标InputAction的触发器，可实现不同输入行为的 各类触发事件（见下图）。对于触发器的配置可参阅：[虚幻UE 增强输入-触发器 - CSDN](https://blog.csdn.net/weixin_45865901/article/details/135407105)

![image-20250607175659814](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250607175659814.png)

## 用法 - 输入优先级问题

常规用法中，往EnhanceInputSubSystem添加某套InputMappingContext，其核心方法为`IEnhancedInputSubsystemInterface::AddMappingContext()`，可以看到 此套IMC储在类型为 `Map<MappingContext, Priority>` 的 `AppliedInputContexts` 变量内

![image-20250813223630822](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250813223630822.png)

这就是EnhancedInputSystem的功能之一：叠加挂载InputMappingContext + 优先级触发

![](https://picx.zhimg.com/v2-ab4413f64d640d093af8340e26fdd19b_r.jpg)

![](https://pic1.zhimg.com/v2-1c34c42da17e01492e1307f10c55a2ee_r.jpg)

下图示例如：PlayerCharacter上默认挂载添加了 `IMC_EnhancedTest`（其内 keyCode=Y键，对应触发 `IA_Y`），现又添加 `IMC_EnhancedTest2`（其内 keyCode=Y键，对应触发 `IA_Y_GetItem`）。则 根据对`IMC_EnhancedTest2`进行`AddMappingContext`时的Priority=1 大于 `IMC_EnhancedTest`的Priority=0，当输入keyCode=Y键时，将只会触发 `IMC_EnhancedTest2`内的 `IA_Y_GetItem` ，其余键正常触发 `IMC_EnhancedTest`内的IA。也就实现了 同按键在不同环境下的 不同交互结果

总结也就是 **多IMC下，KeyAction键入而引发的IA 将根据各IMC的Priority 进行优先级触发**

![image-20250813222633198](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250813222633198.png)

## 参考文章

- [UE5 -- EnhancedInput(增强输入系统) - 知乎](https://zhuanlan.zhihu.com/p/470949422)
- [虚幻5新特性之EnhancedInput - CSDN](https://blog.csdn.net/xcinkey/article/details/124755202)
- [虚幻引擎5：增强输入的使用方法 - CSDN](https://blog.csdn.net/chai_tian/article/details/133818478)
- [增强输入 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/enhanced-input-in-unreal-engine?application_version=5.5)
- [虚幻UE 增强输入-触发器 - CSDN](https://blog.csdn.net/weixin_45865901/article/details/135407105)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# 按键映射修改

实现例如 **游戏运行中** 修改目标 **操作映射/轴映射/InputAction 的键**，并保留设置配置以供重启后使用

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250819204107743.png)

## Enhanced Input的方案

示例1（官方推荐，[Lyra](https://github.com/EpicGames/UnrealEngine/tree/ue5-main/Samples/Games/Lyra)同款）：[Player Mappable Keys using Enhanced Input - UnrealDev](https://dev.epicgames.com/community/learning/tutorials/Vp69/unreal-engine-player-mappable-keys-using-enhanced-input)。其核心流程：

1. 启用Enhanced Input设置内的 Enable User Settings (Experimental)，并配置 `UEnhancedInputUserSettings`、`UEnhancedPlayerMappableKeyProfile`
2. 对各 `InputAction` 配置PlayerMappableKeySetting（逻辑层与表现层所需的数据）
3. 正常使用 Enhanced Input功能的基础上，设置启用上文的UserSetting功能（在 `AddMapping Context` 方法内勾选 `bNotifyUserSettings` 或 自行调用 `Register Input Mapping Context`）
4.  在运行态时 执行 Remapping Key（`UEnhancedInputUserSettings::MapPlayerKey()`）是实现重映射。保存映射修改数据到本地后，重启后 读取存档再重复Remapping Key


示例2：[EnhancedInput - Github](https://github.com/LethalInsect/EnhancedInput)。具有 独立完整示例工程。实现方案同 示例1

![](https://github.com/user-attachments/assets/1391e2dc-d435-469a-96cf-0a9aed83827e)


示例3：[虚幻-UE5增强输入按键设置教程2.0 - BiliBili](https://www.bilibili.com/video/BV1aySiYpErL)。具有 独立完整示例工程。实现方案为 修改 `UInputMappingContext`资产的数据，对 新旧 `InputAction` 进行 `UInputMappingContext::UnmapKey()`/`UInputMappingContext::MapKey()`

![image-20250727175824610](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250727175824610.png)

![image-20250727175951133](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250727175951133.png)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# 手柄适配

## 手柄映射

摇杆的X、Y轴 = 单轴向上的 `float` 值

摇杆的2D轴 = X、Y两个轴向上的 `FVector2D` 值

![image-20250608144608000](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250608144608000.png)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# 输入的处理规则

UE采用了 **InputComponent栈的形式 解决PlayerInput的相应问题**：对于一个输入信号，会从栈顶自上往下检查 若此InputComponent绑定了此输入信号的回调方法，则触发，完成流程；若未未绑定，则向下传递

栈的顺序如图：

![](https://picx.zhimg.com/v2-2d9eb82af8c0f39db80e3ee2930697cd_1440w.jpg)

根据栈的结构，一个**输入信号的触发优先级规则为：Actor/UserWidget -> Controller -> Level -> Pawn**



亲测示例：有一个Actor和Pawn（`BeginPlay`时调用`EnableInput(true)`+绑定按键K的事件），有一个PlayerController（`SetupInputComponent`时调用`EnableInput(true)`+绑定按键K的事件），有一个Level（关卡蓝图绑定了按键K的事件）。当输入按键K后，相应事件顺序同上述结论：Actor会响应，当Actor `EnableInput(false)`后，轮到Controller相应，后续同理）

## 参考文章

- [UE4对玩家输入的处理规则 - 知乎](https://zhuanlan.zhihu.com/p/166547096)
- [UnrealEngine：输入框架 - 知乎](https://zhuanlan.zhihu.com/p/605137767)
- [RouteInputEvent输入消息路由](https://www.yuque.com/kangshifu-oswox/inym9i/nlpp5g)