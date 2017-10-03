# 扩展机制

## 概述

JUnit 5 提供了标准的扩展机制来允许开发人员对 JUnit 5 的功能进行增强。JUnit 5 提供了很多的标准扩展接口，第三方可以直接实现这些接口来提供自定义的行为。通过@ExtendWith 注解可以声明在测试方法和类的执行中启用相应的扩展。
扩展的启用是继承的，这既包括测试类本身的层次结构，也包括测试类中的测试方法。也就是说，测试类会继承其父类中的扩展，测试方法会继承其所在类中的扩展。除此之外，在一个测试上下文中，每一个扩展只能出现一次。


## extension扩展

扩展可以通过@ExtendWith或通过Java的ServiceLoader机制自动注册。

### 声明式注册Extension
开发人员可以通过使用@ExtendWith（...）注释测试接口，测试类，测试方法或定制组合注释来声明性地注册一个或多个扩展，并为扩展注册提供类引用。

例如，要为特定的测试方法注册自定义MockitoExtension，您将按照以下方式注解测试方法。

```java
@ExtendWith(MockitoExtension.class)
@Test
void mockTest() {
    // ...
}
```

要为特定类及其子类中的所有测试注册自定义MockitoExtension，您将按照以下方式对测试类进行注解。

```java
@ExtendWith(MockitoExtension.class)
class MockTests {
    // ...
}
```

多个扩展可以一起注册：

```java
@ExtendWith({ FooExtension.class, BarExtension.class })
class MyTestsV1 {
    // ...
}
```

也可以这样注册:

```java
@ExtendWith(FooExtension.class)
@ExtendWith(BarExtension.class)
class MyTestsV2 {
    // ...
}
```

MyTestsV1和MyTestsV2中的测试执行将按照顺序扩展为FooExtension和BarExtension。

### 自动注册Extension

除了使用注解的声明式注册Extension支持外，JUnit Jupiter还通过Java的java.util.ServiceLoader机制支持全局扩展注册，允许根据classpath中可用的内容自动检测并自动注册第三方扩展。

具体来说，可以通过在JAR文件中的/META-INF/services文件夹中的名为org.junit.jupiter.api.extension.Extension的文件中提供其全限定类名来注册自定义扩展。

#### 启用自动Extension检测

自动检测是一种高级功能，因此默认情况下不启用。 要启用它，只需将junit.jupiter.extensions.autodetection.enabled配置参数设置为true即可。 这可以作为JVM系统属性提供，作为LauncherDiscoveryRequest中传递给Launcher的配置参数，或通过JUnit Platform配置文件提供。
例如，要启用扩展的自动检测，可以使用以下系统属性启动JVM。

```java
-Djunit.jupiter.extensions.autodetection.enabled=true
```

当启用自动检测时，通过ServiceLoader机制发现的扩展将在JUnit Jupiter的全局Extendsion（例如，支持TestInfo，TestReporter等）之后添加到扩展注册表中。

### Extension继承

注册的Extension名在具有自顶向下语义的测试类层次结构中继承。 类似地，在类级别注册的扩展在方法级继承。 此外，特定的扩展实现只能在给定的扩展上下文及其父上下文中注册一次。 因此，任何注册重复扩展实现的尝试都将被忽略。

## 测试执行的条件

[ExecutionCondition](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/ExecutionCondition.html)定义了用于程序化，条件测试执行的Extension API。

对每个容器（例如，测试类）评估执行条件，以确定是否应根据提供的ExtensionContext执行其包含的所有测试。 类似地，对每个测试评估一个ExecutionCondition，以确定是否应该根据提供的ExtensionContext执行给定的测试方法。

当注册了多个ExecutionCondition扩展时，只要其中一个条件返回disabled，容器或测试就被disabled。 因此，不能保证评估条件，因为另一个扩展可能已经导致容器或测试被disabled。 换句话说，评估工作类似于短路的布尔OR运算符。

