# Activiti

> 本文档是 [README.md](README.md) 的中文翻译，若两者有出入以英文原版为准。

[![Join Us in Gitter](https://badges.gitter.im/Activiti/Activiti7.svg)](https://gitter.im/Activiti/Activiti7?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![CI](https://github.com/Activiti/Activiti/actions/workflows/main.yml/badge.svg)](https://github.com/Activiti/Activiti/actions/workflows/main.yml)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/8035801ae94c441981f363fa99824a33)](https://www.codacy.com/gh/Activiti/Activiti?utm_source=github.com&utm_medium=referral&utm_content=Activiti/Activiti&utm_campaign=Badge_Grade)
[![ASL 2.0](https://img.shields.io/hexpm/l/plug.svg)](https://github.com/Activiti/Activiti/blob/develop/LICENSE.txt)
[![CLA](https://cla-assistant.io/readme/badge/Activiti/Activiti)](https://cla-assistant.io/Activiti/Activiti)
[![security status](https://www.meterian.io/badge/gh/Activiti/Activiti/security)](https://www.meterian.io/report/gh/Activiti/Activiti)
[![stability status](https://www.meterian.io/badge/gh/Activiti/Activiti/stability)](https://www.meterian.io/report/gh/Activiti/Activiti)
[![licensing status](https://www.meterian.io/badge/gh/Activiti/Activiti/licensing)](https://www.meterian.io/report/gh/Activiti/Activiti)

主页：<http://activiti.org>

Activiti 是一个轻量级的工作流与业务流程管理（BPM）平台，面向业务人员、开发者和系统管理员。它的核心是一个超快且坚如磐石的 Java BPMN 2 流程引擎。它是开源的，基于 Apache 许可证发布。Activiti 可以运行在任何 Java 应用中，无论是在服务器上、集群中还是云端。它与 Spring 完美集成，极其轻量，并基于简单的概念构建。

\***\*注意：我们已经把用于设计和编写项目下一个大版本的开发分支的全部内容迁移到了 master 分支。如果你想为 6.x 版本做贡献，请查看 6.x 分支。\*\***

如果你想进一步了解我们的仓库结构，可以阅读我们的 [GitBook](https://activiti.gitbooks.io/activiti-7-developers-guide/content/)。

## 预览传播（Preview Propagation）

本仓库包含一套自动化的**预览传播（Preview Propagation）**机制，用于测试与 activiti-cloud 等下游仓库的跨模块集成。当开发涉及多个模块的功能时：

1. 为你的 PR 添加 `preview` + `preview-propagation` 标签
2. 系统会自动构建一个预览版本，并在 activiti-cloud 中创建一个测试 PR
3. 在下游 PR 中测试集成效果
4. 当你的源 PR 关闭/合并时，测试 PR 会自动关闭

预览快照（`0.0.1-PR-<number>-SNAPSHOT`）和 develop 的 alpha 发布分别通过 `activiti-snapshots` 和 `activiti-releases` 发布到 Cloudsmith。在所有消费方都从 Cloudsmith 解析 Activiti 之前，CI 也会把它们发布到相同的 Nexus 仓库。Nexus 不用于解析构件。两次部署都成功后，传播才会开始。

## Maven 仓库

- 本地构建不需要任何凭据，也不需要 `settings.xml`。POM 从 Maven Central 以及公共的 Cloudsmith `activiti-releases` 和 `activiti-snapshots` 仓库解析依赖。
- CI 通过 Cloudsmith 的 `cic-ext-pull-through` 缓存解析所有构件，因此所有依赖都会经过 Cloudsmith 的供应链策略。`setup-java-maven` action 会把 [settings.xml](settings.xml) 复制到 `~/.m2/settings.xml`。其镜像配置（`mirrorOf *`）会把所有请求发送到该缓存。
- `cloudsmith-auth` action 使用 GitHub OIDC 提供临时的 `CLOUDSMITH_USERNAME` 和 `CLOUDSMITH_API_KEY` 凭据，无需静态的 Cloudsmith 密钥。请将 GitHub Actions 变量 `CLOUDSMITH_NAMESPACE` 设置为 `hyland`。运行 Maven 的作业需要 `id-token: write` 权限。
- 来自 fork 的 PR 拿不到 OIDC 令牌。它们会跳过镜像，直接从 POM 中配置的仓库解析依赖，也不会进行发布。
- 如果 Cloudsmith 策略隔离了某个依赖，CI 构建会失败，但本地构建可能通过。请让 Cloudsmith 管理员审查该包。

每次传播都会把 GitHub Actions 运行 ID（`BUILD_RUN_ID`）写入下游仓库的 `preview-build.properties`，确保即使预览快照版本没有变化，每次新的工作流运行也会产生一个新的提交。

这样既免去了手动升级版本号和清理的工作，又能确保集成变更在合并前得到验证。

## 配置 IntelliJ

- 强制语言级别为 25，以便在（意外）使用仅更高 Java 版本才有的特性时快速失败。
  - 打开 _File_ 菜单，选择 _Project Structure_ 菜单项
  - 点击 _Modules_ 列表项，对每个模块，在 _Sources_ 选项卡中，_Language level_ 下拉框应自动设置为 `25 ...`

- 避免某些资源的改动在下次运行/调试时被忽略（导致你被迫使用 mvn）
  - 打开 _File_ 菜单，选择 _Settings_ 菜单项；Mac 上则打开 _IntelliJ IDEA_ 菜单，选择 _Preferences..._ 菜单项
  - 点击 _Compiler_ 树节点，将 _Resource patterns_ 文本框改为 `!?*.java`（删除其他内容）

- 避免构建时出现 `StackOverflowError`
  - 打开 _File_ 菜单，选择 _Settings_ 菜单项；Mac 上则打开 _IntelliJ IDEA_ 菜单，选择 _Preferences..._ 菜单项
  - 点击 _Compiler_ 树节点，再点击 _Java Compiler_ 树节点，找到 _Additional command line parameters_ 文本框
  - 添加 `-J-Xss1024k`

- 推荐代码风格：使用 [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html) 配合 editorconfig
  - 从这里下载 IntelliJ 代码风格 xml：[https://google.github.io/styleguide/intellij-java-google-style.xml]
  - 打开 _File_ 菜单，选择 _Settings_ 菜单项；Mac 上则打开 _IntelliJ IDEA_ 菜单，选择 _Preferences..._ 菜单项
  - 点击 _Code Style_ 树节点，点击齿轮图标并选择 _Import scheme_，然后选择 _IntelliJ code style xml_
  - 浏览到你下载 xml 的位置并打开它。确认 GoogleStyle 是当前启用的方案。
    - 注意：IntelliJ IDEA 不会自动格式化你的代码。编码完成后需要按 Ctrl+Alt+L 组合键触发自动格式化。
  - 仓库中有一个 `.editorconfig`，其中定义了缩进、文件编码和行尾符。
  - 如果禁用它，你需要手动正确设置文件编码和空格数。
  - Eclipse 代码风格 xml：[https://google.github.io/styleguide/eclipse-java-google-style.xml]
  - Eclipse 需要安装 [editorconfig-eclipse](https://marketplace.eclipse.org/content/editorconfig-eclipse) 插件才能支持 EditorConfig 文件。

- 手动设置正确的文件编码（properties 文件除外，均为 UTF-8）和行尾符（unix）：
  - 打开 _File_ 菜单，选择 _Settings_ 菜单项；Mac 上则打开 _IntelliJ IDEA_ 菜单，选择 _Preferences..._ 菜单项
  - 点击 _Code Style_ 树节点，再点击 _General_ 树节点
    - _Line separator (for new files)_ 下拉框：`Unix`
  - 点击 _File Encodings_ 树节点
    - _Global Encoding_ 下拉框：`UTF-8`
    - _Default encoding for properties files_ 下拉框：`ISO-8859-1`
      - 注意：按照 Java `ResourceBundle` 契约的规定，普通的 i18n properties 文件必须使用 `ISO-8859-1` 编码。

- 手动设置按 Tab 键时正确的空格数：
  - 打开 _File_ 菜单，选择 _Settings_ 菜单项；Mac 上则打开 _IntelliJ IDEA_ 菜单，选择 _Preferences..._ 菜单项
  - 点击 _Code Style_ 树节点，再点击 _General_ 树节点
  - 点击 _Java_ 选项卡
    - _Use tab character_ 复选框：`off`
    - _Tab size_ 文本框：`4`
    - _Indent_ 文本框：`4`
    - _Continuation indent_ 文本框：`8`
  - 打开 _XML_ 选项卡
    - _Use tab character_ 复选框：`off`
    - _Tab size_ 文本框：`2`
    - _Indent_ 文本框：`2`
    - _Continuation indent_ 文本框：`4`

- 设置正确的文件头（不要包含 @author 或无意义的 javadoc）：
  - 打开 _File_ 菜单，选择 _Settings_ 菜单项；Mac 上则打开 _IntelliJ IDEA_ 菜单，选择 _Preferences..._ 菜单项
  - 点击 _File templates_ 树节点，切换到 _Includes_ 选项卡，选择 `File Header` 列表项
  - 删除 _@author Your Name_ 这一行。
    - 我们不接受源码文件中的 `@author` 行，见下方 FAQ。
  - 删除整段 javadoc，因为自动模板生成的内容毫无意义。

- 设置正确的许可证头
  - 打开 _File_ 菜单，选择 _Settings_ 菜单项；Mac 上则打开 _IntelliJ IDEA_ 菜单，选择 _Preferences..._ 菜单项
  - 点击 _Copyright_ 树节点，再点击 _Copyright profiles_ 树节点
    - 点击导入按钮导入 _Copyright profile_
    - 选择文件：[Alfresco_Software.xml](./ide-configuration/intellij-configuration/copyright/Alfresco_Software.xml)
  - 点击 _Copyright_ 树节点
    - _Default project copyright_ 下拉框：`Alfresco Software`

# FAQ

- 为什么你们不接受源码中的 `@author` 行？
  - 因为 Java 文件中的作者标签是维护的噩梦
    - 很大一部分是错误、不完整或不准确的。
    - 大多数时候它只包含最初的作者。许多文件已被其他作者彻底重构/扩展。
    - Git 是准确的，它才是查找正确作者的权威来源。

  - 因为作者标签助长了_代码所有权_观念，从长远来看这是有害的。
    - 如果人们在一段他们认为归别人所有的代码上工作，他们往往会：
      - 只修复分配给他们的问题，而不是所有出问题的地方
      - 在代码不能正常工作时推卸责任
      - 害怕踩到所有者的地盘。

  - 作者的贡献会得到认可：
    - 通过 [Open Hub](https://www.openhub.net/p/activiti/contributors)，那里还有统计数据
    - 在 [GitHub 网页界面](https://github.com/activiti)中。

# 开发命令

## 添加许可证头

为文件添加所需的许可证格式：

```bash
mvn license:format
```

## Checkstyle

检查你的代码风格是否遵守所有规则：

```bash
mvn checkstyle:check -DskipCheckstyle=false
```

## 站点

生成 maven 站点：

```bash
mvn clean site site:stage
```

站点将生成在：`target/staging/index.html`

## CI/CD

运行在 GH Actions 上。

要让 Dependabot 的 PR 通过 CI 验证，需要给 PR 添加 "CI" 标签。

需要设置以下 secrets：

| 名称                         | 描述                               |
| ---------------------------- | ---------------------------------- |
| BOT_GITHUB_TOKEN             | 用于在 GH 上触发其他构建的令牌     |
| BOT_GITHUB_USERNAME          | 用于发起传播 PR 的用户名           |
| SLACK_NOTIFICATION_BOT_TOKEN | 用于失败时通知 slack 的令牌        |