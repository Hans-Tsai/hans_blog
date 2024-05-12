Spring Boot 框架
===
- 學習 Spring Boot 框架的基本觀念 & 功能

[TOC]

## 學習路線
- 2024 年
  ![spring_boot_learning_roadmap](../../assets/pics/framework/spring_boot_learning_roadmap.png)

## 建立 Spring Boot 專案
> IDE: IntelliJ Ulitimate 版本
- New Project -> Spring Initializr
    - Type(套件管理包工具): Gradle 或 Maven
    - JDK: 選擇 OpenJDK 的版本 (e.g. 17.0.4)
        - 需留意 JDK 與 Java 的版本需一致!
    - Java: 程式語言的版本 (e.g. 17)
        - Java 版本也會與 Spring Boot 版本有相依性
            - Java 11 開發 Spring Boot 2
            - Java 17 開法 Spring Boot 3
    - Packaging: JAR 或 WAR
- IntelliJ IDE 預設會自動儲存所有程式碼變動

### 專案目錄結構
```markdown
project-name
|
| --- .idea/
|
| --- .mvn/
|
| --- src/
        |--- main/
                |--- java/  放 Java 程式
                |--- resources/  放 Spring Boot 設定檔
        |--- test/
                |--- java/  放測試用的 Java 程式
                |--- resources/ 放測試用的 Spring Boot 設定檔
```
- 專案設定檔 (**pom.xml**)
    - 設定 Spring Boot 版本。更新後，需按右鍵 Maven -> Reload Project
        - ```xml
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

## 基本觀念
- **Spring Boot 框架** 實際上背後仍是使用 Spring 框架的功能，只是包裝得更簡單，希望達到 **開箱即用**

### Spring IoC

### Spring AOP

## Spring MVC

## 參考資料
- [Spring 官方網站-Guides](https://spring.io/guides)
- [Developer Roadmaps-Spring Boot Developer](https://roadmap.sh/spring-boot)
