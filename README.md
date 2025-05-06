# UnrealWorld

记录 **[Unreal Engine](https://www.unrealengine.com/zh-CN)** 相关内容



## 项目

### [Demo_UE_BP_NetEase](https://github.com/SouthBegonia/Demo_UE_BP_NetEase/)

蓝图入门学习项目，涉及 蓝图基础用法、蓝图对[UE Gameplay框架](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/gameplay-framework-in-unreal-engine)的简单实现


## IDE

### Visual Studio

作为UE官方首推的IDE，各方面都与UE适配较好。除了用IDE编写代码，更重要的是需要用到 MSVC、MSBuild等工具及C++环境，以便于构建方案，因此 **建议VS必装**

- 版本推荐：
  - Visual Studio 2022

- 安装方法：
  - [设置Visual Studio - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/setting-up-visual-studio-development-environment-for-cplusplus-projects-in-unreal-engine)
  - [【UE5.1 C++】VS2022下载安装 - CSDN](https://blog.csdn.net/ChaoChao66666/article/details/137961824)

### Rider

目前UE跟Rider已经相互支持，且Rider已经免费（非商业用途），所以也可选用Rider作为IDE

- 版本推荐：
  - UE5 + Rider 2025+

- 安装方法：
  - [UE5 + Rider 配置调试，修改代码操作 - 知乎](https://zhuanlan.zhihu.com/p/8648285372)
    - 备注1：UE编辑器偏好设置->码编辑器设置 选Rider Uproject，而不是Rider
    - 备注2：UE5+Rider打开解决方案时，选择 在游戏中安装 RiderLink插件，而不是 在引擎中安装。若控制台最终提示RiderLink插件安装失败，可能为Rider的问题，可尝试升级Rider版本或重装Rider
  - [rider-for-unreal - Jetbrains](https://www.jetbrains.com/guide/gamedev/tutorials/rider-for-unreal/)

- 其他：
  - [如何使用rider调试UE引擎的源代码 - CSDN](https://blog.csdn.net/weixin_45685193/article/details/135372792)
  - [Rider 使用技巧总结 - 知乎](https://zhuanlan.zhihu.com/p/569938186)



## 其他

- [C++笔记](https://github.com/SouthBegonia/Computer-Course/tree/master/Programming%20Languages/CPP)

- UE内的 C++代码规范：
  - [代码规范 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/epic-cplusplus-coding-standard-for-unreal-engine?application_version=5.5)
  - [UE中的代码命名规范 - CSDN](https://blog.csdn.net/E696472716D4/article/details/143606685)

- UE内的 资产命名规范：
  - [推荐的资产命名规范 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/recommended-asset-naming-conventions-in-unreal-engine-projects)