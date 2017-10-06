# Release Log

## 5.0.1

### 发布日期: 2017年10月3日

### 范围: 5.0.0版本的错误修复

有关此版本的所有已关闭问题和提取请求的完整列表，请参阅GitHub上的JUnit存储库中的[5.0.1](https://github.com/junit-team/junit5/milestone/16?closed=1)页面。

### JUnit Platform

没有代码修改

### JUnit Jupiter

Bug 修复
如果测试类没有声明JUnit 4 ExpectedException  rule，则junit-jupiter-migrationsupport模块的ExpectedExceptionSupport不再会吞掉异常。

因此，现在可以使用@EnableRuleMigrationSupport和ExpectedExceptionSupport，而不声明ExpectedException Rule。

### JUnit Vintage
Bug修复

PackageNameFilter现在应用于通过ClassSelector，MethodSelector或UniqueIdSelector选择的测试。