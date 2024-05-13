Spring Boot 框架
===
- 學習 Spring Boot 框架的基本觀念 & 功能

[TOC]

## 學習路線
- 2024 年
  ![spring_boot_learning_roadmap](../../assets/pics/framework/spring_boot_learning_roadmap.png)

## 建立 Spring Boot 專案
- **Spring Boot 框架** 實際上背後仍是使用 Spring 框架的功能，只是包裝得更簡單，希望達到 **開箱即用**
- IDE: IntelliJ Ulitimate 版本
- New Project -> Spring Initializr
    - Type(套件管理包工具): Gradle 或 Maven
    - JDK: 選擇 OpenJDK 的版本 (e.g. 17.0.4)
        - 需留意 JDK 與 Java 的版本需一致!
    - Java: 程式語言的版本 (e.g. 17)
        - Java 版本也會與 Spring Boot 版本有相依性
            - Java 11 開發 Spring Boot 2
            - Java 17 開法 Spring Boot 3
    - Packaging: JAR 或 WAR
- IntelliJ IDE 
    - 預設會自動儲存所有程式碼變動
    - MacOS: `option + Enter` 顯示目前紅色波浪錯誤的可能解決方法有哪些
    - 輸入 `sout` = `System.out.println();` 

### 專案目錄結構
```markdown
project-name
|
|--- .idea/
|
|--- .mvn/
|
|--- src/
        |--- main/
                |--- java/  放 Java 程式
                |--- resources/  放 Spring Boot 設定檔
                			|--- static/
                			|--- templates/
                			|--- application.properties 最重要的 Spring Boot 設定檔
        |--- test/
                |--- java/  放測試用的 Java 程式
                |--- resources/ 放測試用的 Spring Boot 設定檔
```

- Spring Boot 設定檔 (**application.properties**)
    
    - 存放 Spring Boot 的設定值
    - 使用 properties 語法: `key=value`
        - `=` 的前後不需加上空白鍵
        - key 可以有 `.` 符號，相當於中文 "的"
        - value 若為 String 型別，不需要加上 `" "`
        - 用 `#` 表示註解 (comment)
    
    
    ```properties
    count=5
    my.name=Hans
    my.age=28
    # this is a comment
    ```
    
- 專案設定檔 (**pom.xml**)

    - 設定 Spring Boot 版本。更新後，需按右鍵 Maven -> Reload Project
        ```xml
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>3.2.5</version>
            <relativePath/> <!-- lookup parent from repository -->
        </parent>
        ```

### Annotation (註解)
- `@SpringBootApplication`: 加在 class 上，執行 Spring Boot 程式用
    
    ```java
    @SpringBootApplication
    public class DemoApplication {
    
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    
    }
    ```

- 若有多個 annotation，其之間沒有順序性

## Spring Core 核心觀念

### Spring IoC

- **IoC** (Inversion of Control, **控制反轉**): 將 object 的控制權，(倒轉)交給了外部的 Spring container 來管理

    - Spring 會預先建立物件，存放在 container 管理 (不用管實際是怎麼 `new()` 出來的)

    	> Spring 先買了一台印表機，之後有人要用印表機的話，就跟 Spring 借來用，而不用管理是什麼廠牌的印表機

- **DI** (Dependency Injection, **依賴注入**): Spring container 將 bean 交給某個 class 使用

- **Bean**: 存放在 Spring container 裡的 object

    - 預設的物件名稱 = @Component class 的首字母小寫

- **@Component 註解**: 加在 **class** 上，將 class 變成 **Spring container 所管理的 bean**

    - 較常使用

- **@Autowired 註解**: 加在 **變數** 上，根據**變數的型別**，去 Spring 容器中尋找相對應的 bean

    - 變數型別盡量使用 Interface，讓 Java 可透過多型進行向上轉型，提高程式碼重用性

- **@Qualifier 註解**: 通常加在 **變數** 上，輔助 @Autowired，用來指定要載入的 bean 名稱

```java
// 介面
public interface Printer {
  void print(String message);
}

// 實作介面
@Component  // bean (可想像成 Spring container 為我們執行 Printer hpPrinter = new HpPrinter();)
public class HpPrinter implements Printer {
  @Override
  public void print(String message) {
    System.out.println("HP 印表機: " + message);
  }
}

// 實作介面
@Component 
public class CanonPrinter implements Printer {
  @Override
  public void print(String message) {
    System.out.println("Canon 印表機: " + message);
  }
}
```

```java
// 原寫法
// public class Teacher {
// 	private Printer printer = new HpPrinter();
  
//   public void teach() {
//     printer.print("I'm a teacher.");
//   }
// }

// IoC 寫法: 由 Container 負責管理 Printer 物件
@Component  // 要使用 DI bean 之前，自己本身也要是 bean
public class Teacher {
  @Autowired  // DI (依賴注入)的用法
  @Qualifier("hpPrinter") // 指定要載入的 bean 名稱
  private Printer printer; // 變數型別盡量使用 Interface (e.g. Printer)
  
  public void teach() {
    printer.print("I'm a teacher.");
  }
}
```

- **@Configuration 註解**: 加在 class 上，表示該 class 是用來 **設定 Spring 用的**
    - 這時候 class 名稱是什麼其實不太重要 (e.g. MyConfiguration)
- **@Bean 註解**: 加在帶有 **@Configuration** 的 class 上，於 Spring container 中建立一個 bean
    - 所建立出來的 bean，預設會是 method 名稱
    - 可以在 @Bean 註解後面指定 bean 的名稱
        - `@Bean("xxx")`


