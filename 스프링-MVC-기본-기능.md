## **스프링 MVC - 기본 기능**

![image](https://user-images.githubusercontent.com/79301439/161980057-40e8995f-1b2d-4c1f-bb14-b5718aa27ed1.png)

***
  * 프로젝트 생성
    
    ![image](https://user-images.githubusercontent.com/79301439/161978979-edaab4a8-a412-472f-874d-1335df062e46.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161979071-59a34add-eff9-460d-8c2e-d0108769c4d8.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161979099-bde65eba-4246-43f0-8791-c13eedfca01a.png)
    
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
    
    ![image](https://user-images.githubusercontent.com/79301439/161979346-0de19884-814a-41a4-866f-34be00da5d61.png)
    
    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
    <ul>
        <li>로그 출력
            <ul>
                <li><a href="/log-test">로그 테스트</a></li>
            </ul>
        </li>
        <!-- -->
        <li>요청 매핑
            <ul>
                <li><a href="/hello-basic">hello-basic</a></li>
                <li><a href="/mapping-get-v1">HTTP 메서드 매핑</a></li>
                <li><a href="/mapping-get-v2">HTTP 메서드 매핑 축약</a></li>
                <li><a href="/mapping/userA">경로 변수</a></li>
                <li><a href="/mapping/users/userA/orders/100">경로 변수 다중</a></li>
                <li><a href="/mapping-param?mode=debug">특정 파라미터 조건 매핑</a></li>
                <li><a href="/mapping-header">특정 헤더 조건 매핑(POST MAN 필요)</a></
                li>
                <li><a href="/mapping-consume">미디어 타입 조건 매핑 Content-Type(POST
                    MAN 필요)</a></li>
                <li><a href="/mapping-produce">미디어 타입 조건 매핑 Accept(POST MAN
                    필요)</a></li>
            </ul>
        </li>
        <li>요청 매핑 - API 예시
            <ul>
                <li>POST MAN 필요</li>
            </ul>
        </li>
        <li>HTTP 요청 기본
            <ul>
                <li><a href="/headers">기본, 헤더 조회</a></li>
            </ul>
        </li>
        <li>HTTP 요청 파라미터
            <ul>
                <li><a href="/request-param-v1?username=hello&age=20">요청 파라미터
                    v1</a></li>
                <li><a href="/request-param-v2?username=hello&age=20">요청 파라미터
                    v2</a></li>
                <li><a href="/request-param-v3?username=hello&age=20">요청 파라미터
                    v3</a></li>
                <li><a href="/request-param-v4?username=hello&age=20">요청 파라미터
                    v4</a></li>
                <li><a href="/request-param-required?username=hello&age=20">요청
                    파라미터 필수</a></li>
                <li><a href="/request-param-default?username=hello&age=20">요청
                    파라미터 기본 값</a></li>
                <li><a href="/request-param-map?username=hello&age=20">요청 파라미터
                    MAP</a></li>
                <li><a href="/model-attribute-v1?username=hello&age=20">요청 파라미터
                    @ModelAttribute v1</a></li>
                <li><a href="/model-attribute-v2?username=hello&age=20">요청 파라미터
                    @ModelAttribute v2</a></li>
            </ul>
        </li>
        <li>HTTP 요청 메시지
            <ul>
                <li>POST MAN</li>
            </ul>
        </li>
        <li>HTTP 응답 - 정적 리소스, 뷰 템플릿
            <ul>
                <li><a href="/basic/hello-form.html">정적 리소스</a></li>
                <li><a href="/response-view-v1">뷰 템플릿 v1</a></li>
                <li><a href="/response-view-v2">뷰 템플릿 v2</a></li>
            </ul>
        </li>
        <li>HTTP 응답 - HTTP API, 메시지 바디에 직접 입력
            <ul>
                <li><a href="/response-body-string-v1">HTTP API String v1</a></li>
                <li><a href="/response-body-string-v2">HTTP API String v2</a></li>
                <li><a href="/response-body-string-v3">HTTP API String v3</a></li>
                <li><a href="/response-body-json-v1">HTTP API Json v1</a></li>
                <li><a href="/response-body-json-v2">HTTP API Json v2</a></li>
            </ul>
        </li>
    </ul>
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161979501-633694b8-ae14-44ac-bb10-3f61f40d3aee.png)

***
  * 로깅 간단히 알아보기
    
    ![image](https://user-images.githubusercontent.com/79301439/162102542-29ebec2d-a94c-4e29-be89-2f5c0b61dcee.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162102705-a966eb4c-500d-43b8-a160-f705b75fdc32.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162102734-d6287aa0-c7da-4a3b-b983-3d1152b3a80d.png)
    
    ```java
    package hello.springmvc.basic;

    import lombok.extern.slf4j.Slf4j;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;

    //@Slf4j
    @RestController
    public class LogTestController {

        //@Slf4j 롬복 쓰면 아래행 생략가능
        private final Logger log = LoggerFactory.getLogger(getClass());

        @RequestMapping("/log-test")
        public String logTest() {
            String name = "Spring";

            log.trace("trace log={}", name);
            log.debug("debug log={}", name);
            log.info("info log={}", name);
            log.warn("warn log={}", name);
            log.error("error log={}", name);

            //로그를 사용하지 않아도 a+b 계산 로직이 먼저 실행됨, 이런 방식으로 사용하면 X
            log.debug("String concat log=" + name);
            return "ok";
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162102880-dc9f8d7f-5653-43b0-a228-cf8ee3e4e4bd.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162102925-d8be873e-35d1-4cc1-bbb8-c35e71ffc77a.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162103012-df166ccd-768f-42c5-9bce-832c92af72f2.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162103079-f3e0a95e-7c05-4fb5-93cc-ec2f9a53f918.png)
