## **스프링 MVC - 웹 페이지 만들기**

![image](https://user-images.githubusercontent.com/79301439/162224596-d5d36f72-6c8f-4ce8-bc62-3a9c1e1c8fae.png)

***
  * 프로젝트 생성
    
    ![image](https://user-images.githubusercontent.com/79301439/162344101-6c988ace-ecb6-4000-bea5-fbc72d48de08.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162344161-21d2f1a8-4aa8-4c71-95b6-128c5ee97616.png)
    
    ```gradle
    plugins {
        id 'org.springframework.boot' version '2.6.6'
        id 'io.spring.dependency-management' version '1.0.11.RELEASE'
        id 'java'
    }

    group = 'hello'
    version = '0.0.1-SNAPSHOT'
    sourceCompatibility = '11'

    configurations {
        compileOnly {
            extendsFrom annotationProcessor
        }
    }

    repositories {
        mavenCentral()
    }

    dependencies {
        implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
        implementation 'org.springframework.boot:spring-boot-starter-web'
        compileOnly 'org.projectlombok:lombok'
        annotationProcessor 'org.projectlombok:lombok'
        testImplementation 'org.springframework.boot:spring-boot-starter-test'
    }

    tasks.named('test') {
        useJUnitPlatform()
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162344272-f5c159d6-cd91-4352-afe5-acaf64974c53.png)
    
    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
    <ul>
        <li>상품 관리
            <ul>
                <li><a href="/basic/items">상품 관리 - 기본</a></li>
            </ul>
        </li>
    </ul>
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162344383-29257520-1756-4db7-8c76-172600a7dc09.png)