```java
@Configuration
public class MyConfiguration {
  @Bean
  public Printer myPrinter() {
    // 可想像成 Spring container 幫我們執行以下這個 method
    // Printer myPrinter = 該 method 返回的 object (hpPrinter)
    // 所建立出來的 bean，預設會是 method 名稱 (myPrinter)
    return new HpPrinter();
  }
}
```

- Spring 中初始化 bean 的方法
    - **@PostConstruct 註解**: 設定 Spring container 中的 bean 的初始值
        - 較常使用
        - 必須是 **public** method
        - 返回型別必須是 **void**
        - method 名稱可以隨意取
        - method 不能有參數
    - 實作 InitializingBean interface 的 **afterPropertiesSet()** method

```java
// 法 1: @PostConstruct 註解
public class hpPrinter implements Printer {
  private int count;
  
  @PostConstruct
  public void initialize() {
    count = 5;
  }
  
  @Override
  public void print(String message) {
    count--;
    System.out.println("HP 印表機: " + message);
    System.out.println("剩餘使用次數: " + count);
  }
}
```

```java
// 法 2: 實作 InitializingBean interface 的 afterPropertiesSet() method
@Component
public class HpPrinter implements Printer, InitializingBean {
  private int count;
  
  @Override
  public void afterPropertiesSet throws Exception {
    count = 5;
  }
  
  @Override
  public void print(String message) {
    count--;
    System.out.println("HP 印表機: " + message);
    System.out.println("剩餘使用次數: " + count);
  }
}
```

- Bean 生命週期
    - **創建 -> 初始化 -> 可以被別人拿去使用**
    - 當建立 bean 時發現需要依賴其它 bean，則 Spring 會回過頭去 "建立 + 初始化" 那個被依賴的 bean
    - 勿寫出會發生循環依賴的程式碼 (e.g. A 依賴於 B，且 B 依賴於 A)
- **@Value 註解**: 讀取 Spring Boot 設定檔 (`application.properties`) 中，指定的 key 的值
    - 用法: 加在 **bean** 或是**設定 Spring 用的 class** 裡面的 **變數** 上
        - e.g. 帶有 **@Component 註解、@Configuration 註解** 的 **class** 上
    - 語法: `"${ }"`
        - `${unknown:Amy}`: 設定 @Value 註解的預設值 (若找不到相對應 key 的值，才會使用指定的預設值)

```java
@Component
public class MyBean {
  @Value("${count}")
  private Integer numb;  // 5
  
  @Value("$my.name")
  private String name;  // "Hans"
  
  @Value("${unknown:Amy}")
  private String herName; // "Amy"
}
```

- IoC 優點
    - **鬆耦合** (loose coupling): 讓 class 之間的關聯性降低
    - **生命週期管理** (lifecycle management): 由 Spring container 負責該 object 的建立、初始化、銷毀
    - **方便測試程式** (more testable)

### Spring AOP

- **AOP** (Aspect-Oriented Programming, **切面導向程式設計**): 將所有 method 要執行的共同邏輯寫在切面中，並且讓該切面橫貫所有 method，替所有 method 去執行這個共同的邏輯

- 設定 AOP

  - 在 `pom.xml` 中設定後，按右鍵 Maven -> Reload Project 重新載入新的設定

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-aop</artifactId>
  </dependency>
  ```

- **@Aspect 註解**: 加在 **帶有 @Component 註解的 class** 上 (= bean)，用來 **宣告這個 class 是一個切面**

- **切入點(pointcut)** 表達式: 要用到時再查就可以了

- **@Before 註解**: 加在 **切面 class 的 method** 上，於 **切入點的 method 執行之前** 所執行

- **@After 註解**: 加在 **切面 class 的 method** 上，於 **切入點的 method 執行之後** 所執行

- **@Around 註解**: 加在 **切面 class 的 method** 上，於 **切入點的 method 執行之前、後** 所執行

  - `Object obj = pjp.proceed()`: 執行切入點的方法

- **@AfterThrowing 註解**: 加在 **切面 class 的 method** 上，於 **切入點的 method 拋出異常後** 才會執行

    - 較少使用

- **@AfterReturning 註解**: 加在 **切面 class 的 method** 上，於 **切入點的 method 執行成功後** 才會執行

    - 較少使用

```java
@Aspect
@Component
public class MyAspect {
  // 2. @Before 在執行 A 方法之前   // 1. 我們指定的方法 A
  @Before("execution(* com.example.demo.HpPrinter.*(..))")
  // 3. 先讓 Spring 執行這個 before() 方法
  public void before() {
    System.out.println("I'm before");
  }
  
  public void after() {
    System.out.println("I'm after");
  }
}
```

```java
@Aspect
@Component
public class MyAspectAround {
  @Around("execution(* com.example.demo.HpPrinter.*(..))")
  public Object around(ProceedingJoinPoint pjp) throws Throwable {
    System.out.println("I'm around before.");
    // 執行切入點的方法 (e.g. hpPrinter.print();)
    Object obj = pjp.proceed();
    System.out.println("I'm around after.");
    return obj;
  }
}
```

- AOP 使用情境
    - 權限驗證: **Spring Security**
    - 統一的 Exception 處理: **@ControllerAdvice**
    - Log 紀錄

## Spring MVC

## 參考資料
- [Spring 官方網站-Guides](https://spring.io/guides)
- [Developer Roadmaps-Spring Boot Developer](https://roadmap.sh/spring-boot)
- Hahow-Spring Boot  零基礎入門課程 (古君葳)
