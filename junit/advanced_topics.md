# 高级主题

## JUnit Platform Launcher API

JUnit 5的特性之一是使JUnit与其编程客户端之间的接口（构建工具和IDE）更加强大和稳定。 目的是将发现和执行测试的内部从外部的所有过滤和配置中解耦出来。

JUnit 5引入了可用于发现，过滤和执行测试的Launcher的概念。 此外，第三方测试库（如Spock，Cucumber和FitNesse）可以通过提供定制的TestEngine来插入JUnit Platform的Launcher基础架构。

Launcher API位于[junit-platform-launcher](http://junit.org/junit5/docs/current/api/org/junit/platform/launcher/package-summary.html)模块中。

Launcher API的一个示例消费者是[junit-platform-console](http://junit.org/junit5/docs/current/api/org/junit/platform/console/package-summary.html)项目中的[ConsoleLauncher](http://junit.org/junit5/docs/current/api/org/junit/platform/console/ConsoleLauncher.html)。

### 测试发现

引入测试发现作为平台本身的专用功能（希望）可以免费使用IDE和构建工具，以解决过去遇到的大多数困难，以确定测试类和测试方法。

```java
import static org.junit.platform.engine.discovery.ClassNameFilter.includeClassNamePatterns;
import static org.junit.platform.engine.discovery.DiscoverySelectors.selectClass;
import static org.junit.platform.engine.discovery.DiscoverySelectors.selectPackage;

import org.junit.platform.launcher.Launcher;
import org.junit.platform.launcher.LauncherDiscoveryRequest;
import org.junit.platform.launcher.TestExecutionListener;
import org.junit.platform.launcher.TestPlan;
import org.junit.platform.launcher.core.LauncherDiscoveryRequestBuilder;
import org.junit.platform.launcher.core.LauncherFactory;
import org.junit.platform.launcher.listeners.SummaryGeneratingListener;
```

```java
LauncherDiscoveryRequest request = LauncherDiscoveryRequestBuilder.request()
    .selectors(
        selectPackage("com.example.mytests"),
        selectClass(MyTestClass.class)
    )
    .filters(
        includeClassNamePatterns(".*Tests")
    )
    .build();

Launcher launcher = LauncherFactory.create();

TestPlan testPlan = launcher.discover(request);
```

目前可以选择类，方法和包中的所有类，甚至可以搜索classpath中的所有测试。 发现发生在所有参与测试引擎。

所得到的TestPlan是适用于LauncherDiscoveryRequest的所有引擎，类和测试方法的分层（和只读）描述。 客户端可以遍历树，检索关于节点的细节，并获取到原始源的链接（如类，方法或文件位置）。 测试计划中的每个节点都有一个唯一的ID，可以用于调用特定的测试或一组测试

## 执行测试

要执行测试，客户端可以使用与发现阶段相同的LauncherDiscoveryRequest或创建新的请求。 可以通过使用Launcher注册一个或多个TestExecutionListener实现来实现进度和报告，如以下示例所示。

```java
LauncherDiscoveryRequest request = LauncherDiscoveryRequestBuilder.request()
    .selectors(
        selectPackage("com.example.mytests"),
        selectClass(MyTestClass.class)
    )
    .filters(
        includeClassNamePatterns(".*Tests")
    )
    .build();

Launcher launcher = LauncherFactory.create();

// Register a listener of your choice
TestExecutionListener listener = new SummaryGeneratingListener();
launcher.registerTestExecutionListeners(listener);

launcher.execute(request);
```

execute（）方法没有返回值，但是您可以轻松地使用监听器将最终结果聚合到您自己的对象中。 有关示例，请参阅[SummaryGeneratingListener](http://junit.org/junit5/docs/current/api/org/junit/platform/launcher/listeners/SummaryGeneratingListener.html)。

### 插入自己的测试引擎

JUnit目前提供了两个TestEngine实现：

- junit-jupiter-engine: JUnit jupiter的核心。
- junit-vintage-engine：在JUnit 4之上的一个薄层，允许使用launcher基础架构运行复古测试。

第三方也可以通过在junit-platform-engine模块中实现接口并注册引擎来贡献自己的TestEngine。 目前通过Java的java.util.ServiceLoader机制支持引擎注册。 例如，junit-jupiter引擎模块将其org.junit.jupiter.engine.JupiterTestEngine注册到junit-jupiter-engine JAR中的/ META-INF / services内的名为org.junit.platform.engine.TestEngine的文件中。

### 插入自己的测试执行侦听器

除了以编程方式注册测试执行监听器的公共Launcher API方法外，通过Java的java.util.ServiceLoader工具在运行时发现的自定义TestExecutionListener实现也会自动向DefaultLauncher注册。 例如，一个实现TestExecutionListener并在/META-INF/services/org.junit.platform.launcher.TestExecutionListener文件中声明的example.TestInfoPrinter类被自动加载和注册。
