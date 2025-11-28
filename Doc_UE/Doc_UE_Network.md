介绍 UE 网络同步相关内容



# 基础概念

## 网络架构

UE采用 [客户端/服务器（C/S模型）](https://www.cnblogs.com/ofnoname/p/18476334)实现的多人联网游戏。网络层采用UDP实现以追求高性能，可靠性则由引擎实现保证

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20251128154052008.png)

多人游戏又可分为：

| 多人游戏类型 |                             说明                             |          备注           |
| :----------: | :----------------------------------------------------------: | :---------------------: |
| 本地多人游戏 | 单台计算机作为单机游戏运行（所有玩家、资产、功能均在此处），所有玩家输入连接到此计算机、控制其游戏内容 | 类似一台主机+多手柄玩家 |
| 联网多人游戏 | 所有玩家（具有不同的计算机，即 客户端）通过网络连接到 一台中央计算机（即 服务器）。服务器托管着游戏的核心规则、将游戏状态信息与每个连接的客户端共享 |   常见的多人网络游戏    |

基于C/S模型，Server其Client单向发送/更新数据的过程 即为**复制（Replication）** ，另一种方式为 Server/Client相互发起远程函数调用的 **远程过程调用（Remote Procedure Call, RPC）**，其二者区别：

| 网络同步方式 |                        流程方向                        |      更新频率      |        适用场景         |
| :----------: | :----------------------------------------------------: | :----------------: | :---------------------: |
| Replication  |                 单向（Server->Client）                 |  发生变化自动更新  |  周期性更新Actor或属性  |
|     RPC      | 非单向（Server<->Client、Server->Client1/Client2/...） | 被执行时才调用更新 | 暂时性/修饰性的游戏事件 |



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



## 网络模式

网络模式（`ENetMode`）表述了 计算机与联网多人游戏会话之间的关系

|              网络模式              |                             说明                             |                 适用类型                 |
| :--------------------------------: | :----------------------------------------------------------: | :--------------------------------------: |
|      单机（`NM_Standalone`）       | 有一个或多个本地玩家且没有联网的游戏。仍被视为服务器，因为它包含所有服务器功能，但不接受来自远程客户端的连接 |                 本地游戏                 |
| 专用服务器（`NM_DedicatedServer`） | 没有本地玩家的服务器。服务器仅接受来自远程客户端的连接，放弃了图形、声音、输入和其他面向玩家的功能 |       安全、持久的大规模多玩家游戏       |
|  监听服务器（`NM_ListenServer`）   | 此服务器也有一个本地玩家，此玩家负责托管游戏，其他玩家则作为客户端以进行连接 | 小型玩家群体间进行临时合作和竞技多人游戏 |
|       客户端（`NM_Client`）        |                   连接到远程服务器的客户端                   |                                          |

```c++
enum ENetMode
{
	/** Standalone: a game without networking, with one or more local players. Still considered a server because it has all server functionality. */
	NM_Standalone,

	/** Dedicated server: server with no local players. */
	NM_DedicatedServer,

	/** Listen server: a server that also has a local player who is hosting the game, available to other players on the network. */
	NM_ListenServer,

	/**
	 * Network client: client connected to a remote server.
	 * Note that every mode less than this value is a kind of server, so checking NetMode < NM_Client is always some variety of server.
	 */
	NM_Client,

	NM_MAX,
};
```



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



## 网络同步

当Server与某个Client建立链接，双方`UNetDriver`内就生成了一个`UNetConnection`对象，`UNetConnection`内又包含多个`UActorChannel`，每个`UActorChannel`都表示 进行网络复制的Actor。因此 **网络同步的核心是Actor**

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20251128173907629.png)

Actor与Server进行网路同步的方法有 Replication和RPC：

### Replication

**复制（Replication）** 是指 权威服务器将状态数据发送到连接的客户端的过程。主要包含 Actor的复制、属性的复制

#### Actor的复制

设置 `AActor`内的 `bReplicates`为True 即可启用该Actor的复制

例如 游戏运行中动态生成的此Actor，将由Server复制到所有Client，所有Client才能访问到同一个此Actor

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20251128173725161.png)

#### 属性的复制

设置 Actor内的`UPROPERTY`成员添加元数据说明符`Replicated`或`ReplicatedUsing `即可启用该属性的复制

|  元数据说明符   |                     功能                      |
| :-------------: | :-------------------------------------------: |
|  NotReplicated  |              标记该属性 不被复制              |
|   Replicated    |               标记该属性 被复制               |
| ReplicatedUsing | 标记该属性 被复制，且被复制时执行指定回调函数 |

