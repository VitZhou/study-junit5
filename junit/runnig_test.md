# 运行测试

## IDE的支持

### IntelliJ IDEA

IntelliJ IDEA自版本2016.2开始支持在JUnit Platform上运行测试
IntelliJ IDEA版本和JUnit 5版本的对应关系

| IntelliJ IDEA 版本 | JUnit 5版本 |
|--------|--------|
|     2016.2   |    M2    |
|	2016.3.1	|	M3	|
|	2017.1.2	|	M4	|
|	2017.2.1	|	M5	|
|	2017.2.3	|	RC2	|

> IntelliJ IDEA捆绑某个版本的JUnit 5.这意味着，如果要使用更新的里release版本的Jupiter API，则执行测试可能不起作用。 一旦发布了第一个GA版本的JUnit 5，这种情况就会改善。 在此期间，请按照以下说明使用与IntelliJ IDEA捆绑在一起的较新版本的JUnit 5。

为了使用不同的JUnit 5版本，您必须手动将junit-platform-launcher，junit-jupiter-engine和junit-vintage-engine JAR添加到classpath中。

##### 添加maven 依赖

```xml
	<!--仅在idea需要运行老的junit版本时需要-->
    <dependency>
        <groupId>org.junit.platform</groupId>
        <artifactId>junit-platform-launcher</artifactId>
        <version>1.0.0</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-engine</artifactId>
        <version>5.0.0</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.junit.vintage</groupId>
        <artifactId>junit-vintage-engine</artifactId>
        <version>4.12.0</version>
        <scope>test</scope>
    </dependency>
```

### Maven

JUnit团队开发了一个非常基本的Maven Surefire  provider程序，可以通过mvn test命令运行JUnit 4和JUnit Jupiter测试。 junit5-maven-consumer项目中的pom.xml文件演示了如何使用它，并可以作为起点。

> 由于Surefire 2.20内存泄漏，junit-platform-surefire提供商目前只能与Surefire 2.19.1一起使用。

```xml
...
<build>
    <plugins>
        ...
        <plugin>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.19</version>
            <dependencies>
                <dependency>
                    <groupId>org.junit.platform</groupId>
                    <artifactId>junit-platform-surefire-provider</artifactId>
                    <version>1.0.0</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
...
```

1. 配置测试引擎
    为了让Maven Surefire运行任何测试，必须将TestEngine实现添加到runtime classpath中。

    要配置对基于JUnit Jupiter的测试的支持，请对JUnit Jupiter API配置测试依赖关系，并将JUnit Jupiter TestEngine实现添加到maven-surefire-plugin的依赖关系中，类似于以下内容。

    ```xml
    ...
    <build>
        <plugins>
            ...
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.19</version>
                <dependencies>
                    <dependency>
                        <groupId>org.junit.platform</groupId>
                        <artifactId>junit-platform-surefire-provider</artifactId>
                        <version>1.0.0</version>
                    </dependency>
                    <dependency>
                        <groupId>org.junit.jupiter</groupId>
                        <artifactId>junit-jupiter-engine</artifactId>
                        <version>5.0.0</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
    ...
    <dependencies>
        ...
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>5.0.0</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
    ...
    ```

    JUnit Platform Surefire Provider可以运行基于JUnit 4的测试，只要您配置JUnit 4上的测试依赖关系，并将JUnit Vintage TestEngine实现添加到maven-surefire-plugin的依赖关系中，类似于以下内容。

    ```xml
    ...
    <build>
        <plugins>
            ...
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.19</version>
                <dependencies>
                    <dependency>
                        <groupId>org.junit.platform</groupId>
                        <artifactId>junit-platform-surefire-provider</artifactId>
                        <version>1.0.0</version>
                    </dependency>
                    ...
                    <dependency>
                        <groupId>org.junit.vintage</groupId>
                        <artifactId>junit-vintage-engine</artifactId>
                        <version>4.12.0</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
    ...
    <dependencies>
        ...
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
    ...
    ```

1. 按tag过滤
	您可以使用以下配置属性过滤测试。

    - 要包含标签，请使用groups或includeTags

	- 要排除标记，请使用excludedGroups或excludeTags

    ```xml
    ...
    <build>
        <plugins>
            ...
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.19</version>
                <configuration>
                    <properties>
                        <includeTags>acceptance</includeTags>
                        <excludeTags>integration, regression</excludeTags>
                    </properties>
                </configuration>
                <dependencies>
                    ...
                </dependencies>
            </plugin>
        </plugins>
    </build>
    ...
    ```

