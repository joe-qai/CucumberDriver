# CucumberDriver 项目规范

## 项目概述

CucumberDriver 是一个基于 **Cucumber + Selenium WebDriver** 的行为驱动开发（BDD）自动化测试框架，使用 Java/Maven 构建。

## 技术栈

- **测试框架**: Cucumber 7.x + TestNG
- **Web 自动化**: Selenium WebDriver 4.x
- **构建工具**: Maven
- **报告**: ExtentReports + Cucumber Reports
- **编程语言**: Java 8+

## 项目结构

```
CucumberDriver/
├── configs/
│   └── Configuration.properties          # 全局配置文件
├── drivers/
│   └── chromedriver.exe                  # Chrome 驱动
├── src/
│   ├── main/java/com/cucumber/
│   │   ├── PageObjectManager/            # Page Object 管理器
│   │   │   └── PageObjectManager.java
│   │   ├── constants/                    # 常量定义
│   │   │   └── Constants.java
│   │   ├── contexts/                     # 测试上下文
│   │   │   ├── ScenarioContext.java     # 场景上下文（存储场景级数据）
│   │   │   └── TestContext.java         # 测试上下文（全局数据）
│   │   ├── dataProviders/               # 数据提供器
│   │   │   ├── ConfigFileReader.java    # 配置文件读取
│   │   │   └── JsonDataReader.java      # JSON 数据读取
│   │   ├── enums/                       # 枚举类型
│   │   │   ├── Context.java             # 上下文枚举
│   │   │   ├── DriverType.java          # 驱动类型
│   │   │   └── EnvironmentType.java     # 环境类型
│   │   ├── extentreporter/              # 报告相关
│   │   │   ├── ExtentCucumberFormatter.java
│   │   │   └── Screenshots.java         # 截图工具
│   │   ├── managers/                    # 管理类
│   │   │   ├── FileReaderManager.java   # 文件读取管理
│   │   │   ├── Wait.java                # 等待策略
│   │   │   └── WebDriverManager.java    # WebDriver 管理
│   │   ├── pageObjects/                 # 页面对象
│   │   │   ├── BasePage.java            # 基础页面类
│   │   │   ├── IndexPage.java           # 索引页面
│   │   │   ├── LoginPage.java           # 登录页面
│   │   │   └── SearchPage.java          # 搜索页面
│   │   └── testDataType/                # 测试数据类型
│   │       ├── Customer.java
│   │       └── TestJsonData.java
│   ├── test/
│   │   ├── features/                     # Gherkin 功能文件
│   │   │   ├── BaiduSearch.feature
│   │   │   ├── chineseTest.feature
│   │   │   ├── dataProvider.feature
│   │   │   ├── loginQQEmail.feature
│   │   │   ├── parameters.feature
│   │   │   ├── searchPage.feature
│   │   │   └── testPage.feature
│   │   ├── java/
│   │   │   ├── com/selenium/testcases/  # 测试用例
│   │   │   ├── runners/                 # 测试运行器
│   │   │   │   ├── TestNgRunner.java
│   │   │   │   └── TestRunner.java
│   │   │   └── stepdefinition/          # 步骤定义
│   │   │       ├── BaiduSearch.java
│   │   │       ├── DataProvider.java
│   │   │       ├── Hooks.java           # 生命周期钩子
│   │   │       ├── LoginSteps.java
│   │   │       ├── Parameters.java
│   │   │       ├── SearchSteps.java
│   │   │       └── TestSteps.java
│   │   └── resources/
│   │       └── extent-config.xml        # Extent 报告配置
│   └── testDataResources/
│       └── Customer.json                # 测试数据
├── pom.xml                              # Maven 依赖配置
└── README.md                            # 项目说明
```

## 核心模块说明

### 1. Page Object 模式

页面对象存放在 `src/main/java/com/cucumber/pageObjects/` 目录下：

- **BasePage.java**: 所有页面的基类，提供公共方法（等待、截图、元素操作等）
- **LoginPage.java**: 登录页面对象
- **IndexPage.java**: 索引页面对象
- **SearchPage.java**: 搜索页面对象

**规范**:
- 每个页面对应一个 `.java` 文件
- 页面类继承 `BasePage`
- 页面元素使用 `@FindBy` 注解声明
- 页面方法返回当前页面对象（链式调用）或具体数据类型

### 2. 步骤定义（Step Definitions）

步骤定义存放在 `src/test/java/stepdefinition/` 目录下：

- **Hooks.java**: 包含 `@Before`, `@After` 钩子方法，用于 setup/teardown
- 其他 `*Steps.java` 文件: 对应各自 feature 文件的步骤实现

**规范**:
- 每个 feature 文件对应一个步骤定义类
- 使用 `@Given`, `@When`, `@Then`, `@And`, `@But` 注解
- 步骤文本使用正则表达式或 Cucumber 表达式

