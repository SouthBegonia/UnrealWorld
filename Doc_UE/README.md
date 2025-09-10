- [Unreal Engine流程相关](#unreal-engine流程相关)
  - [Unreal Engine的 启动流程](#unreal-engine的-启动流程)
    - [参考文章](#参考文章)
- [C++与蓝图](#c与蓝图)
  - [C++ VS 蓝图](#c-vs-蓝图)
  - [反射](#反射)
    - [参考文章](#参考文章-1)
  - [标识符](#标识符)
    - [UCLASS](#uclass)
    - [UPROPERTY](#uproperty)
    - [UFUNCTION](#ufunction)
    - [参考文章](#参考文章-2)
  - [Marcro 宏](#marcro-宏)
  - [指针与引用](#指针与引用)
    - [硬引用与软引用](#硬引用与软引用)
      - [使用注意事项](#使用注意事项)
      - [参考文章](#参考文章-3)
    - [`TObjectPtr<T>`](#tobjectptrt)
    - [`TWeakObjectPtr<T>`](#tweakobjectptrt)
    - [`TSubClassOf<T>`](#tsubclassoft)
    - [`TSoftObjectPtr<T>`](#tsoftobjectptrt)
    - [参考文章](#参考文章-4)
  - [容器](#容器)
    - [参考文章](#参考文章-5)
  - [字符串](#字符串)
    - [参考文章](#参考文章-6)
  - [枚举](#枚举)
    - [参考文章](#参考文章-7)
  - [结构体](#结构体)
    - [参考文章](#参考文章-8)
  - [数据表 DataTables](#数据表-datatables)
    - [创建数据表](#创建数据表)
    - [数据表操作](#数据表操作)
    - [数据表导入导出](#数据表导入导出)
    - [参考文章](#参考文章-9)
  - [委托](#委托)
    - [参考文章](#参考文章-10)
  - [断言](#断言)
    - [参考文章](#参考文章-11)
  - [事件、函数、宏](#事件函数宏)
    - [参考文章](#参考文章-12)
  - [DrawDebug](#drawdebug)
    - [参考文章](#参考文章-13)
- [输入](#输入)
  - [Enhanced Input 增强输入](#enhanced-input-增强输入)
    - [基本用法](#基本用法)
      - [用法 - 输入优先级问题](#用法---输入优先级问题)
    - [参考文章](#参考文章-14)
  - [按键映射修改](#按键映射修改)
    - [Enhanced Input的方案](#enhanced-input的方案)
  - [手柄适配](#手柄适配)
    - [手柄映射](#手柄映射)
  - [输入的处理规则](#输入的处理规则)
    - [参考文章](#参考文章-15)
- [UMG](#umg)
  - [Widget的生命周期](#widget的生命周期)
    - [参考文章](#参考文章-16)
- [Level](#level)
  - [Level Streaming 关卡流送](#level-streaming-关卡流送)
    - [参考文章](#参考文章-17)
  - [World Partition 世界分区](#world-partition-世界分区)
    - [参考文章](#参考文章-18)
- [动画](#动画)
  - [Sequencer](#sequencer)
    - [Sequencer的功能](#sequencer的功能)
    - [Sequencer的分类](#sequencer的分类)
    - [Sequencer的基本用法](#sequencer的基本用法)
    - [Sequencer的功能介绍](#sequencer的功能介绍)
      - [轨道内的Actor对象](#轨道内的actor对象)
      - [轨道内Actor对象的重绑定](#轨道内actor对象的重绑定)
      - [轨道 - 镜头切换轨道](#轨道---镜头切换轨道)
      - [轨道 - 镜头轨道](#轨道---镜头轨道)
      - [轨道 - 事件轨道](#轨道---事件轨道)
    - [参考文章](#参考文章-19)
- [UE - Plugins](#ue---plugins)
    - [GAS - Editor](#gas---editor)
- [规范](#规范)
  - [资产规范](#资产规范)
  - [代码规范](#代码规范)



# Unreal Engine流程相关

## Unreal Engine的 启动流程

![UnrealEngine的启动流程](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/UnrealEngine的启动流程.png)

### 参考文章

- [Unreal Engine 的启动流程 - 放牛的星星 - 知乎](https://zhuanlan.zhihu.com/p/610523485)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# C++与蓝图

## C++ VS 蓝图

广义上，游戏内容可分为 逻辑与数据。在实践中，C++或蓝图均可实现二者内容，因此可以根据C++及蓝图的各自优势，选择合适的实现方案：[C++ VS 蓝图 - UnrealEngine](https://dev.epicgames.com/documentation/en-us/unreal-engine/balancing-blueprint-and-cplusplus?application_version=4.27#c++vsblueprints)

## 反射

反射（Reflection）是指一种机制，它允许程序 **在运行时 动态地查询或操作对象的属性、函数和其他元信息**

**UE反射核心步骤：**

1. 标记 UE反射宏：
   - `UObject`：UE所有反射的基类
   - `UClass` 标记类、`USTRUCT` 标记结构体、`UPROPERTY` 标记属性、`UFUNCTION` 标记函数 等
2. 反射代码、元数据的生成：
   - UHT（UnrealHeaderTool）在编译阶段解析 UE反射宏 标记的代码，生成 反射相关代码 及 元数据（记录类、属性、方法等信息）
3. 运行时的反射：
   - 运行时，反射系统通过 元数据及反射API 从而进行动态属性访问、方法调用

**UE反射的应用：**

- 序列化：使用反射来读取和写入对象的属性，以便保存和加载游戏状态、资源等
- 编辑器支持：编辑器能够动态地显示和修改对象的属性，而无需调整代码
- 蓝图支持：暴露类、属性、方法给蓝图使用
- 动态操作：运行时根据名字修改属性、调用函数，或动态创建对象

### 参考文章

- [虚幻引擎反射系统 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/reflection-system-in-unreal-engine)
- [UE反射的初步了解 - 知乎](https://zhuanlan.zhihu.com/p/15356040140)
- [UE4 C++与蓝图 反射宏 - 知乎](https://zhuanlan.zhihu.com/p/435698880)
- [UnrealEngine - 反射系统分析 - 博客园](https://www.cnblogs.com/lawliet12/p/17272835.html)
- [【UE 反射】反射的原理是什么？如何使用机制？ - CSDN](https://blog.csdn.net/hhw_hhw/article/details/139287867)



## 标识符

标识符介绍：[UE5标识符详解 - 知乎 - 大钊](https://zhuanlan.zhihu.com/p/717920216)

常用标识符查阅手册：[UnrealSpecifiers | UE5标识符详解 - Github - 大钊](https://github.com/fjz13/UnrealSpecifiers)

### UCLASS

声明类时，可以为声明添加 **类说明符**，以控制类相对于引擎和编辑器的各个方面的行为

官方文档：[类说明符 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/class-specifiers)

```c++
UCLASS(Blueprintable, BlueprintType)
class DEMO_UE_API AMyActor : public AActor
{
	GENERATED_BODY()

public:
	// Sets default values for this actor's properties
	AMyActor();

protected:
	// Called when the game starts or when spawned
	virtual void BeginPlay() override;

public:
	// Called every frame
	virtual void Tick(float DeltaTime) override;
};
```

### UPROPERTY

声明属性时，**属性说明符** 可被添加到声明，以控制属性与引擎和编辑器诸多方面的相处方式

官方文档：[属性说明符 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/property-specifiers?application_version=4.27)

```c++
protected:
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category="Test", DisplayName="参数A", meta=(ToolTip="这是 参数A"))
	int32 ParamA = 10;

private:
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category="Test", DisplayName="参数B", meta=(ToolTip="这是 参数B", AllowPrivateAccess=true))
	int32 ParamB = 20;
```

![image-20250607151915717](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250607151915717.png)

### UFUNCTION

声明函数时，可以为声明添加 **函数说明符**，以控制函数相对于引擎和编辑器的各个方面的行为方式

官方文档：[函数说明符 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/ufunctions-in-unreal-engine?application_version=5.5)

```c++
UFUNCTION(BlueprintPure)
float  BlueprintPureFunction;

UFUNCTION(BlueprintCallable)
float BlueprintCallableFunction

UFUNCTION(BlueprintCallable)
int32 BlueprintCallableConstFunction() const

UFUNCTION(BlueprintPure=fasle)
Int32 BlueprintPureFalseFunction() const
```

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250819203125764.png)

### 参考文章

- [类说明符 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/class-specifiers)
- [属性说明符 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/property-specifiers?application_version=4.27)
- [函数说明符 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/ufunctions-in-unreal-engine?application_version=5.5)
- [元数据说明符 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/metadata-specifiers-in-unreal-engine?application_version=5.5)
- [UE4 C++学习 浅析UProperty属性说明符 - CSDN](https://www.cnblogs.com/CatSevenMillion/p/16637347.html)



## Marcro 宏

|       宏       |    适用地点     |                  说明                   |
| :------------: | :-------------: | :-------------------------------------: |
|     UCLASS     | UObject的派生类 | 标记从 UObject 派生的类，以被UE反射识别 |
| GENERATED_BODY | UObject的派生类 |     配合UCLASS、生成UE反射相关代码      |
|   UPROPERTY    |    标记属性     |  被UE反射识别，进而反映到 编辑器、蓝图  |
|   UFUNCTION    |    标记函数     |  被UE反射识别，进而反映到 编辑器、蓝图  |



## 指针与引用

### 硬引用与软引用

**硬引用**：

- 含义：直接引用一个对象，引用对象会在初始化时就加载进内存（同理将嵌套引用下去），易造成内存浪费
- 示例：`T*`、`TObjectPtr<T>`、`TSubClassOf<T>`
- 适用情况：
  - 目标对象始终存在，且必须保证可访问。例如 `UWorld`内的 `TObjectPtr<class AGameModeBase> AuthorityGameMode`

**软引用**：

- 含义：通过间接机制（例如字符串形式的对象路径）来引用对象，需自行控制资产的加载流程
- 示例：`TSoftObjectPtr`、`TSoftClassPtr`、`TSoftAssetPtr`
- 适用情况：
  - 可按需动态加载的对象



蓝图内的 对象引用（`TObjectPtr`）、类引用（`TSubClassOf`）= 硬引用，软对象引用（`TSoftObjectPtr`）、软类引用（`TSoftClassPtr`） = 软引用

![image-20250813002554247](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250813002554247.png)

我们可以通过 [引用查看器（Reference Viewer）](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/reference-viewer-in-unreal-engine) 查看某个资产的引用情况，图里的 **白线条=硬引用**，**粉色线条=软引用**，还可以通过 [尺寸贴图（SizeMap）](https://dev.epicgames.com/community/learning/tutorials/r4y7/unreal-engine-size-map) 查看因硬引用而影响的 资产内存大小

例如下图示例：含有一个 软对象引用的变量，且变量默认指向了目标资产，则通过引用查看器可看到其引用关系，且因其为软引用，故不占有资产内存

![image-20250813004018477](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250813004018477.png)

![image-20250813004750983](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250813004750983.png)

#### 使用注意事项

即便我们将变量声明为 软引用，但对其不当操作的话，可能会导致其变为 硬引用 或 被计入资产内存，诊断方法还是 引用查看器+SizeMap。以下罗列可能遇到的情况：

1. `Cast<T>` 类型转变问题：

将软引用对象Load后，对返回的 类引用对象 进行Cast转换，则 Cast目标对象 将变为此资源的 硬引用

![image-20250813010909838](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250813010909838.png)

解决方法为：不用Cast，Load后已经有所需对象了，直接使用即可

![image-20250813011506609](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250813011506609.png)

又继续可能有问题：SpawnActor的返值为 `Actor`，但我想访问 其特定子类（`BP_Box01: Actor`）内的成员，就又不免不了Cast、就又会造成硬引用问题

![image-20250813150330345](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250813150330345.png)

解决方法为：目标子类（`BP_Box01`）实现一个蓝图接口，外部想访问的话走接口、蓝图接口又访问目标子类的内部成员。这样的话虽然会造成 对蓝图接口资源的硬引用，但其大小远远不及 目标子类硬引用所造成的资源占用，几乎可忽略

![image-20250813150954197](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250813150954197.png)

#### 参考文章

- [UE5 Understanding hard and soft references - LeafBrainGames - Youtube](https://www.youtube.com/watch?v=aUG54KCP89M&ab_channel=LeafBranchGames)
- [SOFT Object References in Unreal Engine EXPLAINED - The Game Dev Cave - Youtube](https://www.youtube.com/watch?v=BazkY5aqoig&ab_channel=TheGameDevCave)

### `TObjectPtr<T>`

`TObjectPtr<T>` 是UE5引入的 替代原始指针`T*` 的 `UObject`指针类型，目的在于：

- 提供 编辑器 下的 动态解析 和 访问追踪功能
- 非编辑器模式下 `TObjectPtr<T>` 会退化为 `T*` ，无性能影响

```c++
// UE 4
class ENGINE_API AActor : public UObject
{
	USceneComponent* RootComponent;
}

// UE 5
class ENGINE_API AActor : public UObject
{
	TObjectPtr<USceneComponent> RootComponent;
}
```

因此在开发中，建议对 `UOject`指针属性、`UClass`及`USTRUCT`内的容器类等 使用 `TObjectPtr<T>`以替代原始指针

```c++
UPROPERTY()
TObjectPtr<AActor> MyActor;
UPROPERTY()
TArray<TObjectPtr<AActor>> MyActorArr;
```

此外，`TObjectPtr<T>` + `UPROPERTY()` 的用法，使变量会被UE识别为 **强引用**

### `TWeakObjectPtr<T>`

`TWeakObjectPtr<T>` 是 `UObject`对象的 弱引用指针，其不对`UObject`对象产生引用计数、不阻止其进入GC流程被销毁，常用在：

- 避免 循环引用
- 需要访问对象，但不希望 因此引用而导致对象永远无法被销毁

```c++
AMyActor* Actor
TWeakObjectPtr<AMyActor> ActorReference = Actor;
.......

if (ActorReference.IsValid())
{
    // 使用Get()方法获取对象指针
    AMyActor* ValidActor = ActorReference.Get();
    // 在有效的Actor上执行操作
    ValidActor->SomeMethod();
}
else
{
    // TWeakObjectPtr无效，可能是因为对象已被销毁
    UE_LOG(LogTemp, Warning, TEXT("Actor reference is invalid!"));
}
```

### `TSubClassOf<T>`

`TSubclassOf<T>` 是某个特定 类对象（`UClass`） 的硬引用，用途有：

- 类引用的储存：储存 某个类（或其子类）的引用
- 类的实例化：在运行态时 才实例化某个类（或其子类）的对象
- 蓝图作用：变量在蓝图内可指定 某个类（或其子类）作为节点输入值

```c++
TSubclassOf<AActor> MyActorClass;

AActor* MyNewActor = GetWorld()->SpawnActor<AActor>(MyActorClass, SpawnLocation, SpawnRotation);

UPROPERTY(EditAnywhere, Category = "MyCategory") 
TSubclassOf<AWeapon> WeaponClass;
```

### `TSoftObjectPtr<T>`

`TSoftObjectPtr<T>` 是 `UObject`对象的 软引用指针。其本质上只是存储的 对象的`FSoftObjectPath`资产路径。常用于 加载纹理模型等

```c++
TSoftObjectPtr<UTexture2D> MyTexture;

// 通过路径赋值
MyTexture = TSoftObjectPtr<UTexture2D>(FSoftObjectPath(TEXT("/Game/Textures/MyTexture.MyTexture")));

if (MyTexture.IsValid())	// IsValid() = 检查 资源已加载（且资产路径有效）   IsNull() = 单检查资产路径是否有效
{
    // 资源已经加载
    UTexture2D* Texture = MyTexture.Get();	// 使用已加载资源
}
else
{
    // 资源未加载
    UTexture2D* LoadedTexture = MyTexture.LoadSynchronous();  // 同步加载资源
    if (LoadedTexture)
    {
        // 使用加载后的资源
    }
    
    // 异步加载纹理
    //FStreamableManager& Streamable = UAssetManager::GetStreamableManager();
    //Streamable.RequestAsyncLoad(MyTexture.ToSoftObjectPath(),FStreamableDelegate::CreateUObject(this, &XXXXXXExample::OnTextureLoaded));
}
```

### 参考文章

- [C++ Object Pointer Properties - UnrealEngine](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-engine-5-migration-guide?application_version=5.0#c++objectpointerproperties)
- [【UE5基础】HardRef&SoftRef（TSoftObjectPtr源码浅析） - 知乎](https://zhuanlan.zhihu.com/p/718103181)
- [UE 的常用指针简析 - 知乎](https://zhuanlan.zhihu.com/p/1938026370019099180)
- [ue 为啥要用TObjectPtr＜T＞ - CSDN](https://blog.csdn.net/u013768914/article/details/144094068)
- [简析UE5的对象指针FObjectPtr与TObjectPtr - 知乎](https://zhuanlan.zhihu.com/p/504115127)
- [如何理解UE中的TSubclassOf - CSDN](https://blog.csdn.net/ttod/article/details/136112588)
- [Unreal 浅谈TWeakObjectPtr - 知乎](https://zhuanlan.zhihu.com/p/671665600)
- [UE5中TSoftObjectPtr的使用详解 - CSDN](https://blog.csdn.net/m0_45371381/article/details/147018736)
- [UE5里的TObjectPtr TSharedPtr TWeakPtr TSoftObjectPtr 有什么区别 - CSDN](https://blog.csdn.net/qq_30100043/article/details/143108384)
- [UE4/5 中的TSoftObjectPtr＜＞、TSoftClassPtr＜＞和TSubclassOf＜＞ - CSDN](https://blog.csdn.net/weixin_41130251/article/details/131602636)



## 容器

|  容器  |           描述           |      |
| :----: | :----------------------: | :--: |
| TArray |         动态数组         |      |
|  TMap  |        键值对容器        |      |
|  TSet  | 快速容器，元素本身作为键 |      |

TArray：

```c++
// 创建
TArray<int32> MyArray;
MyArray.Init(0, 5);

// 添加元素
MyArray.Add(10);
MyArray.Emplace(20);
MyArray.Append({30, 40});
MyArray.EmplaceAt(0, 50);
MyArray.Insert(100, 1);
MyArray.AddZeroed();

// 修改元素
if (MyArray.IsValidIndex(MyArray.Num() - 1))
    MyArray[MyArray.Num()-1] = 999;

// 删除元素
MyArray.Append({ 1, 2, 2, 2, 3, 3});
MyArray.RemoveAt(0);
MyArray.RemoveSingle(2);
MyArray.Remove(2);
MyArray.RemoveAll([](const int& num)
{
	return num == 3;
});

// 遍历
for (int i = 0; i < MyArray.Num(); i++)
    UE_LOG(LogTemp, Display, TEXT("%d"), MyArray[i]);
for (int32 a : MyArray)
    UE_LOG(LogTemp, Log, TEXT("%d"), a);
for (auto it = MyArray.CreateConstIterator(); it; ++it)
    UE_LOG(LogTemp, Log, TEXT("%d"), *it);

// 查找元素
if (MyArray.Contains(10))
    UE_LOG(LogTemp, Log, TEXT("MyArray.Contains"));
if (MyArray.Find(10) != INDEX_NONE)
    UE_LOG(LogTemp, Log, TEXT("MyArray.Find"));

// 排序
MyArray.Sort([](const int& a, const int& b)
{
	return a < b;
});
```

TMap：

```c++
// 创建
TMap<int, FString> MyMap;
MyMap = {
    {0, "0"},
};

// 添加元素
MyMap.Add(1, "0");
MyMap.Add(1, "1");	//已有key，则替换value
MyMap.Emplace(6, "5");
MyMap.Emplace(6, "6");	//已有key，则替换value

// 信息
UE_LOG(LogTemp, Log, TEXT("IsEmpty = %hhd"), MyMap.IsEmpty());
UE_LOG(LogTemp, Log, TEXT("Num = %d"), MyMap.Num());

// 查找
if (MyMap.Contains(6))
    UE_LOG(LogTemp, Log, TEXT("Contains 6"));
FString* mapFindResultPtr = MyMap.Find(6);
if (mapFindResultPtr != nullptr)
    UE_LOG(LogTemp, Log, TEXT("Find 6"));

// 遍历
for (const TPair<int, FString>& Pair : MyMap)
    UE_LOG(LogTemp, Log, TEXT("Key = %d, Val = %s"), Pair.Key, *Pair.Value);

// 删除元素
MyMap.Add(10, "10");
MyMap.Add(11, "11");
FString delVal1 = MyMap.FindAndRemoveChecked(10);
FString delVal2 = "-1";
if (MyMap.RemoveAndCopyValue(11, delVal2))
    UE_LOG(LogTemp, Log, TEXT("RemoveAndCopyValue = %s"), *delVal2);

// 清空元素
MyMap.Reset();	//移除全部元素 但 内存尚在
MyMap.Empty();	//移除全部元素 且 释放内存
```

TSet：

```c++
// 创建
TSet<FString> MySet;
MySet = { "A", "B" };

// 信息
UE_LOG(LogTemp, Log, TEXT("MySet.IsEmpty() = %hhd"), MySet.IsEmpty());
UE_LOG(LogTemp, Log, TEXT("MySet.Num() = %d"), MySet.Num());

// 添加元素
MySet.Add("B");
MySet.Add("C");
MySet.Emplace("C");
MySet.Emplace("D");
MySet.Append({"D", "E", "F"});
UE_LOG(LogTemp, Log, TEXT("After add element MySet.Num() = %d"), MySet.Num());

// 查找
UE_LOG(LogTemp, Log, TEXT("MySet.Contains(\"A\") = %hhd"), MySet.Contains("A"));
FString* setFindResultPtr = MySet.Find("A");
if (setFindResultPtr != nullptr)
    UE_LOG(LogTemp, Log, TEXT("MySet.Find(\"A\")"));

// 删除元素
UE_LOG(LogTemp, Log, TEXT("MySet.Remove(\"A\") = %d"), MySet.Remove("A"));
UE_LOG(LogTemp, Log, TEXT("MySet.Remove(\"A\") = %d"), MySet.Remove("A"));

// 清空元素
MySet.Reset();	//移除全部元素 但 内存尚在
MySet.Empty();	//移除全部元素 且 释放内存
```

### 参考文章

- [[UE C++] TArray - CSDN](https://blog.csdn.net/qq_52179126/article/details/130605021)
- [[UE C++] TMap - CSDN](https://blog.csdn.net/qq_52179126/article/details/130712877)

- [[UE C++] TSet - CSDN](https://blog.csdn.net/qq_52179126/article/details/130756860)



## 字符串

|  类型   |                      描述                       |                  备注                   |
| :-----: | :---------------------------------------------: | :-------------------------------------: |
| FString |               可变字符串。较常用                |                                         |
|  FName  | 不可变字符串，不区分大小写。常用于 标识资源路径 | 因不区分大小写，故从FString转换可能损耗 |
|  FText  |         不可变字符串。常用于 本地化文本         |                                         |

> TEXT宏：让UE自动选择适合当前平台环境的编码。只要是代码内的字符串，都应该用TEXT包裹

FString：

```c++
// FString
FString TestHUDString = FString(TEXT("This is my test FString."));

// 查找
FString TestSearchString = FString(TEXT("This is my test FString."));
if (TestSearchString.Contains(TEXT("Test"), ESearchCase::IgnoreCase, ESearchDir::FromEnd))
    UE_LOG(LogTemp, Warning, TEXT("%s"), *TestSearchString);

if (TestSearchString.Find(TEXT("test"), ESearchCase::CaseSensitive, ESearchDir::FromEnd) != INDEX_NONE)
    UE_LOG(LogTemp, Warning, TEXT("%s"), *TestSearchString);

// 拼接
FString TestConcatString = FString(TEXT("Hello"));
TestConcatString += " ";
TestConcatString += FString(TEXT("World"));

// Printf
int32 NumMinutes = 10;
int32 NumSeconds = 20;
FString TestPrintfString = FString::Printf(TEXT("%02d:%02d  %s"), NumMinutes, NumSeconds, *TestHUDString);


// FString -> FName、FText
FName TestHUDName = FName(*TestHUDString);	//FString -> FName不可靠。因为FName不区分大小写，所以转换存在损耗。
FText TestHUDText = FText::FromString(TestHUDString);

// FName、FText -> FString
TestHUDString = TestHUDName.ToString();
TestHUDString = TestHUDText.ToString();
```

FName：

```c++
// FNames：不区分大小写、不可变，且无法被操作
FName TestHUDName = FName(TEXT("ThisIsMyTestFName"));
FName TestHUDNameFromFString = FName(*FString(TEXT("THISISMYTESTFNAME")));

// 比较（注意：均为忽略大小写的）
if (TestHUDName == TestHUDNameFromFString) {}
if (TestHUDName.IsEqual(FName(TEXT("THISISMYTESTFNAME")))) {}


// FName -> FString、FText
FString TestHUDString = TestHUDName.ToString();
FText TestHUDText = FText::FromName(TestHUDName);

// FString -> FName
// 注意：因为FName不区分大小写，所以转换存在损耗
TestHUDName = FName(*TestHUDString);
UE_LOG(LogTemp, Warning, TEXT("%s"), *TestHUDName.ToString());
```

FText：

```c++
// FText
FText TestHUDText = FText::FromString(TEXT("ThisIsMyTestFText"));
```

三者转换：

![](https://pic3.zhimg.com/v2-069231f3f58e2feccecd2c103a6e98d0_1440w.jpg)

### 参考文章

- [字符串处理 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/string-handling-in-unreal-engine)
- [FString、FName、FText的基本使用 - CSDN](https://blog.csdn.net/ChaoChao66666/article/details/144568057)
- [UE4 C++基础 - 字符串和本地化 - 知乎](https://zhuanlan.zhihu.com/p/163587790)



## 枚举

```c++
UENUM(BlueprintType)			// 标记蓝图可用
enum class EMyEnum : uint8		// 不加默认为int
{
	None = 0 UMETA(DisplayName = "None"),	// 蓝图要求 必须含有val=0的枚举值

	Enum1 = 1 UMETA(DisplayName = "MyEnum_1"),	// 修改显示名称
	Enum2 = 2 UMETA(DisplayName = "MyEnum_2"),
	Enum3 = 3 UMETA(DisplayName = "MyEnum_3"),
	Enum4 = 4 UMETA(DisplayName = "MyEnum_4"),
};

// 用法
EMyEnum MyEnum = EMyEnum::Enum1;
const UEnum* EnumPtr = FindObject<UEnum>(ANY_PACKAGE, TEXT("EMyEnum"), true);
if (EnumPtr != nullptr)
{
    // 获取 枚举值对应 枚举名（无效则返 空值）
    EnumPtr->GetNameStringByValue(2);		// "Enum2"
    // 获取 枚举名对应 枚举值（无效则返 -1）
    EnumPtr->GetValueByNameString(FString("Enum3"));	// 3
    // 判断 枚举值的 合法性
    EnumPtr->IsValidEnumValue(4);		// true
    EnumPtr->IsValidEnumValue(9999);	// false
    // 获取 最大枚举值
    EnumPtr->GetMaxEnumValue();
}
```

![image-20250613141116553](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250613141116553.png)

### 参考文章

- [[UE C++] Enum的使用 - CSDN](https://blog.csdn.net/qq_52179126/article/details/129891590)



## 结构体

```c++
USTRUCT(BlueprintType)				// 标记蓝图可用
struct FMyStruct
{
	GENERATED_USTRUCT_BODY()		// 或 GENERATED_BODY()

public:
	UPROPERTY(EditAnywhere, BlueprintReadWrite)
	AActor* TargetActor;

	FString GetStructName()			// 结构体的 成员函数 仅能在C++层用，蓝图无法使用
	{
		return StructName;
	}

private:
	UPROPERTY(EditAnywhere, BlueprintReadWrite, meta=(AllowPrivateAccess))
	FString StructName = TEXT("MyStruct");
};

// 用法
FMyStruct MyStruct = FMyStruct();
UE_LOG(LogTemp, Warning, TEXT("StructName = %s"), *MyStruct.GetStructName());
```

![image-20250613161815851](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250613161815851.png)

### 参考文章

- [UE C++ Struct 的使用 - CSDN](https://blog.csdn.net/qq_52179126/article/details/129768456)



## 数据表 DataTables

数据表是一种表格，表中的数据字段可以是任何有效的 `UObject` 属性，包括资产引用。数据表可 导入/导出 为CSV或JSON

### 创建数据表

表格由列名组成，列名源自 `FTableRowBase` 的派生类，例如：

```c++
// 角色数据表
USTRUCT(Blueprintable)
struct FTableHeroStruct : public FTableRowBase
{
    GENERATED_BODY()

    FTableHeroStruct()
    : HeroID(0)
    , HeroName(TEXT("NONE"))
    {}

    UPROPERTY(EditDefaultsOnly, BlueprintReadWrite, Category= "BaseInfo", DisplayName="角色ID")
    int32 HeroID;

    UPROPERTY(EditDefaultsOnly, BlueprintReadWrite, Category= "BaseInfo", DisplayName="角色名")
    FName HeroName;

    UPROPERTY(EditDefaultsOnly, BlueprintReadWrite, Category= "BaseInfo", DisplayName="角色头像")
    TSoftObjectPtr<UTexture> HeroHeadIcon;
};
```

声明完 `FTableHeroStruct : FTableRowBase` 结构体后，就可创建 **数据表格** 资产（本例的表格类型为 `FTableHeroStruct`）：

![image-20250614164909162](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250614164909162.png)

打开 数据表格 资产后，即可进行表格项编辑：

![image-20250614165621417](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250614165621417.png)

注意：

- 表格的 行命名（RowName）全表唯一，不可重复

### 数据表操作

加载数据表：

```c++
// 加载数据表
UDataTable* MyHeroTable = LoadObject<UDataTable>(NULL, TEXT("DataTable'/Game/DT_Hero.DT_Hero'"));
if (MyHeroTable != nullptr) {}
```

表内容读取：

```c++
UDataTable* MyHeroTable = LoadObject<UDataTable>(NULL, TEXT("DataTable'/Game/DT_Hero.DT_Hero'"));
if (MyHeroTable != nullptr)
{
    // 读表 - GetRowMap()
    for (TPair<FName, unsigned char*> it : MyHeroTable->GetRowMap())
    {
        FTableHeroStruct* RowPtr = (FTableHeroStruct*)it.Value;
        UE_LOG(LogTemp, Log, TEXT("MyHeroTable - GetRowMap : it.Key = %s, HeroID = %d, HeroName = %s"), *it.Key.ToString(), RowPtr->HeroID, *RowPtr->HeroName.ToString());
    }

    // 读表 - GetAllRows()
    TArray<FTableHeroStruct*> TableHeroArr;
    MyHeroTable->GetAllRows("", TableHeroArr);
    for (FTableHeroStruct* RowPtr : TableHeroArr)
    {
        UE_LOG(LogTemp, Log, TEXT("MyHeroTable - GetAllRows : HeroID = %d, HeroName = %s"), RowPtr->HeroID, *RowPtr->HeroName.ToString());
    }

    // 读表 - FindRow()
    for (FName RowName : MyHeroTable->GetRowNames())
    {
        FTableHeroStruct* RowPtr = MyHeroTable->FindRow<FTableHeroStruct>(RowName, TEXT(""));
        UE_LOG(LogTemp, Log, TEXT("MyHeroTable - FindRow : RowName = %s, HeroID = %d, HeroName = %s"), *RowName.ToString(), RowPtr->HeroID, *RowPtr->HeroName.ToString());
    }

    // 遍历
    MyHeroTable->ForeachRow<FTableHeroStruct>(TEXT(""), [](const FName& Key, const FTableHeroStruct& RowRef)
    {
		UE_LOG(LogTemp, Log, TEXT("MyHeroTable - ForeachRow : Key = %s, HeroID = %d, HeroName = %s"), *Key.ToString(), RowRef.HeroID, *RowRef.HeroName.ToString());
    });
}
```

表内容 新加、移除：

```c++
UDataTable* MyHeroTable = LoadObject<UDataTable>(NULL, TEXT("DataTable'/Game/DT_Hero.DT_Hero'"));
if (MyHeroTable != nullptr)
{
    // 写入表项：
    FName RowName = FName(TEXT("NewRow_99"));
    if (!MyHeroTable->FindRow<FTableHeroStruct>(RowName, TEXT("")))
    {
        FTableHeroStruct* DataTableRowInfo = new FTableHeroStruct();
        DataTableRowInfo->HeroID = 999;
        DataTableRowInfo->HeroName = TEXT("NewHero");

        MyHeroTable->AddRow(RowName, *DataTableRowInfo);
    }
    else
    {
        //已有RowName，再AddRow会覆写内容
        //MyHeroTable->AddRow(RowName, *DataTableRowInfo);
    }

    // 删除表项：
    MyHeroTable->RemoveRow(RowName);
}
```

### 数据表导入导出

目前支持 数据表 到 CSV、JSON 的相互转换。例如 将 表格数据 资产 转换为CSV：

![image-20250614173053274](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250614173053274.png)

将 CSV 转换为 表格资产：

（对于CSV、JSON内的 非匹配、缺失列字段，可设置表格资产内的 导入选项 的相关参数）

![image-20250614172937923](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250614172937923.png)

### 参考文章

- [数据驱动的Gameplay元素 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/data-driven-gameplay-elements-in-unreal-engine?application_version=5.5)
- [UE5中的数据表：UDataTable - 知乎](https://zhuanlan.zhihu.com/p/567901416)
- [[UE C++] Data Table的使用 - CSDN](https://blog.csdn.net/qq_52179126/article/details/129800615)



## 委托

委托是一种泛型但类型安全的方式，可在C++对象上调用成员函数。UE常用的委托有3类：

|    委托类型     |                          描述                          |                        备注                         |
| :-------------: | :----------------------------------------------------: | :-------------------------------------------------: |
|    单播委托     |        绑定在单个对象的单个函数上，一一映射触发        |                                                     |
|    多播委托     | 可绑定到 多个 不同对象的不同函数，按照绑定顺序执行触发 |                                                     |
| 动态单/多播委托 |           与单播、多播委托相比，多了蓝图交互           | 以 UPROPERTY(BlueprintAssignable) 标记 动态多播委托 |

用法：

- [单播委托的基本使用 - CSDN](https://zhichao.blog.csdn.net/article/details/144314544)
- [多播委托的基本使用 - CSDN](https://zhichao.blog.csdn.net/article/details/144510562)
- [动态单播/多播的基本使用 - CSDN](https://zhichao.blog.csdn.net/article/details/144543843)

### 参考文章

- [委托 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/delegates-and-lambda-functions-in-unreal-engine?application_version=5.5)
- [动态委托基本用法（单播，多播） - 知乎](https://zhuanlan.zhihu.com/p/27732328690)



## 断言

`assert` 可在 开发期间 帮助检测和诊断不正常或无效的运行时条件

基本格式为：`assert(表达式)`，表达式=false=触发断言

| 断言类型 |         当触发断言         |                     版本区别                     |                             备注                             |
| :------: | :------------------------: | :----------------------------------------------: | :----------------------------------------------------------: |
|  check   |          中断运行          |        发布版本内 不断言 且 不执行表达式         | USE_CHECKS_IN_SHIPPING宏 = 标识断言检查 在发布版本内是否执行，默认为0，可按需设置 |
|  verify  |          中断运行          |         发布版本内 不断言 但 执行表达式          |                                                              |
|  ensure  | 可继续运行，可打印堆栈信息 | 所有版本都执行表达式 但 非发布版本才打印堆栈信息 |                   会将检测结果以bool值返回                   |

断言选择：

![](https://picx.zhimg.com/v2-1f79888849ba93c3dfc12addbeb850a3_1440w.jpg)

### 参考文章

- [断言 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/asserts-in-unreal-engine?application_version=5.5)
- [UE4断言总结 - 知乎](https://zhuanlan.zhihu.com/p/64462945)



## 事件、函数、宏

**事件** 侧重于 逻辑触发，**函数** 侧重于 即时的逻辑执行及结果返回，**宏** 侧重于 逻辑分流处理

![](https://picx.zhimg.com/v2-a5bcc77391e47dd2e117c99634153ae9_1440w.jpg)

### 参考文章

- [【UE4】函数、宏和事件的区别 - 知乎](https://zhuanlan.zhihu.com/p/527787878)
- [UE5 ＜事件、函数、宏＞ - CSDN](https://blog.csdn.net/CandyU2/article/details/147124582)



## DrawDebug

DrawDebug 用于在 编辑器或开发环境下，于场景内绘制 图形或文字，以便直观查验信息

相关绘制方法（C++或蓝图都有）均使用的 `UKismetSystemLibrary` 内的API：

![image-20250615152652698](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250615152652698.png)

效果示例：

![](https://pic1.zhimg.com/v2-2c180bda58846fe93139fa3ceaee0ff4_1440w.jpg)

![](https://pica.zhimg.com/v2-467275fb62cb6808081ec87521f88c9c_1440w.jpg)

![](https://pic3.zhimg.com/v2-76d3f8d5d961e7f5b98a7807bfa2d41c_1440w.jpg)

### 参考文章

- [虚幻引擎中各种场景调试绘制指令（DrawDebug）的使用、效果和原理 - 知乎](https://zhuanlan.zhihu.com/p/718494965)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# 输入

UE中的输入可分为 UE5前的旧版输入、UE5后的EnhancedInput增强输入：

- 旧版输入：
  - 特点：输入映射的基本功能（按键、轴绑定），复杂输入机制（双击、联合输入等）则需要业务层自行处理
- Enhanced Input：
  - 特点：
    - 兼容旧版输入映射
    - 将用户输入到事件处理拆分为不同模块处理：**输入动作（UInputAction）**、**输入修改器（UInputModifier）**、**输入触发器（UInputTrigger）**和 **输入映射环境（UInputMappingContext）**，以asset方式进行配置

## Enhanced Input 增强输入

### 基本用法

基础用法示例：[虚幻5新特性之EnhancedInput - CSDN](https://blog.csdn.net/xcinkey/article/details/124755202)、[虚幻引擎5：增强输入的使用方法 - CSDN](https://blog.csdn.net/chai_tian/article/details/133818478)

通过配置目标InputAction的触发器，可实现不同输入行为的 各类触发事件（见下图）。对于触发器的配置可参阅：[虚幻UE 增强输入-触发器 - CSDN](https://blog.csdn.net/weixin_45865901/article/details/135407105)

![image-20250607175659814](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250607175659814.png)

#### 用法 - 输入优先级问题

常规用法中，往EnhanceInputSubSystem添加某套InputMappingContext，其核心方法为`IEnhancedInputSubsystemInterface::AddMappingContext()`，可以看到 此套IMC储在类型为 `Map<MappingContext, Priority>` 的 `AppliedInputContexts` 变量内

![image-20250813223630822](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250813223630822.png)

这就是EnhancedInputSystem的功能之一：叠加挂载InputMappingContext + 优先级触发

![](https://picx.zhimg.com/v2-ab4413f64d640d093af8340e26fdd19b_r.jpg)

![](https://pic1.zhimg.com/v2-1c34c42da17e01492e1307f10c55a2ee_r.jpg)

下图示例如：PlayerCharacter上默认挂载添加了 `IMC_EnhancedTest`（其内 keyCode=Y键，对应触发 `IA_Y`），现又添加 `IMC_EnhancedTest2`（其内 keyCode=Y键，对应触发 `IA_Y_GetItem`）。则 根据对`IMC_EnhancedTest2`进行`AddMappingContext`时的Priority=1 大于 `IMC_EnhancedTest`的Priority=0，当输入keyCode=Y键时，将只会触发 `IMC_EnhancedTest2`内的 `IA_Y_GetItem` ，其余键正常触发 `IMC_EnhancedTest`内的IA。也就实现了 同按键在不同环境下的 不同交互结果

总结也就是 **多IMC下，KeyAction键入而引发的IA 将根据各IMC的Priority 进行优先级触发**

![image-20250813222633198](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250813222633198.png)

### 参考文章

- [UE5 -- EnhancedInput(增强输入系统) - 知乎](https://zhuanlan.zhihu.com/p/470949422)
- [虚幻5新特性之EnhancedInput - CSDN](https://blog.csdn.net/xcinkey/article/details/124755202)
- [虚幻引擎5：增强输入的使用方法 - CSDN](https://blog.csdn.net/chai_tian/article/details/133818478)
- [增强输入 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/enhanced-input-in-unreal-engine?application_version=5.5)
- [虚幻UE 增强输入-触发器 - CSDN](https://blog.csdn.net/weixin_45865901/article/details/135407105)



## 按键映射修改

实现例如 **游戏运行中** 修改目标 **操作映射/轴映射/InputAction 的键**，并保留设置配置以供重启后使用

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250819204107743.png)

### Enhanced Input的方案

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



## 手柄适配

### 手柄映射

摇杆的X、Y轴 = 单轴向上的 `float` 值

摇杆的2D轴 = X、Y两个轴向上的 `FVector2D` 值

![image-20250608144608000](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250608144608000.png)



## 输入的处理规则

UE采用了 **InputComponent栈的形式 解决PlayerInput的相应问题**：对于一个输入信号，会从栈顶自上往下检查 若此InputComponent绑定了此输入信号的回调方法，则触发，完成流程；若未未绑定，则向下传递

栈的顺序如图：

![](https://picx.zhimg.com/v2-2d9eb82af8c0f39db80e3ee2930697cd_1440w.jpg)

根据栈的结构，一个**输入信号的触发优先级规则为：Actor/UserWidget -> Controller -> Level -> Pawn**



亲测示例：有一个Actor和Pawn（`BeginPlay`时调用`EnableInput(true)`+绑定按键K的事件），有一个PlayerController（`SetupInputComponent`时调用`EnableInput(true)`+绑定按键K的事件），有一个Level（关卡蓝图绑定了按键K的事件）。当输入按键K后，相应事件顺序同上述结论：Actor会响应，当Actor `EnableInput(false)`后，轮到Controller相应，后续同理）

### 参考文章

- [UE4对玩家输入的处理规则 - 知乎](https://zhuanlan.zhihu.com/p/166547096)
- [UnrealEngine：输入框架 - 知乎](https://zhuanlan.zhihu.com/p/605137767)
- [RouteInputEvent输入消息路由](https://www.yuque.com/kangshifu-oswox/inym9i/nlpp5g)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# UMG

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



# Level

[关卡（Level）](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/levels-in-unreal-engine) 是游戏的“世界”的全部或一部分。关卡包含玩家可以看到并与之交互的所有内容，例如环境、可用对象、其他角色，等等

对于 [Level的管理方式](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/managing-multiple-levels-in-unreal-engine)，目前有2类：

- UE4的 **Level Streaming（关卡流送）**：一个Persistant Level + 多个SubLevel。SubLevel 可通过 关卡流送体积 或 代码/蓝图调用 实现加载、显示、卸载
- UE5新增的 **World Partition（世界分区）**：一个World Partition的Level。通过区域网格划分，自动流送相关区域

## Level Streaming 关卡流送

> 关卡流送功能 可以**将地图文件加载到内存中，或者从内存中卸载**，并在游戏过程中**切换地图的可视性**
>
>  这样一来，场景便能拆分为较小的地图块，并且只有相关部分才会占用资源并被渲染。 正确设置后，开发者便能创建大型、无缝衔接的游戏场景，让玩家仿佛置身于"大世界"之中

根据Level的关系，可划分为2类：

- **Persistent Level（持久关卡）**：核心关卡，始终加载，负责管理其他子关卡的动态加载/卸载。通常为 包含基础场景元素（关照、天空盒）的Level
- **Streaming Levels（流送关卡）**：通过流送体积（Level Streaming Volumes）、蓝图或代码动态加载的子关卡，按需加载/卸载

对于一个 子关卡/流送关卡，其流送方式（**Change Streaming Method**）有：

- **固定加载（Always Loaded）**：与所属的 持久关卡（Persistent Level） 一同加载 且 变为可视状态。不受 流送体积域、蓝图、C++的操作影响。常用作 **持久关卡内容拆分的关卡**
- **蓝图（Blueprint）**：关卡通过 **关卡流送体积(Level Streaming Volumes)** 、 **蓝图** 或 **C++代码** 来动态加载、卸载或流送。常用于 **按需动态处理的关卡**

### 参考文章

- [关卡流送概述 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/level-streaming-overview-in-unreal-engine)

- [【UE教程/进阶】篇——关卡流送（Level Streaming）- CSDN](https://blog.csdn.net/YCEykr/article/details/140769854)

## World Partition 世界分区

> 世界分区 是一种 **自动数据管理和基于距离的关卡流送系统**，为大型世界管理提供了完整的解决方案
>
>  以前需要将单个持久关卡中的世界存储到网格单元格中，以便将大型关卡划分成子关卡，但现在的系统已经不需要如此操作，并且你能够使用自动流送系统，根据与流送源的距离来加载和卸载这些单元格

### 参考文章

- [世界分区 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/world-partition-in-unreal-engine)
- [UE5世界分区 - LioMissBlog](https://liomiss.github.io/2025/02/20/ue-wp/)
- [简单尝试UE5的WorldPartition - CSDN](https://blog.csdn.net/u013412391/article/details/120254269)
- [虚幻引擎(UE5)-大世界分区WorldPartition教程(一) - WP的基本用法 - CSDN](https://blog.csdn.net/oFengtingwano/article/details/130871958)
- [虚幻引擎(UE5)-大世界分区WorldPartition教程(二) - OFPA的用法 - CSDN](https://blog.csdn.net/oFengtingwano/article/details/131432827)
- [虚幻引擎(UE5)-大世界分区WorldPartition教程(三) - LevelInstance的用法 - CSDN](https://blog.csdn.net/oFengtingwano/article/details/131433627)
- [虚幻引擎(UE5)-大世界分区WorldPartition教程(四) - DataLayers的用法 - CSDN](https://blog.csdn.net/oFengtingwano/article/details/131433981)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# 动画

## Sequencer

[Sequencer](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/how-to-make-movies-in-unreal-engine) 是虚幻引擎的过场动画编辑器，允许用户为动画角色、摄像机、各种属性以及其他Actor制作动画。它提供了一个非线性的编辑环境，允许你按时间轴创建、修改轨道和关键帧

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/UE_Sequencer.gif)

### Sequencer的功能

可以在一个Sequencer资产内 创建不同特定功能的 [轨道](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/sequencer-track-list-in-unreal-engine) 或是 添加成员到轨道Key动画。常用的功能有：

- 相机的创建、切换、表现处理
- [SkeletalMesh](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/skeletal-mesh-assets-in-unreal-engine)的动画播放
- Actor对象（Level内既有/新生成/重绑定）的关键帧动画

### Sequencer的分类

UE内常见的Sequencer资产有：

- **Level Sequence**：是Sequencer的基本文件。Level Sequence 的内部由 **轨道(Tracks)** 构成，各轨道具备独立功能
- **Master Sequence**：实质也是LevelSequence（默认自动创建了镜头轨道），主要用于 拼接组合多个Level Sequence

![](https://pic1.zhimg.com/v2-a0ce5c61aff418b2a737c8830da09d4a_1440w.jpg)

![LevelSequence轨道](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250801211212359.png)

### Sequencer的基本用法

首先创建Sequencer资产，以 Level Sequence为例：

在内容浏览器中点击右键空白区域，选择 **过场动画（Cinematics） > 关卡序列（Level Sequence）** 即可创建，双击后就可打开Sequencer编辑器进行编辑，注意：Play时建议把Sequencer编辑窗口关闭、避免运行时显示异常

![image-20250801193535590](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250801193535590.png)

后就可以在逻辑层对这个Sequence进行Play播放了：核心API是 `ULevelSequencePlayer::CreateLevelSequencePlayer()`、`UMovieSceneSequencePlayer::Play()`，同时也可以 设置播放参数、监听播放事件等

![image-20250801194038593](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250801194038593.png)

![image-20250801193952902](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250801193952902.png)

![20250801_163229](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250801_163229.gif)

### Sequencer的功能介绍

#### 轨道内的Actor对象

我们可以在轨道内 添加Actor对象并对其Key动画等，针对此Actor对象的来源，可将其分为2类：

- [可持有对象（Possessable）](https://dev.epicgames.com/documentation/en-us/unreal-engine/spawn-temporary-actors-in-unreal-engine-cinematics#possessable)：
  - 含义：即 把**Level内已有的对象** 添加到轨道的的Actor。其原本Actor的生命周期 不受Sequencer影响
  - 适用范围：针对Level内持久性Actor的过场动画
- [可生成对象（Spawnable）](https://dev.epicgames.com/documentation/en-us/unreal-engine/spawn-temporary-actors-in-unreal-engine-cinematics#spawnable)：
  - 含义：即 **因Sequencer所需而生成的临时性Actor**。其Actor的创建、显示、销毁等生命周期 受到Sequencer控制，关闭Sequencer编辑窗口后将连带消失、不存在于Level
  - 适用范围：不会在Level内持久存在、仅需Sequencer临时Actor的过场动画
  - 辨别：Actor图标上带黄色闪电的

![PossessableActor](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250803163452038.png)

![SpawnableActor](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250803163738104.png)

#### 轨道内Actor对象的重绑定

一种常见情况例如：CharacterActor 不是在Level内预先放置、而是运行时人为放置的，而制作过场动画Sequence时，用的是 美术Map下的同CharacterActor，那这样的情况下就需要 **设置Sequence内CharacterActor重绑定为运行态时的CharacterActor**，即为 **Sequence内Actor的重绑定**


Sequence内Actor重绑定的基本流程为：

1. Actor放置于所需轨道，设定其绑定方式：
   - Sequencer编辑窗口内，直接设置Actor的绑定端点：即 蓝图内创建绑定函数，返值重绑定对象
   - Sequence播放前，执行`ALevelSequenceActor::AddBinding()`等方法，直接对 目标Sequence资产内的目标可绑端点，进行绑定操作，才播放Sequence。具体可参阅 [用Sequencer在蓝图中重新绑定Actor - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/change-cinematic-track-bindings-in-unreal-engine)
2. 绑定完成后方可正常播放，后续亦可 移除绑定、重设绑定等


一个重绑定CharacterActor的示例如：Sequence内制作CharacterActor的序列帧动画，设置其为 **可持有对象**，且设置 其绑定端点为 PlayerPawn（[UE内设方法](https://dev.epicgames.com/documentation/en-us/unreal-engine/dynamic-binding-in-sequencer#quickbind)）。则当 运行游戏、播放Sequence时，根据方法获取到 PlayerPawn、进行重绑定节点、正式开播Sequence

![image-20250803180941620](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250803180941620.png)

![20250803_185034](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250803_185034.gif)

除此之外，针对普通的Actor，也可进行重绑定：[用Sequencer在蓝图中重新绑定Actor - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/change-cinematic-track-bindings-in-unreal-engine)（原理同上）

TODO：针对 可生成对象类型的Actor的重绑定？是否能绑定到指定Asset

#### 轨道 - [镜头切换轨道](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/cinematic-camera-cut-track-in-unreal-engine)

主要功能为 处理多个相机下、相互切换混合 **最终呈现镜头**。直白点就是 多个相机能拍多个各自的画面，可以是筛选所需画面、陈列于所需时间轴节点上。官方文档：[创建摄像机动画 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/how-to-animate-cinematic-cameras-in-unreal-engine)

首先需要创建 相机剪切轨道：可人为在Sequencer编辑窗口 添加->相机切换轨道，或是 首次新建相机或把现有相机拖入，会自动创建 相机切换轨道

![image-20250801195523238](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250801195523238.png)

我们可以对目标相机Key动画等，但若是想要把此相机的画面呈现于 最终画面，则需要将其添加到 相机切换轨道内，那此段相机的镜头才能被呈现、剪辑、混合

![image-20250801201459204](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250801201459204.png)

![20250801_202433](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250801_202433.gif)

当然也可以把Character身上的相机加入轨道进行编辑：

（同时建议设置此相机的 属性->完成时->保持状态，以便在Sequence播放完后 恢复此相机的原有状态）

![20250801_205521](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250801_205521.gif)

![image-20250801210200179](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250801210200179.png)

#### 轨道 - [镜头轨道](https://dev.epicgames.com/documentation/en-us/unreal-engine/sequences-shots-and-takes-in-unreal-engine?application_version=5.5#shots)

主要功能为 承载多个Level Sequence片段，并可再次进行剪辑、混合

![20250802_213531](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250802_213531.gif)

#### 轨道 - [事件轨道](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/cinematic-event-track-in-unreal-engine)

主要功能为 在轨道上触发绑定的事件。针对事件的触发方式 又分为2类事件轨道：

- [触发器 Trigger](https://dev.epicgames.com/documentation/en-us/unreal-engine/cinematic-event-track-in-unreal-engine#triggerevents)：绑定事件后，在目标 **关键帧节点 单次触发事件**。轨道内各节点可绑定各自事件
- [重复器 Repeater](https://dev.epicgames.com/documentation/en-us/unreal-engine/cinematic-event-track-in-unreal-engine#repeaterevents)：绑定事件后，在目标 **轨道片段内 每帧触发事件**。轨道内各片段可绑定各自事件

![触发器](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250803153723424.png)

![重复器](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250803154843398.png)

对于绑定的事件，有UE提供的 快速绑定事件，或自行在 Sequence的蓝图（即 [导演蓝图](https://dev.epicgames.com/documentation/en-us/unreal-engine/cinematic-event-track-in-unreal-engine#directorblueprint)）内创建Event提供绑定

![image-20250803155512545](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250803155512545.png)



### 参考文章

- [过场动画和Sequencer - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/cinematics-and-movie-making-in-unreal-engine)
- [UE4中的动画编辑器 —— Sequencer in UE4 - 知乎](https://zhuanlan.zhihu.com/p/386200585)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# UE - Plugins

### [GAS - Editor](https://github.com/polygonvariable/unreal-gas-editor)

功能：可视化创建、管理 GAS中的 AttributeSet、Attribute

适用版本：UE5
![](https://github.com/polygonvariable/unreal-gas-editor/blob/main/Documentation/Image/gas_editor_new_attrs.png?raw=true)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# 规范

## 资产规范

资产命名规范：

- [UE5项目命名规则](https://docs.qq.com/sheet/DV010ZWxaUmhvbXJs?tab=BB08J2)
- [推荐的资产命名规范 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/recommended-asset-naming-conventions-in-unreal-engine-projects)

## 代码规范

UE内的C++代码规范：

- [代码规范 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/epic-cplusplus-coding-standard-for-unreal-engine?application_version=5.5)
- [UE中的代码命名规范 - CSDN](https://blog.csdn.net/E696472716D4/article/details/143606685)