JMH是用于构建，运行和分析以Java和其他针对JVM的语言编写的nano / micro / milli /macro 基准的Java工具。

## 基本注意事项

运行JMH基准测试的推荐方法是使用[Maven][1]来搭建依赖于应用程序的jar文件的独立项目。这种方法是首选，以确保基准正确初始化并产生可靠的结果。可以在现有项目中运行基准测试，甚至可以在IDE中运行基准测试，但是设置比较复杂，结果也不太可靠。

在所有情况下，使用JMH的关键是使注解或字节码处理器能够生成合成基准代码。Maven原型是用于启用此功能的主要机制。我们强烈建议新用户使用原型来设置正确的环境。

## 首选用法：命令行

- **搭建基准项目。** 以下命令将在测试 文件夹中生成新的JMH驱动项目：
```
$ mvn archetype：generate \
          -DinteractiveMode = false \
          -DarchetypeGroupId = org.openjdk.jmh \
          -DarchetypeArtifactId = jmh - java -benchmark-archetype \
          -DgroupId = org.sample \
          -DartifactId = test \
          -Dversion = 1.0
```
如果要对另一个JVM语言进行基准测试，请从[现有列表][2]中使用另一个原型工件ID，通常相当于将**java**替换为下面给出的工件ID中的另一种语言。使用替代原型可能需要对构建配置进行其他更改，请参阅生成的项目中`pom.xml`的内容。

- **构建基准。** 项目生成后，您可以使用以下Maven命令来构建它：
```
$ cd test /
$ mvn clean install
```
- **运行基准。** 完成构建之后，您将获得包含可执行文件JAR（包含您的基准）和所有必需的JMH基础结构代码：
```
$ java -jar target / benchmarks.jar
```
使用 `-h` 运行以查看可用的命令行选项。

在处理大型项目时，通常将基准保持在一个单独的子项目中，然后依赖于通过构建依赖关系的测试模块。

## IDE支持

虽然命令行方法是建议的，有些人更喜欢使用IDE。用户体验在不同的IDE之间有所不同，但我们将在这里概述一般的内容。IDE环境通常不受控制，因此通常不推荐在IDE中运行基准测试。

- **搭建基准项目。** 一些IDE提供GUI，从给定的原型创建Maven项目。确保您的IDE知道中央原型目录，并在那里查找 `org.openjdk.jmh：jmh - $ {lang} -benchmark-archetype`。或者，您可以使用命令行生成基准项目，请参见上文。  
**注：** JMH并不打算像典型的测试库（如JUnit）那样使用。只需将`jmh-corejar`文件添加到您的构建中不足以运行基准测试。


- **构建基准。**
大多数IDE可以打开/导入Maven项目，并从Maven项目配置推断构建配置。IDEA和Netbeans能够毫不费力地构建JMH基准测试项目。Eclipse构建配置可能需要设置JMH注解处理器才能运行。


- **运行基准。** 在IDE中没有直接支持JMH基准测试，但可以使用JMH Java API来调用基准测试。它通常相当于有main 方法，然后调用JMH。有关此方法的示例请参见 [JMH示例][3]。在运行任何基准测试之前，需要构建项目。大多数IDE会自动执行此操作，但有些操作需要在运行之前添加明确的构建操作：添加Maven目标“install”应该有帮助。


## 其他构建系统

我们不提供其他构建系统的构建脚本，但是有一些社区支持的对Gradle，sbt和其他构建器的绑定，请参阅下面的**链接**部分。如果要使用替代构建系统构建，可以引用 [Ant示例][4]，该示例描述使用Ant构建JMH基准项目的步骤。



## 建立“前沿的”JMH

在某些情况下，您需要包含修补程序，功能和API的前沿代码，在发布的版本中尚不可用。本节介绍如何在您的项目中获得“前沿的”JMH。

1. 检查JMH源与[Mercurial][5]：
```
$ hg clone http://hg.openjdk.java.net/code-tools/jmh/ jmh
```
2. 构建JMH。您可以选择跳过测试：
```
$ cd jmh /
$ mvn clean install -DskipTests
```
您只需要执行此步骤一次，Maven将会部署JMH到本地Maven仓库。

3. 如果您已经有了基准项目，那么就可以将JMH依赖版本更改为最新SNAPSHOT 版本（查看[父POM][6]中的实际最新版本）。如果没有，请创建JMH基准测试项目并在其中更改版本。

4. 完成！构建基准项目，并运行它：
```
$ mvn clean install
$ java -jar target / benchmarks.jar
```

## 获得支持

确保您在发布基准测试之前执行此操作，和/或请求JMH功能：

- **JMH注解Javadocs和Samples是必不可少的阅读。**  
遵循[JMH示例][7]来熟悉API，用例，弊端和构建微基准和使用JMH的陷阱。
- **您的基准测试应该经过同行评议。**  
不要以为一个好的工具会神奇地使你免于考虑基准的陷阱。我们只承诺避免它们更容易，而不是完全避免它们。

确保您在获得支持之前尝试了这些事情：

- **原型提供了黄金构建配置。**  
尝试生成干净的JMH基准测试项目并移植基准测试。当升级到较新的JMH版本时，这是很重要的，因为构建配置中的细微差异可能会归因于您所看到的故障。
- **目前的开发代码通常更精简，更好，更好。**  
尝试运行前沿的JMH看看问题是否已经修复。
- **看看你的问题已经被讨论过了。**  
查看邮件列表档案查看是否已经有答案。

如果所有这些都没有帮助，欢迎您来到[JMH邮件列表][8]。



## 链接

- [JMH示例][7]
- [JMH源代码库][9]（包括更新日志）
- [JMH邮件列表][8]


## 相关项目

这些项目由社区支持，而不是由OpenJDK / JMH开发人员支持。

- [Gradle JMH插件][10]
- [Scala SBT JMH插件][11]
- [IntelliJ IDEA JMH插件][12]
- [Jenkins JMH插件][13]
- [Teamcity JMH插件][14]

http://openjdk.java.net/projects/code-tools/jmh/

[1]: http://maven.apache.org/
[2]: http://central.maven.org/maven2/org/openjdk/jmh/
[3]: http://hg.openjdk.java.net/code-tools/jmh/file/tip/jmh-samples/src/main/java/org/openjdk/jmh/samples/
[4]: http://hg.openjdk.java.net/code-tools/jmh/file/tip/jmh-ant-sample/
[5]: http://mercurial.selenic.com/
[6]: http://hg.openjdk.java.net/code-tools/jmh/file/tip/pom.xml#l33
[7]: http://hg.openjdk.java.net/code-tools/jmh/file/tip/jmh-samples/src/main/java/org/openjdk/jmh/samples/
[8]: http://mail.openjdk.java.net/mailman/listinfo/jmh-dev
[9]: http://hg.openjdk.java.net/code-tools/jmh/
[10]: https://github.com/melix/jmh-gradle-plugin
[11]:https://github.com/ktoso/sbt-jmh
[12]:https://github.com/artyushov/idea-jmh-plugin
[13]:https://github.com/brianfromoregon/jmh-plugin
[14]:https://github.com/presidentio/teamcity-plugin-jmh