![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/LOGO_UE.png)

本文用于记录 **UE引擎、功能模块的 介绍及用法**



# Unreal Engine流程相关

## Unreal Engine的 启动流程

![UnrealEngine的启动流程](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/UnrealEngine的启动流程.png)

### 参考文章

- [Unreal Engine 的启动流程 - 放牛的星星 - 知乎](https://zhuanlan.zhihu.com/p/610523485)


# [C++与蓝图](https://github.com/SouthBegonia/UnrealWorld/tree/main/Doc_UE/Doc_UE_CPP_BP.md)

- U++的各类用法：标识符、宏、智能指针、容器、委托、DataTable、DrawDebug
- UE反射介绍

# [Level](https://github.com/SouthBegonia/UnrealWorld/tree/main/Doc_UE/Doc_UE_Level.md)

- 关卡流送（Level Streaming）：持久关卡（Persistent Level）/流送关卡（Streaming Levels）、流送方式
- 世界分区（World Partition）


# [Input](https://github.com/SouthBegonia/UnrealWorld/tree/main/Doc_UE/Doc_UE_Input.md)

- 增强输入（Enhanced Input）：基本用法
- Runtime 按键映射修改方案
- UE的 输入处理规则

# [UI](https://github.com/SouthBegonia/UnrealWorld/tree/main/Doc_UE/Doc_UE_UI.md)

- UMG：生命周期

# [Animation](https://github.com/SouthBegonia/UnrealWorld/tree/main/Doc_UE/Doc_UE_Animation.md)

- 过场动画（Sequencer）：基本用法、轨道介绍

# UE - Plugins

收集实用的 第三方UE插件

### [GAS - Editor](https://github.com/polygonvariable/unreal-gas-editor)

功能：可视化创建、管理 GAS中的 AttributeSet、Attribute

适用版本：UE5
![](https://github.com/polygonvariable/unreal-gas-editor/blob/main/Documentation/Image/gas_editor_new_attrs.png?raw=true)

### [AsyncLoadingScreen](https://github.com/truong-bui/AsyncLoadingScreen)

简介：采用 MoviePlayer+Slate 实现的 Level切换Loading显示

功能：

- 首次启用游戏、切换Level 时的Loading显示
- Loading支持 视频 及 基于模板的自定义图片/图片序列

备注：

- 尚不支持主动拉起/关闭Loading、而是绑定自动跟随Open Level流程（因为插件实现的核心是 MoviePlayer注册PreLoadMap、PostLoadMapWithWorld委托、内部执行的业务流程）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250915181019670.png)



# 规范

## 资产规范

资产命名规范：

- [UE5项目命名规则](https://docs.qq.com/sheet/DV010ZWxaUmhvbXJs?tab=BB08J2)
- [推荐的资产命名规范 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/recommended-asset-naming-conventions-in-unreal-engine-projects)

## 代码规范

UE内的C++代码规范：

- [代码规范 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/epic-cplusplus-coding-standard-for-unreal-engine?application_version=5.5)
- [UE中的代码命名规范 - CSDN](https://blog.csdn.net/E696472716D4/article/details/143606685)