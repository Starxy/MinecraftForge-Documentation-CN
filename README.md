# Minecraft Forge Documentation

这里是 Minecraft Forge **官方文档的非官方中文翻译**。由 Mkdocs 进行构建。

网站同步部署在 Github Page 和 Coding Page 服务上。二者内容上没有任何区别。请根据个人网络状态选择访问

- [Github Page 部署](https://starxy.github.io/MinecraftForge-Documentation-CN/)
- [Coding Page 部署](https://coding.starxy.cc)

英文的文档可以在这里找到: [http://mcforge.readthedocs.io/](http://mcforge.readthedocs.io/)。

原 Github 项目地址: [https://github.com/MinecraftForge/Documentation](https://github.com/MinecraftForge/Documentation)。

本文档旨在为 Forge 开发提供详细的文档。Javadocs 将不会放在这里。

本文档的目标是给予使用 Forge 开发的 mod 一个有组织的并且明确的参考资料。这并不是一个 Java 语言的教程，而且 Java 的基本概念也不应该是本文档的一部分。

本文档目前处在翻译阶段，非常欢迎提交 PR 来对翻译做出贡献。为了保证翻译工作不会产生重复，可以在开始翻译前在 issue 讨论，进行翻译章节的认领。同时也非常欢迎任何时间点对已翻译文本的建议和校对工作。

文档全文翻译完成后会进行相对于原仓库同步的更新。保证文档的时效性。

## 书写风格

> 有研究显示，打字的时候不喜欢在中文和英文之间加空格的人，感情路都走得很辛苦，有七成的比例会在 34 岁的时候跟自己不爱的人结婚，而其余三成的人最后只能把遗产留给自己的猫。毕竟爱情跟书写都需要适时地留白。By [vinta/paranoid-auto-spacing](https://github.com/vinta/pangu.js)

本项目推荐使用 VSCode 进行编写，推荐插件：

- Markdownlint
- Insert Spaces between Half/Fullwidths

中英文混排之间一定要加空格。

其他排版规则请看 [中文文案排版指北](https://github.com/mzlogin/chinese-copywriting-guidelines)。

Markdown 编写遵循 [Markdownlint Rules](https://github.com/DavidAnson/markdownlint/blob/master/doc/Rules.md)。

Markdownlint 是一个 VSCode 的插件，但是也有适用于其他编辑器的版本。

工欲善其事必先利其器，Markdownlint 虽好但也有局限性，很多时候被工具限制了自由度，我们可以对配置文件进行修改以适用于舒适的书写体验，详细的配置文件说明都在 `.vscode/setting.json` 的注释中给出。

如果有其他适用于本项目的配置，可以通过 PR 修改配置文件或 issue 提交讨论。

关于项目英文原文中不符合相关中文写作风格的，一律改正。

翻译最终面向中文用户，有适用于中国用户的额外细节可以酌情添加。

最后，仓库所有者是代码洁癖患者，请尽可能处理掉所有 Waring 信息。

## 已知的问题

- 原文档使用的 Mkdocs 模板由 Forge 官方仓库提供。已修复一些链接问题和资源获取问题。但是仍存在如字体大小和行间距不合适的问题。
- 尽可能使用原始模版，如果存在问题较多且调试和修复较为困难可以考虑更换更加适合的模板。欢迎建议。

## 授权

该仓库 Fork 自 [MinecraftForge/Documentation](https://github.com/MinecraftForge/Documentation) 原仓库采用 [MIT 协议](https://github.com/MinecraftForge/Documentation/blob/master/LICENSE)。该仓库中 `forge_theme` 内容和英文原文继承该协议。其他中文翻译均采用 [知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议](https://creativecommons.org/licenses/by-nc-sa/4.0/)[（简体中文）](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.zh) 进行许可。

## 参考

本项目基于 [MinecraftForge/Documentation](https://github.com/MinecraftForge/Documentation) 英文原文。

参考 [Krasjet/Forge-Documentation-CN](https://github.com/Krasjet/Forge-Documentation-CN) 该中文翻译仓库。该仓库最后构建与 2017 年 10 月。