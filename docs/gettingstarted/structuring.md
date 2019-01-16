# Mod 文件结构

我们会指导你组织你 mod 项目下不同的文件，并且告诉你这些文件的作用。

!!! 译注

    该部分可以参考 Mdk 中附带的 examplemod，该 mod 的文件结构和相应的配置文件内容提供了很好的示例

## 包

选择一个唯一的包名。如果你拥有一个与项目相关的 URL，你可以将其作为顶级包名。例如，如果你有 `example.com` 这个域名，那么你可以将`com.example` 作为顶级包名。

!!! important

    如果没有相应域名的所属权，不要使用该域名作为顶级包名。但是你可以使用其他任何作为包名，比如你的名字或昵称，或者用 mod 的名字来对包命名。

你需要在顶级包名（如果有）之后为你的 mod 添加一个唯一的名字，比如 `examplemod`。在这种情况下，完成的包名是 `com.example.examplemod`。

## `mcmod.info` 文件

该文件定义了你编写的 mod 的元数据（Metadata）。这里面的信息会被玩家在游戏的 Mods 菜单里看到。一个 info 文件可以用来描述多个 mod。当一个 mod 类使用 `@Mod` 注解时，会定义 `useMetadata` 属性，该属性默认为 `false`。当 `useMetadata` 为 `true` 时，`mcmod.info` 文件内定义的键值会覆盖 `@Mod` 注解中定义的键值信息。

`mcmod.info` 为 JSON 格式的文件，根元素是一个对象数组，每一个对象描述 modid。该文件应当被存储在 `src/main/resources/mcmod.info` 目录中。一个用来描述单个 mod 的 `mcmod.info` 文件描述如下：

    [{
      "modid": "examplemod",
      "name": "Example Mod",
      "description": "Lets you craft dirt into diamonds. This is a traditional mod that has existed for eons. It is ancient. The holy Notch created it. Jeb rainbowfied it. Dinnerbone made it upside down. Etc.",
      "version": "1.0.0.0",
      "mcversion": "1.10.2",
      "logoFile": "assets/examplemod/textures/logo.png",
      "url": "minecraftforge.net/",
      "updateJSON": "minecraftforge.net/versions.json",
      "authorList": ["Author"],
      "credits": "I'd like to thank my mother and father."
    }]

默认的 Gradle 会用项目的 version 来替换 `${version}`，用 Minecraft 的版本来替换 `${mcversion}`，但是该替换仅发生在 `mcmod.info` 文件中，所以你应该使用这些替代字符而不是直接将他们写出来。以下表格列出了所有可以使用的属性，其中 `required` 代表该属性没有设置默认值，并且缺失该属性配置会导致错误发生。除了所有的 `required` 属性，你也应该对 `description`，`version`，`mcversion`，`url`，和 `authorList` 属性进行定义。