基本用法（或参阅 [官网用法](https://dev.epicgames.com/documentation/en-us/unreal-engine/replicate-actor-properties-in-unreal-engine?application_version=5.6#replicateactorproperties)）：

```c++
// // ADerivedActor.h
 UCLASS()
 class ADerivedActor : public AActor
 {
     GENERATED_BODY()
 public:
     // Actor的构造函数
     ADerivedActor(const class FPostConstructInitializeProperties & PCIP);
     
     // 要复制的属性
     UPROPERTY(Replicated)
     uint32 Health;
     
     // 要复制的属性 及 复制更新回调事件
     UPROPERTY(ReplicatedUsing=OnRep_HealthUpdate)
     int32 HealthValue1;
     UFUNCTION()
     void OnRep_HealthUpdate(int32 LastHealthValue);

     // 重载复制属性函数
     virtual void GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const override;
 };

// ADerivedActor.cpp
 ADerivedActor::ADerivedActor(const class FPostConstructInitializeProperties & PCIP) : Super(PCIP)
 {
     // 设置Actor的复制
     bReplicates = true;
 }
 void ADerivedActor::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
 {
     // 调用Super
     Super::GetLifetimeReplicatedProps(OutLifetimeProps);

     // 设置属性的复制
     DOREPLIFETIME(ADerivedActor, Health);
     DOREPLIFETIME(ADerivedActor, HealthValue1);
 }
void ADerivedActor::OnRep_HealthUpdate(int32 LastHealthValue)
{
    UE_LOG(LogTemp, Log, TEXT("OnRep_Value with value. Last value: %d"), LastHealthValue)
    // 添加自定义OnRep逻辑
}
```

### RPC

**远程过程调用（RPC）**是指 在一台或多台连接的机器上远程执行本地调用的函数，即 Server与Client通过网络连接可相互调用函数。其类型细分为：

|   RPC类型    |                            介绍                             |                             备注                             | 适用场景 |
| :----------: | :---------------------------------------------------------: | :----------------------------------------------------------: | :------: |
|    Client    |         在Server上调用，在拥有此Actor的Client上执行         |                                                              |          |
|    Server    | 在Client端且拥有目标所有权的Actor上调用，最终在Server上执行 | 客户端常用的拥有所有权的Actor有：PlayerController及其控制的Pawn、PlayerState |          |
| NetMulticast |           仅在Server端调用，最终在所有Client执行            |                                                              |          |

#### RPC的基本用法

参阅 [官网用法](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/remote-procedure-calls-in-unreal-engine?application_version=5.6)：

```c++
// ADerivedActor.h
UCLASS()
class ADerivedActor : public AActor
{
	GENERATED_BODY()
 public:

	// 客户端RPC函数
	UFUNCTION(Client)
	void ClientRPC();

	// 服务器RPC函数
	UFUNCTION(Server)
	void ServerRPC();

	// 组播RPC函数
	UFUNCTION(NetMulticast)
	void MulticastRPC();
}

// ADerivedActor.cpp
ADerivedActor::ADerivedActor(const class FPostConstructInitializeProperties & PCIP) : Super(PCIP)
{
	bReplicates = true;
}
void ADerivedActor::ClientRPC_Implementation()
{
	// 将在每台执行该函数的机器上打印该日志。
	UE_LOG(LogTemp, Log, TEXT("ClientRPC executed."))
}
void ADerivedActor::ServerRPC_Implementation()
{	
	// 仅在ServerRPC_Validate返回true时才执行该函数。
	// 将在每台执行该函数的机器上打印该日志。
	UE_LOG(LogTemp, Log, TEXT("ServerRPC executed."))
}
void ADerivedActor::MulticastRPC_Implementation()
{
	// 将在每台执行该函数的机器上打印该日志。
	UE_LOG(LogTemp, Log, TEXT("MulticastRPC executed."))	
}

/* ===== 调用RPC函数 ===== */
// 从Client调用以在Server上运行
ADerivedClientActor* MyDerivedClientActor;
MyDerivedClientActor->ServerRPC();

// 从Server调用以在Client上运行
ADerivedServerActor* MyDerivedServerActor;
MyDerivedServerActor->ClientRPC();

// 从Server调用以在Server和所有相关Client上运行
ADerviedServerActor* MyDerivedServerActor;
MyDerievedServerActor->MulticastRPC();
```



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# 参考文章

- [网络概述 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/networking-overview-for-unreal-engine?application_version=5.6)
- [UE5 -- Replication（网络复制）- 知乎](https://zhuanlan.zhihu.com/p/578480318)
- [Multiplayer in Unreal Engine: How to Understand Network Replication - YouTube](https://www.youtube.com/watch?v=JOJP0CvpB8w&t=248s)
- [UE5的网络同步 - 知乎](https://zhuanlan.zhihu.com/p/1909663871632908496)


