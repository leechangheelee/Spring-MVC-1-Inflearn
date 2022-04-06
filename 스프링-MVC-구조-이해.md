## **스프링 MVC - 구조 이해**
  * 스프링 MVC 전체 구조
    
    ![image](https://user-images.githubusercontent.com/79301439/161901278-bd225961-2bbd-40c5-bd0e-7eb0cf909f10.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161901307-56b72493-2d47-4046-9dd8-5eeb05099cb6.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161901346-430c64f1-811f-42c5-a697-244414f5eb46.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161901394-f25ee249-a7d2-4df2-b803-3e8dc8b1e103.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161901469-3457ebd5-f37f-4297-9383-077c2f9cf68e.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161901504-e7214f88-7bf8-4d3a-9391-605e73d8c9e3.png)
    
    ```java
    protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
    
        HttpServletRequest processedRequest = request;
        HandlerExecutionChain mappedHandler = null;
        ModelAndView mv = null;

        // 1. 핸들러 조회
        mappedHandler = getHandler(processedRequest);
        if (mappedHandler == null) {
            noHandlerFound(processedRequest, response);
            return;
        }

        // 2. 핸들러 어댑터 조회 - 핸들러를 처리할 수 있는 어댑터
        HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());

        // 3. 핸들러 어댑터 실행 -> 4. 핸들러 어댑터를 통해 핸들러 실행 -> 5. ModelAndView 반환
        mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
        processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
    }
        
    private void processDispatchResult(HttpServletRequest request, HttpServletResponse response, HandlerExecutionChain mappedHandler, ModelAndView mv, Exception exception) throws Exception {
    
        // 뷰 렌더링 호출
        render(mv, request, response);
    }
    
    protected void render(ModelAndView mv, HttpServletRequest request, HttpServletResponse response) throws Exception {
        
        View view;
        String viewName = mv.getViewName();

        // 6. 뷰 리졸버를 통해서 뷰 찾기, 7. View 반환
        view = resolveViewName(viewName, mv.getModelInternal(), locale, request);

        // 8. 뷰 렌더링
        view.render(mv.getModelInternal(), request, response);
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161901913-147a3802-e51e-4cc3-88f2-2f8ff0934ffd.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161901960-92fa01e5-6df1-48f1-8fd7-039a3b16e3ab.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161902104-73532379-5103-471c-bce5-303d35fd433d.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161902133-ba850137-144d-4011-8c78-a2316ce52162.png)

***
  * 핸들러 매핑과 핸들러 어댑터
    
    ![image](https://user-images.githubusercontent.com/79301439/161911968-f9ffab68-93dd-4f45-bd13-6183f3c5c38b.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161912024-6fd05297-5cf6-4e60-9b6f-099251abf310.png)
    
    ```java
    package hello.servlet.web.springmvc.old;

    import org.springframework.stereotype.Component;
    import org.springframework.web.servlet.ModelAndView;
    import org.springframework.web.servlet.mvc.Controller;

    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;

    @Component("/springmvc/old-controller") //빈의 이름을 이렇게 설정하면 호출이 됨
    public class OldController implements Controller {

        @Override
        public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
            System.out.println("OldController.handleRequest");
            return null;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161912154-57ecd5ee-090d-44e4-a77b-1f1c956d0691.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161912377-bf413a53-ad52-4a5e-9385-f83ec0e6c362.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161912552-0aae21cd-01c1-43db-8d30-0cb823a705fa.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161912632-87cdbf50-b5d2-4183-86fd-a157b5fe82f5.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161912968-78561c0d-3a4e-4281-b202-0dc6bed88d7e.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161913030-d7c338e4-87d1-4d26-aae9-db1d001de27a.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161913880-e2ac574e-87a7-4abf-bc7d-86ab4f047317.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161913927-ae431750-3d95-45d9-8e0c-55ba8b70f028.png)
    
    ```java
    package hello.servlet.web.springmvc.old;

    import org.springframework.stereotype.Component;
    import org.springframework.web.HttpRequestHandler;

    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;

    @Component("/springmvc/request-handler")
    public class MyHttpRequestHandler implements HttpRequestHandler {

        @Override
        public void handleRequest(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            System.out.println("MyHttpRequestHandler.handleRequest");
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161914650-e176a86c-23eb-467a-9849-a0ac633141a2.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161914113-8b1f2d05-8bb9-4350-8b2b-2ab017634c70.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161914176-afc11f77-abc6-419f-8899-1c13d269be97.png)

***
  * 뷰 리졸버
    
    ![image](https://user-images.githubusercontent.com/79301439/161924786-8a15ad09-5cb4-4a0e-957c-104dd4f0029f.png)
    
    ```java
    package hello.servlet.web.springmvc.old;

    import org.springframework.stereotype.Component;
    import org.springframework.web.servlet.ModelAndView;
    import org.springframework.web.servlet.mvc.Controller;

    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;

    @Component("/springmvc/old-controller") //빈의 이름을 이렇게 설정하면 호출이 됨
    public class OldController implements Controller {

        @Override
        public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
            System.out.println("OldController.handleRequest");
            return new ModelAndView("new-form");
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161924993-a6d89e26-7db0-4b99-8322-9472f7521de8.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161925434-f6c45b0f-dace-4d87-b1eb-f160d903150f.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161926248-80cef7ec-b384-4765-8aea-4c827e207c53.png)

    ![image](https://user-images.githubusercontent.com/79301439/161925576-b51bcfbb-510f-4748-9dae-735efe35ea9c.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161925884-6a6dc631-e697-4438-9b8b-d34208d2ce8d.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161925991-589a2b7c-d5b7-49bb-92be-2e73f3990d9d.png)

***
  * 스프링 MVC - 시작하기
    
    ![image](https://user-images.githubusercontent.com/79301439/161949805-a4210212-ed1a-40d2-b15a-05d456941e09.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161949871-17f57520-193c-4778-ac0b-acce84edecd1.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161949975-19a1bab9-a9c5-4089-90b8-3ac73dcfaf30.png)
    
    ```java
    package hello.servlet.web.springmvc.v1;

    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.servlet.ModelAndView;

    @Controller
    public class SpringMemberFormControllerV1 {

        @RequestMapping("/springmvc/v1/members/new-form")
        public ModelAndView process() {
            return new ModelAndView("new-form");
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161950166-a6ce220e-014f-4207-80c6-97e3fb968472.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161950265-c5745daf-0571-47f8-9adc-856733c0f0f8.png)
    
    ```java
    @Component //컴포넌트 스캔을 통해 스프링 빈으로 등록
    @RequestMapping
    public class SpringMemberFormControllerV1 {
    
        @RequestMapping("/springmvc/v1/members/new-form")
        public ModelAndView process() {
            return new ModelAndView("new-form");
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161950459-63f0ec25-3a66-4567-a6da-4f326708fe1f.png)
    
    ```java
    @RequestMapping
    public class SpringMemberFormControllerV1 {
        
        @RequestMapping("/springmvc/v1/members/new-form")
        public ModelAndView process() {
            return new ModelAndView("new-form");
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161950668-1e78b872-58cf-4073-afea-70a223f3ea59.png)
    
    ```java
    //스프링 빈 직접 등록
    @Bean
    SpringMemberFormControllerV1 testController() {
        return new SpringMemberFormControllerV1();
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161951478-c37972af-b214-4e79-ae0d-8dee344ae73b.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161951541-58373b81-df34-441f-8175-fa3f4a4ae977.png)
    
    ```java
    package hello.servlet.web.springmvc.v1;

    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.servlet.ModelAndView;

    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;

    @Controller
    public class SpringMemberSaveControllerV1 {

        private MemberRepository memberRepository = MemberRepository.getInstance();

        @RequestMapping("/springmvc/v1/members/save")
        public ModelAndView process(HttpServletRequest request, HttpServletResponse response) {
            String username = request.getParameter("username");
            int age = Integer.parseInt(request.getParameter("age"));

            Member member = new Member(username, age);
            memberRepository.save(member);

            ModelAndView mv = new ModelAndView("save-result");
            mv.addObject("member", member);
            return mv;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161951659-f4d488c7-3571-48f2-a844-d6d17ae7008b.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161951777-744d45c9-a2a7-4aa5-96fa-1f8c2c1c9395.png)
    
    ```java
    package hello.servlet.web.springmvc.v1;

    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.servlet.ModelAndView;

    import java.util.List;

    @Controller
    public class SpringMemberFormListControllerV1 {

        private MemberRepository memberRepository = MemberRepository.getInstance();

        @RequestMapping("/springmvc/v1/members")
        public ModelAndView process() {

            List<Member> members = memberRepository.findAll();

            ModelAndView mv = new ModelAndView("members");
            mv.addObject("members", members);
            return mv;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161951948-9e86eed3-b9ac-4cc4-b772-2bcf37083cc1.png)

***
  * 스프링 MVC - 컨트롤러 통합
    
    ![image](https://user-images.githubusercontent.com/79301439/161955369-4f3bde61-1753-4e6b-94d9-1d69cebd337c.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161955405-785b8da0-ef74-4f48-a06c-46912a4c7e07.png)
    
    ```java
    package hello.servlet.web.springmvc.v2;

    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.servlet.ModelAndView;

    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.util.List;

    /**
     * 클래스 단위 -> 메서드 단위
     * @RequestMapping 클래스 레벨과 메서드 레벨 조합
     */
    @Controller
    @RequestMapping("/springmvc/v2/members")
    public class SpringMemberControllerV2 {

        private MemberRepository memberRepository = MemberRepository.getInstance();

        @RequestMapping("/new-form")
        public ModelAndView newForm() {
            return new ModelAndView("new-form");
        }

        @RequestMapping("/save")
        public ModelAndView save(HttpServletRequest request, HttpServletResponse response) {
            String username = request.getParameter("username");
            int age = Integer.parseInt(request.getParameter("age"));

            Member member = new Member(username, age);
            memberRepository.save(member);

            ModelAndView mv = new ModelAndView("save-result");
            mv.addObject("member", member);
            return mv;
        }

        @RequestMapping
        public ModelAndView members() {

            List<Member> members = memberRepository.findAll();

            ModelAndView mv = new ModelAndView("members");
            mv.addObject("members", members);
            return mv;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161955900-7cb62e15-3230-4cfe-9f45-168846504cb0.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161956000-a1f0f50e-2840-4db4-9e5c-26ebd0d98d95.png)
