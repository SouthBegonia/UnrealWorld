- [简介](#简介)
- [关卡流送（Level Streaming）](#关卡流送level-streaming)
  - [参考文章](#参考文章)
- [世界分区（World Partition）](#世界分区world-partition)
  - [参考文章](#参考文章-1)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# 简介

[关卡（Level）](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/levels-in-unreal-engine) 是游戏的“世界”的全部或一部分。关卡包含玩家可以看到并与之交互的所有内容，例如环境、可用对象、其他角色，等等

对于 [Level的管理方式](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/managing-multiple-levels-in-unreal-engine)，目前有2类：

- UE4的 **Level Streaming（关卡流送）**：一个Persistant Level + 多个SubLevel。SubLevel 可通过 关卡流送体积 或 代码/蓝图调用 实现加载、显示、卸载
- UE5新增的 **World Partition（世界分区）**：一个World Partition的Level。通过区域网格划分，自动流送相关区域



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# 关卡流送（Level Streaming）

> 关卡流送功能 可以**将地图文件加载到内存中，或者从内存中卸载**，并在游戏过程中**切换地图的可视性**
>
> 这样一来，场景便能拆分为较小的地图块，并且只有相关部分才会占用资源并被渲染。 正确设置后，开发者便能创建大型、无缝衔接的游戏场景，让玩家仿佛置身于"大世界"之中

根据Level的关系，可划分为2类：

- **Persistent Level（持久关卡）**：核心关卡，始终加载，负责管理其他子关卡的动态加载/卸载。通常为 包含基础场景元素（关照、天空盒）的Level
- **Streaming Levels（流送关卡）**：通过流送体积（Level Streaming Volumes）、蓝图或代码动态加载的子关卡，按需加载/卸载

对于一个 子关卡/流送关卡，其流送方式（**Change Streaming Method**）有：

- **固定加载（Always Loaded）**：与所属的 持久关卡（Persistent Level） 一同加载 且 变为可视状态。不受 流送体积域、蓝图、C++的操作影响。常用作 **持久关卡内容拆分的关卡**
- **蓝图（Blueprint）**：关卡通过 **关卡流送体积(Level Streaming Volumes)** 、 **蓝图** 或 **C++代码** 来动态加载、卸载或流送。常用于 **按需动态处理的关卡**

## 参考文章

- [关卡流送概述 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/level-streaming-overview-in-unreal-engine)

- [【UE教程/进阶】篇——关卡流送（Level Streaming）- CSDN](https://blog.csdn.net/YCEykr/article/details/140769854)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# 世界分区（World Partition）

> 世界分区 是一种 **自动数据管理和基于距离的关卡流送系统**，为大型世界管理提供了完整的解决方案
>
> 以前需要将单个持久关卡中的世界存储到网格单元格中，以便将大型关卡划分成子关卡，但现在的系统已经不需要如此操作，并且你能够使用自动流送系统，根据与流送源的距离来加载和卸载这些单元格

## 参考文章

- [世界分区 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/world-partition-in-unreal-engine)
- [UE5世界分区 - LioMissBlog](https://liomiss.github.io/2025/02/20/ue-wp/)
- [简单尝试UE5的WorldPartition - CSDN](https://blog.csdn.net/u013412391/article/details/120254269)
- [虚幻引擎(UE5)-大世界分区WorldPartition教程(一) - WP的基本用法 - CSDN](https://blog.csdn.net/oFengtingwano/article/details/130871958)
- [虚幻引擎(UE5)-大世界分区WorldPartition教程(二) - OFPA的用法 - CSDN](https://blog.csdn.net/oFengtingwano/article/details/131432827)
- [虚幻引擎(UE5)-大世界分区WorldPartition教程(三) - LevelInstance的用法 - CSDN](https://blog.csdn.net/oFengtingwano/article/details/131433627)
- [虚幻引擎(UE5)-大世界分区WorldPartition教程(四) - DataLayers的用法 - CSDN](https://blog.csdn.net/oFengtingwano/article/details/131433981)