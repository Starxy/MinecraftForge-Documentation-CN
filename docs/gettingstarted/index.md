# Forge 入门

!!! 译注

    请先浏览下面的章节，遇到网络问题时请回来参考该部分。

    由于中国国内存在的不可抗力，以下提到的某些文件下载可能非常困难。并且由于 ForgeGradle 早期是将下载文件的地址硬编码写入的参考 [issue 343](https://github.com/MinecraftForge/ForgeGradle/issues/343)，故不太可能提供镜像。（不过 [5b59efa](https://github.com/MinecraftForge/ForgeGradle/commit/5b59efa4e735f70749ad2be779b780063abc10a2) 该提交似乎修复了某些问题，有能力的开发者可以看一下）。

    对于目前而言，下载时添加代理是唯一且最好的选择。

        - 对于命令行进行的操作，在相应的 gradlew task 执行时后跟相应的参数。
        - 对于 IntelliJ 用户，在 `Setting -> Build,Execution,Deployment -> Gradle` 中配置 Gradle VM options 添加相关参数。
        - 译者非 Eclipse 用户，请自行摸索或在命令行中配置完成。
        - 或者如果你所用的代理服务支持全局代理的话开启即可。

    使用到的参数， 将 [ip] 和 [port] 替代为你自己的代理服务所提供的选项：

        - 对于 Socks 代理 `-DsocksProxyHost=[ip] -DsocksProxyPort=[port]`
        - 对于 Http 代理 `-Dhttp.proxyHost=[ip] -Dhttp.proxyPort=[port]`
        - 对于 Https 代理 `-Dhttps.proxyHost=[ip] -Dhttps.proxyPort=[port]`

    对于 Gradle 本身下载缓慢的问题

        终端提示从 `services.gradle.org/distributions` 下载时可以 Ctrl + C 将终端终止。
        找到 `C:\Users\用户名\.gradle\wrapper\dists` 可以看到一个 gradle-x.x-all 目录。
        手动去 `gradle.org/releases/` 下载对应版本。放入 gradle-x.x-all 目录中重新执行终端任务。

## 从零开始进行 Mod 开发

该文档是一份指导你从零出发作出一些简单 mod 的指南。整个文档的剩余部分都是以此为目标编写的。

1. 首先在 [Forge 官网](http://files.minecraftforge.net/) 下载源码。（对于高于 1.8 的版本，下载 Mdk 文件；对于早于 1.8/1.7 的版本下载 Src 文件）
1. 该压缩文件中包含有很多文件。但是只有以下文件是 mod 开发必须的，并且在所有的 mod 项目中都可重复使用；同时你可以在该压缩文件里找到 `src` 文件夹，里面包含了一个示例 mod，可以参考该 mod 进行之后的配置。
    - `build.gradle`
    - `gradlew.bat`
    - `gradlew`
    - `gradle` 文件夹
1. 将上面列出的文件和目录移动到一个新的文件夹中，这就是你之后进行 mod 开发所在的目录。
1. 在上一步所创建的新文件夹中打开一个命令提示符，执行 `gradlew setupDecompWorkspace` 命令。这命令将会下载一系列用于反编译和构建 Minecraft 的必要文件。由于需要下载很多文件和反编译 Minecraft，这一步会花费稍长的时间。不过，只要你没有手动清空 Gradle 缓存文件夹，这些文件通常只需要下载并反编译一次，并且在日后的 mod 开发中重复使用。
1. 选择你的集成开发环境（IDE）：Forge 支持使用 Eclipse 或 IntelliJ 进行开发，但是其他开发环境不论是 Netbeans 或者 vi/emacs 都可以正常工作。
    - 对于 Eclipse 用户，你应该运行 `gradlew eclipse` - 这会额外下载一些文件用于在当前目录中构建 eclipse 项目。
    - 对于 IntelliJ 用户，只需要导入 `build.gradle` 文件即可。
1. 将项目加载到 IDE 中：
    - 对于 Eclipse 用户，在任意目录创建一个 workspace （当然，最简单的位置是该项目文件夹的上一级），之后将你的项目文件夹作为 Project 导入 ，之后的事情都会自动完成。
    - 对于 IntelliJ 用户，你只需要创建运行配置（Run/Debug Configurations）。可以通过运行 `gradlew genIntellijRuns` 来完成。

!!! note

    如果你在第四步运行 `:decompileMC` 看到以下错误

    ```
    Execution failed for task ':decompileMc'.
    GC overhead limit exceeded
    ```

    请为 gradle 分配更多内存：在 `~/.gradle/gradle.properties` 文件（该文件不存在时请手动创建）内添加 `org.gradle.jvmargs=-Xmx2G`。`~` 代表用户的 HOME 目录。对于 Windows 来说就是 `C:\Users\用户名\`

## 非控制台下的 IntelliJ IDEA 配置

这部分开始时假设你已经根据上一部分的第一步到第三部创建了项目文件夹，因此这一部分从第四步开始

4. 启动 IDEA 并且选择导入 `build.gradle` 文件，使用默认的 gradle wrapper 选项。等待该步骤完成后，你可以打开右侧的 Gradle 面板，里面会有一些 Gradle Tasks。
5. 双击运行 `forgegradle` 任务组中的`setupDecompWorkspace` 任务。等待该任务完成需要一定时间（译注：墙内用户甚至可能在下载阶段失败），并且使用不少的内存。如果该阶段失败，你可以在 IDEA 的 gradle settings 窗口的 `Gradle VM options` 选项中添加 `-Xmx3G` 或者更改全局的 gradle 配置文件来为 gradle 分配更多可用内存。
6. 下载、反编译完成后，你可以运行 `genIntellijRuns` 任务来对 run/debug 进行配置。
7. 所有这些任务完成后你可以点击位于右侧 **Gradle 面板** 的刷新按钮，这会重新同步 IDEA 项目和 Gradle 数据，来确保所有的依赖和设置都是最新的。
8. 如果你正在使用 IDEA 2016 或高与此的版本，你需要修复项目的 classpath（译注：经测试 2018 的版本不需要这一步），进入 `Edit configurations`，在 `Minecraft Client` 和 `Minecraft Server` 中，将  `Use classpath of module` 指向类似于 `<project>_main` 这样名字的模块。

如果所有的操作都正确完成，你可以在下拉列表中选择 Minecraft 运行任务，选择 Run/Debug 按钮来测试你的配置。

## 自定义 Mod 基本信息

你可以过 `build.gradle` 文件来对 mod 的文件名、版本号、等其他配置来自定义修改。

!!! important
    **千万不要** 编辑 build.gradle 文件中位于 `buildscript{}` 内的内容，该默认代码对 ForgeGradle 的正常运行至关重要。

几乎所有位于 `apply project: forge` 和 `// EDITS GO BELOW HERE` 标记下的内容都可以自定义修改或删除。

这里有一个网站介绍了如何自定义 `build.gradle` 文件，[ForgeGradle cookbook](https://forgegradle.readthedocs.org/en/latest/cookbook/)。一旦你熟悉了 mod 的配置，你可以在那里发现很多有用的东西。

### 简单的 `build.gradle` 自定义设置

建议所有项目都对以下值进行设置

- 更改构建文件的文件名 - 修改 `archivesBaseName` 的值
- 更改 maven coordinates - 修改 `group` 的值
- 更改版本号 - 修改 `version` 的值

## 构建并测试你的 Mod

1. 如果你想构建你的 mod，运行 `gradlew build` 任务。这会在 `build/libs` 目录中产生一个 `[archivesBaseName]-[version].jar` 命名的文件。这个文件可以放在一个装有 Forge 的 Minecraft 的 `mods`目录下；该文件可以用来分发。
2. 如果你想测试你的 mod，最简单的方式是使用配置项目时生成的运行设置。或者你可以运行 `gradlew runClient` 任务。这会在 `<runDir>` 的位置启动一个包含有你编写的 mod 在内的 Minecraft 客户端。可以在 [ForgeGradle cookbook](https://forgegradle.readthedocs.org/en/latest/cookbook/) 中找到有关该命令的不同配置信息。
3. 你也可以使用位于 「run/debug configurations」 内的 「Minecraft server」 配置项或运行 `gradlew runServer` 任务来启动一个专用服务器。这会启动一个带有图形界面的 Minecraft 服务端（译注：新版的 Minecraft 服务端需要同意 ELUA 协议）。

!!! note

    如果你想编写服务端 mod，强烈建议你在专用服务器环境下来测试你的 mod。