有关具体示例，请参阅[DisabledCondition](https://github.com/junit-team/junit5/blob/r5.0.0/junit-jupiter-engine/src/main/java/org/junit/jupiter/engine/extension/DisabledCondition.java)和[@Disabled](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/Disabled.html)的源代码。
> 以上都是官网的内容,看不太懂,于是乎我去看代码，翻资料。 其实就是junit 5现在支持指定在满足某些条件下才运行测试.接下来给个例子方便大家理解

写一个自定义条件,这个条件是:如果该方法被@Tag("Api")注解了就disabled掉

```java
/**
 * @author zsh 2017/10/3
 */
public class DisableAPITests implements ExecutionCondition {
    private static final ConditionEvaluationResult ENABLED = ConditionEvaluationResult.enabled(
            "@Tag is not present");

    @Override
    public ConditionEvaluationResult evaluateExecutionCondition(ExtensionContext context) {
        Optional<AnnotatedElement> element = context.getElement();
        Optional<Tag> disabled = findAnnotation(element, Tag.class);
        if (disabled.isPresent()) {
            String reason = disabled.map(Tag::value).filter(StringUtils::isNotBlank).filter(value->value.equals
                    ("Api")).orElseGet(()->element.get()+"api tag can't run");
            return ConditionEvaluationResult.disabled(reason+" can't run");
        }


        return ENABLED;
    }
}
```
使用它

```java
/**
 * @author zsh 2017/10/1
 */
@ExtendWith(DisableAPITests.class)
class PersonTest {
    @InjectMocks
    private Person person;

    @Mock
    private Age age;
    @BeforeEach
    void setUp() {
        person = new Person();
        MockitoAnnotations.initMocks(this);
    }

    @AfterEach
    void tearDown() {
        reset(age);
    }

    @Test
    @Tag("Api")
    void addAge() {
        System.out.println("111111");
        person.addAge();
        Mockito.verify(age).setA(1);
    }

    @Test
    void addAge2() {
        System.out.println("2222222");
    }
}
```

addAge()方法被disabled
输出结果如下:

```xml
Api can't run
2222222
```
### 停用条件

在某些时候,执行的测试套件，在没有某些测试条件的情况下任是有效的.例如,你希望运行注解了@Disabled的方法，以便查看他们现在是否可以正确运行.为此，只需提供一个用于junit.jupiter.conditions.deactivate配置参数的模式，以指定为当前测试运行停用（即未评估）哪些条件。 该模式可以作为JVM系统属性提供，作为LauncherDiscoveryRequest中的配置参数传递给Launcher，或通过JUnit Platform配置文件提供

例如，要停用JUnit的@Disabled条件，可以使用以下系统属性启动JVM。

```xml
-Djunit.jupiter.conditions.deactivate= org.junit。* DisabledCondition
```
> 简单来说,就是Junit 5支持将Condition禁用

#### 模式匹配语法

如果junit.jupiter.conditions.deactivate模式仅由星号（*）组成，则所有条件都将被禁用。 否则，该模式将用于匹配每个注册条件的完全限定类名（FQCN）。 图案中的任何点（。）将与FQCN中的点（。）或美元符号（$）匹配。 任何星号（*）将与FQCN中的一个或多个字符匹配。 该模式中的所有其他字符将与FQCN一对一匹配。

例子：
- *:禁用所有条件
- org.junit.*:停用org.junit基础包及其任何子包下的每个条件。
- *.MyCondition:取消类名的后缀名为MyCondition的每个条件。
- *System*：	停用其简单类名称包含system的每个条件。
- org.example.MyCondition: 停用其全限定名为org.example.MyCondition的条件。

## 后处理测试实例

通过[TestInstancePostProcessor](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/TestInstancePostProcessor.html)可以对测试实例添加后处理的逻辑，从而进一步对实例进行定制，比如可以通过依赖注入的方式来设置其中的属性，或是添加额外的初始化逻辑等。

通常的用例包括将依赖关系注入到测试实例中，在测试实例上调用自定义初始化方法等。

具体的例子请参考[MockitoExtension](https://github.com/junit-team/junit5-samples/tree/r5.0.0/junit5-mockito-extension/src/main/java/com/example/mockito/MockitoExtension.java)和[SpringExtension](https://github.com/spring-projects/spring-framework/blob/master/spring-test/src/main/java/org/springframework/test/context/junit/jupiter/SpringExtension.java)的源代码。

> 官网资料说的云里雾里.接下来用个例子帮助大家离家

定义自己的TestInstancePostProcessor
```java
import org.junit.jupiter.api.extension.ExtensionContext;
import org.junit.jupiter.api.extension.TestInstancePostProcessor;

/**
 * @author zsh 2017/10/3
 */
public class DiplayNameShow implements TestInstancePostProcessor {
    @Override
    public void postProcessTestInstance(Object testInstance, ExtensionContext context) throws Exception {
        System.out.println(context.getDisplayName());
    }
}

```
使用自己的TestInstancePostProcessor
```java
/**
 * @author zsh 2017/10/1
 */
@ExtendWith(DiplayNameShow.class)
class PersonTest {
    @InjectMocks
    private Person person;

    @Mock
    private Age age;
    @BeforeEach
    void setUp() {
        person = new Person();
        MockitoAnnotations.initMocks(this);
    }

    @AfterEach
    void tearDown() {
        reset(age);
    }

    @Test
    void addAge() {
        System.out.println("=================");
        person.addAge();
        Mockito.verify(age).setA(1);
    }


}
```
结果如下:

```console
PersonTest
=================
```
## 参数解析

ParameterResolver定义了用于在运行时动态解析参数的Extension API。

如果测试构造函数或@Test，@TestFactory，@BeforeEach，@AfterEach，@BeforeAll或@AfterAll注解的方法接受一个参数，参数必须在运行时由ParameterResolver解析。 ParameterResolver可以内置（见[TestInfoParameterResolver](https://github.com/junit-team/junit5/tree/r5.0.0/junit-jupiter-engine/src/main/java/org/junit/jupiter/engine/extension/TestInfoParameterResolver.java)）或用户注册。 一般来说，参数可以通过名称，类型，注解或其任何组合来解决。 具体的例子请参考[CustomTypeParameterResolver](https://github.com/junit-team/junit5/blob/r5.0.0/junit-jupiter-engine/src/test/java/org/junit/jupiter/engine/execution/injection/sample/CustomTypeParameterResolver.java)和[CustomAnnotationParameterResolver](https://github.com/junit-team/junit5/tree/r5.0.0/junit-jupiter-engine/src/test/java/org/junit/jupiter/engine/execution/injection/sample/CustomAnnotationParameterResolver.java)的源代码。

```java
/**
 * @author zsh 2017/10/3
 */
public class ApiResolver implements ParameterResolver {
    @Override
    public boolean supportsParameter(ParameterContext parameterContext,
            ExtensionContext extensionContext) throws ParameterResolutionException {
        return parameterContext.getParameter().getType() == String.class
                && parameterContext.getIndex() == 0;
    }

    @Override
    public Object resolveParameter(ParameterContext parameterContext,
            ExtensionContext extensionContext) throws ParameterResolutionException {
        return "Api";
    }
}

@ExtendWith(ApiResolver.class)
class PersonTest {

    @Test
    void addAge(final String api) {
        assertThat(api).isEqualTo("Api");
    }

}
```

## 测试生命周期回调

JUnit 5 提供了一系列与测试执行过程相关的回调方法，在测试执行中的不同阶段，运行自定义的逻辑。这些回调方法可以用来做一些与日志和性能分析的任务。总共有如下注解:
- [BeforeAllCallback](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/BeforeAllCallback.html)
	- [BeforeEachCallback](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/BeforeEachCallback.html)
		- [BeforeTestExecutionCallback](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/BeforeTestExecutionCallback.html)
		- [AfterTestExecutionCallback](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/AfterTestExecutionCallback.html)
    - [AfterEachCallback](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/AfterEachCallback.html)
- [AfterAllCallback](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/AfterAllCallback.html)

> 实现多个扩展API
> 扩展开发人员可以选择在单个扩展中实现任意数量的这些接口。 参考[SpringExtension](https://github.com/spring-projects/spring-framework/tree/master/spring-test/src/main/java/org/springframework/test/context/junit/jupiter/SpringExtension.java)的源代码一个具体的例子。

### 在测试方法运行之前或之后执行

BeforeTestExecutionCallback和AfterTestExecutionCallback都是扩展的定制API，分别希望添加将在执行测试方法之前和之后立即执行的行为。 因此，这些回调非常适合于定时，跟踪和类似的用例。 如果需要实现在@BeforeEach和@AfterEach方法下调用的回调，请改用BeforeEachCallback和AfterEachCallback。

以下示例显示如何使用这些回调来计算和记录测试方法的执行时间。 TimingExtension实现了BeforeTestExecutionCallback和AfterTestExecutionCallback之间的时间和日志测试执行。

```java
import java.lang.reflect.Method;
import java.util.logging.Logger;

import org.junit.jupiter.api.extension.AfterTestExecutionCallback;
import org.junit.jupiter.api.extension.BeforeTestExecutionCallback;
import org.junit.jupiter.api.extension.ExtensionContext;
import org.junit.jupiter.api.extension.ExtensionContext.Namespace;
import org.junit.jupiter.api.extension.ExtensionContext.Store;

public class TimingExtension implements BeforeTestExecutionCallback, AfterTestExecutionCallback {

    private static final Logger LOG = Logger.getLogger(TimingExtension.class.getName());

    @Override
    public void beforeTestExecution(ExtensionContext context) throws Exception {
        getStore(context).put(context.getRequiredTestMethod(), System.currentTimeMillis());
    }

    @Override
    public void afterTestExecution(ExtensionContext context) throws Exception {
        Method testMethod = context.getRequiredTestMethod();
        long start = getStore(context).remove(testMethod, long.class);
        long duration = System.currentTimeMillis() - start;

        LOG.info(() -> String.format("Method [%s] took %s ms.", testMethod.getName(), duration));
    }

    private Store getStore(ExtensionContext context) {
        return context.getStore(Namespace.create(getClass(), context));
    }

}
```

由于TimingExtensionTests类通过@ExtendWith注册TimingExtension，所以它的测试将在执行时应用这个timing。

```java
@ExtendWith(TimingExtension.class)
class TimingExtensionTests {

    @Test
    void sleep20ms() throws Exception {
        Thread.sleep(20);
    }

    @Test
    void sleep50ms() throws Exception {
        Thread.sleep(50);
    }

}
```

以下是运行TimingExtensionTests时生成的日志记录的示例。

```console
INFO: Method [sleep20ms] took 24 ms.
INFO: Method [sleep50ms] took 53 ms.
```

## 异常处理

[TestExecutionExceptionHandler]是Extension的API，希望处理在测试执行期间抛出的异常。

以下示例显示一个扩展名，它将吞并IOException的所有实例，但会重新抛出任何其他类型的异常。

```java
public class IgnoreIOExceptionExtension implements TestExecutionExceptionHandler {

    @Override
    public void handleTestExecutionException(ExtensionContext context, Throwable throwable)
            throws Throwable {

        if (throwable instanceof IOException) {
            return;
        }
        throw throwable;
    }
}
```

## 提供测试模板的调用上下文

@TestTemplate方法只能在至少一个[TestTemplateInvocationContextProvider](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/TestTemplateInvocationContextProvider.html)注册时执行。 每个这样的提供者负责提供[TestTemplateInvocationContext](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/TestTemplateInvocationContext.html)实例流。 每个上下文都可以指定自定义显示名称和仅用于下一次调用@TestTemplate方法的附加扩展名列表。

以下示例显示了如何编写测试模板以及如何注册和实现TestTemplateInvocationContextProvider。

```java
@TestTemplate
@ExtendWith(MyTestTemplateInvocationContextProvider.class)
void testTemplate(String parameter) {
    assertEquals(3, parameter.length());
}

static class MyTestTemplateInvocationContextProvider implements TestTemplateInvocationContextProvider {
    @Override
    public boolean supportsTestTemplate(ExtensionContext context) {
        return true;
    }

    @Override
    public Stream<TestTemplateInvocationContext> provideTestTemplateInvocationContexts(ExtensionContext context) {
        return Stream.of(invocationContext("foo"), invocationContext("bar"));
    }

    private TestTemplateInvocationContext invocationContext(String parameter) {
        return new TestTemplateInvocationContext() {
            @Override
            public String getDisplayName(int invocationIndex) {
                return parameter;
            }

            @Override
            public List<Extension> getAdditionalExtensions() {
                return Collections.singletonList(new ParameterResolver() {
                    @Override
                    public boolean supportsParameter(ParameterContext parameterContext,
                            ExtensionContext extensionContext) {
                        return parameterContext.getParameter().getType().equals(String.class);
                    }

                    @Override
                    public Object resolveParameter(ParameterContext parameterContext,
                            ExtensionContext extensionContext) {
                        return parameter;
                    }
                });
            }
        };
    }
}
```

在这个例子中，测试模板将被调用两次。 调用的显示名称将是调用上下文指定的“foo”和“bar”。 每个调用都会注册一个用于解析该方法参数的自定义ParameterResolver。 使用ConsoleLauncher时的输出如下。

```console
└─ testTemplate(String) ✔
   ├─ foo ✔
   └─ bar ✔
```

TestTemplateInvocationContextProvider Extendsion API主要用于实现不同类型的测试，这些测试依赖于重复调用类似于测试的方法，尽管在不同的上下文中（例如，通过不同的参数），通过不同的准备测试类实例，或多次而不修改上下文.

## 保持Extendsion状态

通常，一个Extension只被实例化一次。 所以这个问题变得相关：你如何将状态从一个调用扩展到下一个？ ExtensionContext API为此提供了一个存储（[store](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/ExtensionContext.Store.html)）。 Extendsion可以将值放入store以供以后检索。 有关使用store与方法级范围的示例，请参阅TimingExtension。 重要的是要记住，在测试执行期间存储在ExtensionContext中的值在周围的ExtensionContext中将不可用。 由于ExtensionContexts可能嵌套，因此内部上下文的范围也可能受到限制。

## 扩展中支持的实用程序
JUnit Platform Commons工件公开了一个名为org.junit.platform.commons.support的包，该包包含用于处理注解，反射和classpath扫描任务的维护实用方法。 鼓励TestEngine和Extension作者使用这些支持的方法，以便与JUnit Platform的行为保持一致。

## 用户代码和Extendsion的相对执行顺序

当执行包含一个或多个测试方法的测试类时，除了用户提供的测试和生命周期方法之外，还会调用多个扩展回调。 下图说明了用户提供的代码和扩展代码的相对顺序。

![](img/02.png)

用户提供的测试和生命周期方法以橙色显示，回调代码由蓝色显示。 灰色框表示单个测试方法的执行，并将在测试类中对每个测试方法重复执行。

下表进一步说明了用户代码和扩展代码图中的十二个步骤。

| 步骤 | 接口/注解 | 描述 |
|--------|--------|--------|
|   1     |    interface org.junit.jupiter.api.extension.BeforeAllCallback    |   执行所有容器测试之前执行的扩展代码     |
|	2	|	annotation org.junit.jupiter.api.BeforeAll	|	执行所有容器测试之前执行的用户代码	|
|	3	|	interface org.junit.jupiter.api.extension.BeforeEachCallback	|	在执行每个测试之前执行的扩展代码	|
|	4	|	annotation org.junit.jupiter.api.BeforeEach	|	执行每个测试之前执行的用户代码	|
|	5	|	interface org.junit.jupiter.api.extension.BeforeTestExecutionCallback	|	在执行测试之前执行的扩展代码	|
|	6	|	annotation org.junit.jupiter.api.Test	|	用户代码的实际测试方法	|
|	7	|	interface org.junit.jupiter.api.extension.TestExecutionExceptionHandler	|	用于处理测试期间抛出的异常的扩展代码	|
|	8	|	interface org.junit.jupiter.api.extension.AfterTestExecutionCallback	|	测试执行后立即执行扩展代码及其相应的异常处理程序	|
|	9	|	annotation org.junit.jupiter.api.AfterEach	|	执行每次测试后执行的用户代码	|
|	10	|	interface org.junit.jupiter.api.extension.AfterEachCallback	|	执行每个测试后执行的扩展代码	|
|	11	|	annotation org.junit.jupiter.api.AfterAll	|	执行每个测试后执行的扩展代码	|
|	12	|	interface org.junit.jupiter.api.extension.AfterAllCallback	|	执行所有容器测试后执行的扩展代码	|

在最简单的情况下，仅执行实际的测试方法（步骤6）; 所有其他步骤都是可选的，具体取决于用户代码的存在或相应生命周期回调的扩展支持。 有关各种生命周期回调的更多详细信息，请参阅相应的JavaDoc以获取每个注解和扩展名。