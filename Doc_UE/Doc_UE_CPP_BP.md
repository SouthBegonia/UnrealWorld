- [C++ VS 蓝图](#c-vs-蓝图)
- [反射](#反射)
  - [参考文章](#参考文章)
- [标识符](#标识符)
  - [UCLASS](#uclass)
  - [UPROPERTY](#uproperty)
  - [UFUNCTION](#ufunction)
  - [参考文章](#参考文章-1)
- [Marcro 宏](#marcro-宏)
- [指针与引用](#指针与引用)
  - [硬引用与软引用](#硬引用与软引用)
      - [使用注意事项](#使用注意事项)
    - [参考文章](#参考文章-2)
  - [TObjectPtr](#tobjectptr)
  - [TWeakObjectPtr](#tweakobjectptr)
  - [TSubClassOf](#tsubclassof)
  - [TSoftObjectPtr](#tsoftobjectptr)
  - [TSharedPtr](#tsharedptr)
  - [TSharedRef](#tsharedref)
  - [TWeakPtr](#tweakptr)
  - [参考文章](#参考文章-3)
- [接口](#接口)
  - [蓝图接口](#蓝图接口)
    - [基本用法](#基本用法)
  - [C++接口](#c接口)
    - [基本用法](#基本用法-1)
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
- [资源](#资源)
  - [资源加载](#资源加载)
    - [硬引用资源](#硬引用资源)
      - [编辑器直接加载](#编辑器直接加载)
      - [构造函数加载](#构造函数加载)
      - [主动调用LoadClass、LoadObject](#主动调用loadclassloadobject)
    - [软引用资源](#软引用资源)
      - [FSoftObjectPath、FSoftClassPath](#fsoftobjectpathfsoftclasspath)
      - [TSoftObjectPtr、TSoftClassPtr](#tsoftobjectptrtsoftclassptr)
  - [参考文章](#参考文章-14)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# C++ VS 蓝图

广义上，游戏内容可分为 逻辑与数据。在实践中，C++或蓝图均可实现二者内容，因此可以根据C++及蓝图的各自优势，选择合适的实现方案：[C++ VS 蓝图 - UnrealEngine](https://dev.epicgames.com/documentation/en-us/unreal-engine/balancing-blueprint-and-cplusplus?application_version=4.27#c++vsblueprints)

# 反射

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

## 参考文章

- [虚幻引擎反射系统 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/reflection-system-in-unreal-engine)
- [UE反射的初步了解 - 知乎](https://zhuanlan.zhihu.com/p/15356040140)
- [UE4 C++与蓝图 反射宏 - 知乎](https://zhuanlan.zhihu.com/p/435698880)
- [UnrealEngine - 反射系统分析 - 博客园](https://www.cnblogs.com/lawliet12/p/17272835.html)
- [【UE 反射】反射的原理是什么？如何使用机制？ - CSDN](https://blog.csdn.net/hhw_hhw/article/details/139287867)



# 标识符

标识符介绍：[UE5标识符详解 - 知乎 - 大钊](https://zhuanlan.zhihu.com/p/717920216)

常用标识符查阅手册：[UnrealSpecifiers | UE5标识符详解 - Github - 大钊](https://github.com/fjz13/UnrealSpecifiers)

## UCLASS

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

## UPROPERTY

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

## UFUNCTION

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

## 参考文章

- [类说明符 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/class-specifiers)
- [属性说明符 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/property-specifiers?application_version=4.27)
- [函数说明符 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/ufunctions-in-unreal-engine?application_version=5.5)
- [元数据说明符 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/metadata-specifiers-in-unreal-engine?application_version=5.5)
- [UE4 C++学习 浅析UProperty属性说明符 - CSDN](https://www.cnblogs.com/CatSevenMillion/p/16637347.html)



# Marcro 宏

|       宏       |    适用地点     |                  说明                   |
| :------------: | :-------------: | :-------------------------------------: |
|     UCLASS     | UObject的派生类 | 标记从 UObject 派生的类，以被UE反射识别 |
| GENERATED_BODY | UObject的派生类 |     配合UCLASS、生成UE反射相关代码      |
|   UPROPERTY    |    标记属性     |  被UE反射识别，进而反映到 编辑器、蓝图  |
|   UFUNCTION    |    标记函数     |  被UE反射识别，进而反映到 编辑器、蓝图  |



# 指针与引用

## 硬引用与软引用

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

### 参考文章

- [UE5 Understanding hard and soft references - LeafBrainGames - Youtube](https://www.youtube.com/watch?v=aUG54KCP89M&ab_channel=LeafBranchGames)
- [SOFT Object References in Unreal Engine EXPLAINED - The Game Dev Cave - Youtube](https://www.youtube.com/watch?v=BazkY5aqoig&ab_channel=TheGameDevCave)

## TObjectPtr

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

此外，`TObjectPtr<T>` + `UPROPERTY()` 的用法，使变量会被UE识别为 **硬引用**

## TWeakObjectPtr

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

## TSubClassOf

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

## TSoftObjectPtr

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

## TSharedPtr

TSharedPtr 是 非`UObject`对象的 共享智能指针，使用 **引用计数 来控制对象生命周期**（最后一个持有的TSharedPtr被销毁时，对象才会释放）

```c++
USTRUCT(BlueprintType)
struct FMyStruct
{
	GENERATED_USTRUCT_BODY()
public:
	void TestFunc() {}
};

//创建 共享指针
TSharedPtr<FMyStruct> MySharedPtr = nullptr;
//MySharedPtr = MakeShareable(new FMyStruct);
MySharedPtr = MakeShared<FMyStruct>();

if (MySharedPtr.IsValid())	// 判断有效性
{
    //成员访问
    MySharedPtr->TestFunc();

    //获取 普通指针
    FMyStruct* MyStructPtr = MySharedPtr.Get();
    MyStructPtr->TestFunc();

    //获取 引用计数
    int32 MyStructPtrRefCount = MySharedPtr.GetSharedReferenceCount();
    //引用计数：MySharedPtr[1]

    //复制 共享指针
    TSharedPtr<FMyStruct> MySharedPtr2 = MySharedPtr;
    //引用计数：MySharedPtr[2]，MySharedPtr2[2]

    //转移 共享指针
    TSharedPtr<FMyStruct> MySharedPtr3 = MoveTemp(MySharedPtr);
    //引用计数：MySharedPtr[0]，MySharedPtr2[2]，MySharedPtr3[2]

    //清空 共享指针
    MySharedPtr2.Reset();
    //引用计数：MySharedPtr[0]，MySharedPtr2[0]，MySharedPtr3[1]
    MySharedPtr3.Reset();
    //引用计数：MySharedPtr[0]，MySharedPtr2[0]，MySharedPtr3[0]
}
```

## TSharedRef

TSharedRef是 非`UObject`对象的 共享智能指针，与 TSharedPtr 类似也是引用计数，但其 **固定引用 非空对象，且不可置为nullptr**

```c++
USTRUCT(BlueprintType)
struct FMyStruct
{
	GENERATED_USTRUCT_BODY()
public:
	void TestFunc() {}
};

//创建 共享引用
//TSharedRef<FMyStruct> MySharedRef(new FMyStruct);
//TSharedRef<FMyStruct> MySharedRef = MakeShareable(new FMyStruct);
TSharedRef<FMyStruct> MySharedRef = MakeShared<FMyStruct>();

//成员访问
MySharedRef->TestFunc();

//获取 普通引用
FMyStruct& MyStructRefTemp = MySharedRef.Get();
MyStructRefTemp.TestFunc();

//获取 引用计数
int32 MyStructRefCount = MySharedRef.GetSharedReferenceCount();
//引用计数：MySharedRef[1]

//复制 共享引用
TSharedRef<FMyStruct> MySharedRef2 = MySharedRef;
//引用计数：MySharedRef[2]，MySharedRe2[2]
MyStructRefCount = MySharedRef.GetSharedReferenceCount();
MyStructRefCount = MySharedRef2.GetSharedReferenceCount();

//转换为 TSharedPtr、TWeakPtr
TSharedPtr<FMyStruct> MySharedPtr = MySharedRef.ToSharedPtr();
TWeakPtr<FMyStruct> MyWeakPtr = MySharedRef.ToWeakPtr();
```

## TWeakPtr

TWeakPtr是 非`UObject`对象的 弱指针，其引入旨在于 **解决TSharedPtr可能带来的循环引用问题**

与 TSharedPtr 相比，其不参与引用计数、不会阻止对象的销毁

```c++
USTRUCT(BlueprintType)
struct FMyStruct
{
	GENERATED_USTRUCT_BODY()
public:
	void TestFunc() {}
};

TSharedPtr<FMyStruct> MySharedPtr = nullptr;
//TSharedRef<FMyStruct> MySharedRef = MakeShared<FMyStruct>();

//创建 弱指针
TWeakPtr<FMyStruct> MyWeakPtr(MySharedPtr);
//TWeakPtr<FMyStruct> MyWeakPtr(MySharedRef);

if (MyWeakPtr.IsValid())	// 判断有效性
{
    //成员访问：需转换为TSharedPtr，以进行指针对象访问
    TSharedPtr<FMyStruct> MySharedPtrByPin = MyWeakPtr.Pin();
    if (MySharedPtrByPin.IsValid())
    {
        MySharedPtrByPin->TestFunc();
    }

    //复制 弱指针
    TWeakPtr<FMyStruct> MyWeakPtr2 = MyWeakPtr;

    //清空 弱指针
    MyWeakPtr2.Reset();

	//清空共享指针后，弱指针变空
    MySharedPtr.Reset();
    if (!MyWeakPtr.Pin())
    {
        //弱指针已空
    }
}
```

## 参考文章

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



# 接口

> **接口**（Interface）是一种定义一组方法或功能的约定或协议，它规定了 类或对象应该提供哪些功能和如何提供这些功能，但不涉及具体的实现细节，使得 不同的类或对象 能够通过相同的方式进行交互

UE可从 C++层、蓝图层 创建接口，接口的引入旨在于推荐使用 **面向接口编程**：

- 解耦合：通过接口 而非具体的类成员方法，使不同对象间的耦合度降低（避免无效强引用）
- 增强可替换性
- 实现多态性
- 提高可维护性

## 蓝图接口

[**蓝图接口（Blueprint Interface）**](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/blueprint-interface-in-unreal-engine) 是一个或多个函数的集合，且 **函数只有声明、不可实现**，由其他蓝图对象进行 接口的继承实现

### 基本用法

通过 内容浏览器内的 蓝图->蓝图接口，即可创建蓝图接口，后其他蓝图对象即可 实现该接口。详细过程可参阅：[【教程】UE4中接口的使用--蓝图篇（一） - 知乎](https://zhuanlan.zhihu.com/p/36734872)

需要注意的是，当外部尝试调用一个接口方法时，[接口方法的 函数类型](https://dev.epicgames.com/documentation/en-us/unreal-engine/implementing-blueprint-interfaces-in-unreal-engine?application_version=5.5#callinterfacefunctions)有3种：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250911193638279.png)

|     函数类型      |                             描述                             |           适用情况           |
| :---------------: | :----------------------------------------------------------: | :--------------------------: |
|  对象（Object）   |            安全调用 实现了接口的对象上的 接口方法            | 不推荐，违背面向接口编程原则 |
| 接口（Interface） |            安全调用 实现了接口的对象上的 接口方法            |     已知 对象实现了接口      |
|      Message      | 尝试调用 对象上的 接口方法，若对象未实现接口则 调用失败。相比于其他2个 有性能消耗 |  推荐。未知 对象实现了接口   |

## C++接口

在C++层实现的接口，则更为灵活：接口方法可以 蓝图可见/不可见

### 基本用法

在C++层声明一个接口，主要有2块构成：派生自`UInterface`的空白类 + 实际接口类

```c++
// ReactToTriggerInterface.h
#pragma once

#include "ReactToTriggerInterface.generated.h"

//声明 接口的空白类：只是为了向虚幻引擎反射系统确保可见性。通常无需修改
UINTERFACE(MinimalAPI, Blueprintable)
class UReactToTriggerInterface : public UInterface
{
    GENERATED_BODY()
};

//声明 接口的实际类：真正能被实现的接口类
class IReactToTriggerInterface
{
    GENERATED_BODY()

public:
    /** 在此处添加接口函数声明 */
};
```

后即可 被其他类实现此接口：

```c++
// ATrap.h
#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "Trap.generated.h"

#include "ReactToTriggerInterface.h"

UCLASS(Blueprintable)
class ATrap : public AActor, public IReactToTriggerInterface
{
	GENERATED_BODY()

public:
	/** Add interface function overrides here. */
}
```

对于添加 **蓝图不可见 的接口方法**，声明为 不带有`UFUNCTION`宏的 虚函数 即可：

```c++
// ReactToTrigger.h
public:	
	virtual bool ReactToTrigger();

// ReactToTrigger.cpp
bool IReactToTriggerInterface::ReactToTrigger()
{		
	return false;
}


// Trap.h
public:
	virtual bool ReactToTrigger() override;

// Trap.cpp
bool ATrap::ReactToTrigger()
{
	return true;
}
```

对于添加 **蓝图可见  的接口方法**，声明为 `UFUNCTION`宏 + `BlueprintCallable` + `BlueprintNativeEvent/BlueprintImplementableEvent `的 非虚函数 即可：

```c++
// ReactToTrigger.h
public:	
	/* 可以 在C++或蓝图 中实现 */
	UFUNCTION(BlueprintCallable, BlueprintNativeEvent)
	bool ReactToTrigger();
	
	/* 只能在 蓝图 中实现 */
	UFUNCTION(BlueprintCallable, BlueprintImplementableEvent)
	bool ReactToTriggerOnlyBP();


// Trap.h
public:
	bool ReactToTrigger_Implementation() override;

// Trap.cpp
bool ATrap::ReactToTrigger_Implementation() const
{
	return false;
}
```

对于 **判断对象是否实现了接口**：

```c++
// 判断 OriginalObject 是否实现了 UReactToTriggerInterface
bool bIsImplemented = OriginalObject->GetClass()->ImplementsInterface(UReactToTriggerInterface::StaticClass());
//（与上等效）
bIsImplemented = OriginalObject->Implements<UReactToTriggerInterface>();

// 判断 OriginalObject 是否实现了 UReactToTriggerInterface，实现则 ReactingObject非空
IReactToTriggerInterface* ReactingObject = Cast<IReactToTriggerInterface>(OriginalObject);
```

## 参考文章

- [蓝图接口 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/blueprint-interface-in-unreal-engine?application_version=5.5)

- [【UE4 C++ 基础知识】<9> Interface 接口 - cnblogs](https://www.cnblogs.com/shiroe/p/14691197.html)

- [【UE】在C++中定义和使用接口 - 知乎](https://zhuanlan.zhihu.com/p/556880518)



# 容器

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

## 参考文章

- [[UE C++] TArray - CSDN](https://blog.csdn.net/qq_52179126/article/details/130605021)
- [[UE C++] TMap - CSDN](https://blog.csdn.net/qq_52179126/article/details/130712877)

- [[UE C++] TSet - CSDN](https://blog.csdn.net/qq_52179126/article/details/130756860)



# 字符串

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

## 参考文章

- [字符串处理 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/string-handling-in-unreal-engine)
- [FString、FName、FText的基本使用 - CSDN](https://blog.csdn.net/ChaoChao66666/article/details/144568057)
- [UE4 C++基础 - 字符串和本地化 - 知乎](https://zhuanlan.zhihu.com/p/163587790)



# 枚举

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

## 参考文章

- [[UE C++] Enum的使用 - CSDN](https://blog.csdn.net/qq_52179126/article/details/129891590)



# 结构体

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

## 参考文章

- [UE C++ Struct 的使用 - CSDN](https://blog.csdn.net/qq_52179126/article/details/129768456)



# 数据表 DataTables

数据表是一种表格，表中的数据字段可以是任何有效的 `UObject` 属性，包括资产引用。数据表可 导入/导出 为CSV或JSON

## 创建数据表

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

## 数据表操作

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

## 数据表导入导出

目前支持 数据表 到 CSV、JSON 的相互转换。例如 将 表格数据 资产 转换为CSV：

![image-20250614173053274](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250614173053274.png)

将 CSV 转换为 表格资产：

（对于CSV、JSON内的 非匹配、缺失列字段，可设置表格资产内的 导入选项 的相关参数）

![image-20250614172937923](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250614172937923.png)

## 参考文章

- [数据驱动的Gameplay元素 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/data-driven-gameplay-elements-in-unreal-engine?application_version=5.5)
- [UE5中的数据表：UDataTable - 知乎](https://zhuanlan.zhihu.com/p/567901416)
- [[UE C++] Data Table的使用 - CSDN](https://blog.csdn.net/qq_52179126/article/details/129800615)



# 委托

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

## 参考文章

- [委托 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/delegates-and-lambda-functions-in-unreal-engine?application_version=5.5)
- [动态委托基本用法（单播，多播） - 知乎](https://zhuanlan.zhihu.com/p/27732328690)



# 断言

`assert` 可在 开发期间 帮助检测和诊断不正常或无效的运行时条件

基本格式为：`assert(表达式)`，表达式=false=触发断言

| 断言类型 |         当触发断言         |                     版本区别                     |                             备注                             |
| :------: | :------------------------: | :----------------------------------------------: | :----------------------------------------------------------: |
|  check   |          中断运行          |        发布版本内 不断言 且 不执行表达式         | USE_CHECKS_IN_SHIPPING宏 = 标识断言检查 在发布版本内是否执行，默认为0，可按需设置 |
|  verify  |          中断运行          |         发布版本内 不断言 但 执行表达式          |                                                              |
|  ensure  | 可继续运行，可打印堆栈信息 | 所有版本都执行表达式 但 非发布版本才打印堆栈信息 |                   会将检测结果以bool值返回                   |

断言选择：

![](https://picx.zhimg.com/v2-1f79888849ba93c3dfc12addbeb850a3_1440w.jpg)

## 参考文章

- [断言 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/asserts-in-unreal-engine?application_version=5.5)
- [UE4断言总结 - 知乎](https://zhuanlan.zhihu.com/p/64462945)



# 事件、函数、宏

**事件** 侧重于 逻辑触发，**函数** 侧重于 即时的逻辑执行及结果返回，**宏** 侧重于 逻辑分流处理

![](https://picx.zhimg.com/v2-a5bcc77391e47dd2e117c99634153ae9_1440w.jpg)

## 参考文章

- [【UE4】函数、宏和事件的区别 - 知乎](https://zhuanlan.zhihu.com/p/527787878)
- [UE5 ＜事件、函数、宏＞ - CSDN](https://blog.csdn.net/CandyU2/article/details/147124582)



# DrawDebug

DrawDebug 用于在 编辑器或开发环境下，于场景内绘制 图形或文字，以便直观查验信息

相关绘制方法（C++或蓝图都有）均使用的 `UKismetSystemLibrary` 内的API：

![image-20250615152652698](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250615152652698.png)

效果示例：

![](https://pic1.zhimg.com/v2-2c180bda58846fe93139fa3ceaee0ff4_1440w.jpg)

![](https://pica.zhimg.com/v2-467275fb62cb6808081ec87521f88c9c_1440w.jpg)

![](https://pic3.zhimg.com/v2-76d3f8d5d961e7f5b98a7807bfa2d41c_1440w.jpg)

## 参考文章

- [虚幻引擎中各种场景调试绘制指令（DrawDebug）的使用、效果和原理 - 知乎](https://zhuanlan.zhihu.com/p/718494965)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# 资源

## 资源加载

### 硬引用资源

#### 编辑器直接加载

当变量被标记为 `UPROPERTY()` + 裸指针/TObjectPtr/TSubClassOf，且 暴露给蓝图+挂载资产，则 此资产资源将随Owner加载时被一并加载，也将计入 Owner内存占用的一部分

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250913145809206.png)

#### 构造函数加载

在`UObject`类型的 构造函数内 使用 `ConstructorHelpers::FClassFinder`、`ConstructorHelpers::FObjectFinder` 以在编辑器态、运行态时进行资源加载：

备注：此情况虽已形成硬引用关系，但 引用查看器、SizeMap 均无法捕获，不便于后期排查

```c++
// .h
TSubclassOf<AActor> HardClass;

// .cpp
[TESTCLASS]::TESTCLASS()
{
	HardClass = ConstructorHelpers::FClassFinder<AActor>(TEXT("/Script/Engine.Blueprint'/Game/BP_BoxActor01.BP_BoxActor01_C'")).Class;
}
```

备注：资源为 蓝图对象时，末尾添加 `_C`，原因可参阅：[UE蓝图资源路径中“_C“的含义 - CSDN](https://blog.csdn.net/Mnsentinor/article/details/143088840)

#### 主动调用LoadClass、LoadObject

在运行态时，主动调用 `LoadClass`、`LoadObject` 以同步加载资源：

```c++
// .h
TSubclassOf<AActor> HardClass;

HardClass = LoadClass<AActor>(nullptr, TEXT("/Script/Engine.Blueprint'/Game/BP_BoxActor01.BP_BoxActor01_C'"));
```

### 软引用资源

#### FSoftObjectPath、FSoftClassPath

`FSoftObjectPath`可以软引用 任何资源和UClass

`FSoftClassPath`只能软引用 Class、DynamicClass、BlueprintGeneratedClass、WidgetBlueprintGeneratedClass、AnimBluprintGeneratedClass和LinkerPlaceholderClass

```c++
// .h
FSoftObjectPath SoftObjectPath;
TSharedPtr<FStreamableHandle> AsyncLoadAssetTestHandle;

// .cpp
FStreamableDelegate AsyncLoadAssetTestDelegate;

if (SoftObjectPath.IsValid())
{
	// 资源合法有效

	if (SoftObjectPath.ResolveObject() == nullptr)
	{
		// 资源尚未加载

		// 同步加载 1：
		UObject* ObjectPtr = SoftObjectPath.TryLoad();

		// 同步加载 2：
		FStreamableManager& StreamableManager = UAssetManager::GetStreamableManager();
		UObject* ObjectPtr = StreamableManager.LoadSynchronous(SoftObjectPath);

		// 异步加载：
		AsyncLoadAssetTestDelegate = FStreamableDelegate::CreateLambda([this]()
		{
			UObject* LoadObject = AsyncLoadAssetTestHandle->GetLoadedAsset();
		});
		FStreamableManager& StreamableManager = UAssetManager::GetStreamableManager();
		AsyncLoadAssetTestHandle = StreamableManager.RequestAsyncLoad(SoftObjectPath, AsyncLoadAssetTestDelegate);
	}else
	{
		// 资源已加载
		UObject* ObjectPtr = SoftObjectPath.ResolveObject();
	}
}else
{
	// 资源无效
}
```

```c++
// .h
FSoftClassPath SoftClassPath;
TSharedPtr<FStreamableHandle> AsyncLoadAssetTestHandle;

// .cpp
FStreamableDelegate AsyncLoadAssetTestDelegate;

if (SoftClassPath.IsValid())
{
	// 资源合法有效

	if (SoftClassPath.ResolveClass() == nullptr)
	{
		// 资源尚未加载

		// 同步加载 1：
		UClass* ClassPtr = SoftClassPath.TryLoadClass<AActor>();

		// 同步加载 2：
		FStreamableManager& StreamableManager = UAssetManager::GetStreamableManager();
		UClass* ClassPtr = Cast<UClass>(StreamableManager.LoadSynchronous(SoftClassPath));

		// 异步加载：
		AsyncLoadAssetTestDelegate = FStreamableDelegate::CreateLambda([this]()
		{
			UObject* LoadObject = AsyncLoadAssetTestHandle->GetLoadedAsset();
			UClass* LoadClass = Cast<UClass>(LoadObject);
		});
		FStreamableManager& StreamableManager = UAssetManager::GetStreamableManager();
		AsyncLoadAssetTestHandle = StreamableManager.RequestAsyncLoad(SoftClassPath, AsyncLoadAssetTestDelegate);
	}else
	{
		// 资源已加载
        UClass* ClassPtr = SoftClassPath.ResolveClass();
	}
}
else
{
	// 资源无效
}
```

#### TSoftObjectPtr、TSoftClassPtr

```c++
// .h
TSoftClassPtr<AActor> SoftClassPtr;
TSoftObjectPtr<UAnimMontage> SoftObjectPtr;	// TSoftClassPtr内部也是走的TSoftObjectPtr，故二者用法类似
void LoadSourceCallback();

// .cpp
if (SoftClassPtr.IsNull() == false)
{
	// 资源合法有效

	if (SoftClassPtr.IsPending())	// 内部展开为：Get() == nullptr && !IsNull()
	{
		// 资源尚未加载

		// 同步加载 1：
		UClass* ClassPtr = SoftClassPtr.LoadSynchronous();

		// 同步加载 2：
		FStreamableManager& StreamableManager = UAssetManager::GetStreamableManager();
		TSubclassOf<AActor> ClassPtr = StreamableManager.LoadSynchronous(SoftClassPtr);

        // 异步加载
        FStreamableManager& StreamableManager = UAssetManager::GetStreamableManager();
        StreamableManager.RequestAsyncLoad(
            SoftClassPtr.ToSoftObjectPath(),
            FStreamableDelegate::CreateUObject(this, &[TESTCLASS]::LoadSourceCallback)
        );
	}else
	{
		// 资源已加载
		UClass* ClassPtr = SoftClassPtr.Get();
	}
}
else
{
	// 资源无效
}

void [TESTCLASS]::LoadSourceCallback()
{
	UClass* ClassPtr = SoftClassPtr.Get();
}

```

## 参考文章

- [【UE5 资源管理】LoadAsset加载资源 - 知乎](https://zhuanlan.zhihu.com/p/691079389)
- [UE4中资源的引用 - cnblogs](https://www.cnblogs.com/kekec/p/13357937.html)
- [[UE C++] 资源加载(二) 查找资源——FindObject - CSDN](https://blog.csdn.net/qq_52179126/article/details/130114399)
- [UE蓝图资源路径中“_C“的含义 - CSDN](https://blog.csdn.net/Mnsentinor/article/details/143088840)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)