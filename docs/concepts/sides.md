# Minecraft 中 Side 的概念

想要进行 mod 开发，理解 Side 的概念是非常重要的，Minecraft 中存在两种 side，一个为 *客户端（client）*，另一个位 *服务端（server）*。事实上关于 side 有很多误导性的概念和错误，对该概念理解错误可能会导致许多 bug，虽然不会导致游戏崩溃但是会产生一系列意想不到的影响。

## 不同 Side 的区别

当我们谈及到 「client」 或者 「server」，通常我们都能明白我们在谈论游戏的哪一部分：客户端是用来与用户交互的内容，而服务端是让用户连接来进行多人游戏的地方。

事实上，即便是有这两个概念如此清晰，还是有一些表述不清的地方。为了消除歧义，我们这里对客户端和服务端四种不同的情况进行详细讨论

- 物理客户端（Physical client）：物理客户端是整个用于运行 Minecraft 游戏的应用程序。在游戏运行过程中，所有的线程、进程、服务都是物理客户端的一部分。
- 物理服务端（Physical server）：我们常说的物理服务端通常指的是专用服务器（dedicated server）。物理服务端是用于运行 `minecraft_server.jar` 的应用程序，该程序没有可视化的 GUI。
- 逻辑服务端（Logical server）：逻辑服务端通常指的是运行游戏逻辑的地方，包括怪物生成、天气、背包更新、生命值、AI 等之类的游戏机制。逻辑服务端存在于物理服务器中，但也可以和逻辑客户端一起一同运行在一个单人游戏的物理客户端上。逻辑服务器总会运行在一个名为 `Server Thread` 的线程上。
- 逻辑客户端（Logical client）：逻辑客户端用来接收用户输入并将输入传达给逻辑服务端。除此之外，逻辑客户端还从逻辑服务端中接受数据并将这些数据转为换可以被玩家接受的画面和信息。逻辑客户端运行在 `Client Thread` 线程上，当然也存在一些其他专门用于处理音频和区块加载的线程。

## 执行特定端（Side-Specific）的操作

### `world.isRemote`

<!-- !!! 译注
    刚接触该字段的时候可能会对 `isRemote` 有所疑惑，这里可以将逻辑服务端所在的地方理解为 `Local`，这样的话该布尔值的真假就易于理解了。毕竟 mod 的逻辑总是运行在逻辑服务端里的。如果世界运行在逻辑客户端，与 mod 所在的逻辑服务端相比该值就应该为 `true` 即 Remote 了。 -->

对该布尔类型的字段检查是你之后最常用的检查 side 的方法。查询 `World` 对象的该字段可以得知当前游戏正处在的**逻辑端**。比如，如果该字段值为 `true`，当前世界运行在一个逻辑客户端上；如果该字段为 `false` ，当前世界运行在一个逻辑服务端上。并且，在物理服务端上该字段的值总是为 `false` 但是你不能假定值为 `false` 的时候意味着当前世界运行在物理服务端上，因为在物理客户端上的逻辑服务端（即单人游戏）该值也可以为 `false`。

当你需要确定是否需要运行某项游戏逻辑或机制时，请检查该值。例如，如果你想对任何破坏你的方块的玩家造成伤害，或者你想创造一个将泥土加工成钻石的机器，你需要保证 `world.isRemote` 的值为 `false` 的情况下再进行这些逻辑的处理。将游戏逻辑在逻辑客户端加载轻则造成游戏不同步（幽灵实体，不同步的状态等等），重则会导致游戏崩溃

这个检查方法应当是你优先考虑的。除了 proxy 之外你很少再需要其他之外的方法来调整 siede 的行为状态。

### `@SidedProxy`

考虑到我们在客户端和服务端都用一个通用的 jar 文件，并且物理客户端和物理服务端单独加载各自的 mod jar 文件，由此引出了一个重要的问题，怎样使用仅存在物理端方面的代码？所有在 `net.minecraft.client` 包里面的代码都只应用在物理客户端，所有在 `net.minecraft.server.dedicated` 包里的代码都只应用在物理服务端。当你写的任何类引用了一些类和方法，但是游戏运行家在该类时当前环境中不存在引用的类和方法时，会引起游戏崩溃。初学者常犯的错误是，在一个 block 或 title entity 类中调用 `Minecraft.getMinecraft().<doStuff>()` 该代码会让任何尝试加载该类的物理服务端崩溃。

所以我们应该怎样解决这个问题？幸运的是，FML 提供了一个 `@sideProxy` 注解。该注解用来指定两个类的路径，一个是 `serverSide`，一个是 `clientSide`，并且用该注解来注释一个类内的字段。当 mod 启动时，fml 会根据当前所处的**物理侧**来实例化两个类中的其中一个。

```java
@SidedProxy(clientSide="com.xray.common.proxy.ClientProxy", serverSide="com.xray.common.proxy.ServerProxy")
private static ClientProxy proxy;
```

!!! note

    理解 FML 是基于物理端来挑选相应的 proxy 类来实例化是非常重要的。一个单人游戏世界（逻辑服务端和逻辑客户端同在一个物理客户端中）会有一个位于 `clientSide` 的 proxy 类被实例化。

该方法常被用来注册渲染器（Renderers）和模型（Models），它们必须在主要的初始化方法 `preInit`、`init` 或 `postInit` 中调用。然而与图像渲染有关的方法在物理服务端上都不存在并且调用会引起崩溃。因此，我们应该将这些方法只放在客户端的 proxy 中，保证他们只在客户端中运行。

请记住，两个指定的代理必须具有可被分配到使用 `@SidedProxy` 字段的类型。一个通用的模式但不绝对的的模式是为该字段的类型指定为一个 `IProxy` 接口，并且不同的物理端添加 `ClientProxy` 和 `ServerProxy` 实现类。

### `getEffectiveSide`

当你没办法获得 `World` 对象的 `isRemote` 属性时，你可以调用 `FMLCommonHandler.getEffectiveSide()` 方法来获得代码运行所处的**逻辑端**。这个方法通过检查当前程序所处的线程的名字来*猜测*当前所处的 side。因此该方法只能够在其他方法不可用的时使用，谨记无论如何请将检查 `world.isRemote` 作为查询 side 的首要方法。

### `getSide` and `@SideOnly`

你可以通过调用 `FMLCommonHandler.getSide()` 方法来获得代码运行所在的**物理端**。由于物理端在程序启动的时候就决定下来了，因此该方法的返回值是准确的，不需要猜测。但是该方法的使用情况很少。


Annotating a method or field with the `@SideOnly` annotation indicates to the loader that the respective member should be completely stripped out of the definition not on the specified **physical** side. Usually, these are only seen when browsing through the decompiled Minecraft code, indicating methods that the Mojang obfuscator stripped out. There is little to no reason for using this annotation directly. Only use it if you are overriding a vanilla method that already has `@SideOnly` defined. In most other cases where you need to dispatch behavior based on physical sides, use `@SidedProxy` or a check on `getSide()` instead.

## 常见错误

### Reaching Across Logical Sides

Whenever you want to send information from one logical side to another, you must **always** use network packets. It is incredibly tempting, when in a single player scenario, to directly transfer data from the logical server to the logical client.

This is actually very commonly inadvertently done through static fields. Since the logical client and logical server share the same JVM in a single player scenario, both threads writing to and reading from static fields will cause all sorts of race conditions and the classical issues associated with threading.

This mistake can also be made explicitly by accessing physical client-only classes such as `Minecraft` from common code that runs or can run on the logical server. This mistake is easy to miss for beginners, who debug in a physical client. The code will work there, but will immediately crash on a physical server.
