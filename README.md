# CucumberDriver

一个基于 **Cucumber + Selenium WebDriver** 的行为驱动开发（BDD）自动化测试框架，采用 Page Object 设计模式，支持多浏览器测试。

## 功能特性

- **BDD 模式**：使用 Gherkin 语言编写自然语言测试用例
- **Page Object 模式**：页面元素与操作分离，便于维护
- **多浏览器支持**：Chrome、Firefox、Internet Explorer
- **智能等待机制**：统一管理 WebDriver 等待策略
- **丰富报告**：集成 ExtentReports + Cucumber Reports
- **上下文管理**：支持场景级和全局测试数据共享
- **中文支持**：原生支持中文 Gherkin 语法

## 技术栈

| 类别 | 技术 |
|------|------|
| 测试框架 | Cucumber 1.2.5 + JUnit 4.13.1 |
| Web 自动化 | Selenium WebDriver |
| 构建工具 | Maven 3.x |
| 报告 | ExtentReports 3.0.6 |
| Java 版本 | JDK 8+ |

## 快速开始

### 环境要求

- JDK 1.8 或更高版本
- Maven 3.x
- Chrome/Firefox/IE 浏览器

### 运行测试

```bash
# 运行所有测试
mvn test

# 运行特定 Runner
mvn test -Dtest=TestRunner

# 按标签运行
mvn test -Dcucumber.filter.tags="@Regression"
```

### 项目结构

```
CucumberDriver/
├── configs/                          # 配置文件
│   └── Configuration.properties
├── drivers/                          # WebDriver 驱动
│   └── chromedriver.exe
├── src/
│   ├── main/java/com/cucumber/
│   │   ├── PageObjectManager/        # Page Object 管理器
│   │   ├── contexts/                 # 测试上下文
│   │   ├── dataProviders/           # 数据提供器
│   │   ├── enums/                   # 枚举常量
│   │   ├── extentreporter/          # 报告工具
│   │   ├── managers/               # WebDriver 管理
│   │   ├── pageObjects/           # 页面对象
│   │   └── testDataType/          # 数据类型
│   └── test/
│       ├── features/               # Gherkin 特性文件
│       ├── java/
│       │   ├── runners/            # 测试运行器
│       │   └── stepdefinition/    # 步骤定义
│       └── resources/             # 报告配置
└── pom.xml
```

### 核心组件

#### WebDriverManager

负责 WebDriver 生命周期管理，支持多种浏览器：

```java
WebDriverManager manager = new WebDriverManager();
WebDriver driver = manager.getDriver();
```

#### PageObjectManager

统一管理所有页面对象：

```java
PageObjectManager pom = new PageObjectManager(driver);
LoginPage loginPage = pom.getLoginPage();
```

#### TestContext & ScenarioContext

- **TestContext**：全局测试上下文，管理跨场景共享数据
- **ScenarioContext**：场景级上下文，存储当前场景数据

#### Hooks

提供测试生命周期钩子：

- `@Before`：测试前准备（打开浏览器、初始化数据）
- `@After`：测试后清理（截图、关闭浏览器）

### 编写测试

#### 1. 创建 Feature 文件

```gherkin
# language: zh-CN
功能: 百度搜索测试

  @Smoke
  场景: 执行搜索操作
    假如 打开浏览器
    当 输入搜索关键词 "Cucumber"
    并且 点击搜索按钮
    那么 显示搜索结果
```

#### 2. 实现步骤定义

```java
public class SearchSteps {
    private TestContext testContext;

    public SearchSteps(TestContext context) {
        this.testContext = context;
    }

    @Given("^打开浏览器$")
    public void openBrowser() {
        // 实现步骤
    }
}
```

#### 3. 创建页面对象

```java
public class SearchPage extends BasePage {
    @FindBy(id = "kw")
    private WebElement searchInput;

    public SearchPage enterKeyword(String keyword) {
        sendKeys(searchInput, keyword);
        return this;
    }
}
```

### 配置文件

`configs/Configuration.properties`:

```properties
environment=local
browser=chrome
driverPath=drivers/chromedriver.exe
implicitlyWait=10
url=https://www.baidu.com
windowMaximize=true
reportConfigPath=src/test/resources/extent-config.xml
```

## 测试报告

运行后在以下位置查看报告：

| 报告类型 | 路径 |
|----------|------|
| Cucumber JSON | `cucumber-reports/Cucumber.json` |
| Cucumber XML | `cucumber-reports/Cucumber.xml` |
| Extent HTML | `cucumber-reports/Jhtml/index.html` |
| JUnit HTML | `test-output/html/index.html` |

## 常用标签

| 标签 | 说明 |
|------|------|
| `@Smoke` | 冒烟测试 |
| `@Regression` | 回归测试 |
| `@WIP` | 正在进行 |
| `@Chinese` | 中文测试 |

## 中文支持

Cucumber 支持中文关键字，步骤定义中使用中文：

```gherkin
# language: zh-CN
功能: 中文测试
  场景: 测试中文步骤
    假如 我打开登录页面
    当 我输入用户名 "admin"
    那么 显示登录成功
```

```java
@Given("^我打开登录页面$")
public void openLoginPage() {
    // implementation
}
```

## 扩展指南

### 添加新页面对象

1. 在 `pageObjects/` 创建类继承 `BasePage`
2. 使用 `@FindBy` 注解声明元素
3. 在 `PageObjectManager` 中添加获取方法

### 添加新测试数据源

1. 在 `testDataResources/` 添加 JSON 文件
2. 使用 `JsonDataReader` 读取数据
3. 通过 `@DataProvider` 驱动测试

### 添加新浏览器支持

1. 在 `DriverType` 枚举添加浏览器类型
2. 在 `WebDriverManager.createLocalDriver()` 添加创建逻辑
3. 在 `Configuration.properties` 配置驱动路径

## 常见问题

1. **WebDriver 版本不匹配**：确保 chromedriver 与浏览器版本一致
2. **元素找不到**：增加等待时间或检查定位器是否正确
3. **截图失败**：检查截图保存路径是否有写权限

## License

MIT License
