# UMG

**虚幻示意图形界面设计器（Unreal Motion Graphics UI Designer）** 可用于实现 Game中的 UI元素

## Widget的生命周期

涉及方法主要有：

```c++
bool Initialize()
void NativeOnInitialized()
    
void AddToScreen(ULocalPlayer* LocalPlayer, int32 ZOrder)
    
void NativePreConstruct()
void NativeConstruct()
    
void NativeTick()
    
void RemoveFromParent()
void NativeDestruct()
```

但在 编辑器 及 Game/PIE 下，会有调用区别：

- Game/PIE：
  - 运行：`bool Initialize()` -> `void NativeOnInitialized()` -> `void AddToScreen(ULocalPlayer* LocalPlayer, int32 ZOrder)` -> `void NativePreConstruct()` -> `void NativeConstruct()` -> `void NativeTick()` -> `void RemoveFromParent()` -> `void NativeDestruct()`

- 编辑器下：
  - 打开预览UMG：`bool Initialize()` -> `void NativePreConstruct()`
  - 保存UMG：`bool Initialize()`
  - UMG内控件调整：`bool Initialize()` -> `void NativePreConstruct()`

基于上述区别，在控件初始化、绑定时，需要区分处理：

|                                                         |               编辑器               |                  Game / PIE                  |
| :-----------------------------------------------------: | :--------------------------------: | :------------------------------------------: |
|    C++：`Initialize()` / BP：`Event On Initialized`     |              控件绑定              |                   控件绑定                   |
|    C++：`NativeConstruct()` / BP：`Event Construct`     |                 x                  | 控件绑定、控件使用（运行态才可预览修改效果） |
| C++：`NativePreConstruct()` / BP：`Event Pre Construct` | 控件使用（编辑器就可预览修改效果） |                  不推荐使用                  |

### 参考文章

- [UMG生命周期 - 博客园](https://www.cnblogs.com/sin998/p/15490311.html)
- [[UE5]UMGにおける初期化処理について](https://historia.co.jp/archives/39279/)
- [[UEC++]UMG的构建函数 - CSDN](https://blog.csdn.net/q757745037/article/details/143270863)
- [UE4 UMG中C++成员变量绑定蓝图Widget - 知乎](https://zhuanlan.zhihu.com/p/337908390)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# Slate

**Slate** 是完全自定义、与平台无关的 **用户界面框架**，可用于 **UE编辑器界面扩展开发** 或 **游戏中的UI开发**