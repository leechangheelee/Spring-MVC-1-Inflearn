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

***
  * 요청 매핑
    
    ![image](https://user-images.githubusercontent.com/79301439/162109908-808dbfeb-40af-42cc-a540-6cf26b9b10df.png)
    
    ```java
    package hello.springmvc.basic.requestmapping;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.http.MediaType;
    import org.springframework.web.bind.annotation.*;

    @RestController
    public class MappingController {

        private Logger log = LoggerFactory.getLogger(getClass());

        /**
         * 기본 요청
         * 둘다 허용 /hello-basic, /hello-basic/
         * HTTP 메서드 모두 허용 GET, HEAD, POST, PUT, PATCH, DELETE
         */
        @RequestMapping("/hello-basic")
        public String helloBasic() {
            log.info("helloBasic");
            return "ok";
        }
        
        ...
        
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162110113-19763777-16ff-44b6-b107-e834773321ca.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162110155-232385bf-5cc9-44db-be1a-98e0534a50d5.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162110179-44bde390-9a02-4d0f-a3ca-d4aaeeb32f8f.png)
    
    ```java
    /**
     * method 특정 HTTP 메서드 요청만 허용
     * GET, HEAD, POST, PUT, PATCH, DELETE
     */
    @RequestMapping(value = "/mapping-get-v1", method = RequestMethod.GET)
    public String mappingGetV1() {
        log.info("mappingGetV1");
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162110292-df652cdd-ea30-4d85-abe5-6b37db28bd48.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162110309-eeb7af09-6562-4d7b-8a65-d0b847b9851a.png)
    
    ```java
    /**
     * 편리한 축약 애노테이션 (코드보기)
     * @GetMapping
     * @PostMapping
     * @PutMapping
     * @DeleteMapping
     * @PatchMapping
     */
    @GetMapping(value = "/mapping-get-v2")
    public String mappingGetV2() {
        log.info("mapping-get-v2");
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162110407-144cd46e-dd8c-498e-80cf-69abc05643f6.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162111554-b55d21cd-fdd2-4f78-a6ad-396917604d98.png)
    
    ```java
    /**
     * PathVariable 사용
     * 변수명이 같으면 생략 가능
     * @PathVariable("userId") String userId -> @PathVariable userId
     * /mapping/userA
     */
    @GetMapping("/mapping/{userId}")
    public String mappingPath(@PathVariable("userId") String data) {
        log.info("mappingPath userId={}", data);
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162110559-38eb1cb5-9af8-4780-968e-20ea485a6e31.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162110590-e0ad14b2-3af6-4f30-9053-0d589083ff8c.png)
    
    ```java
    /**
     * PathVariable 사용 다중
     */
    @GetMapping("/mapping/users/{userId}/orders/{orderId}")
    public String mappingPath(@PathVariable String userId, @PathVariable Long orderId) {
        log.info("mappingPath userId={}, orderId={}", userId, orderId);
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162110683-12ff4d53-229e-452e-ba73-aefbd014b041.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162110725-337438ac-8c14-4599-8f6e-27fd94666ada.png)
    
    ```java
    /**
     * 파라미터로 추가 매핑
     * params="mode",
     * params="!mode"
     * params="mode=debug"
     * params="mode!=debug" (! = )
     * params = {"mode=debug","data=good"}
     */
    @GetMapping(value = "/mapping-param", params = "mode=debug")
    public String mappingParam() {
        log.info("mappingParam");
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162110812-3d16e26f-13ab-4e82-8099-63527034fc45.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162110845-9236887d-09ba-4bb6-bfa2-f69fde3f34b9.png)
    
    ```java
    /**
     * 특정 헤더로 추가 매핑
     * headers="mode",
     * headers="!mode"
     * headers="mode=debug"
     * headers="mode!=debug" (! = )
     */
    @GetMapping(value = "/mapping-header", headers = "mode=debug")
    public String mappingHeader() {
        log.info("mappingHeader");
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162110905-7c26ac63-f9c7-4f1d-a606-17be002a667a.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162110927-2dee1b3b-822e-470b-b7c3-a83e94f12f66.png)
    
    ```java
    /**
     * Content-Type 헤더 기반 추가 매핑 Media Type
     * consumes="application/json"
     * consumes="!application/json"
     * consumes="application/*"
     * consumes="*\/*"
     * MediaType.APPLICATION_JSON_VALUE
     */
    @PostMapping(value = "/mapping-consume", consumes = MediaType.APPLICATION_JSON_VALUE)
    public String mappingConsumes() {
        log.info("mappingConsumes");
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162111062-3b6703bd-f024-44e9-ae30-e7967d9fa0a9.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162111084-aa88da49-abed-4591-880b-c3ca31094c5f.png)
    
    ```java
    /**
     * Accept 헤더 기반 Media Type
     * produces = "text/html"
     * produces = "!text/html"
     * produces = "text/*"
     * produces = "*\/*"
     */
    @PostMapping(value = "/mapping-produce", produces = MediaType.TEXT_HTML_VALUE)
    public String mappingProduces() {
        log.info("mappingProduces");
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162111152-890ba5f5-924d-44ca-a891-f198fe8d09ee.png)
