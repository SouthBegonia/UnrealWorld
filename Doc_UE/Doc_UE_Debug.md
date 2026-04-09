介绍 UE 下的调试方法、问题定位等



## 相关调试

### 代码调试

#### 断点时判断Server或Client

添加如下参数到监视，即可在断点时间 查看代码执行于Server还是Client：

```
// UE4适用
{,,UE4Editor-Engine.dll}::GPlayInEditorContextString

// UE5适用
{,,UnrealEditor-Engine.dll}::GPlayInEditorContextString
```

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20251219150552634.png)

#### 参考文章

- [Rider 使用笔记 - Zerol](https://kisspread.github.io/notes/Basic/C++/Rider.html)



### 控制台调试

控制台调试基本流程为：**键入指定命令到控制台 以触发指定逻辑**

#### FAutoConsoleCommand

通过定义 `FAutoConsoleCommand` 类型的静态成员，即可注册指令。在控制台键入相应指令即可触发指令所绑定的委托方法：

```c++
// 原型
// IComsoleManager.h
/**
 * Autoregistering console command
 */
class FAutoConsoleCommand : private FAutoConsoleObject
{
public:
	/**
	 * Register a console command that takes no arguments
	 *
	 * @param	Name		The name of this command (must not be nullptr)
	 * @param	Help		Help text for this command
	 * @param	Command		The user function to call when this command is executed
	 * @param	Flags		Optional flags bitmask
	 */
	FAutoConsoleCommand(const TCHAR* Name, const TCHAR* Help, const FConsoleCommandDelegate& Command, uint32 Flags = ECVF_Default)
		: FAutoConsoleObject(IConsoleManager::Get().RegisterConsoleCommand(Name, Help, Command, Flags))
	{
	}
    // ...
}

// 用法示例
static FAutoConsoleCommand MyTestCommand(
	TEXT("MyTestCommand"),		//指令
	TEXT("Print a message"),	//指令描述
	FConsoleCommandDelegate::CreateLambda([]()
	{
		UE_LOG(LogTemp, Log, TEXT("Hello World"));
	})
);
```

指令可绑定的委托类型还有 `FConsoleCommandWithWorldDelegate`、`FConsoleCommandWithArgsDelegate` 等，功能上就是 携带有 `UWorld*` 参数或自定义参数等：

```c++
// 仅带 World*
static FAutoConsoleCommand MyTestCommandWithWorld(
	TEXT("MyTestCommand.WithWorld"),    //指令
	TEXT(""),
	FConsoleCommandWithWorldDelegate::CreateLambda([](UWorld* World)
	{
		if (World) 
        {
            // ...
        }
	})
);

// 仅带 自定义参数
static FAutoConsoleCommand MyTestCommandWitArgs(
	TEXT("MyTestCommand.WithArgs"),		//指令
	TEXT(""),
	FConsoleCommandWithArgsDelegate::CreateLambda([](const TArray< FString >& Args)
	{
        // 自定义参数之间由 空格 隔开
        // 例如：键入 "MyTestCommand.WithArgs 1 2 3 Hello World"，则Args就为该5个字符串
		for (const FString& Arg : Args)
			UE_LOG(LogTemp, Log, TEXT("Arg = %s"), *Arg);
	})
);

// 带 World、自定义参数、输出设备信息
static FAutoConsoleCommand MyTestCommandWithWorldArgsAndOutputDevice(
	TEXT("MyTestCommand.WithWorldArgsAndOutputDevice"),		//指令
	TEXT(""),
	FConsoleCommandWithWorldArgsAndOutputDeviceDelegate::CreateLambda([](const TArray< FString >& Args, UWorld* World, FOutputDevice& Device)
	{
        // ...
	})
);
```

除了 `FAutoConsoleCommand` 类以外，功能类似类还有 `FAutoConsoleCommandWithWorld`、`FAutoConsoleCommandWithWorldAndArgs` 等，可自行查阅 *IConsoleManager.h*

#### UCheatManager

通过 继承`class ENGINE_API UCheatManager : public UObject` 并添自定义函数，即可 **在游戏中的控制台内 键入同名函数名 以触发函数逻辑**：

```c++
// UMyCheatManager.h
#include "CoreMinimal.h"
#include "GameFramework/CheatManager.h"
#include "MyCheatManager.generated.h"

UCLASS()
class UMyCheatManager : public UCheatManager
{
    GENERATED_BODY()

public:
    // 在控制台键入 MyTestCommand 即可触发
    UFUNCTION(exec)
    void MyTestCommand() { UE_LOG(LogTemp, Log, TEXT("Hello World")); }
}
```

但需要注意，因 `UCheatManager` 的Outer必须为 `APlayerController` 类型，所以常用办法就是 指定`APlayerController::CheatClass` 成员为自定义的派生类（后续将自动在 `APlayerController::AddCheats()` 内进行实例化）

#### 参考文章

- [UE4 浅谈Console可执行函数 - 知乎](https://zhuanlan.zhihu.com/p/673148714)
- [【UE4/UE5】在虚幻引擎中创建控制台指令的几种方法 - CSDN](https://blog.csdn.net/2301_78977377/article/details/152319601)



## 崩溃闪退

UE 会在游戏**崩溃闪退时** 收集本次运行的日志文件、生成**崩溃转存文件（Crash Dump File）**，通过该文件我们可以可以做到 **定位崩溃代码调用堆栈信息**

日志文件目录位于 Saved/Crashes/UECC-XXXX/ 内，其包含4个文件：

- CrashContext.runtime-xml：dump平替，文本直接呈现崩溃信息
- CrashReportClient.ini
- [PROJECTNAME].log
- UEMinidump.dmp：使用VS分析、复现崩溃现场

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20251031110339850.png)

### 示例

例如下图示例：代码执行了 `NewObject<T>(UObject* Outer, const UClass* Class)` 但其中的 `const UClass* Class` 为 nullptr。在游戏崩溃后通过 `.xml` 文件内的调用堆栈信息，就可定位到具体出问题的代码行

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20251031111849259.png)

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20251031111513772.png)

### 参考文章

- [Unreal 的 MiniDump 机制调研 - 知乎](https://zhuanlan.zhihu.com/p/649610542)
- [UE 崩溃调试指南和技巧 - 知乎](https://zhuanlan.zhihu.com/p/655457589)




![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)




## 内存泄漏

### 排查方法

#### Memreport

控制台输入指令 以对内存进行快照、生成日志文件。多次手动快照、对比日志文件以排查问题

日志文件位置：*Game/Saved/Profiling/MemReports*

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20251216152356639.png)

常用指令有：

```
# 生成简要内存分析
MemReport

# 生成信息内存分析
MemReport -full
```

### 参考文章

- [UE4/5内存追踪方法 - 知乎](https://zhuanlan.zhihu.com/p/646523625)
