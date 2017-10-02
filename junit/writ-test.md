# Writing Test

## 第一个测试例子
```java
import static org.junit.jupiter.api.Assertions.assertEquals;

import org.junit.jupiter.api.Test;

class FirstJUnit5Tests {

    @Test
    void myFirstTest() {
        assertEquals(2, 1 + 1);
    }

}
```

## 注解

JUnit Jupiter支持以下注解，用于配置测试和扩展框架。
所有核心注解位于junit-jupiter-api模块中的org.junit.jupiter.api包中。

| 注解 | 描述 |
|--------|--------|
|    @Test    |    表示一个方法是一种可测试方法。 与JUnit 4的@Test注解不同，此注解不声明任何属性，因为JUnit Jupiter中的测试扩展基于自己的专用注解进行操作。 除非被覆盖，否则这些方法是继承的。    |
|	@ParameterizedTest	|	表示一个方法是参数化测试。 除非被覆盖，否则这些方法是继承的。	|
|	@RepeatedTest	|	表示方法是重复测试的测试模板。 除非被覆盖，否则这些方法是继承的。	|
|	@TestFactory	|	表示方法是动态测试的测试工厂。 除非被覆盖，否则这些方法是继承的。	|
|	@TestInstance	|	用于配置注解测试类的测试实例生命周期。 这些注释是继承的。	|
|	@TestTemplate	|	表示一种方法是用于根据注册提供者返回的调用上下文的数量而被多次调用的测试用例的模板。 除非被覆盖，否则这些方法是继承的。|
|	@DisplayName	|	为测试类或测试方法声明自定义显示名称。 此类注解不会继承。 |
|	@BeforeEach	|	表示注解方法应该在当前类中的每个@Test，@RepeatedTest，@ParameterizedTest或@TestFactory方法之前执行; 类似于JUnit 4的@Before。 除非被覆盖，否则这些方法是继承的。	|
|	@AfterEach	|	表示注解方法应该在当前类中的每个@Test，@RepeatedTest，@ParameterizedTest或@TestFactory方法之后执行; 类似于JUnit 4的@After。 除非被覆盖，否则这些方法是继承的。	|
|	@BeforeAll	|	表示注解方法应该在当前类中的所有@Test，@RepeatedTest，@ParameterizedTest和@TestFactory方法之前执行; 类似于JUnit 4的@BeforeClass。 这样的方法是继承的（除非它们被隐藏或覆盖），并且必须是静态的（除非使用“per-class”测试实例生命周期）。	|
|	@AfterAll	|	表示注解方法应该在当前类中的所有@Test，@RepeatedTest，@ParameterizedTest和@TestFactory方法之后执行; 类似于JUnit 4的@AfterClass。 这样的方法是继承的（除非它们被隐藏或覆盖），并且必须是静态的（除非使用“pre-class”测试实例生命周期）。|
|	@Nested	|	表示注解类是嵌套的非静态测试类。 @BeforeAll和@AfterAll方法不能直接在@Nested测试类中使用，除非使用“pre-class”测试实例生命周期。 此类注释不会继承。	|
|	@Tag	|	用于在类或方法级别声明过滤测试的标签; 类似于TestNG中的测试组或JUnit 4中的类别。此类注解在类级别继承，但不在方法级别继承。	|
|	@Disabled	|	用于禁用测试类或测试方法; 类似于JUnit 4的@Ignore。 此类注解不会继承。	|
|	@ExtendWith	|	用于注册自定义扩展。 这些注释是继承的。	|

用@Test，@TestTemplate，@RepeatedTest，@BeforeAll，@AfterAll，@BeforeEach或@AfterEach注解注解的方法不能有返回值。