### 3. 上下文管理（Context Pattern）

使用 `ScenarioContext` 和 `TestContext` 管理测试数据：

- **ScenarioContext**: 存储场景级数据，测试结束后清空
- **TestContext**: 存储跨场景的全局数据

### 4. WebDriver 管理

`WebDriverManager.java` 负责：
- WebDriver 实例的创建
- 浏览器类型管理（Chrome、Firefox、Edge 等）
- 浏览器配置（窗口大小、隐身模式等）

### 5. 等待策略

`Wait.java` 提供智能等待机制：
- 显式等待
- 条件等待（元素可点击、可见、存在等）

### 6. 数据提供器

- **ConfigFileReader.java**: 读取 `configs/Configuration.properties`
- **JsonDataReader.java**: 读取 JSON 测试数据文件

## 运行测试

### 通过 Maven 运行

```bash
# 运行所有测试
mvn test

# 运行特定测试类
mvn test -Dtest=TestRunner

# 运行特定 feature
mvn test -Dcucumber.filter.tags="@tagName"

# 生成报告
mvn verify
```

### 通过 TestNG 运行

使用 `src/test/java/runners/` 下的运行器：
- `TestRunner.java`: 标准 Cucumber 运行器
- `TestNgRunner.java`: 结合 TestNG 的运行器

## Feature 文件规范

```gherkin
功能: 登录功能
  场景: 用户成功登录
    假设 我打开登录页面
    当 我输入用户名 "admin" 和密码 "123456"
    并且 点击登录按钮
    那么 我应该看到欢迎页面
```

### 标签（Tags）使用

- `@smoke`: 冒烟测试
- `@regression`: 回归测试
- `@wip`: 正在进行的工作

## 报告输出

测试报告生成在以下位置：

- **Cucumber JSON**: `cucumber-reports/Cucumber.json`
- **Cucumber XML**: `cucumber-reports/Cucumber.xml`
- **Extent HTML**: `cucumber-reports/Jhtml/index.html` 或 `Thtml/index.html`
- **TestNG HTML**: `test-output/html/index.html`

## 配置管理

### Configuration.properties

```properties
driver.type=chrome
environment=local
base.url=https://example.com
implicit.wait=10
explicit.wait=30
page.load.timeout=60
screenshot.path=target/screenshots/
```

### 枚举类型

| 枚举 | 用途 |
|------|------|
| `DriverType` | 浏览器类型（CHROME、FIREFOX、EDGE） |
| `EnvironmentType` | 环境类型（LOCAL、REMOTE） |
| `Context` | 上下文键值（USER_TOKEN、SEARCH_RESULT 等） |

## 开发规范

### Java 代码规范

1. **命名规范**:
   - 类名：PascalCase（如 `LoginPage`）
   - 方法名：camelCase（如 `enterUsername`）
   - 常量：UPPER_SNAKE_CASE（如 `DEFAULT_TIMEOUT`）

2. **Page Object 规范**:
   ```java
   public class LoginPage extends BasePage {
       @FindBy(id = "username")
       private WebElement usernameInput;

       public LoginPage enterUsername(String username) {
           sendKeys(usernameInput, username);
           return this;
       }
   }
   ```

3. **步骤定义规范**:
   ```java
   @Given("^用户打开登录页面$")
   public void userOpensLoginPage() {
       loginPage.open();
   }
   ```

### 异常处理

- 使用 `try-catch` 捕获预期异常
- 使用 `Assert.assertEquals()` 进行断言
- 关键失败点应截图保存

### 日志记录

- 使用合适的日志级别（INFO、DEBUG、ERROR）
- 关键操作前记录日志
- 异常信息完整记录

## 常见问题排查

1. **WebDriver 版本不匹配**: 确保 chromedriver 版本与 Chrome 浏览器版本匹配
2. **元素找不到**: 检查元素定位符是否正确，增加等待时间
3. **测试并行执行问题**: 确保每个测试用例独立的测试数据
4. **截图失败**: 检查截图保存路径是否有写权限

## 扩展指南

### 添加新页面对象

1. 在 `pageObjects/` 创建新类继承 `BasePage`
2. 使用 `@FindBy` 注解声明元素
3. 封装页面操作方法
4. 在 `PageObjectManager` 中注册

### 添加新功能测试

1. 在 `features/` 创建 `.feature` 文件
2. 编写 Gherkin 场景
3. 在 `stepdefinition/` 创建对应的步骤定义类
4. 实现步骤方法

### 添加新数据驱动测试

1. 在 `testDataResources/` 添加 JSON 数据文件
2. 使用 `JsonDataReader` 读取数据
3. 在 feature 或测试类中使用 `@DataProvider`
