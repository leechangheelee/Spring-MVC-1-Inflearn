## **MVC 프레임워크 만들기**
  * 프론트 컨트롤러 패턴 소개
    
    ![image](https://user-images.githubusercontent.com/79301439/161658626-10999f7f-b87a-4829-b4b3-bf88a2ad0215.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161658654-155cc706-374c-47a5-89d0-9d4da6d514ce.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161658727-8bce4dbd-aae5-4851-a5ec-081eb75e7ab6.png)

***
  * 프론트 컨트롤러 도입 - v1
    
    ![image](https://user-images.githubusercontent.com/79301439/161670616-77e52317-cceb-4386-8d10-e54d07be701e.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161670656-7356570f-77a9-41a7-afe2-7fb157c96c5e.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161670676-dae420d4-c314-4524-bcc7-cdb41f4abb2f.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v1;

    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;

    public interface ControllerV1 {

        void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException;
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161670722-6d565bcd-399e-459f-bd2e-40e3a7b47154.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161670759-664c69b5-fa36-4d7e-b09c-d233d0dddb57.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v1.controller;

    import hello.servlet.web.frontcontroller.v1.ControllerV1;

    import javax.servlet.RequestDispatcher;
    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;

    public class MemberFormControllerV1 implements ControllerV1 {

        @Override
        public void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            String viewPath = "/WEB-INF/views/new-form.jsp";
            RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
            dispatcher.forward(request, response);
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161670825-64107d8b-845c-4047-ad76-4670db2ed11e.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v1.controller;

    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;
    import hello.servlet.web.frontcontroller.v1.ControllerV1;

    import javax.servlet.RequestDispatcher;
    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;

    public class MemberSaveControllerV1 implements ControllerV1 {

        MemberRepository memberRepository = MemberRepository.getInstance();

        @Override
        public void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

            String username = request.getParameter("username");
            int age = Integer.parseInt(request.getParameter("age"));

            Member member = new Member(username, age);
            memberRepository.save(member);

            request.setAttribute("member", member);

            String viewPath = "/WEB-INF/views/save-result.jsp";
            RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
            dispatcher.forward(request, response);
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161670885-6665d162-4435-4f3e-b7c9-1a5a48789e2e.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v1.controller;

    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;
    import hello.servlet.web.frontcontroller.v1.ControllerV1;

    import javax.servlet.RequestDispatcher;
    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    import java.util.List;

    public class MemberListControllerV1 implements ControllerV1 {

        private MemberRepository memberRepository = MemberRepository.getInstance();

        @Override
        public void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

            List<Member> members = memberRepository.findAll();

            request.setAttribute("members", members);

            String viewPath = "/WEB-INF/views/members.jsp";
            RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
            dispatcher.forward(request, response);
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161670941-a67b9113-18e4-4e84-b796-8c69778b4423.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161670967-17133769-dba1-4357-96bd-7964a93d8561.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v1;

    import hello.servlet.web.frontcontroller.v1.controller.MemberFormControllerV1;
    import hello.servlet.web.frontcontroller.v1.controller.MemberListControllerV1;
    import hello.servlet.web.frontcontroller.v1.controller.MemberSaveControllerV1;

    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    import java.util.HashMap;
    import java.util.Map;

    @WebServlet(name = "frontControllerServletV1", urlPatterns = "/front-controller/v1/*") //v1아래 어떤 경로가 요청이 와도 무조건 여기로 들어옴
    public class FrontControllerServletV1 extends HttpServlet {

        private Map<String, ControllerV1> controllerMap = new HashMap<>();

        public FrontControllerServletV1() {
            controllerMap.put("/front-controller/v1/members/new-form", new MemberFormControllerV1());
            controllerMap.put("/front-controller/v1/members/save", new MemberSaveControllerV1());
            controllerMap.put("/front-controller/v1/members", new MemberListControllerV1());
        }

        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            System.out.println("FrontControllerServletV1.service");

            String requestURI = request.getRequestURI();

            ControllerV1 controller = controllerMap.get(requestURI);
            if (controller == null) {
                response.setStatus(HttpServletResponse.SC_NOT_FOUND);
                return;
            }

            controller.process(request, response);
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161671070-39183d95-582d-4032-ab44-96e4b43cccbf.png)

***
  * View 분리 - v2
    
    ![image](https://user-images.githubusercontent.com/79301439/161678525-ec9e989d-56ca-465d-bd37-f78dd451f7b8.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161678556-06d65e99-f846-4dc2-9e52-bbf8d8cbf4c3.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161678583-00c3e81d-d243-4703-a4b5-ec760c2f9503.png)
    
    ```java
    package hello.servlet.web.frontcontroller;

    import javax.servlet.RequestDispatcher;
    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;

    public class MyView {

        private String viewPath;

        public MyView(String viewPath) {
            this.viewPath = viewPath;
        }

        public void render(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
            dispatcher.forward(request, response);
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161678636-3b0b706f-6f84-414b-8494-ee04f2522348.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161678673-acd8cb3b-9205-43c1-bb12-9fc7a95ccbdf.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v2;

    import hello.servlet.web.frontcontroller.MyView;

    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;

    public interface ControllerV2 {

        MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException;
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161678724-c687e6aa-17a1-4b5c-96b1-7d46a70ec67f.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v2.controller;

    import hello.servlet.web.frontcontroller.MyView;
    import hello.servlet.web.frontcontroller.v2.ControllerV2;

    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;

    public class MemberFormControllerV2 implements ControllerV2 {

        @Override
        public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            return new MyView("/WEB-INF/views/new-form.jsp");
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161678815-460b8b06-1b30-4004-8572-e171643f60f1.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161678832-ae178ccd-f78b-4c36-90da-38db24dfa2e2.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v2.controller;

    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;
    import hello.servlet.web.frontcontroller.MyView;
    import hello.servlet.web.frontcontroller.v2.ControllerV2;

    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;

    public class MemberSaveControllerV2 implements ControllerV2 {

        private MemberRepository memberRepository = MemberRepository.getInstance();

        @Override
        public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

            String username = request.getParameter("username");
            int age = Integer.parseInt(request.getParameter("age"));

            Member member = new Member(username, age);
            memberRepository.save(member);

            request.setAttribute("member", member);

            return new MyView("/WEB-INF/views/save-result.jsp");
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161678895-ee2b1a02-e90b-4944-8dda-91ccc187b827.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v2.controller;

    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;
    import hello.servlet.web.frontcontroller.MyView;
    import hello.servlet.web.frontcontroller.v2.ControllerV2;

    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    import java.util.List;

    public class MemberListControllerV2 implements ControllerV2 {

        private MemberRepository memberRepository = MemberRepository.getInstance();

        @Override
        public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

            List<Member> members = memberRepository.findAll();
            request.setAttribute("members", members);
            return new MyView("/WEB-INF/views/members.jsp");
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161678963-39be208e-44f3-46ce-8780-110fb07f20e2.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v2;

    import hello.servlet.web.frontcontroller.MyView;
    import hello.servlet.web.frontcontroller.v2.controller.MemberFormControllerV2;
    import hello.servlet.web.frontcontroller.v2.controller.MemberListControllerV2;
    import hello.servlet.web.frontcontroller.v2.controller.MemberSaveControllerV2;

    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    import java.util.HashMap;
    import java.util.Map;

    @WebServlet(name = "frontControllerServletV2", urlPatterns = "/front-controller/v2/*")
    public class FrontControllerServletV2 extends HttpServlet {

        private Map<String, ControllerV2> controllerMap = new HashMap<>();

        public FrontControllerServletV2() {
            controllerMap.put("/front-controller/v2/members/new-form", new MemberFormControllerV2());
            controllerMap.put("/front-controller/v2/members/save", new MemberSaveControllerV2());
            controllerMap.put("/front-controller/v2/members", new MemberListControllerV2());
        }

        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

            String requestURI = request.getRequestURI();

            ControllerV2 controller = controllerMap.get(requestURI);
            if (controller == null) {
                response.setStatus(HttpServletResponse.SC_NOT_FOUND);
                return;
            }

            MyView view = controller.process(request, response);
            view.render(request, response);
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161679052-5bc8a9b8-af39-4d68-856c-04deacc08ea4.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161679073-a02cf6dc-1406-4fc2-9995-4c1657b17525.png)
    
    ```java
    public void render(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161679135-7a497c01-6cae-4fa4-8861-ab1a1c5b9c38.png)

***
  * Model 추가 - v3
    
    ![image](https://user-images.githubusercontent.com/79301439/161691499-fadfb790-264e-4056-a8a6-d4c993f7660f.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161691548-d3602303-8151-4808-b8bc-4d44ccb4dcc1.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161691639-9aa88179-b0f4-44b5-9760-9d29e0e102e0.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161691675-94a93df1-04ba-4657-bf9c-9f1b7e26d16b.png)
    
    ```java
    package hello.servlet.web.frontcontroller;

    import java.util.HashMap;
    import java.util.Map;

    public class ModelView {
        private String viewName;
        private Map<String, Object> model = new HashMap<>();

        public ModelView(String viewName) {
            this.viewName = viewName;
        }

        public String getViewName() {
            return viewName;
        }

        public void setViewName(String viewName) {
            this.viewName = viewName;
        }

        public Map<String, Object> getModel() {
            return model;
        }

        public void setModel(Map<String, Object> model) {
            this.model = model;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161691954-be086a5b-d158-4f0d-81a2-26e86f6a8c03.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161691980-0ae61df1-6c0b-406e-aef9-46f861164c10.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v3;

    import hello.servlet.web.frontcontroller.ModelView;

    import java.util.Map;

    public interface ControllerV3 {

        ModelView process(Map<String, String> paramMap);
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161692048-12feaa91-485a-49ae-bfd4-07bac41bfc82.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161692114-47063a95-b5f4-4357-a908-3cff22bac2c4.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v3.controller;

    import hello.servlet.web.frontcontroller.ModelView;
    import hello.servlet.web.frontcontroller.v3.ControllerV3;

    import java.util.Map;

    public class MemberFormControllerV3 implements ControllerV3 {

        @Override
        public ModelView process(Map<String, String> paramMap) {
            return new ModelView("new-form");
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161692267-d96e550d-aa70-4a2f-a8c7-71e2f21324c8.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161692307-c515f5e3-43fe-4f0d-b965-0c447562bbfc.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v3.controller;

    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;
    import hello.servlet.web.frontcontroller.ModelView;
    import hello.servlet.web.frontcontroller.v3.ControllerV3;

    import java.util.Map;

    public class MemberSaveControllerV3 implements ControllerV3 {

        private MemberRepository memberRepository = MemberRepository.getInstance();

        @Override
        public ModelView process(Map<String, String> paramMap) {
            String username = paramMap.get("username");
            int age = Integer.parseInt(paramMap.get("age"));

            Member member = new Member(username, age);
            memberRepository.save(member);

            ModelView mv = new ModelView("save-result");
            mv.getModel().put("member", member);
            return mv;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161692489-0ae29782-103a-496c-a12f-7ed2738dcf0a.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161692586-97a9cc10-4d46-4e4a-a522-7019d4540982.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v3.controller;

    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;
    import hello.servlet.web.frontcontroller.ModelView;
    import hello.servlet.web.frontcontroller.v3.ControllerV3;

    import java.util.List;
    import java.util.Map;

    public class MemberListControllerV3 implements ControllerV3 {

        private MemberRepository memberRepository = MemberRepository.getInstance();

        @Override
        public ModelView process(Map<String, String> paramMap) {

            List<Member> members = memberRepository.findAll();
            ModelView mv = new ModelView("members");
            mv.getModel().put("members", members);

            return mv;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161692683-2f5f4d89-4ac5-45e1-8e41-76415d45ea52.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v3;

    import hello.servlet.web.frontcontroller.ModelView;
    import hello.servlet.web.frontcontroller.MyView;
    import hello.servlet.web.frontcontroller.v3.controller.MemberFormControllerV3;
    import hello.servlet.web.frontcontroller.v3.controller.MemberListControllerV3;
    import hello.servlet.web.frontcontroller.v3.controller.MemberSaveControllerV3;

    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    import java.util.HashMap;
    import java.util.Map;

    @WebServlet(name = "frontControllerServletV3", urlPatterns = "/front-controller/v3/*")
    public class FrontControllerServletV3 extends HttpServlet {

        private Map<String, ControllerV3> controllerMap = new HashMap<>();

        public FrontControllerServletV3() {
            controllerMap.put("/front-controller/v3/members/new-form", new MemberFormControllerV3());
            controllerMap.put("/front-controller/v3/members/save", new MemberSaveControllerV3());
            controllerMap.put("/front-controller/v3/members", new MemberListControllerV3());
        }

        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

            String requestURI = request.getRequestURI();

            ControllerV3 controller = controllerMap.get(requestURI);
            if (controller == null) {
                response.setStatus(HttpServletResponse.SC_NOT_FOUND);
                return;
            }

            Map<String, String> paramMap = createParamMap(request);
            ModelView mv = controller.process(paramMap);

            String viewName = mv.getViewName(); //논리이름

            MyView view = viewResolver(viewName);
            view.render(mv.getModel(), request, response);
        }

        private MyView viewResolver(String viewName) {
            return new MyView("/WEB-INF/views/" + viewName + ".jsp");
        }

        private Map<String, String> createParamMap(HttpServletRequest request) {
            Map<String, String> paramMap = new HashMap<>();
            request.getParameterNames().asIterator()
                    .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
            return paramMap;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161692898-e34c44cc-1c06-45cd-825a-7358fdecc8a7.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161692984-0646e420-9468-4ca2-a15d-a4bb39b8dee6.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161693159-2a221757-3132-4d13-85b2-f601c5ab738e.png)
    
    ```java
    package hello.servlet.web.frontcontroller;

    import javax.servlet.RequestDispatcher;
    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    import java.util.Map;

    public class MyView {

        private String viewPath;

        public MyView(String viewPath) {
            this.viewPath = viewPath;
        }

        public void render(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
            dispatcher.forward(request, response);
        }

        public void render(Map<String, Object> model, HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            modelToRequestAttribute(model, request);
            RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
            dispatcher.forward(request, response);
        }

        private void modelToRequestAttribute(Map<String, Object> model, HttpServletRequest request) {
            model.forEach((key, value) -> request.setAttribute(key, value)); //JSP는 여기에서 값을 꺼내기때문에 setAttribute로 데이터 넣어줘야함
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161693273-10788d97-6a11-417b-9751-aff97236ced9.png)

***
  * 단순하고 실용적인 컨트롤러 - v4
    
    ![image](https://user-images.githubusercontent.com/79301439/161698902-05b10c7f-a809-4c33-8bcb-af199957493f.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161698943-dee04275-3a35-478d-8ad4-b2cd61092e5b.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161698972-cbf2eb0e-129b-4044-87f8-f1145f17aaef.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v4;

    import java.util.Map;

    public interface ControllerV4 {

        /**
         *
         * @param paramMap
         * @param model
         * @return viewName
         */
        String process(Map<String, String> paramMap, Map<String, Object> model);
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161699071-e0f589b5-e26b-4f68-a6d2-eef893957c47.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161699101-6be95fe3-edda-4d90-acd4-4b55f9494c30.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v4.controller;

    import hello.servlet.web.frontcontroller.v4.ControllerV4;

    import java.util.Map;

    public class MemberFormControllerV4 implements ControllerV4 {

        @Override
        public String process(Map<String, String> paramMap, Map<String, Object> model) {
            return "new-form";
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161699187-6e4617ab-047f-4d72-bc77-46207274a4ae.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161699218-3a3bcac6-1eba-4914-96e6-1c05524f436a.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v4.controller;

    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;
    import hello.servlet.web.frontcontroller.v4.ControllerV4;

    import java.util.Map;

    public class MemberSaveControllerV4 implements ControllerV4 {

        private MemberRepository memberRepository = MemberRepository.getInstance();

        @Override
        public String process(Map<String, String> paramMap, Map<String, Object> model) {
            String username = paramMap.get("username");
            int age = Integer.parseInt(paramMap.get("age"));

            Member member = new Member(username, age);
            memberRepository.save(member);

            model.put("member", member);
            return "save-result";
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161699315-1b7d2694-86bb-430f-9e7e-cca533d64a0f.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161699348-4e12462d-0f53-43de-ae94-a430156caece.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v4.controller;

    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;
    import hello.servlet.web.frontcontroller.v4.ControllerV4;

    import java.util.List;
    import java.util.Map;

    public class MemberListControllerV4 implements ControllerV4 {

        private MemberRepository memberRepository = MemberRepository.getInstance();

        @Override
        public String process(Map<String, String> paramMap, Map<String, Object> model) {
            List<Member> members = memberRepository.findAll();

            model.put("members", members);
            return "members";
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161699469-d82fe093-2ff7-47c7-bf75-0e8facd8b96e.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v4;

    import hello.servlet.web.frontcontroller.MyView;
    import hello.servlet.web.frontcontroller.v4.controller.MemberFormControllerV4;
    import hello.servlet.web.frontcontroller.v4.controller.MemberListControllerV4;
    import hello.servlet.web.frontcontroller.v4.controller.MemberSaveControllerV4;

    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    import java.util.HashMap;
    import java.util.Map;

    @WebServlet(name = "frontControllerServletV4", urlPatterns = "/front-controller/v4/*")
    public class FrontControllerServletV4 extends HttpServlet {

        private Map<String, ControllerV4> controllerMap = new HashMap<>();

        public FrontControllerServletV4() {
            controllerMap.put("/front-controller/v4/members/new-form", new MemberFormControllerV4());
            controllerMap.put("/front-controller/v4/members/save", new MemberSaveControllerV4());
            controllerMap.put("/front-controller/v4/members", new MemberListControllerV4());
        }

        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

            String requestURI = request.getRequestURI();

            ControllerV4 controller = controllerMap.get(requestURI);
            if (controller == null) {
                response.setStatus(HttpServletResponse.SC_NOT_FOUND);
                return;
            }

            Map<String, String> paramMap = createParamMap(request);
            Map<String, Object> model = new HashMap<>(); //추가

            String viewName = controller.process(paramMap, model);

            MyView view = viewResolver(viewName);

            view.render(model, request, response);
        }

        private MyView viewResolver(String viewName) {
            return new MyView("/WEB-INF/views/" + viewName + ".jsp");
        }

        private Map<String, String> createParamMap(HttpServletRequest request) {
            Map<String, String> paramMap = new HashMap<>();
            request.getParameterNames().asIterator()
                    .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
            return paramMap;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161699739-6292a395-3e60-45c5-9754-2eb0dfe3d408.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161699875-b052ebab-c99c-496a-908b-c52da2aba84b.png)

***
  * 유연한 컨트롤러1 - v5
    
    ![image](https://user-images.githubusercontent.com/79301439/161716615-ba8b4035-1f96-431a-9d69-10ab0a94368a.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161716664-1663189f-3f94-4a63-8e06-2cb3785149ef.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161716917-ba2dc863-fc94-430f-a903-6faf40531311.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161716973-41d053c2-69ce-4315-baeb-5dfa33d07aca.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v5;

    import hello.servlet.web.frontcontroller.ModelView;

    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;

    public interface MyHandlerAdapter {

        boolean supports(Object handler);

        ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException;
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161717222-5801978a-5c12-4cee-8e99-d01f8eff4115.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161717289-0477a407-14d1-4906-9e1b-7c8bfcfc5927.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v5.adapter;

    import hello.servlet.web.frontcontroller.ModelView;
    import hello.servlet.web.frontcontroller.v3.ControllerV3;
    import hello.servlet.web.frontcontroller.v5.MyHandlerAdapter;

    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    import java.util.HashMap;
    import java.util.Map;

    public class ControllerV3HandlerAdapter implements MyHandlerAdapter {

        @Override
        public boolean supports(Object handler) {
            return (handler instanceof ControllerV3);
        }

        @Override
        public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {
            ControllerV3 controller = (ControllerV3) handler;

            Map<String, String> paramMap = createParamMap(request);
            ModelView mv = controller.process(paramMap);

            return mv;
        }

        private Map<String, String> createParamMap(HttpServletRequest request) {
            Map<String, String> paramMap = new HashMap<>();
            request.getParameterNames().asIterator()
                    .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
            return paramMap;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161717573-e0e4a546-bc82-4533-bc10-d85aff55543c.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161717629-cff65691-db7c-4614-82ee-64cb97259bdd.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v5;

    import hello.servlet.web.frontcontroller.ModelView;
    import hello.servlet.web.frontcontroller.MyView;
    import hello.servlet.web.frontcontroller.v3.controller.MemberFormControllerV3;
    import hello.servlet.web.frontcontroller.v3.controller.MemberListControllerV3;
    import hello.servlet.web.frontcontroller.v3.controller.MemberSaveControllerV3;
    import hello.servlet.web.frontcontroller.v5.adapter.ControllerV3HandlerAdapter;

    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;

    @WebServlet(name = "frontControllerServletV5", urlPatterns = "/front-controller/v5/*")
    public class FrontControllerServletV5 extends HttpServlet {

        private final Map<String, Object> handlerMappingMap = new HashMap<>();
        private final List<MyHandlerAdapter> handlerAdapters = new ArrayList<>();

        public FrontControllerServletV5() {
            initHandlerMappingMap();
            initHandlerAdapters();
        }

        private void initHandlerMappingMap() {
            handlerMappingMap.put("/front-controller/v5/v3/members/new-form", new MemberFormControllerV3());
            handlerMappingMap.put("/front-controller/v5/v3/members/save", new MemberSaveControllerV3());
            handlerMappingMap.put("/front-controller/v5/v3/members", new MemberListControllerV3());
        }

        private void initHandlerAdapters() {
            handlerAdapters.add(new ControllerV3HandlerAdapter());
        }

        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

            Object handler = getHandler(request);

            if (handler == null) {
                response.setStatus(HttpServletResponse.SC_NOT_FOUND);
                return;
            }

            MyHandlerAdapter adapter = getHandlerAdapter(handler);

            ModelView mv = adapter.handle(request, response, handler);


            String viewName = mv.getViewName();
            MyView view = viewResolver(viewName);

            view.render(mv.getModel(), request, response);
        }

        private MyView viewResolver(String viewName) {
            return new MyView("/WEB-INF/views/" + viewName + ".jsp");
        }

        private MyHandlerAdapter getHandlerAdapter(Object handler) {
            for (MyHandlerAdapter adapter : handlerAdapters) {
                if (adapter.supports(handler)) {
                    return adapter;
                }
            }

            throw new IllegalArgumentException("handler adapter를 찾을 수 없습니다. handler=" + handler);
        }

        private Object getHandler(HttpServletRequest request) {
            String requestURI = request.getRequestURI();
            return handlerMappingMap.get(requestURI);
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161717847-d2b9be35-0ad7-4bc3-9cc6-4a259bcb9360.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161718039-06687db0-7ed3-4eaf-ba42-c76907bf7eb9.png)
    
    ```java
    private Object getHandler(HttpServletRequest request) {
        String requestURI = request.getRequestURI();
        return handlerMappingMap.get(requestURI);
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161718197-fbb728bf-c988-43a6-b1fd-b673f67ece16.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161718278-29db5ef7-0569-4ea2-abad-8a3098af3d95.png)
    
    ```java
    for (MyHandlerAdapter adapter : handlerAdapters) {
        if (adapter.supports(handler)) {
            return adapter;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161718421-0104364e-ab37-4026-ae6e-31fba51032c2.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161718509-5a9fc5aa-f93a-4074-a236-d098a7e98162.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161718566-4ba38b5f-3c89-44f9-a19d-d5114ebcb989.png)

***
  * 유연한 컨트롤러2 - v5
    
    ![image](https://user-images.githubusercontent.com/79301439/161883460-b043f688-6442-4919-8ba4-1f9ba8fbf6a1.png)
    
    ```java
    private void initHandlerMappingMap() {
        handlerMappingMap.put("/front-controller/v5/v3/members/new-form", new MemberFormControllerV3());
        handlerMappingMap.put("/front-controller/v5/v3/members/save", new MemberSaveControllerV3());
        handlerMappingMap.put("/front-controller/v5/v3/members", new MemberListControllerV3());

        //V4 추가
        handlerMappingMap.put("/front-controller/v5/v4/members/new-form", new MemberFormControllerV4());
        handlerMappingMap.put("/front-controller/v5/v4/members/save", new MemberSaveControllerV4());
        handlerMappingMap.put("/front-controller/v5/v4/members", new MemberListControllerV4());
    }

    private void initHandlerAdapters() {
        handlerAdapters.add(new ControllerV3HandlerAdapter());
        handlerAdapters.add(new ControllerV4HandlerAdapter()); //V4 추가
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161883580-d5e382a5-6183-43a9-81eb-e157f0257fd0.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161883604-4d61c0ac-92b5-4a74-99d6-baa2857d3619.png)
    
    ```java
    package hello.servlet.web.frontcontroller.v5.adapter;

    import hello.servlet.web.frontcontroller.ModelView;
    import hello.servlet.web.frontcontroller.v4.ControllerV4;
    import hello.servlet.web.frontcontroller.v5.MyHandlerAdapter;

    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    import java.util.HashMap;
    import java.util.Map;

    public class ControllerV4HandlerAdapter implements MyHandlerAdapter {

        @Override
        public boolean supports(Object handler) {
            return (handler instanceof ControllerV4);
        }

        @Override
        public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {
            ControllerV4 controller = (ControllerV4) handler;

            Map<String, String> paramMap = createParamMap(request);
            HashMap<String, Object> model = new HashMap<>();

            String viewName = controller.process(paramMap, model);
            ModelView mv = new ModelView(viewName);
            mv.setModel(model);

            return mv;
        }

        private Map<String, String> createParamMap(HttpServletRequest request) {
            Map<String, String> paramMap = new HashMap<>();
            request.getParameterNames().asIterator()
                    .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
            return paramMap;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161883728-6a965955-a658-488e-94ab-12fac60b8544.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161883760-f207018d-b7ef-4035-ae3c-a5c19ff73eb7.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161883857-c058ff0e-6052-4963-a314-319d402bc6b5.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161883942-bc662820-26b9-48b7-9387-7255d5a69342.png)

***
  * 정리
    
    ![image](https://user-images.githubusercontent.com/79301439/161888109-179b5506-5b08-4cd3-9424-60be9de98a8b.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161888166-6ba9ba02-e7f2-46e7-a414-64abe0d85697.png)
