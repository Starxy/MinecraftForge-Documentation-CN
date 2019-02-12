# 资源（Resources）

资源（Resources）是游戏中使用的外部数据，这些数据储存在数据文件夹中而不是在代码中编写。通常资源被包含在位于 classpath 目录下的 `assets` 文件夹中。默认的 mod 开发中通常位于项目的 `src/main/resources` 目录下。该文件通常包含模型，纹理材质，和用于本地化的语言文件。

当多个资源包被启用时，这些资源包会产生合并。通常来说，在栈顶的资源文件会覆盖位于他们之下的资源文件；然而对于特定的文件，比如说本地化文件，数据事实上是按照内容来进行合并的。Mod 也会定义一些位于其 `resources` 文件夹下的资源包，但是这些资源包都会被视为默认启用的 `Default` 资源包的子集，是不可以被禁用的，但是这些资源包可以被其他的资源包来重写。

所有的资源文件都应当使用『蛇形命名法』（全部小写字母用下划线连接），该命名法在 1.11 之后的版本中是强制要求的。

## `ResourceLocation`

Minecraft 使用 `ResourceLocation` 来识别资源。一个 `ResourceLocation` 包含两个部分：一个命名空间和一个路径。这通常指向 `assets/<namespace>/<ctx>/<path>`，其中 `ctx` 是与上下文有关的路径片段，取决于 `ResourceLocation` 的使用方式。当一个 `ResourceLocation` 从字符串中读或写时应当为 `<namespace>:<path>` 的格式。当用作 `ResourceLocation` 的字符串的命名空间和冒号被留空，该资源的命名空间会被指向默认的 `minecraft`。一个 mod 应当将它使用的资源放在以其 modid 命名的资源空间中。（例如：一个 id 为 `examplemod` 的 mod 应当将资源放在 `assets/examplemod` 中，并且指向这些文件的 `ResourceLocation` 字符串应当为 `examplemod:<path>`）。这不是一个硬性的要求，并且在一些情况下使用其他的命名空间或者多个命名空间会更好。`ResourceLocation` 在这之外也有其他应用，因为这种方式是一个标识唯一对象的好方法。