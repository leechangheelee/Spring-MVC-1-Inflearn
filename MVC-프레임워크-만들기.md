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