1. 配置参数
	您可以通过使用configurationParameters属性,并文件中properties文件中使用java语法提供key-value对来设置配置参数来影响测试发现和执行。

    ```java
    ...
    <build>
        <plugins>
            ...
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.19</version>
                <configuration>
                    <properties>
                        <configurationParameters>
                            junit.jupiter.conditions.deactivate = *
                            junit.jupiter.extensions.autodetection.enabled = true
                            junit.jupiter.testinstance.lifecycle.default = per_class
                        </configurationParameters>
                    </properties>
                </configuration>
                <dependencies>
                    ...
                </dependencies>
            </plugin>
        </plugins>
    </build>
    ...
	```

### ConsoleLauncher

[ConsoleLauncher](http://junit.org/junit5/docs/current/api/org/junit/platform/console/ConsoleLauncher.html)是一个命令行Java应用程序，可让您从控制台启动JUnit Platform。 例如，它可以用于运行JUnit Vintage和JUnit Jupiter测试，并将测试执行结果打印到控制台。

在Maven中央存储库中的[junit-platform-console-standalone](https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone)目录下包含所有依赖的可执行jar包例如:junit-platform-console-standalone-1.0.0.jar。 您可以运行独立的ConsoleLauncher，如下所示。

java -jar junit-platform-console-standalone-1.0.0.jar < Option\>
以下是其输出的示例：
```console
├─ JUnit Vintage
│  └─ example.JUnit4Tests
│     └─ standardJUnit4Test ✔
└─ JUnit Jupiter
   ├─ StandardTests
   │  ├─ succeedingTest() ✔
   │  └─ skippedTest() ↷ for demonstration purposes
   └─ A special test case
      ├─ Custom test name containing spaces ✔
      ├─ ╯°□°）╯ ✔
      └─ 😱 ✔

Test run finished after 64 ms
[         5 containers found      ]
[         0 containers skipped    ]
[         5 containers started    ]
[         0 containers aborted    ]
[         5 containers successful ]
[         0 containers failed     ]
[         6 tests found           ]
[         1 tests skipped         ]
[         5 tests started         ]
[         0 tests aborted         ]
[         5 tests successful      ]
[         0 tests failed          ]
```

> 退出代码
如果任何容器或测试失败，ConsoleLauncher退出状态码为1。 否则退出代码为0。

#### 选项(Options)

| 选项 | 描述 |
|--------|--------|
|    -h, --help    |    显示帮助信息    |
|	--disable-ansi-colors |	在输出中禁用ANSI颜色（不受所有终端支持）。 |
|	--details <[none,flat,tree,verbose]>	|	选择执行测试时的输出详细信息模式。 使用以下之一：[none，flat，tree，verbose]。 如果选择“none”，则仅显示汇总和测试失败。 （默认：tree）|
|	--details-theme <[ascii,unicode]>	|	选择执行测试时的输出详细信息树主题。 使用以下之一：[ascii，unicode]（默认值：unicode）|
|	--class-path, --classpath, --cp <Path:path1:path2:...>	|	提供其他classpath条目 - 例如，添加引擎及其依赖关系。 可以重复此选项。|
|	--reports-dir <Path> |	将报告输出到指定的本地目录（如果目录不存在则会创建该目录）。	|
|	--scan-class-path, --scan-classpath [Path:path1:path2:...]	|	扫描classpath或显式classpath根目录。 没有参数，只扫描系统classpath上的目录以及通过-cp（目录和JAR文件）提供的其他classpath条目。 不在classpath中的显式classpath根将被默认忽略。 可以重复此选项。|
|	-u, --select-uri <URI>  |	选择测试发现的URI。 这个选项可以重复。	|
|	-f, --select-file <String>	|	Select a file for test discovery. This option can be repeated.	|
|	-d, --select-directory <String>	|	选择用于测试发现的目录。可以重复此选项。	|
|	-p, --select-package <String> 	|	选择用于测试发现的包。 可以重复此选项。	|
|	-c, --select-class <String>  |	选择用于测试发现的类,可重复	|
|	-m, --select-method <String> 	|	选择用于测试发现的方法, 可重复	|
|	-r, --select-resource <String>	|	选择一个classpath source用于测试发现,可重复	|
|	-n，--include-classname <String>	|	提供一个正则表达式，仅包括完全限定名称匹配的类。 为避免不必要地加载类，默认模式仅包括以“Test”或“Tests”结尾的类名。 当重复此选项时，所有模式将使用OR语义进行组合。 （默认值：^。*Test？$）	|
|	-N, --exclude-classname <String> 	|	提供一个正则表达式来排除其全限定名称匹配的类。 当重复此选项时，所有模式将使用OR语义进行组合。|
|	--include-package <String> 	|	提供需要在测试运行中包含的包。 可以重复此选项。	|
|	--exclude-package <String>	|	提供要从测试运行中排除的包。 可以重复此选项。	|
|	-t, --include-tag <String> 	|	提供需要在测试运行中包含的tag。 可以重复此选项	|
|	-T, --exclude-tag <String>	|	提供要从测试运行中排除的tag。 可以重复此选项。	|
|	-e, --include-engine <String>	|	提供要包括在测试运行中的引擎的ID。 可以重复此选项。	|
|	-E, --exclude-engine <String>	|	提供要从测试运行中排除的引擎的ID。 可以重复此选项。|
|	--config <key=value> 	|	设置测试发现和执行的配置参数。 可以重复此选项。	|

### 在JUnit Platform使用JUnit 4

JUnitPlatform运行程序是一个基于JUnit 4的Runner，JUnit Platform支持在JUnit 4的环境中运行任何编程模型的测试 - 例如JUnit Jupiter测试类。

使用@RunWith（JUnitPlatform.class）注解类允许它使用IDE运行，并构建支持JUnit 4但尚未直接支持JUnitPlatform的系统。

> 由于JUnitPlatform具有JUnit 4没有的功能，所以运行程序只能支持JUnit Platform功能的一个子集，特别是在报告方面。 但是现在的的JUnitPlatform runner是一个简单的入门方式。

#### 设置

您需要以下artifacts及其对classpath的依赖关系。
1. 明确依赖
	- junit-4.12.jar在test scope: 使用JUnit 4进行测试
	- junit-platform-runner在test scope: JUnitPlatform runner的范围
	- junit-jupiter-api 在test scope:	用于写测试的API，包括@Test等
	- junit-jupiter-engine 在test scope:	实现JUnit Jupiter的Engine API
1. 传递依赖关系
	- junit-platform-launcher
	- junit-platform-engine
	- junit-platform-commons
	- opentest4j

#### 显示名称与技术名称

默认情况下，显示名称将用于测试工件; 然而，当JUnitPlatform Runner用于使用诸如Gradle或Maven之类的构建工具执行测试时，生成的测试报告通常需要包括测试工件的技术名称，例如完全限定类名称，而不是较短的显示名称，例如 测试类的简单名称或包含特殊字符的自定义显示名称。 为了实现报告的技术名称，只需在@RunWith（JUnitPlatform.class）旁边声明@UseTechnicalNames注解。

#### 单个测试类

使用JUnitPlatform Runner的一种方法是直接用@RunWith（JUnitPlatform.class）注解测试类。 请注意，以下示例中的测试方法使用org.junit.jupiter.api.Test（JUnit Jupiter）注解，而不是org.junit.Test（JUnit Vintage）。 此外，在这种情况下，测试类必须是public的; 否则，IDE将不会将其识别为JUnit 4测试类。

```java
import static org.junit.jupiter.api.Assertions.fail;

import org.junit.jupiter.api.Test;
import org.junit.platform.runner.JUnitPlatform;
import org.junit.runner.RunWith;

@RunWith(JUnitPlatform.class)
public class JUnit4ClassDemo {

    @Test
    void succeedingTest() {
        /* no-op */
    }

    @Test
    void failingTest() {
        fail("Failing for failing's sake.");
    }

}
```

#### Test 套件

如果您有多个测试类，则可以创建一个测试套件，如以下示例所示。

```java
import org.junit.platform.runner.JUnitPlatform;
import org.junit.platform.suite.api.SelectPackages;
import org.junit.runner.RunWith;

@RunWith(JUnitPlatform.class)
@SelectPackages("example")
public class JUnit4SuiteDemo {
}
```

JUnit4SuiteDemo将在示例包及其子包中发现并运行所有测试。 默认情况下，它只会包含名称匹配正则表达式^。* Tests？$的测试类。
> 其他配置选项
有更多的配置选项用于发现和过滤测试，而不仅仅是@SelectPackages。 有关详细信息，请咨询[Javadoc](http://junit.org/junit5/docs/current/api/org/junit/platform/suite/api/package-summary.html)。

##### 过滤测试类

在JUnitPlatform上配置测试套件的注解。

| 注解类型 | 描述 |
|--------|--------|
|    [ExcludeClassNamePatterns](http://junit.org/junit5/docs/current/api/org/junit/platform/suite/api/ExcludeClassNamePatterns.html)    |    @ExcludeClassNamePatterns指定在JUnitPlatform上运行测试套件时用于与完全限定类名匹配的正则表达式。    |
|	[ExcludeEngines](http://junit.org/junit5/docs/current/api/org/junit/platform/suite/api/ExcludeEngines.html)	|	@ExcludeEngines指定在JUnitPlatform上运行测试套件时要排除的TestEngines的ID。	|
|	[ExcludePackages](http://junit.org/junit5/docs/current/api/org/junit/platform/suite/api/ExcludePackages.html)	|	@ExcludePackages指定在JUnitPlatform上运行测试套件时要排除的包。	|
|	[ExcludeTags](http://junit.org/junit5/docs/current/api/org/junit/platform/suite/api/ExcludeTags.html)	|	@ExcludeTags指定在JUnitPlatform上运行测试套件时要排除的标记。	|
|	[IncludeClassNamePatterns](http://junit.org/junit5/docs/current/api/org/junit/platform/suite/api/IncludeClassNamePatterns.html)	|	@IncludeClassNamePatterns指定在JUnitPlatform上运行测试套件时用于与完全限定类名匹配的正则表达式。	|
|	[IncludeEngines](http://junit.org/junit5/docs/current/api/org/junit/platform/suite/api/IncludeEngines.html)	|	@IncludeEngines指定在JUnit平台上运行测试套件时要包括的TestEngines的ID。	|
|	[IncludePackages](http://junit.org/junit5/docs/current/api/org/junit/platform/suite/api/IncludePackages.html)	|	@IncludePackages指定在JUnitPlatform上运行测试套件时要包括的包。	|
|	[IncludeTags](http://junit.org/junit5/docs/current/api/org/junit/platform/suite/api/IncludeTags.html)	|	@IncludeTags指定在JUnitPlatform上运行测试套件时要包含的标签。	|
|	[SelectClasses](http://junit.org/junit5/docs/current/api/org/junit/platform/suite/api/SelectClasses.html)	|	@SelectClasses指定在JUnitPlatform上运行测试套件时要选择的类。	|
|	[SelectPackages](http://junit.org/junit5/docs/current/api/org/junit/platform/suite/api/SelectPackages.html)	|	@SelectPackages指定在JUnitPlatform上运行测试套件时要选择的包的名称。	|
|	[UseTechnicalNames](http://junit.org/junit5/docs/current/api/org/junit/platform/suite/api/UseTechnicalNames.html)	|	@UseTechnicalNames指定在JUnitPlatform上运行测试套件时应使用技术名称而不是显示名称。	|

### 配置参数

除了指示测试类和测试引擎的平台需要包含哪些包进行扫描之外，有时还需要提供特定于特定测试引擎的其他定制配置参数。 例如，JUnit Jupiter TestEngine支持以下用例的配置参数。

- 更改默认测试实例生命周期
- 启用自动扩展检测
- 停用条件

配置参数是基于文本的键值对，可以通过以下机制之一提供给在JUnitPlatform运行的测试引擎。

1. LauncherDiscoveryRequestBuilder中的configurationParameter（）和configurationParameters（）方法，用于构建提供给Launcher API的请求。 当通过JUnit Platform提供的一个工具运行测试时，您可以指定配置参数如下：
	- Maven Surefire provider:使用configurationParameters属性
	- Gradle插件:使用configurationParameter或者configurationParameters DSL
	- Console Launcher: 使用--config命令行选项
1. JVM系统属性
1. JUnitPlatform配置文件：在classpath的根目录中名为junit-platform.properties的遵循Java语法规则的属性文件。
