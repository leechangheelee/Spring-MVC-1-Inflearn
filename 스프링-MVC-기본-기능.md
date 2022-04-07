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

***
  * 요청 매핑 - API 예시
    
    ![image](https://user-images.githubusercontent.com/79301439/162118750-a0b75ff2-b239-4e11-ad6f-f735aee21b87.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162118773-6dfde9a8-2ed8-457e-8d74-6100d1d04d46.png)
    
    ```java
    package hello.springmvc.basic.requestmapping;

    import org.springframework.web.bind.annotation.*;

    @RestController
    @RequestMapping("/mapping/users")
    public class MappingClassController {

        /**
         * 회원 목록 조회: GET /users
         * 회원 등록: POST /users
         * 회원 조회: GET /users/{userId}
         * 회원 수정: PATCH /users/{userId}
         * 회원 삭제: DELETE /users/{userId}
         */

        @GetMapping
        public String user() {
            return "get users";
        }

        @PostMapping
        public String addUser() {
            return "post user";
        }

        @GetMapping("/{userId}")
        public String findUser(@PathVariable String userId) {
            return "get userId=" + userId;
        }

        @PatchMapping("/{userId}")
        public String updateUser(@PathVariable String userId) {
            return "update userId=" + userId;
        }

        @DeleteMapping("/{userId}")
        public String deleteUser(@PathVariable String userId) {
            return "delete userId=" + userId;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162118875-6d20e9cd-4067-4341-ada0-30f60f3aa9e5.png)

***
  * HTTP 요청 - 기본, 헤더 조회
    
    ![image](https://user-images.githubusercontent.com/79301439/162131605-942dc6b8-7441-43bc-953c-9e8dc80cbaae.png)
    
    ```java
    package hello.springmvc.basic.request;

    import lombok.extern.slf4j.Slf4j;
    import org.springframework.http.HttpMethod;
    import org.springframework.util.MultiValueMap;
    import org.springframework.web.bind.annotation.CookieValue;
    import org.springframework.web.bind.annotation.RequestHeader;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;

    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.util.Locale;

    @Slf4j
    @RestController
    public class RequestHeaderController {

        @RequestMapping("/headers")
        public String headers(HttpServletRequest request,
                              HttpServletResponse response,
                              HttpMethod httpMethod,
                              Locale locale,
                              @RequestHeader MultiValueMap<String, String> headerMap,
                              @RequestHeader("host") String host,
                              @CookieValue(value = "myCookie", required = false) String cookie
                              ) {

            log.info("request={}", request);
            log.info("response={}", response);
            log.info("httpMethod={}", httpMethod);
            log.info("locale={}", locale);
            log.info("headerMap={}", headerMap);
            log.info("header host={}", host);
            log.info("myCookie={}", cookie);

            return "ok";
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162131780-4105c4a0-9480-4bb1-89f4-77e3d02c8c83.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162131860-481732e8-f0b4-4a71-ac00-ae05e73f9ffc.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162131937-604836e8-b5dd-46ec-895c-2584ea5d08f2.png)

***
  * HTTP 요청 파라미터 - 쿼리 파라미터, HTML Form
    
    ![image](https://user-images.githubusercontent.com/79301439/162133936-7d294058-78a0-4152-a4f4-cae18d538228.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162134019-43ca596f-537b-4893-8b52-cf4b08af4b36.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162134048-53974213-979b-4856-b2e0-c64e377c85dc.png)
    
    ```java
    package hello.springmvc.basic.request;

    import lombok.extern.slf4j.Slf4j;
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;

    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;

    @Slf4j
    @Controller
    public class RequestParamController {

        /**
         * 반환 타입이 없으면서 이렇게 응답에 값을 직접 집어넣으면, view 조회X
         */
        @RequestMapping("/request-param-v1")
        public void requestParamV1(HttpServletRequest request, HttpServletResponse response) throws IOException {
            String username = request.getParameter("username");
            int age = Integer.parseInt(request.getParameter("age"));
            log.info("username={}, age={}", username, age);

            response.getWriter().write("ok");
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162134180-a5fa7824-51d0-4831-b7ef-df9b820cf589.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162134229-15b9e71c-3cf3-4e62-992c-fc6cb82f234a.png)
    
    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
    <form action="/request-param-v1" method="post">
        username: <input type="text" name="username" />
        age: <input type="text" name="age" />
        <button type="submit">전송</button>
    </form>
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162134341-07a2d869-c92b-4988-b8bf-08a8f3ea9103.png)

***
  * HTTP 요청 파라미터 - @RequestParam
    
    ![image](https://user-images.githubusercontent.com/79301439/162139608-6bab662f-6cfc-45bb-b676-570a0df6fcd9.png)
    
    ```java
    /**
     * @RequestParam 사용
     * - 파라미터 이름으로 바인딩
     * @ResponseBody 추가
     * - View 조회를 무시하고, HTTP message body에 직접 해당 내용 입력
     */
    @ResponseBody
    @RequestMapping("/request-param-v2")
    public String requestParamV2(
            @RequestParam("username") String memberName,
            @RequestParam("age") int memberAge) {

        log.info("username={}, age={}", memberName, memberAge);
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162140032-639359d7-8735-4e6c-8c54-415f05128f7c.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162140168-24b36bb2-f115-42a6-a090-104511787849.png)
    
    ```java
    /**
     * @RequestParam 사용
     * HTTP 파라미터 이름이 변수 이름과 같으면 @RequestParam(name="xx") 생략 가능
     */
    @ResponseBody
    @RequestMapping("/request-param-v3")
    public String requestParamV3(
            @RequestParam String username,
            @RequestParam int age) {

        log.info("username={}, age={}", username, age);
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162140317-9fb3cae2-04b7-4e66-8a0b-4646647afab0.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162140349-cb9c5104-43f2-42fe-aa89-8f6db189ba54.png)
    
    ```java
    /**
     * @RequestParam 사용
     * String, int 등의 단순 타입이면 @RequestParam 도 생략 가능
     */
    @ResponseBody
    @RequestMapping("/request-param-v4")
    public String requestParamV4(String username, int age) {

        log.info("username={}, age={}", username, age);
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162140465-62842fab-7e58-46ac-9155-96b5a1eefd32.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162140501-a5d45d1a-79e4-42f7-bd17-40ded9b792e3.png)
    
    ```java
    /**
     * @RequestParam.required
     * /request-param -> username이 없으므로 예외
     *
     * 주의!
     * /request-param?username= -> 빈문자로 통과
     *
     * 주의!
     * /request-param
     * int age -> null을 int에 입력하는 것은 불가능, 따라서 Integer 변경해야 함(또는 다음에 나오는 defaultValue 사용)
     */
    @ResponseBody
    @RequestMapping("/request-param-required")
    public String requestParamRequired(
            @RequestParam(required = true) String username,
            @RequestParam(required = false) Integer age) {

        log.info("username={}, age={}", username, age);
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162140741-922a7463-c216-4778-9c48-72c9231079e4.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162140879-a56f6dd2-259e-4539-8757-f59ffb379af7.png)
    
    ```java
    /**
     * @RequestParam
     * - defaultValue 사용
     *
     * 참고: defaultValue는 빈 문자의 경우에도 적용
     * /request-param?username=
     */
    @ResponseBody
    @RequestMapping("/request-param-default")
    public String requestParamDefault(
            @RequestParam(required = true, defaultValue = "guest") String username,
            @RequestParam(required = false, defaultValue = "-1") int age) {

        log.info("username={}, age={}", username, age);
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162140984-a77087d5-58d5-464c-941d-bf847198ab27.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162141028-729f7373-5aab-45ce-b380-d68432a232ff.png)
    
    ```java
    /**
     * @RequestParam Map, MultiValueMap
     * Map(key=value)
     * MultiValueMap(key=[value1, value2, ...] ex) (key=userIds, value=[id1, id2])
     */
    @ResponseBody
    @RequestMapping("/request-param-map")
    public String requestParamMap(@RequestParam Map<String, Object> paramMap) {
        log.info("username={}, age={}", paramMap.get("username"), paramMap.get("age"));
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162141166-117939b3-c78c-4b19-99e3-d908c3a09774.png)

***
  * HTTP 요청 파라미터 - @ModelAttribute
    
    ![image](https://user-images.githubusercontent.com/79301439/162147246-46eaa43d-f744-4d97-bf9e-e31f87b34faa.png)
    
    ```java
    package hello.springmvc.basic;

    import lombok.Data;

    @Data
    public class HelloData {
        private String username;
        private int age;
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162147492-cd0c5d04-9fae-4473-9bd5-772aab7af628.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162147536-ca627a17-f49f-4544-857b-0917c5517906.png)
    
    ```java
    /**
     * @ModelAttribute 사용
     * 참고: model.addAttribute(helloData) 코드도 함께 자동 적용됨, 뒤에 model을 설명할 때 자세히 설명
     */
    @ResponseBody
    @RequestMapping("/model-attribute-v1")
    public String modelAttributeV1(@ModelAttribute HelloData helloData) {
        log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());
        //log.info("helloData={}", helloData); //toString 자동으로 찍어줌
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162147746-06f98145-3b92-45fc-956d-850b5ef506b6.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162147860-58e9f5c8-e377-41d7-a4c5-14ac008fc2e3.png)
    
    ```java
    /**
     * @ModelAttribute 생략 가능
     * String, int 같은 단순 타입 = @RequestParam
     * argument resolver 로 지정해둔 타입 외 = @ModelAttribute
     */
    @ResponseBody
    @RequestMapping("/model-attribute-v2")
    public String modelAttributeV2(HelloData helloData) {
        log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162148004-be01a6d5-d13f-4ba5-b7eb-64799a8d6572.png)

***
  * HTTP 요청 메시지 - 단순 텍스트
    
    ![image](https://user-images.githubusercontent.com/79301439/162154359-c38d9970-0f6a-45e4-bbce-b88b42bb7f03.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162154510-51a07e6b-c044-483a-82bd-b40362d40ec2.png)
    
    ```java
    @Slf4j
    @Controller
    public class RequestBodyStringController {

        @PostMapping("/request-body-string-v1")
        public void requestBodyString(HttpServletRequest request, HttpServletResponse response) throws IOException {
            ServletInputStream inputStream = request.getInputStream();
            String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

            log.info("messageBody={}", messageBody);

            response.getWriter().write("ok");
        }
        
        ...
        
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162154722-6622a648-1a67-4075-b357-f683d65fbf40.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162154783-5dc10a80-fe95-4148-96b2-8334c5d71b56.png)
    
    ```java
    /**
     * InputStream(Reader): HTTP 요청 메시지 바디의 내용을 직접 조회
     * OutputStream(Writer): HTTP 응답 메시지의 바디에 직접 결과 출력
     */
    @PostMapping("/request-body-string-v2")
    public void requestBodyStringV2(InputStream inputStream, Writer responseWriter) throws IOException {

        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
        log.info("messageBody={}", messageBody);
        responseWriter.write("ok");
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162154944-a41b7ed7-ed41-40f7-8ff8-1e5983e0690f.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162155004-2ef1a8fe-d4a8-4bd3-a8a2-8b46d8fcd02b.png)
    
    ```java
    /**
     * HttpEntity: HTTP header, body 정보를 편라하게 조회
     * - 메시지 바디 정보를 직접 조회(@RequestParam X, @ModelAttribute X)
     * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
     *
     * 응답에서도 HttpEntity 사용 가능
     * - 메시지 바디 정보 직접 반환(view 조회X)
     * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
     */
    @PostMapping("/request-body-string-v3")
    public HttpEntity<String> requestBodyStringV3(HttpEntity<String> httpEntity) throws IOException {

        String messageBody = httpEntity.getBody();
        log.info("messageBody={}", messageBody);

        return new HttpEntity<>("ok");
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162155323-ef6cf562-4890-406b-ac87-5c0fb9791b50.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162155403-4e635cbe-534d-46db-b3b9-b007b2788522.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162155449-d8921664-6670-48c1-b7e8-0f972183b9c6.png)
    
    ```java
    /**
     * @RequestBody
     * - 메시지 바디 정보를 직접 조회(@RequestParam X, @ModelAttribute X)
     * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
     *
     * @ResponseBody
     * - 메시지 바디 정보 직접 반환(view 조회X)
     * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
     */
    @ResponseBody
    @PostMapping("/request-body-string-v4")
    public String requestBodyStringV4(@RequestBody String messageBody) {
        log.info("messageBody={}", messageBody);
        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162155627-b50c7d7c-90c8-4938-ba79-503b24999d2d.png)

***
  * HTTP 요청 메시지 - JSON
    
    ![image](https://user-images.githubusercontent.com/79301439/162174142-dcd3399f-da69-405c-8ddf-32f68712a609.png)
    
    ```java
    package hello.springmvc.basic.request;

    import com.fasterxml.jackson.databind.ObjectMapper;
    import hello.springmvc.basic.HelloData;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.http.HttpEntity;
    import org.springframework.stereotype.Controller;
    import org.springframework.util.StreamUtils;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestBody;
    import org.springframework.web.bind.annotation.ResponseBody;

    import javax.servlet.ServletInputStream;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    import java.nio.charset.StandardCharsets;

    /**
     * {"username":"hello", "age":20}
     * content-type: application/json
     */
    @Slf4j
    @Controller
    public class RequestBodyJsonController {

        private ObjectMapper objectMapper = new ObjectMapper();

        @PostMapping("/request-body-json-v1")
        public void requestBodyJsonV1(HttpServletRequest request, HttpServletResponse response) throws IOException {
            ServletInputStream inputStream = request.getInputStream();
            String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

            log.info("messageBody={}", messageBody);
            HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);
            log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());

            response.getWriter().write("ok");
        }
        
        ...
        
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162174357-571bc914-80b7-4d02-9754-ffa1f93174e9.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162174430-1f4a9887-b7f2-43fb-84df-019ef4b35c52.png)
    
    ```java
    /**
     * @RequestBody
     * HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
     *
     * @ResponseBody
     * - 모든 메서드에 @ResponseBody 적용
     * - 메시지 바디 정보 직접 반환(view 조회X)
     * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
     */
    @ResponseBody
    @PostMapping("/request-body-json-v2")
    public String requestBodyJsonV2(@RequestBody String messageBody) throws IOException {
        log.info("messageBody={}", messageBody);
        HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);
        log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());

        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162174626-c7144ae9-81eb-4695-98f9-f175268ee760.png)
    
    ```java
    /**
     * @RequestBody 생략 불가능(@ModelAttribute 가 적용되어 버림)
     * HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter (content-type: application/json)
     *
     */
    @ResponseBody
    @PostMapping("/request-body-json-v3")
    public String requestBodyJsonV3(@RequestBody HelloData data) {
        log.info("username={}, age={}", data.getUsername(), data.getAge());

        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162174856-b962e096-815f-434d-b8f4-5525ed1ea369.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162175147-6176e158-8b87-4d9c-9747-5455815b2d49.png)
    
    ```java
    @ResponseBody
    @PostMapping("/request-body-json-v4")
    public String requestBodyJsonV4(HttpEntity<HelloData> httpEntity) {
        HelloData data = httpEntity.getBody();
        log.info("username={}, age={}", data.getUsername(), data.getAge());

        return "ok";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162175359-3a60a93a-56e4-42f7-92c5-2b0854fab4b3.png)
    
    ```java
    /**
     * @RequestBody 생략 불가능(@ModelAttribute 가 적용되어 버림)
     * HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter (content-type: application/json)
     *
     * @ResponseBody 적용
     * - 메시지 바디 정보 직접 반환(view 조회X)
     * - HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter 적용
    (Accept: application/json)
     */
    @ResponseBody
    @PostMapping("/request-body-json-v5")
    public HelloData requestBodyJsonV5(@RequestBody HelloData data) {
        log.info("username={}, age={}", data.getUsername(), data.getAge());
        return data;
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162175552-f54a1b07-29d2-4dfe-ad95-2320ad424f73.png)

***
  * HTTP 응답 - 정적 리소스, 뷰 템플릿
    
    ![image](https://user-images.githubusercontent.com/79301439/162182311-20627f8c-ad8d-4639-a3dc-1681112f2e70.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162182415-216f0d48-dfb3-4d44-9f8a-0d46e5c0543f.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162182649-7afa6328-9db3-43f0-88fd-68fa9ba04cbd.png)
    
    ```html
    <!DOCTYPE html>
    <html xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
    <p th:text="${data}">empty</p>
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162182751-13efaea0-e0bc-4858-a819-ddb0abd4b07a.png)
    
    ```java
    package hello.springmvc.basic.response;

    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.servlet.ModelAndView;

    @Controller
    public class ResponseViewController {

        @RequestMapping("/response-view-v1")
        public ModelAndView responseViewV1() {
            ModelAndView mav = new ModelAndView("response/hello")
                    .addObject("data", "hello!");
            return mav;
        }

        @RequestMapping("/response-view-v2")
        public String responseViewV2(Model model) {
            model.addAttribute("data", "hello!");
            return "response/hello";
        }

        @RequestMapping("/response/hello")
        public void responseViewV3(Model model) {
            model.addAttribute("data", "hello!");
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162182942-524eadfe-76d6-422e-b5ba-db65ea2a1996.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162183134-89c30437-bca9-42c8-a836-897e4513e5ae.png)

***
  * HTTP 응답 - HTTP API, 메시지 바디에 직접 입력
    
    ![image](https://user-images.githubusercontent.com/79301439/162187439-f32ad7d8-a7c3-49a2-822d-0923708825bc.png)
    
    ```java
    package hello.springmvc.basic.response;

    import hello.springmvc.basic.HelloData;
    import lombok.extern.slf4j.Slf4j;
    import org.springframework.http.HttpStatus;
    import org.springframework.http.ResponseEntity;
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.ResponseBody;
    import org.springframework.web.bind.annotation.ResponseStatus;
    import org.springframework.web.bind.annotation.RestController;

    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;

    @Slf4j
    @Controller
    //@RestController
    public class ResponseBodyController {

        @GetMapping("/response-body-string-v1")
        public void responseBodyV1(HttpServletResponse response) throws IOException {
            response.getWriter().write("ok");
        }

        @GetMapping("/response-body-string-v2")
        public ResponseEntity<String> responseBodyV2() {
            return new ResponseEntity<String>("ok", HttpStatus.OK);
        }

        @ResponseBody
        @GetMapping("/response-body-string-v3")
        public String responseBodyV3() {
            return "ok";
        }

        @GetMapping("/response-body-json-v1")
        public ResponseEntity<HelloData> responseBodyJsonV1() {
            HelloData helloData = new HelloData();
            helloData.setUsername("userA");
            helloData.setAge(20);
            return new ResponseEntity<>(helloData, HttpStatus.OK);
        }

        @ResponseStatus(HttpStatus.OK)
        @ResponseBody
        @GetMapping("/response-body-json-v2")
        public HelloData responseBodyJsonV2() {
            HelloData helloData = new HelloData();
            helloData.setUsername("userA");
            helloData.setAge(20);
            return helloData;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162187748-f973b7db-8239-4e59-880a-febb9a55b33f.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162187789-7690960e-fbf5-4801-8bf9-1b6c5ab25e7b.png)