|     属性 |   类型   | 默认值  | 描述 |
|-------------:|:--------:|:--------:|:------------|
|        modid |  string  | required | 所描述的 mod 的 modid，如果该 mod 没有加载，该描述将会被忽略
|         name |  string  | required | 该 mod 的一个用于显示的易于理解的名字 |
|  description |  string  |   `""`   | 一到两段有关该 mod 的描述信息|
|      version |  string  |   `""`   | mod 的版本 |
|    mcversion |  string  |   `""`   | Minecraft 的版本 |
|          url |  string  |   `""`   | mod 的主页链接 |
|    updateUrl |  string  |   `""`   | 无用的定义。现在被 `updateJSON` 取代 |
|   updateJSON |  string  |   `""`   | [version JSON](autoupdate#forge-update-checker) 的地址 |
|   authorList | [string] |   `[]`   | mod 的作者列表 |
|      credits |  string  |   `""`   | 包含任何你想提到的致谢信息 |
|     logoFile |  string  |   `""`   | mod 的 logo 地址。 该地址会从 classpath 的最顶层开始解析，所以你应该将该文件放在一个不会产生命名冲突的位置， 建议放在 assets 文件夹。 |
|  screenshots | [string] |   `[]`   | 你想在 mod 介绍页面显示的截图列表。目前尚未实现。 |
|       parent |  string  |   `""`   | 如果用到的的话，这里代指父 mod 的 modid。允许在信息页面出现另一个 mod 模块 |
| useDependencyInformation |  boolean |  `false` | 如果该属性值和 `Mod.useMetadata` 的值都为 `true`，接下来的三个依赖项都会起作用。否则的话则无效 |
| requiredMods | [string] |   `[]`   | 一个 modid 列表。如果其中有一个 mod 缺失，会引起游戏崩溃。 这一属性的值**不会影响到 mod 的加载顺序**，想要同时指定 mod 的加载顺序，请在 `dependencies` 属性中填写相同的值。 |
| dependencies | [string] |   `[]`   | 一个 modid 列表。 所有列出的 mod 都会在当前 mod**加载前**加载。该项为空则顺序不会做出改变 |
|   dependants | [string] |   `[]`   |  一个 modid 列表。所有列出的 mod 都会在当前 mod**加载后**加载。该项为空则顺序不会做出改变 |

[BuildCraft](https://gist.github.com/anonymous/05ad9a1e0220bbdc25caed89ef0a22d2) 使用了列出的大多数的属性值，可以作为一个很好的示例

## Mod 文件

我们在之前指定的包中创建一个你的 mod 名字的文件。这将作为 mod 的 *入口点*（entry point），并且包含一些特殊的标记。

### 什么是 `@Mod`

`@Mod` 是一个 Java 注解，用来对 Forge Mod Loader（FML）说明当前类为 Mod 入口。该注解包含一些有关 mod 的元数据。并且该类会接受 `@EventHandler` 事件。

这里有一个 `@Mod` 所包含的属性的表格:

|                         属性 |        类型        |    默认值     | 描述 |
|---------------------------------:|:------------------:|:--------------:|:------------|
|                            modid |       String       |    required    |一个唯一的 mod 标识。必须为小写字符。并且长度在 64 字符以内 |
|                             name |       String       |       ""       | 一个可读性较强的用来显示的名字 |
|                          version |       String       |       ""       | mod 的版本。该属性值只能为由点号分隔的数字组成。 理想情况下，该值符合 [语义化的版本控制](https://semver.org/)。 Even if `useMetadata` is set to `true`,即便是 `useMetadata` 设置为 `true`，将该值放在此处定义也是比较好的选择|
|                     dependencies |       String       |       ""       | mod 的依赖。 The specification is described in the Forge `@Mod` javadoc:<br><blockquote><p>A dependency string can start with the following four prefixes: `"before"`, `"after"`, `"required-before"`, `"required-after"`; then `":"` and the `modid`.</p><p>Optionally, a version range can be specified for the mod by adding `"@"` and then the version range.[\*](#version-ranges)</p><p>If a "required" mod is missing, or a mod exists with a version outside the specified range, the game will not start and an error screen will tell the player which versions are required.</p>
|                      useMetadata |       boolean      |      false     | 若该属性值设置为 `true`，由 `@Mod` 定义的内容会被 `mcmod.info` 内容覆盖 |
| clientSideOnly | boolean | false | If either is set to `true`, the jar will be skipped on the other side, and the mod will not load. If both are true, the game crashes. |
| serverSideOnly | boolean | false | If either is set to `true`, the jar will be skipped on the other side, and the mod will not load. If both are true, the game crashes. |
|        acceptedMinecraftVersions |       String       |       ""       | The version range of Minecraft the mod will run on.[\*](#version-ranges) An empty string will match all versions. |
|         acceptableRemoteVersions |       String       |       ""       | Specifies a remote version range that this mod will accept as valid.[\*](#version-ranges) `""` Matches the current version, and `"*"` matches all versions. |
|           acceptableSaveVersions |       String       |       ""       | A version range specifying compatible save version information.[\*](#version-ranges) If you follow an unusual version convention, use `SaveInspectionHandler` instead. |
|           certificateFingerprint |       String       |       ""       | See the tutorial on [jar signing](../concepts/jarsigning.md). |
|                      modLanguage |       String       |     "java"     | The programming language the mod is written in. Can be either `"java"` or `"scala"`. |
|               modLanguageAdapter |       String       |       ""       | Path to a language adapter for the mod. The class must have a default constructor and must implement `ILanguageAdapter`. If it doesn't, Forge will crash. If set, overrides `modLanguage`. |
|                 canBeDeactivated |       boolean      |      false     | This is not implemented, but if the mod could be deactivated (e.g. a minimap mod), this would be set to `true` and the mod would [receive](../events/intro.md#creating-an-event-handler) `FMLDeactivationEvent` to perform cleanup tasks. |
|                       guiFactory |       String       |       ""       | Path to the mod's GUI factory, if one exists. GUI factories are used to make custom config screens, and must implement `IModGuiFactory`. For an example, look at `FMLConfigGuiFactory`. |
|                       updateJSON |       String       |       ""       | URL to an update JSON file. See [Forge Update Checker](autoupdate.md) |

<a name="version-ranges" style="color: inherit; text-decoration: inherit">\* All version ranges use the [Maven Version Range Specification](https://maven.apache.org/enforcer/enforcer-rules/versionRanges.html).</a>

You can find an example mod in the [Forge src download](https://files.minecraftforge.net/).

Keeping Your Code Clean Using Sub-packages
------------------------------------------

Rather than clutter up a single class and package with everything, it is recommended you break your mod into subpackages.

A common subpackage strategy has packages for `common` and `client` code, which is code that can be run on server/client and client, respectively. Inside the `common` package would go things like Items, Blocks, and Tile Entities (which can each in turn be another subpackage). Things like GUIs and Renderers would go inside the `client` package.

!!! note

    This package style is only a suggestion, though it is a commonly used style. Feel free to use your own packaging system.

By keeping your code in clean subpackages, you can grow your mod much more organically.

Class Naming Schemes
--------------------

A common class naming scheme allows easier deciphering of what a class is, and also makes it easier for someone developing with your mod to find things.

For Example:

* An `Item` called `PowerRing` would be in an `item` package, with a class name of `ItemPowerRing`.
* A `Block` called `NotDirt` would be in a `block` package, with a class name of `BlockNotDirt`.
* Finally, a `TileEntity` for a block called `SuperChewer` would be a `tile` or `tileentity` package, with a class name of `TileSuperChewer`.

Prepending your class names with what *kind* of object they are makes it easier to figure out what a class is, or guess the class for an object.
