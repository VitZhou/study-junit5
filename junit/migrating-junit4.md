# 迁移JUnit 4

尽管JUnit Jupiter编程模型和扩展模型本身不支持JUnit 4功能，如规则和运行器，但并不期望源代码维护者需要更新其现有的所有测试，测试扩展和自定义构建测试基础架构以进行迁移 到JUnit Jupiter。

相反，JUnit通过JUnit Vintage测试引擎提供了一个平缓的迁移路径，该引擎允许使用JUnit Platform基础架构执行基于JUnit 3和JUnit 4的现有测试。 由于JUnit Jupiter特有的所有类和注解位于新的org.junit.jupiter基础包下，因此在classpath中同时具有JUnit 4和JUnit Jupiter不会导致任何冲突。因此，与JUnit Jupiter测试一起保持现有的JUnit 4测试是安全的。 此外，由于JUnit团队将继续为JUnit 4.x基准提供维护和错误修复版本，因此开发人员有足够的时间按照自己的时间安排迁移到JUnit Jupiter。

## 在JUnit Platform上运行JUnit 4测试

只需确保junit-vintage-engine artifact位于测试运行时路径中。JUnit 3和JUnit 4测试就会由JUnit Platform launcher自动获取。

## 迁移技巧

以下是将现有JUnit 4测试迁移到JUnit Jupiter时需要注意的事项。

- 注解位于org.junit.jupiter.api包中
- 断言在org.junit.jupiter.api.Assertions包中。
- Assumption(前置条件)位于org.junit.jupiter.api.Assumptions中。
- @Before和@After不再存在; 使用@BeforeEach和@AfterEach代替。
- @BeforeClass和@AfterClass不再存在; 使用@BeforeAll和@AfterAll代替。
- @Ignore不再存在：改用@Disabled。
- @Category不再存在; 使用@Tag代替。
- @RunWith不再存在 被@ExtendWith取代。
- @Rule和@ClassRule不再存在; 被@ExtendWith取代; 有关部分规则支持，请参阅以下部分。

## 对JUnit 4规则的有限支持

如上所述，JUnit Jupiter不会也不会原生支持JUnit 4 rule。 然而，JUnit团队意识到许多组织，特别是大型组织，可能会拥有大量的使用自定义rule的JUnit 4代码库。 为了服务这些组织并启用渐进的迁移路径，JUnit团队决定在JUnit Jupiter中有限地支持JUnit 4规则的选择。 该支持基于适配器，并且限于与JUnit Jupiter扩展模型语义兼容的那些规则，即那些不完全改变测试的整体执行流程的规则。

JUnit Jupiter目前支持以下三种规则类型，包括这些类型的子类：

- org.junit.rules.ExternalResource(包括org.junit.rules.TemporaryFolder)
- org.junit.rules.Verifier(包括org.junit.rules.ErrorCollector)
- org.junit.rules.ExpectedException

在JUnit 4中，支持规则注解字段以及方法。 通过在测试类上使用这些类级别的扩展，可以保留原有代码库中的规则实现，包括JUnit 4规则导入语句。

可以通过类级别注解org.junit.jupiter.migrationsupport.rules.EnableRuleMigrationSupport打开这种有限形式的规则支持。 此注解是一个组合的注释，它支持所有迁移支持扩展：VerifierSupport，ExternalResourceSupport和ExpectedExceptionSupport。

但是，如果您打算为JUnit 5开发一个新扩展，请使用JUnit Jupiter的新扩展模型，而不是基于JUnit 4的基于规则的模型。

> JUnit JUnit 4 JUnit Jupiter中的规则支持目前是一个实验功能