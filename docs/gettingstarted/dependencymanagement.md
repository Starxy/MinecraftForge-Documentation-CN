# 依赖管理管理

Forge 对管理和加载依赖项有一定的支持。所需的库文件、甚至其他的 mod 都可以嵌入到构建之中，能够让 Forge 以兼容的方式在 mod 运行的时候提取和加载这些文件。

## Mod 仓库

Mod 仓库是一种包含许多 mod 和库文件的类似于 Maven 的仓库。在该仓库中存储的 Artifact（译注：该词的准确翻译待商榷） 由其 Maven coordinate 来标识：`groupId:artifactId:version:classifier@extention`。Classifer 和 extension 是可选的。Forge 可以对该仓库内的 mod 和库文件进行打包、管理和加载。Mod 仓库可以包含包括快照版在内的多个版本的 mod 和库文件。

当 manifest 中 `Maven-Artifact` 属性被定义时，Forge 可以对定义的该 Artifact 进行打包。该属性的值应当设置为该 Artifact 的 Maven coordinate。

该 Mod 仓库支持对 Artifacts 的快照管理。如果一个 artifact 的版本以 `-SNAPSHOT` 结尾，该 artifact 将被解析为具有最新时间戳的版本。该时间戳可以被 manifest 的 `Timestamp` 属性设置，该属性值应当是从该 epoch 开始的毫秒数。

## 依赖提取

Forge 提供了一种非常简单的在 mod 中嵌入依赖项的方式，并且能够在 mod 运行的过程中提取依赖。通过将依赖的 jar 文件放在你自己的 jar 文件中，Forge 可以将他们提取到 mod 存储库并加载这些依赖。这可以用作着色的替代方法，并且在解决依赖冲突方面有一些潜在的好处。

jar 文件包含的依赖项由 manifest 的 `ContainedDeps` 属性所定义。该属性的值应当是一个由空格分割的列表，其中包含了要提取的 jar 文件的名字。这些 jar 文件会被放在 `/META-INF/libraries/{entry}` 目录下。

Forge 会检查所包含的 jar 文件的 manifest 来确定该 jar 文件的 Maven coordinate，以便打包。如果存在一个 `/META-INF/libraries/{entry}.meta` 文件，Forge 将会读取该文件作为 manifest 来读取。该依赖会被根据 manifest 的 `Maven-Artifact` 属性值打包在本地仓库。