>一些注解可能是实验性的。 有关详细信息，请参阅[实验API](http://junit.org/junit5/docs/current/user-guide/#api-evolution-experimental-apis)中的表格。

### 元注解和组合注解

JUnit Jupiter注解可以作为元注解,这意味着你可以自定义注解,而且它还会继承元注解的语义。
例如，您可以在代码库中复制和粘贴@Tag（“fast”），您可以按以下方式创建一个名为@Fast的自定义注解。 @Fast可以用作@Tag（“fast”）的替换。
```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import org.junit.jupiter.api.Tag;

@Target({ ElementType.TYPE, ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
@Tag("fast")
public @interface Fast {
}
```

## 标准测试类

```java
import static org.junit.jupiter.api.Assertions.fail;

import org.junit.jupiter.api.AfterAll;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;

class StandardTests {

    @BeforeAll
    static void initAll() {
    }

    @BeforeEach
    void init() {
    }

    @Test
    void succeedingTest() {
    }

    @Test
    void failingTest() {
        fail("a failing test");
    }

    @Test
    @Disabled("for demonstration purposes")
    void skippedTest() {
        // not executed
    }

    @AfterEach
    void tearDown() {
    }

    @AfterAll
    static void tearDownAll() {
    }

}
```

> 测试类和测试方法都不需要修饰成public

## 显示名

测试类和测试方法可以声明由test runner和测试报告显示的自定义显示名（包括空格，特殊字符甚至emojis）。

```java
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

@DisplayName("A special test case")
class DisplayNameDemo {

    @Test
    @DisplayName("Custom test name containing spaces")
    void testWithDisplayNameContainingSpaces() {
    }

    @Test
    @DisplayName("╯°□°）╯")
    void testWithDisplayNameContainingSpecialCharacters() {
    }

    @Test
    @DisplayName("😱")
    void testWithDisplayNameContainingEmoji() {
    }

}
```

## 断言

JUnit Jupiter带有许多JUnit 4类似的断言方法，并添加了一些可以很好地与Java 8 lambdas一起使用的方法。 所有JUnit Jupiter断言都是org.junit.jupiter.Assertions类中的静态方法。

```java
import static java.time.Duration.ofMillis;
import static java.time.Duration.ofMinutes;
import static org.junit.jupiter.api.Assertions.assertAll;
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.junit.jupiter.api.Assertions.assertTimeout;
import static org.junit.jupiter.api.Assertions.assertTimeoutPreemptively;
import static org.junit.jupiter.api.Assertions.assertTrue;

import org.junit.jupiter.api.Test;

class AssertionsDemo {

    @Test
    void standardAssertions() {
        assertEquals(2, 2);
        assertEquals(4, 4, "The optional assertion message is now the last parameter.");
        assertTrue(2 == 2, () -> "Assertion messages can be lazily evaluated -- "
                + "to avoid constructing complex messages unnecessarily.");
    }

    @Test
    void groupedAssertions() {
        // 在一个group的断言都会被执行,只要有一个失败就会导致整个group断言的失败
        assertAll("person",
            () -> assertEquals("John", person.getFirstName()),
            () -> assertEquals("Doe", person.getLastName())
        );
    }

    @Test
    void dependentAssertions() {
        // 在代码块中，如果断言失败
   		// 将跳过同一块中的后续代码。
        assertAll("properties",
            () -> {
                String firstName = person.getFirstName();
                assertNotNull(firstName);

                // 仅在先前的断言有效时执行。
                assertAll("first name",
                    () -> assertTrue(firstName.startsWith("J")),
                    () -> assertTrue(firstName.endsWith("n"))
                );
            },
            () -> {
                // 分组断言，每个断言的结果独立
                String lastName = person.getLastName();
                assertNotNull(lastName);

                // 仅在先前的断言有效时执行。
                assertAll("last name",
                    () -> assertTrue(lastName.startsWith("D")),
                    () -> assertTrue(lastName.endsWith("e"))
                );
            }
        );
    }

    @Test
    void exceptionTesting() {
        Throwable exception = assertThrows(IllegalArgumentException.class, () -> {
            throw new IllegalArgumentException("a message");
        });
        assertEquals("a message", exception.getMessage());
    }

    @Test
    void timeoutNotExceeded() {
    	//超时断言,在规定超时范围内执行完成,就表示成功
        assertTimeout(ofMinutes(2), () -> {
            // 执行不到两分钟的任务
        });
    }

    @Test
    void timeoutNotExceededWithResult() {
        // 以下断言成功,并返回提供的对象
        String actualResult = assertTimeout(ofMinutes(2), () -> {
            return "a result";
        });
        assertEquals("a result", actualResult);
    }

    @Test
    void timeoutNotExceededWithMethod() {
        // 以下断言调用方法引用并返回一个对象。
        String actualGreeting = assertTimeout(ofMinutes(2), AssertionsDemo::greeting);
        assertEquals("hello world!", actualGreeting);
    }

    @Test
    void timeoutExceeded() {
    	// 以下断言失败，并提示信息类似于：execution exceeded timeout of 10 ms by 91 ms
        assertTimeout(ofMillis(10), () -> {
            // 模拟需要超过10 ms的任务。
            Thread.sleep(100);
        });
    }

    @Test
    void timeoutExceededWithPreemptiveTermination() {
        //以下断言失败，并提示信息类似于：
        // execution timed out after 10 ms
        assertTimeoutPreemptively(ofMillis(10), () -> {
            // 模拟执行超过10ms的任务
            Thread.sleep(100);
        });
    }

    private static String greeting() {
        return "hello world!";
    }

}
```

### 第三方断言库

即使由JUnit Jupiter提供的断言功能对于许多测试场景足够的，但有时候需要或需要更多的功能和附加功能，如matchers。 在这种情况下，JUnit团队建议使用第三方断言库，如AssertJ(译者注:想要了解该框架,请点击[这里](https://henryz.gitbooks.io/java-test-learning/content/assertj/))，Hamcrest，Truth等。因此，开发人员可以自由使用他们选择的断言库。

例如，matchers和fluent API的组合可用于使断言更具描述性和可读性。 但是，JUnit Jupiter的org.junit.jupiter.Assertions类不提供像JUnit 4的org.junit.Assert类中的assertThat（）方法，它支持Hamcrest Matcher。 相反，鼓励开发人员使用第三方断言库提供的matchers的内置支持。

以下示例演示了如何在JUnit Jupiter测试中使用Hamcrest的assertThat（）支持。 只要将Hamcrest库添加到类路径中，您可以静态导入assertThat（），is（）和equalsTo（）等方法，然后在下面的assertWithHamcrestMatcher（）方法中进行测试。

```java
import static org.hamcrest.CoreMatchers.equalTo;
import static org.hamcrest.CoreMatchers.is;
import static org.hamcrest.MatcherAssert.assertThat;

import org.junit.jupiter.api.Test;

class HamcrestAssertionDemo {

    @Test
    void assertWithHamcrestMatcher() {
        assertThat(2 + 1, is(equalTo(3)));
    }

}
```

> 当然，基于JUnit 4编程模型的遗留测试可以继续使用org.junit.Assert＃assertThat。

## Assumptions(假设)

JUnit Jupiter带有JUnit 4提供的一些Assumpe方法的子集，并添加了一些适用于Java 8 lambdas的api。 所有JUnit Jupiter assume都是org.junit.jupiter.Assumptions类中的静态方法。

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assumptions.assumeTrue;
import static org.junit.jupiter.api.Assumptions.assumingThat;

import org.junit.jupiter.api.Test;

class AssumptionsDemo {

    @Test
    void testOnlyOnCiServer() {
        assumeTrue("CI".equals(System.getenv("ENV")));
        // remainder of test
    }

    @Test
    void testOnlyOnDeveloperWorkstation() {
        assumeTrue("DEV".equals(System.getenv("ENV")),
            () -> "Aborting test: not on developer workstation");
        // remainder of test
    }

    @Test
    void testInAllEnvironments() {
        assumingThat("CI".equals(System.getenv("ENV")),
            () -> {
                // perform these assertions only on the CI server
                assertEquals(2, 2);
            });

        // perform these assertions in all environments
        assertEquals("a string", "a string");
    }

}
```

## 禁用测试

禁用测试类的例子

```java
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;

@Disabled
class DisabledClassDemo {
    @Test
    void testWillBeSkipped() {
    }不得包含ISO控制字符。
}
```

禁用某个测试方法的例子

```java
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;

class DisabledTestsDemo {

    @Disabled
    @Test
    void testWillBeSkipped() {
    }

    @Test
    void testWillBeExecuted() {
    }
}
```

## 标记和过滤

测试类和方法可以被标记。 这些tag可以稍后用于过滤test case的发现和执行。

### tag语法规则

- trimmed tag不能为null和空
- trimmed tag不能包含空格
- trimmed tag不得包含ISO控制字符
- trimmed tag不能包含以下任何保留字符:
	- ,, (, ), &, |, !

> 在上述上下文中，“trimmed”表示前导和后缀,空格字符已被删除。

```java
import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;

@Tag("fast")
@Tag("model")
class TaggingDemo {

    @Test
    @Tag("taxes")
    void testingTaxCalculation() {
    }

}
```

## 测试实例生命周期

为了支持单个的测试方法被隔离执行并避免由于可变测试实例状态引起的意外的副作用，JUnit在执行每个测试方法之前都会创建每个测试类的新实例（参考下面的注解，作为测试方法的资格））。 这个“pre-method”的测试实例生命周期是JUnit Jupiter中的默认行为，类似于所有先前版本的JUnit。

如果你希望JUnit Jupiter在同一测试实例上执行所有测试方法，只需使用@TestInstance（Lifecycle.PER_CLASS）注解测试类。 使用此模式时，每个测试类只会创建一个新的测试实例。 因此，如果您的测试方法依赖于存储在实例变量中的状态，则可能需要在@BeforeEach或@AfterEach方法中重置该状态。

“pre-class”模式比默认的“pre-method”模式有一些额外的好处。 具体来说，使用“pre-class”模式，可以在非静态方法以及接口默认方法上声明@BeforeAll和@AfterAll。 因此，“pre-class”模式也可以在@Nested测试类中使用@BeforeAll和@AfterAll方法。

如果您使用Kotlin编程语言编写测试，还可以通过切换到“pre-class”测试实例生命周期模式，轻松实现@BeforeAll和@AfterAll方法。

> 在测试实例生命周期的上下文中，测试方法是使用@Test，@RepeatedTest，@ParameterizedTest，@TestFactory或@TestTemplate注解的任何方法。

### 更改默认测试实例生命周期

如果没有使用@TestInstance注解测试类或测试接口，JUnit Jupiter将使用默认的生命周期模式。 标准默认模式为PER_METHOD; 但是，可以更改执行整个测试计划的默认值。 要更改默认的测试实例生命周期模式，只需将junit.jupiter.testinstance.lifecycle.default配置参数设置为TestInstance.Lifecycle中定义的枚举常量的名称，忽略大小写。 这可以作为JVM系统属性提供，作为LauncherDiscoveryRequest中传递给Launcher的配置参数，或通过JUnit Platform配置文件提供（有关详细信息，请参阅配置参数）。

例如，要将默认测试实例生命周期模式设置为Lifecycle.PER_CLASS，可以使用以下系统属性启动JVM。

-Djunit.jupiter.testinstance.lifecycle.default=per_class

但是请注意，通过JUnit Platform配置文件设置默认测试实例生命周期模式是一个更强大的解决方案，因为配置文件可以与项目一起check in版本控制系统，因此可以在IDE和您的构建软件中使用。

要通过JUnit Platform配置文件将默认测试实例生命周期模式设置为Lifecycle.PER_CLASS，请在具有以下内容的类路径根目录（例如src/test/resources）中创建一个名为junit-platform.properties的文件。

junit.jupiter.testinstance.lifecycle.default = per_class

> 更改默认的测试实例生命周期模式可能导致不可预测的结果和脆弱的构建，如果不一致应用。 例如，如果构建将“pre-class”语义配置为默认值，但是使用“pre-method”语义执行IDE中的测试，则可能会使构建服务器上发生的错误进行调试变得困难。 因此，建议更改JUnit Platform配置文件中的默认值，而不是通过JVM系统属性更改默认值。

## 嵌套测试(Nested Tests)

嵌套测试使Tester能够表达几组测试之间的关系。 以下是一个精心设计的例子。
用于测试堆栈的嵌套测试套件
```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.junit.jupiter.api.Assertions.assertTrue;

import java.util.EmptyStackException;
import java.util.Stack;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;

@DisplayName("A stack")
class TestingAStackDemo {

    Stack<Object> stack;

    @Test
    @DisplayName("is instantiated with new Stack()")
    void isInstantiatedWithNew() {
        new Stack<>();
    }

    @Nested
    @DisplayName("when new")
    class WhenNew {

        @BeforeEach
        void createNewStack() {
            stack = new Stack<>();
        }

        @Test
        @DisplayName("is empty")
        void isEmpty() {
            assertTrue(stack.isEmpty());
        }

        @Test
        @DisplayName("throws EmptyStackException when popped")
        void throwsExceptionWhenPopped() {
            assertThrows(EmptyStackException.class, () -> stack.pop());
        }

        @Test
        @DisplayName("throws EmptyStackException when peeked")
        void throwsExceptionWhenPeeked() {
            assertThrows(EmptyStackException.class, () -> stack.peek());
        }

        @Nested
        @DisplayName("after pushing an element")
        class AfterPushing {

            String anElement = "an element";

            @BeforeEach
            void pushAnElement() {
                stack.push(anElement);
            }

            @Test
            @DisplayName("it is no longer empty")
            void isNotEmpty() {
                assertFalse(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when popped and is empty")
            void returnElementWhenPopped() {
                assertEquals(anElement, stack.pop());
                assertTrue(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when peeked but remains not empty")
            void returnElementWhenPeeked() {
                assertEquals(anElement, stack.peek());
                assertFalse(stack.isEmpty());
            }
        }
    }
}
```

> 只有非静态嵌套类（即内部类）可以作为@Nested测试类。 嵌套可以是任意深度的，并且那些内部类被认为是测试类家族的完整成员，但有一个例外：@BeforeAll和@AfterAll方法在默认情况下不起作用。 原因是Java不允许内部类中有静态成员。 但是，可以通过使用@TestInstance（Lifecycle.PER_CLASS）注解@Nested测试类来规避此限制。

## 构造函数和方法的依赖注入

在所有先前的JUnit版本中，测试类的构造函数或方法都不允许具有参数（至少不符合标准Runner实现）。 作为JUnit Jupiter的主要变化之一，现在允许测试构造函数和方法具有参数。 这允许更大的灵活性，并为构造函数和方法启用依赖注入。

[ParameterResolver](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/ParameterResolver.html)定义了希望在运行时动态解析参数的test extensions API。 如果测试构造函数或@Test，@TestFactory，@BeforeEach，@AfterEach，@BeforeAll或@AfterAll方法接受一个参数，那么该参数必须在运行时由注册的ParameterResolver解析。

目前有三个自动注册的内置解析器。
1. [TestInfoParameterResolver](https://github.com/junit-team/junit5/tree/r5.0.0/junit-jupiter-engine/src/main/java/org/junit/jupiter/engine/extension/TestInfoParameterResolver.java) 如果方法参数的类型为TestInfo，则TestInfoParameterResolver将提供与当前测试对应的TestInfo实例作为参数的值。 然后可以使用TestInfo来检索有关当前测试的信息，例如测试的显示名称，测试类，测试方法或相关标签。 显示名称是技术名称，例如测试类或测试方法的名称，或通过@DisplayName配置的自定义名称。

	TestInfo作为JUnit 4中TestName规则的替代替代。以下演示了如何将TestInfo注入到测试构造函数@BeforeEach方法和@Test方法中。

    ```java
    import static org.junit.jupiter.api.Assertions.assertEquals;
    import static org.junit.jupiter.api.Assertions.assertTrue;

    import org.junit.jupiter.api.BeforeEach;
    import org.junit.jupiter.api.DisplayName;
    import org.junit.jupiter.api.Tag;
    import org.junit.jupiter.api.Test;
    import org.junit.jupiter.api.TestInfo;

    @DisplayName("TestInfo Demo")
    class TestInfoDemo {

        TestInfoDemo(TestInfo testInfo) {
            assertEquals("TestInfo Demo", testInfo.getDisplayName());
        }

        @BeforeEach
        void init(TestInfo testInfo) {
            String displayName = testInfo.getDisplayName();
            assertTrue(displayName.equals("TEST 1") || displayName.equals("test2()"));
        }

        @Test
        @DisplayName("TEST 1")
        @Tag("my-tag")
        void test1(TestInfo testInfo) {
            assertEquals("TEST 1", testInfo.getDisplayName());
            assertTrue(testInfo.getTags().contains("my-tag"));
        }

        @Test
        void test2() {
        }

    }
	```

1. RepetitionInfoParameterResolver: 如果@RepeatedTest，@BeforeEach或@AfterEach方法中的方法参数类型为[RepetitionInfo](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/RepetitionInfo.html)，则RepetitionInfoParameterResolver将提供RepetitionInfo的实例。 然后可以使用RepetitionInfo来检索有关当前重复的信息以及相应的@RepeatedTest的重复次数。 但是请注意，RepetitionInfoParameterResolver不会在@RepeatedTest的上下文之外注册。

1. [TestReporterParameterResolver](https://github.com/junit-team/junit5/tree/r5.0.0/junit-jupiter-engine/src/main/java/org/junit/jupiter/engine/extension/TestReporterParameterResolver.java):如果方法参数的类型为[TestReporter](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/TestReporter.html)，则TestReporterParameterResolver将提供TestReporter的实例。 TestReporter可用于发布有关当前测试运行的其他数据。 数据可以通过TestExecutionListener.reportingEntryPublished（）使用，因此可以被IDE查看或包含在报告中。

	在JUnit Jupiter中，您应该使用TestReporter，用于在JUnit 4中将信息打印到stdout或stderr。使用@RunWith（JUnitPlatform.class）甚至可以将所有报告的条目输出到stdout。

```java
import java.util.HashMap;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.TestReporter;

class TestReporterDemo {

    @Test
    void reportSingleValue(TestReporter testReporter) {
        testReporter.publishEntry("a key", "a value");
    }

    @Test
    void reportSeveralValues(TestReporter testReporter) {
        HashMap<String, String> values = new HashMap<>();
        values.put("user name", "dk38");
        values.put("award year", "1974");

        testReporter.publishEntry(values);
    }

}
```

>必须通过@ExtendWith注册适当的扩展名来明确启用其他参数解析器。

可以从junit的官方例子中check out出ParameterResolver的[MockitoExtension](https://github.com/junit-team/junit5-samples/tree/r5.0.0/junit5-mockito-extension/src/main/java/com/example/mockito/MockitoExtension.java)例子.它展现了扩展模型和参数解析过程的简单性和表现力。 MyMockitoTest演示了如何将Mockito模拟注入@BeforeEach和@Test方法。
```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.when;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import com.example.Person;
import com.example.mockito.MockitoExtension;

@ExtendWith(MockitoExtension.class)
class MyMockitoTest {

    @BeforeEach
    void init(@Mock Person person) {
        when(person.getName()).thenReturn("Dilbert");
    }

    @Test
    void simpleTestWithInjectedMock(@Mock Person person) {
        assertEquals("Dilbert", person.getName());
    }

}
```

## 测试接口和它的默认方法

JUnit Jupiter允许在接口的默认方法上声明@Test，@RepeatedTest，@ParameterizedTest，@TestFactory，@TestTemplate，@BeforeEach和@AfterEach。 如果使用@TestInstance（Lifecycle.PER_CLASS）注解测试接口或测试类，则@BeforeAll和@AfterAll可以在测试接口中的静态方法上声明，也可以在接口的默认方法上声明。 这里有些例子。

```java
@TestInstance(Lifecycle.PER_CLASS)
interface TestLifecycleLogger {

    static final Logger LOG = Logger.getLogger(TestLifecycleLogger.class.getName());

    @BeforeAll
    default void beforeAllTests() {
        LOG.info("Before all tests");
    }

    @AfterAll
    default void afterAllTests() {
        LOG.info("After all tests");
    }

    @BeforeEach
    default void beforeEachTest(TestInfo testInfo) {
        LOG.info(() -> String.format("About to execute [%s]",
            testInfo.getDisplayName()));
    }

    @AfterEach
    default void afterEachTest(TestInfo testInfo) {
        LOG.info(() -> String.format("Finished executing [%s]",
            testInfo.getDisplayName()));
    }

}
```
```java
interface TestInterfaceDynamicTestsDemo {

    @TestFactory
    default Collection<DynamicTest> dynamicTestsFromCollection() {
        return Arrays.asList(
            dynamicTest("1st dynamic test in test interface", () -> assertTrue(true)),
            dynamicTest("2nd dynamic test in test interface", () -> assertEquals(4, 2 * 2))
        );
    }

}
```

@ExtendWith和@Tag可以在测试界面上声明，以便实现接口的类自动继承其标签和扩展。

```java
@Tag("timed")
@ExtendWith(TimingExtension.class)
interface TimeExecutionLogger {
}
```
在测试类中，您可以实现这些测试接口来应用它们。
```java
class TestInterfaceDemo implements TestLifecycleLogger,
        TimeExecutionLogger, TestInterfaceDynamicTestsDemo {

    @Test
    void isEqualValue() {
        assertEquals(1, 1, "is always equal");
    }

}
```
运行TestInterfaceDemo会产生类似于以下内容的输出：
```console
:junitPlatformTest
INFO  example.TestLifecycleLogger - Before all tests
INFO  example.TestLifecycleLogger - About to execute [dynamicTestsFromCollection()]
INFO  example.TimingExtension - Method [dynamicTestsFromCollection] took 13 ms.
INFO  example.TestLifecycleLogger - Finished executing [dynamicTestsFromCollection()]
INFO  example.TestLifecycleLogger - About to execute [isEqualValue()]
INFO  example.TimingExtension - Method [isEqualValue] took 1 ms.
INFO  example.TestLifecycleLogger - Finished executing [isEqualValue()]
INFO  example.TestLifecycleLogger - After all tests

Test run finished after 190 ms
[         3 containers found      ]
[         0 containers skipped    ]
[         3 containers started    ]
[         0 containers aborted    ]
[         3 containers successful ]
[         0 containers failed     ]
[         3 tests found           ]
[         0 tests skipped         ]
[         3 tests started         ]
[         0 tests aborted         ]
[         3 tests successful      ]
[         0 tests failed          ]

BUILD SUCCESSFUL
```

此功能的另一个可能应用是编写接口契约测试。 例如，您可以编写一个如何实现Object.equals或Comparable.compareTo方法的接口测试，实现应如下。
```java
public interface Testable<T> {

    T createValue();

}
```

```java
public interface EqualsContract<T> extends Testable<T> {

    T createNotEqualValue();

    @Test
    default void valueEqualsItself() {
        T value = createValue();
        assertEquals(value, value);
    }

    @Test
    default void valueDoesNotEqualNull() {
        T value = createValue();
        assertFalse(value.equals(null));
    }

    @Test
    default void valueDoesNotEqualDifferentValue() {
        T value = createValue();
        T differentValue = createNotEqualValue();
        assertNotEquals(value, differentValue);
        assertNotEquals(differentValue, value);
    }
}
```

```java
public interface ComparableContract<T extends Comparable<T>> extends Testable<T> {

    T createSmallerValue();

    @Test
    default void returnsZeroWhenComparedToItself() {
        T value = createValue();
        assertEquals(0, value.compareTo(value));
    }

    @Test
    default void returnsPositiveNumberComparedToSmallerValue() {
        T value = createValue();
        T smallerValue = createSmallerValue();
        assertTrue(value.compareTo(smallerValue) > 0);
    }

    @Test
    default void returnsNegativeNumberComparedToSmallerValue() {
        T value = createValue();
        T smallerValue = createSmallerValue();
        assertTrue(smallerValue.compareTo(value) < 0);
    }
}
```

在测试类中，您可以实现两个契约接口，从而继承相应的测试。 当然，你必须实现抽象方法。
```java
class StringTests implements ComparableContract<String>, EqualsContract<String> {

    @Override
    public String createValue() {
        return "foo";
    }

    @Override
    public String createSmallerValue() {
        return "bar"; // 'b' < 'f' in "foo"
    }

    @Override
    public String createNotEqualValue() {
        return "baz";
    }

}
```

## 重复测试

JUnit Jupiter提供了通过使用@RepeatedTest注解方法并指定所需重复次数的方式，重复测试指定次数的功能。 每次调用重复测试的行为就像执行一个常规的@Test方法，并且完全支持相同的生命周期回调和扩展。

以下示例演示如何声明一个将重复10次的名为repeatTest（）的测试。
```java
@RepeatedTest(10)
void repeatedTest() {
    // ...
}
```

除了指定重复次数之外，还可以通过@RepeatedTest注释的name属性为每次重复配置自定义显示名称。此外，显示名称可以是由静态文本和动态占位符的组合组成的图案。目前支持以下占位符。
- {displayName}：显示@RepeatedTest方法的名称
- {currentRepetition}：当前的重复计数
- {totalRepetitions}：重复次数
给定重复的默认显示名称是根据以下模式生成的：”repetition {currentRepetition} of {totalRepetitions}“。因此，默认的repeatTest（）示例的单独重复的显示名称将是：repetition 1 of 10, repetition 2 of 10等。如果希望包含在每个重复的名称中的@RepeatedTest方法的显示名称，您可以定义自己的自定义模式或使用预定义的RepeatedTest.LONG_DISPLAY_NAME模式。后者等于{totalRepetitions}的“{displayName} :: repetition {currentRepetition} of {totalRepetitions}”，这将导致单独重复的显示名称，如repeatTest（）:: repeat 1 of 10，repeatedTest（）:: repeated 2 of 10等。

为了以编程方式检索有关当前重复的信息和总重复次数，开发人员可以选择将RepetitionInfo的实例注入到@RepeatedTest，@BeforeEach或@AfterEach方法中。

### 重复测试例子

本节末尾的RepeatedTestsDemo类演示了重复测试的几个示例。

repeatTest（）方法与上一节的示例相同;而repeatTestWithRepetitionInfo（）演示了如何将RepetitionInfo的实例注入到测试中以访问当前重复测试的总重复次数。

接下来的两个方法演示了如何在每次重复的显示名称中为@RepeatedTest方法包含一个自定义@DisplayName。 customDisplayName（）将自定义显示名称与自定义模式相结合，然后使用TestInfo验证生成的显示名称的格式。repeated！是来自@DisplayName声明的{displayName}，1/1来自{currentRepetition} / {totalRepetitions}。相比之下，customDisplayNameWithLongPattern（）使用上述预定义的RepeatedTest.LONG_DISPLAY_NAME模式。

repeatedTestInGerman（）演示了将重复测试的显示名称翻译成外语的能力 - 在这种情况下为德语，从而导致单独重复的名称，例如：Wiederholung 1 von 5，Wiederholung 2 von 5等。

由于beforeEach（）方法用@BeforeEach注解，因此每次重复测试之前都会执行它。通过将TestInfo和RepetitionInfo注入到方法中，我们看到可以获取有关当前执行的重复测试的信息。在启用INFO日志级别的情况下执行RepeatedTestsDemo会导致以下输出。
```console
INFO: About to execute repetition 1 of 10 for repeatedTest
INFO: About to execute repetition 2 of 10 for repeatedTest
INFO: About to execute repetition 3 of 10 for repeatedTest
INFO: About to execute repetition 4 of 10 for repeatedTest
INFO: About to execute repetition 5 of 10 for repeatedTest
INFO: About to execute repetition 6 of 10 for repeatedTest
INFO: About to execute repetition 7 of 10 for repeatedTest
INFO: About to execute repetition 8 of 10 for repeatedTest
INFO: About to execute repetition 9 of 10 for repeatedTest
INFO: About to execute repetition 10 of 10 for repeatedTest
INFO: About to execute repetition 1 of 5 for repeatedTestWithRepetitionInfo
INFO: About to execute repetition 2 of 5 for repeatedTestWithRepetitionInfo
INFO: About to execute repetition 3 of 5 for repeatedTestWithRepetitionInfo
INFO: About to execute repetition 4 of 5 for repeatedTestWithRepetitionInfo
INFO: About to execute repetition 5 of 5 for repeatedTestWithRepetitionInfo
INFO: About to execute repetition 1 of 1 for customDisplayName
INFO: About to execute repetition 1 of 1 for customDisplayNameWithLongPattern
INFO: About to execute repetition 1 of 5 for repeatedTestInGerman
INFO: About to execute repetition 2 of 5 for repeatedTestInGerman
INFO: About to execute repetition 3 of 5 for repeatedTestInGerman
INFO: About to execute repetition 4 of 5 for repeatedTestInGerman
INFO: About to execute repetition 5 of 5 for repeatedTestInGerman
```
```java
import static org.junit.jupiter.api.Assertions.assertEquals;

import java.util.logging.Logger;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.RepeatedTest;
import org.junit.jupiter.api.RepetitionInfo;
import org.junit.jupiter.api.TestInfo;

class RepeatedTestsDemo {

    private Logger logger = // ...

    @BeforeEach
    void beforeEach(TestInfo testInfo, RepetitionInfo repetitionInfo) {
        int currentRepetition = repetitionInfo.getCurrentRepetition();
        int totalRepetitions = repetitionInfo.getTotalRepetitions();
        String methodName = testInfo.getTestMethod().get().getName();
        logger.info(String.format("About to execute repetition %d of %d for %s", //
            currentRepetition, totalRepetitions, methodName));
    }

    @RepeatedTest(10)
    void repeatedTest() {
        // ...
    }

    @RepeatedTest(5)
    void repeatedTestWithRepetitionInfo(RepetitionInfo repetitionInfo) {
        assertEquals(5, repetitionInfo.getTotalRepetitions());
    }

    @RepeatedTest(value = 1, name = "{displayName} {currentRepetition}/{totalRepetitions}")
    @DisplayName("Repeat!")
    void customDisplayName(TestInfo testInfo) {
        assertEquals(testInfo.getDisplayName(), "Repeat! 1/1");
    }

    @RepeatedTest(value = 1, name = RepeatedTest.LONG_DISPLAY_NAME)
    @DisplayName("Details...")
    void customDisplayNameWithLongPattern(TestInfo testInfo) {
        assertEquals(testInfo.getDisplayName(), "Details... :: repetition 1 of 1");
    }

    @RepeatedTest(value = 5, name = "Wiederholung {currentRepetition} von {totalRepetitions}")
    void repeatedTestInGerman() {
        // ...
    }

}
```
当启用了启用unicode主题的ConsoleLauncher或junitPlatformTest Gradle插件时，执行RepeatedTestsDemo将导致以下输出。
```console
├─ RepeatedTestsDemo ✔
│  ├─ repeatedTest() ✔
│  │  ├─ repetition 1 of 10 ✔
│  │  ├─ repetition 2 of 10 ✔
│  │  ├─ repetition 3 of 10 ✔
│  │  ├─ repetition 4 of 10 ✔
│  │  ├─ repetition 5 of 10 ✔
│  │  ├─ repetition 6 of 10 ✔
│  │  ├─ repetition 7 of 10 ✔
│  │  ├─ repetition 8 of 10 ✔
│  │  ├─ repetition 9 of 10 ✔
│  │  └─ repetition 10 of 10 ✔
│  ├─ repeatedTestWithRepetitionInfo(RepetitionInfo) ✔
│  │  ├─ repetition 1 of 5 ✔
│  │  ├─ repetition 2 of 5 ✔
│  │  ├─ repetition 3 of 5 ✔
│  │  ├─ repetition 4 of 5 ✔
│  │  └─ repetition 5 of 5 ✔
│  ├─ Repeat! ✔
│  │  └─ Repeat! 1/1 ✔
│  ├─ Details... ✔
│  │  └─ Details... :: repetition 1 of 1 ✔
│  └─ repeatedTestInGerman() ✔
│     ├─ Wiederholung 1 von 5 ✔
│     ├─ Wiederholung 2 von 5 ✔
│     ├─ Wiederholung 3 von 5 ✔
│     ├─ Wiederholung 4 von 5 ✔
│     └─ Wiederholung 5 von 5 ✔
```

## 参数化测试

参数化测试使得可以用不同的参数多次运行测试。它使用@ParameterizedTest注解,但是被声明为常规的@test方法。 此外，您必须声明至少为调用提供一个参数的源。
> 参数化测试目前是一个实验功能。 有关详细信息，请参阅实验API中的表格。

```java
@ParameterizedTest
@ValueSource(strings = { "Hello", "World" })
void testWithStringParameter(String argument) {
    assertNotNull(argument);
}
```
此参数化测试使用@ValueSource注解来指定String数组作为参数的来源。 执行此方法时，将分别报告每次调用。 例如，ConsoleLauncher将打印输出类似于以下内容。
```console
testWithStringParameter(String) ✔
├─ [1] Hello ✔
└─ [2] World ✔
```

### 必要的设置

为了使用参数化测试，您需要在junit-jupiter-params artifact上添加依赖关系。

```xml
 <dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-params</artifactId>
    <version>${junit.jupiter.version}</version>
    <scope>test</scope>
</dependency>
```

### 参数源

开箱即用,junit jupiter提供了不少关于source的注解，以下各小节提供了一个简要的概述和每个示例。 有关其他信息，请参阅[org.junit.jupiter.params.provider](http://junit.org/junit5/docs/current/api/org/junit/jupiter/params/provider/package-summary.html)包中的JavaDoc。

#### @ValueSource

@ValueSource是最简单sources之一。 它允许您指定原始类型（String，int，long或double）的文字数组，并且只能用于每次调用提供单个参数。

```java
@ParameterizedTest
@ValueSource(ints = { 1, 2, 3 })
void testWithValueSource(int argument) {
    assertNotNull(argument);
}
```

#### @EnumSource

@EnumSource提供了一种方便使用枚举常量的方法。 注解提供了一个可选的名称参数，可以指定使用哪些常量。 如果省略，将使用所有常量，如下例所示。

```java
@ParameterizedTest
@EnumSource(TimeUnit.class)
void testWithEnumSource(TimeUnit timeUnit) {
    assertNotNull(timeUnit);
}
```
```java
@ParameterizedTest
@EnumSource(value = TimeUnit.class, names = { "DAYS", "HOURS" })
void testWithEnumSourceInclude(TimeUnit timeUnit) {
    assertTrue(EnumSet.of(TimeUnit.DAYS, TimeUnit.HOURS).contains(timeUnit));
}
```

@EnumSource注解还提供了一个可选的mode参数，可以对传递给测试方法的常量进行更细粒度的控制。 例如，您可以从枚举常量池中排除名称或指定正则表达式，如以下示例所示。

```java
@ParameterizedTest
@EnumSource(value = TimeUnit.class, mode = EXCLUDE, names = { "DAYS", "HOURS" })
void testWithEnumSourceExclude(TimeUnit timeUnit) {
    assertFalse(EnumSet.of(TimeUnit.DAYS, TimeUnit.HOURS).contains(timeUnit));
    assertTrue(timeUnit.name().length() > 5);
}
```

```java
@ParameterizedTest
@EnumSource(value = TimeUnit.class, mode = MATCH_ALL, names = "^(M|N).+SECONDS$")
void testWithEnumSourceRegex(TimeUnit timeUnit) {
    String name = timeUnit.name();
    assertTrue(name.startsWith("M") || name.startsWith("N"));
    assertTrue(name.endsWith("SECONDS"));
}
```

#### @MethodSource

@MethodSource允许您引用测试类的一个或多个方法。 每个方法都必须返回一个Stream，Iterable，Iterator或参数数组。 另外，每个方法都不能接受任何参数。 默认情况下，除非使用@TestInstance（Lifecycle.PER_CLASS）注解测试类，否则这些方法必须是静态的。

如果只需要一个参数，则可以直接返回参数类型的实例，如以下示例所示。

```java
@ParameterizedTest
@MethodSource("stringProvider")
void testWithSimpleMethodSource(String argument) {
    assertNotNull(argument);
}

static Stream<String> stringProvider() {
    return Stream.of("foo", "bar");
}
```
还支持原始类型（DoubleStream，IntStream和LongStream）的流。

```java
@ParameterizedTest
@MethodSource("range")
void testWithRangeMethodSource(int argument) {
    assertNotEquals(9, argument);
}

static IntStream range() {
    return IntStream.range(0, 20).skip(10);
}
```

如果需要多个参数，则需要返回一个Arguments实例，如下所示。 请注意，Arguments.of（Object ...）是在接口本身中定义的静态工厂方法。

```java
@ParameterizedTest
@MethodSource("stringAndIntProvider")
void testWithMultiArgMethodSource(String first, int second) {
    assertNotNull(first);
    assertNotEquals(0, second);
}

static Stream<Arguments> stringAndIntProvider() {
    return Stream.of(Arguments.of("foo", 1), Arguments.of("bar", 2));
}
```

#### @CsvSource

@CsvSource允许您将参数列表表示为逗号分隔的形式（即CSV格式）。

```java
@ParameterizedTest
@CsvSource({ "foo, 1", "bar, 2", "'baz, qux', 3" })
void testWithCsvSource(String first, int second) {
    assertNotNull(first);
    assertNotEquals(0, second);
}
```

@CsvSource使用单引号作为其引号。 请参见上面的示例和下表中的'baz，qux'值。 一个空的引用值''会被当做一个空字符串; 而一个完全空的值被解释为null引用。 如果null引用的目标类型是原始类型，则会抛出ArgumentConversionException。
| 输入例子 | 返回的参数列表 |
|--------|--------|
|    @CsvSource({ "foo, bar" })    |     "foo", "bar"   |
|	@CsvSource({ "foo, 'baz, qux'" })	|	"foo", "baz, qux"	|
|	@CsvSource({ "foo, ''" })	|	"foo", ""	|
|	@CsvSource({ "foo, " })	|	"foo", null	|

#### @CsvFileSource

@CsvFileSource可以让您从classpath中使用CSV文件。 来自CSV文件的每一行都会执行一次调用参数化测试。

```java
@ParameterizedTest
@CsvFileSource(resources = "/two-column.csv")
void testWithCsvFileSource(String first, int second) {
    assertNotNull(first);
    assertNotEquals(0, second);
}
```

two-column.csv

```csv
foo, 1
bar, 2
"baz, qux", 3
```

> 与@CvSource中使用的语法相反，@CsvFileSource使用双引号“作为引用字符，请参见上述示例中的”baz，qux“值，空引用的值”“会被解释为一个空字符串; 一个完全空的值被解释为null引用。如果null引用的目标类型是原始类型，则会抛出ArgumentConversionException。

#### @ArgumentsSource

@ArgumentsSource可用于指定自定义的可重用的ArgumentsProvider。

```java
@ParameterizedTest
@ArgumentsSource(MyArgumentsProvider.class)
void testWithArgumentsSource(String argument) {
    assertNotNull(argument);
}

static class MyArgumentsProvider implements ArgumentsProvider {

    @Override
    public Stream<? extends Arguments> provideArguments(ExtensionContext context) {
        return Stream.of("foo", "bar").map(Arguments::of);
    }
}
```

### 参数转换

#### 隐式转换

为了支持@CvSource这样的用例，JUnit Jupiter提供了许多内置的隐式类型转换器。 转换过程取决于每个方法的参数声明的类型。

例如，如果@ParameterizedTest声明一个类型为TimeUnit的参数，并且由声明的源提供的实际类型为String，则该字符串将自动转换为相应的TimeUnit枚举常量。

```java
@ParameterizedTest
@ValueSource(strings = "SECONDS")
void testWithImplicitArgumentConversion(TimeUnit argument) {
    assertNotNull(argument.name());
}
```

字符串实例当前被隐式转换为以下目标类型.

| 目标类型 | 例子 |
|--------|--------|
|    boolean/Boolean    |    "true" → true    |
|	byte/Byte	|	"1" → (byte) 1	|
|	char/Character	|	"o" → 'o'	|
|	short/Short	|	"1" → (short) 1	|
|	int/Integer	|		"1" → 1	|
|	long/Long	|	"1" → 1L	|
|	float/Float	|	"1.0" → 1.0f	|
|	double/Double	|	"1.0" → 1.0d	|
|	Enum subclass	|	"SECONDS" → TimeUnit.SECONDS	|
|	java.time.Instant	|	"1970-01-01T00:00:00Z" → Instant.ofEpochMilli(0)	|
|	java.time.LocalDate	|	"2017-03-14" → LocalDate.of(2017, 3, 14)	|
|	java.time.LocalDateTime	|	"2017-03-14T12:34:56.789" → LocalDateTime.of(2017, 3, 14, 12, 34, 56, 789_000_000)	|
|	java.time.LocalTime	|	"12:34:56.789" → LocalTime.of(12, 34, 56, 789_000_000)	|
|	java.time.OffsetDateTime	|	"2017-03-14T12:34:56.789Z" → OffsetDateTime.of(2017, 3, 14, 12, 34, 56, 789_000_000, ZoneOffset.UTC)	|
|	java.time.OffsetTime	|	"12:34:56.789Z" → OffsetTime.of(12, 34, 56, 789_000_000, ZoneOffset.UTC)	|
|	java.time.Year	|	"2017" → Year.of(2017)	|
|	java.time.YearMonth	|	"2017-03" → YearMonth.of(2017, 3)	|
|	java.time.ZonedDateTime	|	"2017-03-14T12:34:56.789Z" → ZonedDateTime.of(2017, 3, 14, 12, 34, 56, 789_000_000, ZoneOffset.UTC)	|

#### 显示转换

您可以使用@ConvertWith注解来显式指定一个ArgumentConverter用于某个参数，而不是使用隐式参数转换.如下例所示。

```java
@ParameterizedTest
@EnumSource(TimeUnit.class)
void testWithExplicitArgumentConversion(@ConvertWith(ToStringArgumentConverter.class) String argument) {
    assertNotNull(TimeUnit.valueOf(argument));
}

static class ToStringArgumentConverter extends SimpleArgumentConverter {

    @Override
    protected Object convert(Object source, Class<?> targetType) {
        assertEquals(String.class, targetType, "Can only convert to String");
        return String.valueOf(source);
    }
}
```

明确的参数转换器是由Tester来实现的。 因此，junit-jupiter-params只提供一个单独的显式参数转换器，也可以作为参考实现：JavaTimeArgumentConverter。 它通过组合注解JavaTimeConversionPattern使用。

```java
@ParameterizedTest
@ValueSource(strings = { "01.01.2017", "31.12.2017" })
void testWithExplicitJavaTimeConverter(@JavaTimeConversionPattern("dd.MM.yyyy") LocalDate argument) {
    assertEquals(2017, argument.getYear());
}
```

### 自定义显示名称

默认情况下，参数化测试调用的显示名称包含该特定调用的所有参数的调用索引和String表示形式。 但是，您可以通过@ParameterizedTest注解的name属性来定制显示名称，如下例所示。

```java
@DisplayName("Display name of container")
@ParameterizedTest(name = "{index} ==> first=''{0}'', second={1}")
@CsvSource({ "foo, 1", "bar, 2", "'baz, qux', 3" })
void testWithCustomDisplayNames(String first, int second) {
}
```

当使用ConsoleLauncher执行上述方法时，您将看到类似于以下内容的输出。

```console
Display name of container ✔
├─ 1 ==> first='foo', second=1 ✔
├─ 2 ==> first='bar', second=2 ✔
└─ 3 ==> first='baz, qux', second=3 ✔
```

自定义显示名称中支持以下占位符。

| 占位符 | 描述 |
|--------|--------|
|    {index}    |    当前的调用索引（1）    |
|	{arguments}	|	完整的逗号分隔的参数列表	|
|	{0}, {1}, …	|	指定的某个参数	|

### 生命周期和互操作性

参数化测试的每次调用与常规的@Test方法具有相同的生命周期。 例如，@BeforeEach方法将在每次调用之前执行。 类似于动态测试，调用将在IDE的测试树中逐个显示。 您可以在同一测试类中混合常规的@Test方法和@ParameterizedTest方法。

您可以使用@ParameterizedTest方法的ParameterResolver扩展。 但是，由参数源解析的方法参数需要先在参数列表中。 由于测试类可能包含常规测试以及具有不同参数列表的参数化测试，因此生命周期方法（例如@BeforeEach）和测试类构造函数的参数源中的值不能解析。

```java
@BeforeEach
void beforeEach(TestInfo testInfo) {
    // ...
}

@ParameterizedTest
@ValueSource(strings = "foo")
void testWithRegularParameterResolver(String argument, TestReporter testReporter) {
    testReporter.publishEntry("argument", argument);
}

@AfterEach
void afterEach(TestInfo testInfo) {
    // ...
}
```

## 测试模板

[@TestTemplate](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/TestTemplate.html)方法不是常规测试用例，而是测试用例的模板。 因此，它被设计为可以被多次调用，这取决于registered providers返回的调用上下文的数量。 因此，它必须与注册的[TestTemplateInvocationContextProvider](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/TestTemplateInvocationContextProvider.html)扩展结合使用。 每个调用测试模板方法的行为就像一个常规的@Test方法的执行，并且完全支持相同的生命周期回调和扩展。

## 动态测试

JUnit Jupitr中描述的标准注解@Test跟junit 4中的@Test非常相似，都描述了实现测试用例的方法.这些测试用例是在编译时指定的,这也意味着它是静态的。并且它们无法在运行时发生任何改变，假设你想要提供一种动态的方式,但是由于之前所述的原因而受到了限制.

因此除了标准的@Test测试之外，JUnit Jupiter还引入了一种全新的测试编程模型。 这种新型的测试是一种动态测试，它是在运行时通过使用@ TestFactory.e注解的工厂方法生成的。

与@Test方法相反，@TestFactory方法本身不是测试用例，而是测试用例的工厂。因此，动态测试是工厂的产物。从技术上讲，@TestFactory方法必须返回一个StreamNode，Stream，Collection，Iterable或者是DynamicNode实例的迭代器。 DynamicNode的实例子类是DynamicContainer和DynamicTest。 DynamicContainer实例由显示名称和动态子节点列表组成，可以创建动态节点的任意嵌套层次结构。然后，DynamicTest实例将被延迟执行，从而实现动态甚至非确定性的测试用例生成。

通过调用stream.close（）可以正确地关闭@TestFactory返回的任何Stream，使其可以安全地使用诸如Files.lines（）的资源。

与@Test方法一样，@TestFactory方法不能是private的或static的，并且可以选择声明由ParameterResolvers解析的参数。

DynamicTest是在运行时生成的测试用例。它由显示名称和可执行文件组成。 Executable是一个@FunctionalInterface，这意味着动态测试的实现可以作为lambda表达式或方法引用来提供。

> Dynamic Test Lifecycle
> 动态测试的执行生命周期与标准的@Test情况完全不同。 具体来说，对于单个动态测试，没有生命周期回调。 这意味着@BeforeEach和@AfterEach方法及其对应的扩展回调对于@TestFactory方法执行，但不是针对每个动态测试执行的。 换句话说，如果从动态测试的lambda表达式中的测试实例访问字段，这些字段将不会被由同一@TestFactory方法生成的各个动态测试之间的回调方法或扩展名执行重置(注:意思是所有由同一个@TestFactory方法生成的各个动态测试之间共享字段？)。

从JUnit Jupiter 5.0.0起，动态测试必须始终由工厂方法创建; 但是，这可能会在以后的版本中被registration facility所补充。

> 动态测试目前是一个实验功能。

### 动态测试例子
以下DynamicTestsDemo类演示了测试工厂和动态测试的几个示例。

第一种方法返回无效的返回类型。由于在编译时无法检测到无效的返回类型，因此只能在运行时检测到JUnitException。

接下来的五个方法是非常简单的示例，演示了生成一个Collection，Iterable，Iterator或DynamicTest实例流。这些例子中的大多数并不真正表现出动态行为，而只是原则上证明了支持的返回类型。但是，dynamicTestsFromStream（）和dynamicTestsFromIntStream（）表明为给定的一组字符串或输入数字范围生成动态测试是多么容易。

下一个方法本质上是真正的动态的。 generateRandomNumberOfTests（）实现一个生成随机数的迭代器，显示名称生成器和测试执行程序，然后将所有三个提供给DynamicTest.stream（）。虽然generateRandomNumberOfTests（）的非确定性行为当然与单元测试的可重复性相冲突，因此应该小心使用，它用于展示动态测试的表现力和力量。

最后一个方法使用DynamicContainer生成动态测试的嵌套层次结构。

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertNotNull;
import static org.junit.jupiter.api.Assertions.assertTrue;
import static org.junit.jupiter.api.DynamicContainer.dynamicContainer;
import static org.junit.jupiter.api.DynamicTest.dynamicTest;

import java.util.Arrays;
import java.util.Collection;
import java.util.Iterator;
import java.util.List;
import java.util.Random;
import java.util.function.Function;
import java.util.stream.IntStream;
import java.util.stream.Stream;

import org.junit.jupiter.api.DynamicNode;
import org.junit.jupiter.api.DynamicTest;
import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.TestFactory;
import org.junit.jupiter.api.function.ThrowingConsumer;

class DynamicTestsDemo {

    // This will result in a JUnitException!
    @TestFactory
    List<String> dynamicTestsWithInvalidReturnType() {
        return Arrays.asList("Hello");
    }

    @TestFactory
    Collection<DynamicTest> dynamicTestsFromCollection() {
        return Arrays.asList(
            dynamicTest("1st dynamic test", () -> assertTrue(true)),
            dynamicTest("2nd dynamic test", () -> assertEquals(4, 2 * 2))
        );
    }

    @TestFactory
    Iterable<DynamicTest> dynamicTestsFromIterable() {
        return Arrays.asList(
            dynamicTest("3rd dynamic test", () -> assertTrue(true)),
            dynamicTest("4th dynamic test", () -> assertEquals(4, 2 * 2))
        );
    }

    @TestFactory
    Iterator<DynamicTest> dynamicTestsFromIterator() {
        return Arrays.asList(
            dynamicTest("5th dynamic test", () -> assertTrue(true)),
            dynamicTest("6th dynamic test", () -> assertEquals(4, 2 * 2))
        ).iterator();
    }

    @TestFactory
    Stream<DynamicTest> dynamicTestsFromStream() {
        return Stream.of("A", "B", "C")
            .map(str -> dynamicTest("test" + str, () -> { /* ... */ }));
    }

    @TestFactory
    Stream<DynamicTest> dynamicTestsFromIntStream() {
        // Generates tests for the first 10 even integers.
        return IntStream.iterate(0, n -> n + 2).limit(10)
            .mapToObj(n -> dynamicTest("test" + n, () -> assertTrue(n % 2 == 0)));
    }

    @TestFactory
    Stream<DynamicTest> generateRandomNumberOfTests() {

        // Generates random positive integers between 0 and 100 until
        // a number evenly divisible by 7 is encountered.
        Iterator<Integer> inputGenerator = new Iterator<Integer>() {

            Random random = new Random();
            int current;

            @Override
            public boolean hasNext() {
                current = random.nextInt(100);
                return current % 7 != 0;
            }

            @Override
            public Integer next() {
                return current;
            }
        };

        // Generates display names like: input:5, input:37, input:85, etc.
        Function<Integer, String> displayNameGenerator = (input) -> "input:" + input;

        // Executes tests based on the current input value.
        ThrowingConsumer<Integer> testExecutor = (input) -> assertTrue(input % 7 != 0);

        // Returns a stream of dynamic tests.
        return DynamicTest.stream(inputGenerator, displayNameGenerator, testExecutor);
    }

    @TestFactory
    Stream<DynamicNode> dynamicTestsWithContainers() {
        return Stream.of("A", "B", "C")
            .map(input -> dynamicContainer("Container " + input, Stream.of(
                dynamicTest("not null", () -> assertNotNull(input)),
                dynamicContainer("properties", Stream.of(
                    dynamicTest("length > 0", () -> assertTrue(input.length() > 0)),
                    dynamicTest("not empty", () -> assertFalse(input.isEmpty()))
                ))
            )));
    }
}
```
