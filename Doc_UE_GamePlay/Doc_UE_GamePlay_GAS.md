**Gameplay技能系统（Gameplay Ability System）** 是UE的一种框架，用于实现 `Actor`可以 **拥有和触发的属性、技能和交互**

该系统可适应各种各样的[Gameplay驱动型](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/data-driven-gameplay-elements-in-unreal-engine?application_version=5.4)项目，例如 **角色扮演游戏** （RPG）、 **动作冒险（Action-Adventure）** 游戏和 **多玩家在线战术竞技** 游戏（MOBA）

通过GAS，可以快速制作游戏内的 主动/被动技能、Buff效果、伤害计算、战斗状态逻辑等


- [Ability System Component（ASC）](#ability-system-componentasc)
	- [用法-基本配置](#用法-基本配置)
- [Gameplay Tags](#gameplay-tags)
	- [Tags的定义](#tags的定义)
		- [从DataTable导入Tags](#从datatable导入tags)
	- [Tags的规划](#tags的规划)
		- [ARPG类的Tags规划](#arpg类的tags规划)
		- [参考文章](#参考文章)
- [Gameplay Ability（GA）](#gameplay-abilityga)
	- [用法](#用法)
		- [1. 创建GA](#1-创建ga)
		- [2. 添加GA到ASC](#2-添加ga到asc)
		- [3.1 GA的激活](#31-ga的激活)
		- [3-2. GA的激活 - Input触发](#3-2-ga的激活---input触发)
			- [常规Input输入以激活GA](#常规input输入以激活ga)
			- [EnhancedInput增强输入以激活GA](#enhancedinput增强输入以激活ga)
			- [参考文章](#参考文章-1)
	- [GA细节面板 - Tags标签](#ga细节面板---tags标签)
		- [关于 Source XXX Tags、Target XXX Tags](#关于-source-xxx-tagstarget-xxx-tags)
	- [GA细节面板 - Triggers触发器](#ga细节面板---triggers触发器)
	- [GA细节面板 - Cooldowns](#ga细节面板---cooldowns)
	- [GA细节面板 - Costs](#ga细节面板---costs)
	- [GA - AbilityTask](#ga---abilitytask)
- [Gameplay Attributes](#gameplay-attributes)
	- [AS用法](#as用法)
		- [1. 创建Attribute及AS](#1-创建attribute及as)
		- [2. AS的初始化](#2-as的初始化)
		- [3. AS的使用](#3-as的使用)
- [Gameplay Effect（GE）](#gameplay-effectge)
	- [GE的功能简介](#ge的功能简介)
		- [修改Attribute](#修改attribute)
		- [赋予GameplayTag](#赋予gameplaytag)
		- [赋予GA](#赋予ga)
		- [嵌套应用GE](#嵌套应用ge)
		- [调用GC](#调用gc)
	- [GE功能 - 修改Attribute](#ge功能---修改attribute)
		- [Modifiers](#modifiers)
			- [Attribute Based](#attribute-based)
			- [Custom Calculation Class](#custom-calculation-class)
			- [Set By Caller](#set-by-caller)
		- [Executions](#executions)
			- [Execution Calculation Class](#execution-calculation-class)
		- [参考文章](#参考文章-2)
	- [GE细节面板](#ge细节面板)
		- [GE细节面板 - Duration](#ge细节面板---duration)
		- [GE细节面板 - Period](#ge细节面板---period)
	- [GE的使用](#ge的使用)
- [Gameplay Cue（GC）](#gameplay-cuegc)
	- [用法](#用法-1)
		- [1. 创建GC](#1-创建gc)
		- [2. GC蓝图设置](#2-gc蓝图设置)
		- [3. GC的使用](#3-gc的使用)
- [GAS的调试方法](#gas的调试方法)
	- [showdebug abilitysystem](#showdebug-abilitysystem)
	- [AbilitySystem.DebugBasicHUD](#abilitysystemdebugbasichud)
	- [Debug Widgets](#debug-widgets)
	- [Gameplay Debugger](#gameplay-debugger)
	- [GAS in Visual Logger](#gas-in-visual-logger)
- [参考文章](#参考文章-3)




![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# Ability System Component（ASC）

ASC本质是一个 `UActorComponent`，用于处理 GAS框架下的 交互逻辑：

- 使用技能（GamePlayAbility）
- 属性设置（AttributeSet）
- 效果处理（GamePlayEffect）

任何需要使用GAS的Actor对象，都必须拥有ASC组件（可将其放到Actor或PlayerState上）

ASC所附着的Actor称为 ASC的`OwnerActor`，ASC实际作用的Actor称为 ASC的`AvatarActor`

## 用法-基本配置

首先确保项目启用了GAS插件，后即可配置ASC组件：例如 在目标Actor上添加ASC组件，并继承实现`IAbilitySystemInterface`接口

```c++
// AGASSampleCharacter.h
class AGASSampleCharacter : public ACharacter, public IAbilitySystemInterface
{
    // 申明ASC
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = GameplayAbilities, meta = (AllowPrivateAccess = "true"))
	class UAbilitySystemComponent* AbilitySystem;
    
    // 实现IAbilitySystemInterface接口
	UAbilitySystemComponent* GetAbilitySystemComponent() const override;
}
```

```c++
// AGASSampleCharacter.cpp
AGASSampleCharacter::AGASSampleCharacter()
{
	// 实例化ASC
	AbilitySystem = CreateDefaultSubobject<UAbilitySystemComponent>(TEXT("AbilitySystem"));
}

UAbilitySystemComponent* AGASSampleCharacter::GetAbilitySystemComponent() const
{
    // 实现IAbilitySystemInterface接口，返回ASC实例
	return AbilitySystem;
}
```

![image-20250628172718541](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250628172718541.png)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)

# Gameplay Tags

`FGameplayTags` 是一种 **树状层级标签**，用于表示GAS内的 **某一种或某一类 状态/效果**

其基本结构例如 `Parent.Child.GrandChild`，其每一层都是一种标签，可粗略可细化，替代了常规的 `Bool`、`Enum`等多重嵌套、结合的结构

GameplayTags的管理位于 项目设置->项目->GameplayTags 内，对应配置亦被保存在 DefaultGameplayTags.ini 文件内

![image-20250629154748694](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250629154748694.png)

## Tags的定义

Tags可以通过如下方式进行定义、修改：

- [直接 项目设置->项目->GameplayTags 内手动进行修改](https://dev.epicgames.com/documentation/en-us/unreal-engine/using-gameplay-tags-in-unreal-engine?application_version=5.4#addingtagsinprojectsettings)
- [从 DataTable 导入](https://dev.epicgames.com/documentation/en-us/unreal-engine/using-gameplay-tags-in-unreal-engine?application_version=5.4#importingtagsfromdatatableassets)
- [C++内定义](https://dev.epicgames.com/documentation/en-us/unreal-engine/using-gameplay-tags-in-unreal-engine?application_version=5.4#definingtagswithc++)

### 从DataTable导入Tags

使用数据表进行Tags导入，更方便 重复利用、项目移植。基本用法为：

在 项目设置->项目->GameplayTags->Gameplay标签表列表（Gameplay Tag Table List）内 创建、指定数据表（行类型为 `GameplayTagTableRow`）

![image-20250807144218835](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250807144218835.png)

后即可进行Tags配置，配置后生效的Tags就能在 Gameplay标签列表 内看到

![image-20250807144326996](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250807144326996.png)

## Tags的规划

因Tags可能会用于不同的业务模块（例如 技能功能、数值计算、特效表现、冷却等），因此需要Tags的规划尽量 分层合理、维护成本低、可移植性好

### ARPG类的Tags规划

以 [ActionRPG - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/action-rpg-game?application_version=4.27) 为例，适用于ARPG类游戏的Tags规划可例如：

```
Ability（能力类型：使用物品、技能等）
|   |──Item
|   |──Jump
|   |──Sprint
|   |──Melee（普攻）
|   |	|──Close
|   |	└──Far
|   └──Skill（技能）
|   	└──Skill_A
|
Cooldown（技能冷却：存在即代表技能处于冷却状态）
|   └──Skill
|   	└──Skill_A
|
Event（事件）
|   └──Montage 
|        |──Player
|        |   |──Combo
|        |   |──BurstPound
|        |   |──ChestKick
|        |   |──FrontalAttack
|        |   |──GroundPound
|        |   └──JumpSlam
|        └──Shared
|            |──UseItem
|            |──UseSkill
|            └──WeaponHit
|
GameplayCue（表现处理：特效、音效、震动等）
|   |──Player
|   |	└──Sprint
|   └──Shared
|   	└──Stun
|
Status（状态）
    |──Dead
    |──Sprinting
    |──DamageImmune
    └──Debuff
        └──Stun
```

### 参考文章

- [UE4 Gameplay Tags怎么划分合理？ - 知乎](https://www.zhihu.com/question/377026014/answer/1068575405)
- [GASDocumentation - Github](https://github.com/tranek/GASDocumentation)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# Gameplay Ability（GA）

`UGameplayAbility` 表示 **一段可被 激活触发的 游戏逻辑**

GA可用于表示 攻击、被攻击、技能等，甚至于 角色跳跃、物品交互等

![image-20250629164241509](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250629164241509.png)

## 用法

### 1. 创建GA

以从 `UGamePlayAbility` 派生GA蓝图为例，GA蓝图的基本结构如下：

![image-20250629171246560](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250629171246560.png)

GA的职责：

- 生命周期事件：

  - `事件ActivateAbility`：成功激活此GA后调用，无传入参数。适用于 任意激活途径（`TryActivateAbilityByClass`或`SendGameplayEventToActor`）

  - `事件ActivateAbilityFromEvent`：在未使用 `事件ActivateAbility` 且 通过 `SendGameplayEventToActor`形式 成功激活此GA后调用，携带传入了`Event Data`。源码逻辑位于 `void UGameplayAbility::ActivateAbility`

  - `事件OnEndAbility`：GA结束事件
  - `EndAbility`：结束GA的方法
  - `CommitAbility`：执行应用Cost、Cooldown（如果GA有配置Cost或Cooldown、且使用了 `事件ActivateAbility` 或 `事件ActivateAbilityFromEvent` 则必须主动调用一次，源码逻辑位于 `void UGameplayAbility::ActivateAbility`）

- 配置 GameplayTags、Cost、CoolDowns等属性

- 业务逻辑：视觉表现、应用GE等

### 2. 添加GA到ASC

常规方法是 在C++层 把GA添加到ASC组件内、初始化ASC，则后续ASC就可使用此GA

```c++
// AGASSampleCharacter.h
class AGASSampleCharacter : public ACharacter, public IAbilitySystemInterface
{
    // ASC
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = GameplayAbilities, meta = (AllowPrivateAccess = "true"))
	class UAbilitySystemComponent* AbilitySystem;

    // 声明Ability数组
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Abilities)
	TArray<TSubclassOf<UGameplayAbility>> MyAbilities;
}
```

```c++
// AGASSampleCharacter.cpp
void AGASSampleCharacter::BeginPlay()
{
	Super::BeginPlay();

	if (nullptr != AbilitySystem)
	{
		// 把GA添加到ASC
		if (MyAbilities.Num() > 0)
		{
			for (auto i = 0; i < MyAbilities.Num(); i++)
			{
				if (MyAbilities[i] == nullptr)
					continue;

				AbilitySystem->GiveAbility(FGameplayAbilitySpec(MyAbilities[i].GetDefaultObject(), 1, 0));
			}
		}

		// 初始化ASC
		AbilitySystem->InitAbilityActorInfo(this, this);
	}
}
```

![image-20250629172722366](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250629172722366.png)

### 3.1 GA的激活

激活某个GA的方式有：

A. 主动调用ASC组件提供的 `TryActivateAbilityByClass`、`TryActivateAbilitiesByTag` 方法

B. GA自身配置Trigger条件，当ASC收到Trigger后将触发激活 其拥有的、满足Trigger条件的 GA。常用方法是 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor`

![image-20250629174728912](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250629174728912.png)

### 3-2. GA的激活 - Input触发

GA的激活，还可以通过绑定Input输入进行触发，触发将直接进入GA的 `事件ActivateAbility`。Input方式则可以用 **常规Input输入** 或 **EnhancedInput增强输入**

#### 常规Input输入以激活GA

以 [GASDocumentation - Github](https://github.com/tranek/GASDocumentation#462-binding-input-to-the-asc) 为例子，其基本用法为：

1. 项目设置内添加操作映射

![image-20250815221500561](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250815221500561.png)

2. 声明 Ability对应触发的InputID的 枚举列表（注意 枚举名和上面的操作映射名字 必须一致）

```c++
// GASDocumentation.h
UENUM(BlueprintType)
enum class EGDAbilityInputID : uint8
{
	// 0 None
	None			UMETA(DisplayName = "None"),
	// 1 Confirm
	Confirm			UMETA(DisplayName = "Confirm"),
	// 2 Cancel
	Cancel			UMETA(DisplayName = "Cancel"),
	// 3 LMB
	Ability1		UMETA(DisplayName = "Ability1"),
	// 4 RMB
	Ability2		UMETA(DisplayName = "Ability2"),
	// 5 Q
	Ability3		UMETA(DisplayName = "Ability3"),
	// 6 E
	Ability4		UMETA(DisplayName = "Ability4"),
	// 7 R
	Ability5		UMETA(DisplayName = "Ability5"),
	// 8 Sprint
	Sprint			UMETA(DisplayName = "Sprint"),
	// 9 Jump
	Jump			UMETA(DisplayName = "Jump")
};
```
后 建立 `UGameplayAbility` 和上述 `EGDAbilityInputID` 的关联。示例为 从 `UGameplayerAbility` 派生子类，添加成员`AbilityInputID : EGDAbilityInputID`，则各功能GA 就可唯一对应各自的 `EGDAbilityInputID`

```c++
UCLASS()
class GASDOCUMENTATION_API UGDGameplayAbility : public UGameplayAbility
{
	GENERATED_BODY()
	
public:
	UGDGameplayAbility();

	// Abilities with this set will automatically activate when the input is pressed
	UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Ability")
	EGDAbilityInputID AbilityInputID = EGDAbilityInputID::None;

	UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Ability")
	EGDAbilityInputID AbilityID = EGDAbilityInputID::None;
};
```

![image-20250815222837342](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250815222837342.png)


3. 运行态时 对 ASC组件执行 绑定Input激活Ability方法：`UGDAbilitySystemComponent::BindAbilityActivationToInputComponent(UInputComponent* InputComponent, FGameplayAbilityInputBinds BindInfo)`。这使得 `EGDAbilityInputID` 枚举内的InputID在触发时 可响应到ASC组件内

```c++
// AGDHeroCharacter.cpp
void AGDHeroCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
	Super::SetupPlayerInputComponent(PlayerInputComponent);

	// Bind player input to the AbilitySystemComponent.
	BindASCInput();
}

void AGDHeroCharacter::BindASCInput()
{
	if (!ASCInputBound && AbilitySystemComponent.IsValid() && IsValid(InputComponent))
	{
		FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
		AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
			FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));

		ASCInputBound = true;
	}
}
```

4. 运行态时 在添加GA时 绑定激活此GA的InputID，以最终实现 触发InputAction->相应到ASC->ASC激活符合绑定的GA 整个流程

```c++
// AGDCharacterBase.cpp
void AGDCharacterBase::AddCharacterAbilities()
{
	for (TSubclassOf<UGDGameplayAbility>& StartupAbility : CharacterAbilities)
	{
		int32 AbilityInputID = static_cast<int32>(StartupAbility.GetDefaultObject()->AbilityInputID);
		int32 AbilityLevel = GetAbilityLevel(StartupAbility.GetDefaultObject()->AbilityID);

		FGameplayAbilitySpec GameplayAbilitySpec = FGameplayAbilitySpec(StartupAbility, AbilityLevel, AbilityInputID, this);

		AbilitySystemComponent->GiveAbility(GameplayAbilitySpec);
	}
}

// GameplayAbilityTypes.cpp
FGameplayAbilitySpec::FGameplayAbilitySpec(UGameplayAbility* InAbility, int32 InLevel, int32 InInputID, UObject* InSourceObject)
	: Ability(InAbility)
	, Level(InLevel)
	, InputID(InInputID)
	, SourceObject(InSourceObject)
	, ActiveCount(0)
	, InputPressed(false)
	, RemoveAfterActivation(false)
	, PendingRemove(false)
	, bActivateOnce(false) 
{
	Handle.GenerateNewHandle();
}
```

#### EnhancedInput增强输入以激活GA

1. 按常规EnhancedInput用法创建IMC、IA、接入使用
2. 复用上例1、2内的 `EGDAbilityInputID`枚举 和 自行派生的`UGDGameplayAbility : UGameplayAbility`。后需新创建 `EGDAbilityInputID` 与 `UInputAction` 的映射关系，例如 创建一个 `TMap<EGDAbilityInputID, TObjectPtr<UInputAction>> AbilityEnumToAction` 

```c++
// AGDCharacterBase.cpp
class GASDOCUMENTATION_API AGDCharacterBase : public ACharacter, public IAbilitySystemInterface
{
    UPROPERTY(BlueprintReadWrite, EditAnywhere, Category = "GASDocumentation|Abilities")
	TMap<EGDAbilityInputID, TObjectPtr<UInputAction>> AbilityEnumToAction;

	virtual TMap<EGDAbilityInputID, TObjectPtr<UInputAction>> GetAbilityEnumMap();
}
```

![image-20250815230413771](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250815230413771.png)

3. 重写改造 ASC组件执行 绑定Input激活Ability方法：`UGDAbilitySystemComponent::BindAbilityActivationToInputComponent(UInputComponent* InputComponent, FGameplayAbilityInputBinds BindInfo)` 。因为其内部绑定逻辑是针对 常规Input输入，即 `UInputComponent`，而我们则是需要对 `UEnhancedInputComponent` 进行绑定

```c++
// UGDAbilitySystemComponent.cpp
void UGDAbilitySystemComponent::BindAbilityActivationToInputComponent(UInputComponent* InputComponent, FGameplayAbilityInputBinds BindInfo)
{
	Super::BindAbilityActivationToInputComponent(InputComponent, BindInfo);

    AGDCharacterBase* Character = Cast<AGDCharacterBase>(GetAvatarActor());
	if(!Character) { return; }
    
    // 换为处理 EnhancedInput
	UEnhancedInputComponent* EnhancedInputComponent = Cast<UEnhancedInputComponent>(InputComponent);
	if(EnhancedInputComponent)
	{
        // 获取 InputID与InputAction的映射表
        TMap<EGDAbilityInputID, TObjectPtr<UInputAction>> CurrentMap = Character->GetAbilityEnumMap();
        
		UEnum* EnumBinds = BindInfo.GetBindEnum();
		SetBlockAbilityBindingsArray(BindInfo);
		for(int32 idx=0; idx < EnumBinds->NumEnums(); ++idx)
		{
			UInputAction* InputAction = CurrentMap.FindRef(static_cast<EGDAbilityInputID>(idx));
			if(InputAction)
			{
				// Pressed event
				{
					EnhancedInputComponent->BindAction(InputAction, ETriggerEvent::Started,this, &UAbilitySystemComponent::AbilityLocalInputPressed, idx);
				}

				// Released event
				{
					EnhancedInputComponent->BindAction(InputAction, ETriggerEvent::Completed,this, &UAbilitySystemComponent::AbilityLocalInputReleased, idx);
				}
			}
		}
	}
}

// AbilitySystemComponent_Abilities.cpp
void UAbilitySystemComponent::BindAbilityActivationToInputComponent(UInputComponent* InputComponent, FGameplayAbilityInputBinds BindInfo)
{
	UEnum* EnumBinds = BindInfo.GetBindEnum();

	SetBlockAbilityBindingsArray(BindInfo);

	for(int32 idx=0; idx < EnumBinds->NumEnums(); ++idx)
	{
		const FString FullStr = EnumBinds->GetNameStringByIndex(idx);
		
		// Pressed event
		{
			FInputActionBinding AB(FName(*FullStr), IE_Pressed);
			AB.ActionDelegate.GetDelegateForManualSet().BindUObject(this, &UAbilitySystemComponent::AbilityLocalInputPressed, idx);
			InputComponent->AddActionBinding(AB);
		}

		// Released event
		{
			FInputActionBinding AB(FName(*FullStr), IE_Released);
			AB.ActionDelegate.GetDelegateForManualSet().BindUObject(this, &UAbilitySystemComponent::AbilityLocalInputReleased, idx);
			InputComponent->AddActionBinding(AB);
		}
	}

	// Bind Confirm/Cancel. Note: these have to come last!
	if (BindInfo.ConfirmTargetCommand.IsEmpty() == false)
	{
		FInputActionBinding AB(FName(*BindInfo.ConfirmTargetCommand), IE_Pressed);
		AB.ActionDelegate.GetDelegateForManualSet().BindUObject(this, &UAbilitySystemComponent::LocalInputConfirm);
		InputComponent->AddActionBinding(AB);
	}
	
	if (BindInfo.CancelTargetCommand.IsEmpty() == false)
	{
		FInputActionBinding AB(FName(*BindInfo.CancelTargetCommand), IE_Pressed);
		AB.ActionDelegate.GetDelegateForManualSet().BindUObject(this, &UAbilitySystemComponent::LocalInputCancel);
		InputComponent->AddActionBinding(AB);
	}

	if (BindInfo.CancelTargetInputID >= 0)
	{
		GenericCancelInputID = BindInfo.CancelTargetInputID;
	}
	if (BindInfo.ConfirmTargetInputID >= 0)
	{
		GenericConfirmInputID = BindInfo.ConfirmTargetInputID;
	}
}
```

4. 同上例步骤4：运行态时 在添加GA时 绑定激活此GA的InputID

#### 参考文章

- [GASDocumentation - Github](https://github.com/tranek/GASDocumentation)
- [UE5中GAS接入增强输入(EnhancedInput)](https://mytechplayer.com/archives/ue5中gas接入增强输入enhancedinput)



## GA细节面板 - Tags标签

GA上可配置系列 GameplayTags，使其进行 预设的逻辑处理

![image-20250701160404864](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250701160404864.png)

|           名称            |                             描述                             |                        备注                        |
| :-----------------------: | :----------------------------------------------------------: | :------------------------------------------------: |
|       Ability Tags        |              此GA 的 Tags（即 描述GA用的标签）               |                                                    |
| Cancel Abilities with Tag |      当此GA激活时 打断其他所有 拥有配置AbilityTags的GA       |           此类Tag是 某个GA的AbilityTags            |
| Block Abilities with Tag  | 当此GA激活时 其他所有 拥有配置AbilityTags的GA 禁止激活（已激活了的不会被打断） |           此类Tag是 某个GA的AbilityTags            |
|   Activation Owned Tags   | 当此GA激活时 GA的 `AvatarActor` 的ASC 将获取 配置的Tags，反之 当GA结束时 将对应移除 | 此类Tag是附属于ASC上的，而不是 某个GA的AbilityTags |
| Activation Required Tags  | 当GA的 `AvatarActor` 的ASC上 拥有全部 配置的Tags，则此GA才允许被激活 | 此类Tag是附属于ASC上的，而不是 某个GA的AbilityTags |
|  Activation Blocked Tags  | 若此GA的 `AvatarActor` 的ASC上 拥有任意个 配置的Tags，则此GA禁止激活 | 此类Tag是附属于ASC上的，而不是 某个GA的AbilityTags |
|   Source Required Tags    | 当`FGameplayEventData.InstigatorTags` 内 包含全部 配置的Tags，则此GA才允许被激活 |                                                    |
|    Source Blocked Tags    | 当`FGameplayEventData.InstigatorTags` 内 拥有任意个 配置的Tags，则此GA禁止激活 |                                                    |
|   Target Required Tags    | 当`FGameplayEventData.TargetTags` 内 包含全部 配置的Tags，则此GA才允许被激活 |                                                    |
|    Target Blocked Tags    | 当`FGameplayEventData.TargetTags` 内 拥有任意个 配置的Tags，则此GA禁止激活 |                                                    |

### 关于 Source XXX Tags、Target XXX Tags

当我们采用 **[GameplayEvent的形式激活GA](https://dev.epicgames.com/documentation/en-us/unreal-engine/using-gameplay-abilities-in-unreal-engine?application_version=5.4#triggeringwithgameplayevents)**（例如调用方法 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`，其中的形参 `FGameplayEventData Payload` 其内的下列2成员参数，即对应 Source XXX Tags、Target XXX Tags

```c++
/** Metadata for a tag-based Gameplay Event, that can activate other abilities or run ability-specific logic */
USTRUCT(BlueprintType)
struct GAMEPLAYABILITIES_API FGameplayEventData
{
    // ...
    
	/** Tags that the instigator has */
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = GameplayAbilityTriggerPayload)
	FGameplayTagContainer InstigatorTags;

    /** Tags that the target has */
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = GameplayAbilityTriggerPayload)
	FGameplayTagContainer TargetTags;
    
    // ...
}
```

而当 **非GameplayEvent的形式激活GA的情况**，例如常用的 `UAbilitySystemComponent::TryActivateAbilityByClass()` 其内部流程中 未创建使过`FGameplayEventData`类型的数据，因此这类方法 **会跳过对 Source XXX Tags、Target XXX Tags 的判断**，核心代码位于：

```c++
//AbilitySystemComponent_Abilities.cpp
bool UAbilitySystemComponent::InternalTryActivateAbility(FGameplayAbilitySpecHandle Handle, FPredictionKey InPredictionKey, UGameplayAbility** OutInstancedAbility, FOnGameplayAbilityEnded::FDelegate* OnGameplayAbilityEndedDelegate, const FGameplayEventData* TriggerEventData)
{
    // Core ...
}
```

一个例子：

1. 已有一个目标GA：TargetBlockedTags=`Ability.Test.A`，其带有GameplayEvent的Trigger（TriggerTag=`Ability.Test.C`）
2. 现通过 GameplayEvent的形式 尝试激活目标GA：激活的EventTag对应为`Ability.Test.C`，其将传递 `FGameplayEventData Payload` 参数（其TargetTags配置为`Ability.Test.B`）
3. 执行结果为 因TargetTags内配置的Tags，均不在 GA的TargetBlockedTags列表内，故 成功激活GA
4. 若将 TargetTags从`Ability.Test.B`改配置为`Ability.Test.A`，则执行结果为 因TargetTags内配置的Tags，有存在于 GA的TargetBlockedTags列表内，故 将被阻止激活GA。其余同理

![image-20250701225905311](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250701225905311.png)

总结：

- Source XXX Tags、Target XXX Tags 仅在 通过GameplayEvent激活GA 且有对应非空`Payload`配置下 才可能生效，否则GA的配置无需关心这2项目的内容

## GA细节面板 - Triggers触发器

触发器用于 配置系列触发条件、当达成触发条件后 就激活此GA

**TriggerTag**：即 触发激活此GA的 标识GameplayTag

**Triggers Source**：

- Gameplay Event：当`OwnerActor`收到 配置TriggerTag的Gameplay Event时（例如 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`）激活一次此GA
- Owned Tag Added：当`OwnerActor`获得 配置TriggerTag的时候 激活一次此GA，当`OwnerActor`失去此Tag时 不会移除此GA，需要自行移除
- Owned Tag Present：当`OwnerActor`获得 配置TriggerTag的时候 激活一次此GA，当`OwnerActor`失去此Tag时 会自动移除此GA（GA若执行未完成会被Cancel）

## GA细节面板 - Cooldowns

Cooldown 用于 限制GA的触发间隔，配置项为 GE。其本质为 Cooldown的GE激活期间 添加Tag到ASC，通过此Tag进行限制、实现冷却

Cooldown GE的配置要求：

- Duration Policy = Has Duration类型，并对应配置有效时长
- Granted Tags->Added 配置 Cooldown相关的Tag

![image-20250706175722358](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250706175722358.png)

## GA细节面板 - Costs

Costs 用于 消耗目标Attribute以激活GA，配置项为 GE

Costs GE的配置要求：

- Duration Policy = Instant
- 一个或多个Modifier

![image-20250706180450910](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250706180450910.png)

## GA - AbilityTask

[UAbilityTask](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/gameplay-ability-tasks-in-unreal-engine?application_version=5.4) 是指GA蓝图专用的 **一段异步处理逻辑**

UE已内置诸多常用节点（节点功能可参考 [AbilityTask原生类盘点 - 知乎](https://zhuanlan.zhihu.com/p/431081292)），亦可自行从 `UAbilityTask` 派生实现（可参考 [UE5 GAS RPG创建自定义的Ability Task - CSDN](https://blog.csdn.net/qq_30100043/article/details/137859611)

![image-20250702113549438](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250702113549438.png)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# Gameplay Attributes

`FGameplayAttributeData ` 即 **Attribute**，是 **描述 一项属性 的结构体**，用于表示游戏的某项数值属性（例如 HP、MP等）。其核心成员为：

- `float BaseValue`：基础值。例如 升级后MP永久性的提升
- `float CurrentValue`：当前值。较常用。例如 使用技能会消耗MP，MP也可用药品恢复或随时间自动恢复等

`UAttributeSet` 即 **AS**，负责 **定义和持有 Attribute**，并 **管理Attribute的变化**，包括网络同步。挂载到ASC组件上

## AS用法

### 1. 创建Attribute及AS

先从 `UAttributeSet` 派生出所需AS，头部添加 [Defining Attributes](https://github.com/tranek/GASDocumentation/tree/master?tab=readme-ov-file#concepts-as-attributes)，并声明所需Attribute：

```c++
// SampleAttributeSet.h
#include "CoreMinimal.h"
#include "AttributeSet.h"
#include "AbilitySystemComponent.h"
#include "SampleAttributeSet.generated.h"

// Defining Attributes : 增加各种Getter和Setter方法的宏（必要）
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)

UCLASS()
class [PROJECTNAME]_API USampleAttributeSet : public UAttributeSet
{
	GENERATED_BODY()

public:
	USampleAttributeSet();

public:
	UPROPERTY(BlueprintReadOnly, Category = "Health")
	FGameplayAttributeData Health;
	ATTRIBUTE_ACCESSORS(USampleAttributeSet, Health);

	UPROPERTY(BlueprintReadOnly, Category = "Health")
	FGameplayAttributeData MaxHealth;
	ATTRIBUTE_ACCESSORS(USampleAttributeSet, MaxHealth)

public:
	// 属性修改前回调
	virtual void PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue) override;

	// GE执行后属性回调
	virtual void PostGameplayEffectExecute(const FGameplayEffectModCallbackData& Data) override;
};
```

```c++
// SampleAttributeSet.cpp
void USampleAttributeSet::PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)
{
    // CurrentValue 被改变前调用
	if (Attribute == GetHealthAttribute())
	{
        // NewValue的边界处理
		NewValue = FMath::Clamp(NewValue, 0.f, GetMaxHealth());
	}
}

void USampleAttributeSet::PostGameplayEffectExecute(const FGameplayEffectModCallbackData& Data)
{
	// 注意：仅在instant GameplayEffect使Attribute的 BaseValue改变时触发
	Super::PostGameplayEffectExecute(Data);

    if (Data.EvaluatedData.Attribute == GetHealthAttribute())
    {
        SetHealth(FMath::Clamp(GetHealth(), 0.0f, GetMaxHealth()));
    }
}
```

若想单纯 **监听 Attribute的变化**，则可以用 ASC注册监听（常用于 通知表现层等外部业务）：

```c++
// AGASSampleCharacter.cpp
void AGASSampleCharacter::BeginPlay()
{
	Super::BeginPlay();

	if (nullptr != AbilitySystem)
	{
        // ASC 初始化
        
        // 向ASC注册Attribute变化事件
		AbilitySystem->GetGameplayAttributeValueChangeDelegate(USampleAttributeSet::GetHealthAttribute()).AddUObject(this, &AGASSampleCharacter::OnHealthChanged);
	}
}

void AGASSampleCharacter::OnHealthChanged(const FOnAttributeChangeData& Data)
{
    // 获取最新值 Data.NewValue
	// 自行Notify
}
```

后需要建立 AttributeSet 与 ASC的联系：即让**ASC注册持有AS**。方法为：在与ASC同一OwnerActor上的 构造方法中 就创建AttributeSet，则当 ASC执行 `UAbilitySystemComponent::InitializeComponent()` 时，就会自动对此AS并进行相关处理：

```c++
// AGDPlayerState.cpp
AGDPlayerState::AGDPlayerState()
{
	// 实例化ASC
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));

    // 在与ASC同OwnerActor的构造方法中创建AttributeSet，则将会在 UAbilitySystemComponent::InitializeComponent() 时被注册到ASC
	AttributeSetBase = CreateDefaultSubobject<UGDAttributeSetBase>(TEXT("AttributeSetBase"));
}

// UAbilitySystemComponent_Abilities.cpp
void UAbilitySystemComponent::InitializeComponent()
{
	Super::InitializeComponent();

	// Look for DSO AttributeSets (note we are currently requiring all attribute sets to be subobjects of the same owner. This doesn't *have* to be the case forever.
	AActor *Owner = GetOwner();
	InitAbilityActorInfo(Owner, Owner);	// Default init to our outer owner

	// cleanup any bad data that may have gotten into SpawnedAttributes
	for (int32 Idx = SpawnedAttributes.Num()-1; Idx >= 0; --Idx)
	{
		if (SpawnedAttributes[Idx] == nullptr)
		{
			SpawnedAttributes.RemoveAt(Idx);
		}
	}

	TArray<UObject*> ChildObjects;
	GetObjectsWithOuter(Owner, ChildObjects, false, RF_NoFlags, EInternalObjectFlags::Garbage);

	for (UObject* Obj : ChildObjects)
	{
		UAttributeSet* Set = Cast<UAttributeSet>(Obj);
		if (Set)  
		{
			SpawnedAttributes.AddUnique(Set);
		}
	}

	SetSpawnedAttributesListDirty();
}
```

### 2. AS的初始化

方法1（不推荐）：为目标AS配置对应的 DataTable，挂载到ASC上的Default Starting Table项：

![image-20250709195207309](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250709195207309.png)

方法2（官方推荐）：新建GE专用于初始化目标AS，运行态时自行Apply以进行初始化：

![image-20250709195748391](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250709195748391.png)

参考文章：

- [【UE】记录GAS中AttributeSet初始化流程 - 知乎](https://zhuanlan.zhihu.com/p/687390022)
- [How should I put attribute sets on an actor? - UnrealEngineDev](https://dev.epicgames.com/community/learning/tutorials/DPpd/unreal-engine-gameplay-ability-system-best-practices-for-setup#howshouldiputattributesetsonanactor?)

### 3. AS的使用

![image-20250703174705572](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250703174705572.png)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# Gameplay Effect（GE）

`UGameplayEffect` 即 GE，是**Ability对自己或他人产生影响的途径**。可理解为游戏内的 一段伤害、一个Buff等 **一项游戏效果**，其注重于 效果的结果表现，而不是 效果的实现流程（这是GA的职责），因此其 **不可添加逻辑、相当于一个配置各类效果的配置表**

![image-20250704204529992](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704204529992.png)

## GE的功能简介

在 UE 5.3 版本后，GE稍有改动，改动内容可参阅 [UE5-GAS插件UE5.3改动 - 知乎](https://zhuanlan.zhihu.com/p/657035214)

### 修改Attribute

通过配置GE细节面板->GameplayEffect->Modifier，可修改Attribute：

![image-20250704174546020](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704174546020.png)

### 赋予GameplayTag

当此GE Apply成功时，会赋予目标ASC 配置的GameplayTag：

![image-20250704180912409](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704180912409.png)

### 赋予GA

当此GE Apply成功时，会赋予目标ASC 配置的GA：

- Removal Policy：GE移除时 目标GA的处理

  - Cancel Ability Immediately：移除，并触发事件EndAbility

  - Remove Ability on End：移除，但是不触发EndAbility

  - Do Nothing：GA不会被移除

![image-20250704181210460](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704181210460.png)

### 嵌套应用GE

当此GE Apply成功时，Apply配置的GE：

![image-20250704174447542](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704174447542.png)

当此GE 的Duration被打断或结束时，Apply配置的GE：

（备注：适用情况为 DurationPolicy=HasDuration）

![image-20250704180213438](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704180213438.png)

当此GE 的Stacking溢出时，Apply配置的GE：

（备注：Stacking溢出仅适用于 DurationPolicy=Infinite/HasDuration）

![image-20250704175840562](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704175840562.png)

### 调用GC

当此GE Apply成功时，触发配置的GC：

![image-20250704181859613](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704181859613.png)

## GE功能 - 修改Attribute

作为GE的核心功能，修改Attribute的功能主要由 GE->GameplayEffect页签->Modifiers、Executions实现

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250817003752686.png)

### Modifiers

一项 Modifiers 可修改一项 Attribute

- Attribute：要修改的Attribute
- Modifier Op：运算符。包含 加、乘、除、覆盖。配合 Magnitude值 对Attribute进行修改
- Modifier Magnitude：运算值。也就是 将要和 Attribute进行 Modifier Op运算的值
  - Scalable Float：直接使用 浮点数 作为Magnitude值
  - Attribute Based：使用 从Source或Target上 快照捕获的某个Attribute 作为Magnitude值
  - Custom Calculation Class：使用 自定义计算类的返值 作为Magnitude值。可自行实现复杂计算逻辑、捕获所需的Attribute
  - Set By Caller：使用 蓝图Caller的传值 作为Magnitude值

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250816225154917.png)

#### Attribute Based

核心是 从目标对象上获取Attribute 作为Magnitude值 以供 ModifierOp 进行最终运算

例如：配置实现了 扣除目标身上的HealthAttribute值（值为 目标身上的 当前HealthAttribute值），GE执行表现为 目标Health扣光

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250817210511175.png)

#### Custom Calculation Class

核心是 从[`UGameplayModMagnitudeCalculation`](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation) 派生子类、自行按需捕获Attribute、计算返回Magnitude值 以供 ModifierOp 进行最终运算

例如：实现了 `UMMC_Test : UGameplayModMagnitudeCalculation`，GE的执行表现为 扣除Source身上当前HealthAttribute值的一半

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250817002729020.png)

```c++
// UMMC_Test.h
UCLASS()
class [PROJECTNAME]_API UMMC_Test : public UGameplayModMagnitudeCalculation
{
	GENERATED_BODY()

public:
	UMMC_Test();

	virtual float CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec& Spec) const override;

private:
	// 声明捕获属性
	FGameplayEffectAttributeCaptureDefinition CaptureHealthDef;
};
```

```c++
// UMMC_Test.cpp

// 构造函数内 初始化捕获信息
UMMC_Test::UMMC_Test()
{
	// 设置 需要捕获的Attribute 及捕获设置参数
	CaptureHealthDef.AttributeToCapture = UGDAttributeSetBase::GetHealthAttribute();
	CaptureHealthDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Source;
	CaptureHealthDef.bSnapshot = false;		// true = 捕获ApplyGE时刻的值, false = 捕获每次计算时刻的值

	// 添加到捕获列表
	RelevantAttributesToCapture.Add(CaptureHealthDef);
}

// Magnitude计算逻辑
float UMMC_Test::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec& Spec) const
{
	// 获取 捕获值
	FAggregatorEvaluateParameters EvaluateParams;
	float CaptureHealthValue = 0.0f;
	GetCapturedAttributeMagnitude(CaptureHealthDef, Spec, EvaluateParams, CaptureHealthValue);

    // 返回计算完成的 Magnitude值
	return CaptureHealthValue / 2.0f;
}
```

#### Set By Caller

核心为 蓝图函数（`AssignTagSetByCallerMagnitude`）传入自定义Magnitude值，并以 DataTag（GameplayTag）或DataName（FName） 为唯一标识，GE在Apply时刻 根据此唯一标识获取到 Magnitude值 以供 ModifierOp 进行最终运算

例如：使用DataTag=Data.Damage、Magnitude=-50，GE的执行表现为 扣除目标身上当前HealthAttribute值（50）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250817212113127.png)

### Executions

相比于 Modifier，Executions则更为灵活：可自定义计算逻辑和Attribute修改

#### Execution Calculation Class

与 Custom Calculation Class方法类似，Execution Calculation Class也可以 捕获Attribute、自行计算Magnitude值。其从 [`UGameplayEffectExecutionCalculation`](https://dev.epicgames.com/documentation/en-us/unreal-engine/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-) 派生计算类

优点：

- 多Attribute修改应用：Execution Calculation Class 可直接计算Magnitude值并应用修改到Attribute，而不再是 计算Magnitude值并返回 以供ModifierOp 进行最终运算
- 可从传入参数（`ExecutionParams`）内获取到source、target双方诸多信息（ASC、Actor、Tags...）
- 快照值预修改：可使用 CalculationModifiers 对快照Attribute值 预先进行ModifierOp修改、才传递给业务层

缺点：

- 不可预测性，仅能在C++层实现
- 不支持 未设置Period的Infinite
- 不会触发属性的 `PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)`，需要自行实现属性限制

例如：实现了 `UTestExecutionCalculation : UGameplayEffectExecutionCalculation`，GE的执行表现为 扣除身上当前HealthAttribute值（MaxHealthAttribute/2）、并扣除了ManaAttribute值（10）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250817231043917.png)

```c++
// UTestExecutionCalculation.h
UCLASS()
class [PROJECTNAME]_API UTestExecutionCalculation : public UGameplayEffectExecutionCalculation
{
	GENERATED_BODY()

public:
	UTestExecutionCalculation();

	virtual void Execute_Implementation(const FGameplayEffectCustomExecutionParameters& ExecutionParams, FGameplayEffectCustomExecutionOutput& OutExecutionOutput) const override;
};
```

```c++
// UTestExecutionCalculation.cpp

struct STestCaptureDefStatics
{
	DECLARE_ATTRIBUTE_CAPTUREDEF(Health)
	DECLARE_ATTRIBUTE_CAPTUREDEF(MaxHealth)

	STestCaptureDefStatics()
	{
		// 设置 需要捕获的Attribute、捕获源、是否快照
		DEFINE_ATTRIBUTE_CAPTUREDEF(UGDAttributeSetBase, Health, Target, false);
		DEFINE_ATTRIBUTE_CAPTUREDEF(UGDAttributeSetBase, MaxHealth, Target, true);
	}
};

static const STestCaptureDefStatics& TestCaptureDefStatics()
{
	static STestCaptureDefStatics DStatics;
	return DStatics;
}

// 构造函数内 初始化捕获信息
UTestExecutionCalculation::UTestExecutionCalculation()
{
	// 添加到捕获列表
	RelevantAttributesToCapture.Add(TestCaptureDefStatics().HealthDef);
	RelevantAttributesToCapture.Add(TestCaptureDefStatics().MaxHealthDef);
}

// 计算处理逻辑
void UTestExecutionCalculation::Execute_Implementation(const FGameplayEffectCustomExecutionParameters& ExecutionParams, FGameplayEffectCustomExecutionOutput& OutExecutionOutput) const
{
	// 获取 source、target相关信息
	UAbilitySystemComponent* TargetAbilitySystem = ExecutionParams.GetTargetAbilitySystemComponent();
	UAbilitySystemComponent* SourceAbilitySystem = ExecutionParams.GetSourceAbilitySystemComponent();

	AActor* TargetActor = TargetAbilitySystem ? TargetAbilitySystem->GetAvatarActor() : nullptr;
	AActor* SourceActor = SourceAbilitySystem ? SourceAbilitySystem->GetAvatarActor() : nullptr;

	const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();

	const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();
	const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();

	FAggregatorEvaluateParameters EvaluationParameters;
	EvaluationParameters.SourceTags = SourceTags;
	EvaluationParameters.TargetTags = TargetTags;


	// 获取 捕获值
	float HealthVal = 0.0f;
	ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(TestCaptureDefStatics().HealthDef, EvaluationParameters, HealthVal);
	float MaxHealthVal = 0.0f;
	ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(TestCaptureDefStatics().MaxHealthDef, EvaluationParameters, MaxHealthVal);

	// 传入计算后的Magnitude值，生成一个Modifier，以达到修改Attribute的效果
	float MagnitudeVal_Health = -MaxHealthVal;
	auto HealthMod = FGameplayModifierEvaluatedData(TestCaptureDefStatics().HealthProperty, EGameplayModOp::Additive, MagnitudeVal_Health);
	OutExecutionOutput.AddOutputModifier(HealthMod);

	// 同理，可修改其他Attribute
	float MagnitudeVal_Mana = -10;
	auto ManaMod = FGameplayModifierEvaluatedData(UGDAttributeSetBase::GetManaAttribute(), EGameplayModOp::Additive, MagnitudeVal_Mana);
	OutExecutionOutput.AddOutputModifier(ManaMod);
}
```

### 参考文章

- [GASDocumentation - Github](https://github.com/tranek/GASDocumentation)
- [UE GAS框架中GameplayEffect/Attribute Based Modifier详解 - CSDN](https://blog.csdn.net/m0_45371381/article/details/146031577)
- [虚幻四Gameplay Ability System入门(7)-Gameplay Effect详解(2)自定义Calculation Class - 知乎](https://zhuanlan.zhihu.com/p/368112930)
- [UE5 GAS RPG使用MMC根据等级设置血量和蓝量（下） - CSDN](https://blog.csdn.net/qq_30100043/article/details/136884265)
- [UE5 GAS RPG 使用Execution Calculations处理对目标造成的最终伤害 - CSDN](https://blog.csdn.net/qq_30100043/article/details/138673957)
- [UE5 Gameplay Ability System(GAS) 简单记录 - cnblog](https://www.cnblogs.com/rkexy/p/17961311)
- [GAS GE 自定义计算过程 - Zerol](https://kisspread.github.io/notes/GAS/6GE_Calculation.html)

## GE细节面板

### GE细节面板 - Duration

**Duration Policy**：描述GE的 持续类型

| Duration Policy  |                         描述                          |                 修改对象                  | Period | 应用示例 |
| :--------------: | :---------------------------------------------------: | :---------------------------------------: | :----: | :------: |
|   **Instant**    |                立即触发、立即自动结束                 |                 BaseValue                 | 不可用 | 常规扣血 |
|   **Infinite**   |                立即触发、人为手动结束                 | 无Period=CurrentValue；有Period=BaseValue |  可用  | 长按奔跑 |
| **Has Duration** | 立即触发、到期自动结束（持续时间=Duration Magnitude） | 无Period=CurrentValue；有Period=BaseValue |  可用  | 临时Buff |

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250820160625227.png)

### GE细节面板 - Period

**Period**：设置GE的触发周期（仅 Infinite、Has Duration 可用），时间单位为 秒

**Execute Periodic Effect on Application**：GE首次触发、开始计时Time=0时 是否就触发一次

**Periodic Inhibition Policy**：GE被阻断后的处理方式

- Never Reset：从被打断时的位置 继续执行
- Reset Period：从0开始计算周期
- Execute and Reset Period：打断时立即执行一次，下次从0开始计算周期

|      Period配置       |                   表现效果                    |                  备注                   |
| :-------------------: | :-------------------------------------------: | :-------------------------------------: |
|   Infinite + Period   |         GE以 每Period秒 永远触发下去          | 相当于 以每Period秒 执行一次Instant触发 |
| Has Duration + Period | GE在 DurationMagnitude秒内 以 每Period秒 触发 |                                         |

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250820161047369.png)

## GE的使用

从 `UGameplayEffect` 即可派生出GE蓝图，后就可 **从ASC 或 GA 内Apply指定类型的GE**：

![ASC内应用GE](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704204038864.png)

![GA内应用GE](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250704204158308.png)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# Gameplay Cue（GC）

GameplayCue（GC）常用于处理 GAS系统内 **非游戏流程逻辑相关的业务**，例如 粒子特效、音效、震动等 **交互反馈表现**

目前GC分为2类：

- `UGameplayCueNotify_Static`：适用于 一次性播放的特效。其不会在Level内生成实例。对应Instant和Periodic的GE
- `AGameplayCueNotify_Actor`：适用于 持久性/不定时长的特效。每次触发会在Level内生成对应实例。对应Infinity和Has Duration的GE

## 用法

### 1. 创建GC

打开GameplayCue编辑器（UE5位于 工具栏内），为所需GC创建一个Tag，后点击 处理器->新增，就显示为此Tag创建GC的蓝图的界面：

![image-20250705113603476](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250705113603476.png)

### 2. GC蓝图设置

若是 Static类型的GC，则需要在蓝图内 重载`OnExecute`方法，并对 类默认值->细节面板->Gameplay Cue进行相关设置：

![image-20250705174816951](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250705174816951.png)

若是 Actor类型的GC，则需要在蓝图内 重载`OnActive`、`OnRemove`方法，并对 细节面板->Gameplay Cue、Clean Up 进行相关设置：

![image-20250705171754483](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250705171754483.png)

### 3. GC的使用

可通过GE触发 目标Gameplay Cue Tags 的GC：

![image-20250705172452344](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250705172452344.png)

也可在GA内调用触发GC：

![image-20250705173205714](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/image-20250705173205714.png)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# GAS的调试方法

## [showdebug abilitysystem](https://dev.epicgames.com/community/learning/tutorials/Y477/unreal-engine-gameplay-ability-system-debugging-tools#showdebugabilitysystem)

运行中通过`~`键打开控制台，输入 `showdebug abilitysystem` 即可显示当前控制的Character的GAS相关数据（PageUp、PageDown切换查看对象）

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250818202442845.png)

## [AbilitySystem.DebugBasicHUD](https://dev.epicgames.com/community/learning/tutorials/Y477/unreal-engine-gameplay-ability-system-debugging-tools#abilitysystemdebugbasichud)

运行中通过`~`键打开控制台，输入 `AbilitySystem.DebugBasicHUD` 即可显示，主要内容有：当前AttributeSet的信息、Attribute的监听对象、当前GE信息

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250818213114903.png)

## [Debug Widgets](https://dev.epicgames.com/community/learning/tutorials/Y477/unreal-engine-gameplay-ability-system-debugging-tools#debugwidgets)

运行中通过`~`键打开控制台，输入下列指令，即能可视化查看特定数据：

|                         commands                         |                             描述                             |                           使用示例                           |
| :------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| `AbilitySystem.DebugAttribute [Attribute1] [Attribute2]` | 在所有带有 目标Attribute的Actor上，绘制目标Attribute的数据信息 | `AbilitySystem.DebugAttribute Health MaxHealth WeaponDamage` |
|      `AbilitySystem.DebugAbilityTags [Tag1] [Tag2]`      | 在所有带有 目标GamePlayTag的Actor，绘制带有Tag及层数信息（无Tag就不绘制） | 显示=`AbilitySystem.DebugAbilityTags Damage.Conditions.DamageImmune`；清除=`AbilitySystem.DebugAbilityTags` |
|           `AbilitySystem.ClearDebugAttributes`           |       清除 `AbilitySystem.DebugAttribute` 所绘制的信息       |                                                              |

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250819203922424.png)

## [Gameplay Debugger](https://dev.epicgames.com/community/learning/tutorials/Y477/unreal-engine-gameplay-ability-system-debugging-tools#gasingameplaydebugger)

UE内置的调试器，能在 运行态时 可视化查看 目标Actor上的 AI、BehaviorTree、Abilities、EQS、Perception相关的数据

在Outliner大纲视图内，选中想要查看的Actor，回到Game中 点击键盘`'`键，即可显示窗口。通过快捷键（修改快捷键在 项目设置->引擎->Gameplay调试器）切换页签到 3.Abilities 后，即可查看GAS相关数据：

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250818201841408.png)

## [GAS in Visual Logger](https://dev.epicgames.com/community/learning/tutorials/Y477/unreal-engine-gameplay-ability-system-debugging-tools#gasinvisuallogger)

可视化记录器（Visual Logger），位于 工具->调试->可视化记录器。可将AbilitySystem相关日志 以时间轴形式展现：
![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20250819203951301.png)



![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/CrossLine_01.png)



# 参考文章

- [Gameplay技能系统 - UnrealEngine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/gameplay-ability-system-for-unreal-engine?application_version=5.4)
- [Your First 60 Minutes with Gameplay Ability System - UnrealEngineDev](https://dev.epicgames.com/community/learning/tutorials/8Xn9/unreal-engine-epic-for-indies-your-first-60-minutes-with-gameplay-ability-system#enablingthegameplayabilitiesplugin)
- [虚幻引擎游戏技能系统文档 - CSDN](https://blog.csdn.net/pirate310/article/details/106311256)
- [【Unreal】虚幻GAS系统快速入门 - 知乎](https://zhuanlan.zhihu.com/p/486808688)
- [UE5 GAS Base - CSDN](https://blog.csdn.net/qq_52179126/article/details/131860252)
- [UE4 GAS/ActionRPG学习导图——AttributeSet（Gameplay Ability System）- CSDN](https://blog.csdn.net/jk_chen_acmer/article/details/115309073)
- [Gameplay Ability System - Debugging Tools - UnrealEngineDev](https://dev.epicgames.com/community/learning/tutorials/Y477/unreal-engine-gameplay-ability-system-debugging-tools)
