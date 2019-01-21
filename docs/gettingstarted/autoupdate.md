# Forge 更新检查器

Forge 提供了一个轻量级的可选的更新检查框架。该框架只检查 mod 是否有更新并且在存在新版本时，在游戏主菜单的 Mods 按钮上闪烁一个图标来提醒用户，并且提供相应的 mod 更新列表和相应的更新日志。该框架*不会主动进行*mod 的自动更新下载

## 起步

首先你要为 `@Mod` 注解中的 `updateJSON` 参数指定相应的值。该参数的值应该是一个指向具有特定格式的含有更新信息的 JSON 文件的有效 URL 地址。你可以将该文件托管到任何你的 mod 用户能够稳定访问到的位置，比如你自己的 web 服务器、Github 等。

## 更新 JSON 的格式

该 JSON 文件格式相对比较简单，示例如下：

```Javascript
{
  "homepage": "<homepage/download page for your mod>",
  "<mcversion>": {
    "<modversion>": "<changelog for this version>",
    // 此处应列出 Minecraft 版本和相应的你的 mod 的版本，并且一同列出相应的更新日志。
    ...
  },
  ...
  "promos": {
    "<mcversion>-latest": "<modversion>",
    // 此处应列出 Minecraft 版本和相应的你的 mod 的最新构建版本
    "<mcversion>-recommended": "<modversion>",
    // 此处应列出 Minecraft 版本和相应的你的 mod 的推荐使用的稳定版本
    ...
  }
}
```

该文件的结构是非常清晰的，但是有几点需要注意：

- `homepage` 所填写的链接应该是用户的 mod 版本落后之后所访问的
- Forge 使用一个内部的算法来判断一个 mod 的版本是落后还是超前。该算法应该能适应大部分情况下的版本命名方式。但是如果你不清楚你的命名方式能否被支持，请参见 `ComparableVersion` 类查阅。强烈建议遵守 [语义化版本规则](https://semver.org/) 来对版本进行命名
- 「更新日志字段」可以用 `\n` 来分割成多行。有些人习惯在此处简写更新信息，然后链接到一个提供完整更新信息的外部网站
- 手动对该文件进行编辑可能会非常繁琐，你可以通过配置 `build.gradle` 文件来实现自动化，当每构建一个 release 版本的时候更新该 JSON 文件。Groovy 原生支持 JSON 解析。该功能我们不提供实现，在此处留作一个练习给各位读者。

<!-- 你可以在 [Charset](https://gist.githubusercontent.com/Meow-J/fe740e287c2881d3bf2341a62a7ce770/raw/bf829cdefc84344d86d1922e2667778112b845b1/update.json) 和 [Botania Unofficial](https://gist.githubusercontent.com/Meow-J/1299068c775c2b174632534a18b65fb8/raw/42c578cf2303aa76d8900f5fdc6366122549d2a8/update.json) 参见具体的例子。

译注：原文上述两个文件来自 gist 并且已经不能正常访问。 -->

## 获取更新检查结果

你可以通过调用 `ForgeVersion.getResult(ModContainer)` 来获取 Forge 更新检查器的结果。该方法返回一个包含 `status` 字段的对象，该字段代表了版本检查的状态。

该字段可能取到的值：`FAILED`（版本检查器不能与 URL 建立连接）、`UP_TO_DATA`（目前版本等于或高于最新的稳定版本）、`OUTDATED`（存在一个新的稳定版本）、`BETA_OUTDATED`（存在一个新的测试版本）、`BETA`（目前版本等于或高于最新的测试版本）。当结果请求尚未完成的时候该状态值将会为 `PENDING`，在这种情况下你应该在稍后重试。

除此之外，返回的对象还会包含一个目标版本和定义在 `update.json` 之内的更新日志。你可以通过调用 `Loader.instance().activeModContainer()` 方法来获得你的 mod 的 `ModContainer`。 也可以通过调用 `Loader.instance().getIndexedModList().get(<modid>)` 来获得其他任何 mod 的`ModContainer`。
