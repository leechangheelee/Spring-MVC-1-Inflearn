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
