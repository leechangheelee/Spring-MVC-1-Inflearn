## **서블릿, JSP, MVC 패턴**
  * 회원 관리 웹 애플리케이션 요구사항
    
    ![image](https://user-images.githubusercontent.com/79301439/161493266-c1536fc1-264d-4bac-a8d0-fcc99dd6a2c0.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161493302-d19313a8-054e-40ac-9a62-475f7b88dd26.png)
    
    ```java
    package hello.servlet.domain.member;

    import lombok.Getter;
    import lombok.Setter;

    @Getter @Setter
    public class Member {

        private Long id;
        private String username;
        private int age;

        public Member() {
        }

        public Member(String username, int age) {
            this.username = username;
            this.age = age;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161493489-afe36751-8b82-43de-94e6-84bd5d8b6447.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161493520-bd65a654-9a3d-4f4f-bef2-92d23d65314c.png)
    
    ```java
    package hello.servlet.domain.member;

    import java.util.ArrayList;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;

    /**
     * 동시성 문제가 고려되어 있지 않음, 실무에서는 ConcurrentHashMap, AtomicLong 사용 고려
     */
    public class MemberRepository {

        //아래 변수들은 static이기 때문에 MemberRepository가 아무리 많이 생성되어도 한개씩만 유지됨
        private static Map<Long, Member> store = new HashMap<>();
        private static long sequence = 0L;

        //싱글톤으로 구현
        private static final MemberRepository instance = new MemberRepository();

        public static MemberRepository getInstance() {
            return instance;
        }

        //외부에서 생성자 호출하는 것을 차단
        private MemberRepository() {
        }

        public Member save(Member member) {
            member.setId(++sequence);
            store.put(sequence, member);
            return member;
        }

        public Member findById(Long id) {
            return store.get(id);
        }

        public List<Member> findAll() {
            return new ArrayList<>(store.values());
        }

        //테스트에서 많이 사용
        public void clearStore() {
            store.clear();
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161493714-de53372c-f287-4c7d-9fea-fda931d4a2ab.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161493772-dec3bb14-004f-41d4-8639-b2f2e0520068.png)
    
    ```java
    package hello.servlet.domain.member;

    import org.junit.jupiter.api.AfterEach;
    import org.junit.jupiter.api.Test;

    import java.util.List;

    import static org.assertj.core.api.Assertions.assertThat;

    class MemberRepositoryTest {

        MemberRepository memberRepository = MemberRepository.getInstance();

        @AfterEach
        void afterEach() {
            memberRepository.clearStore();
        }

        @Test
        void save() {
            //given
            Member member = new Member("hello", 20);

            //when
            Member savedMember = memberRepository.save(member);

            //then
            Member findMember = memberRepository.findById(savedMember.getId());
            assertThat(findMember).isEqualTo(savedMember);
        }

        @Test
        void findAll() {
            //given
            Member member1 = new Member("member1", 20);
            Member member2 = new Member("member2", 30);

            Member savedMember1 = memberRepository.save(member1);
            Member savedMember2 = memberRepository.save(member2);

            //when
            List<Member> result = memberRepository.findAll();

            //then
            assertThat(result.size()).isEqualTo(2);
            assertThat(result).contains(savedMember1, savedMember2);
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161493984-8cf638ec-de74-44ee-b90a-7101f9249593.png)

***
  * 서블릿으로 회원 관리 웹 애플리케이션 만들기
    
    ![image](https://user-images.githubusercontent.com/79301439/161504186-8b10365d-1d00-438f-affc-e22434383b5d.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161504250-b981fb51-f9cd-4763-b785-d5f7100ab824.png)
    
    ```java
    package hello.servlet.web.servlet;

    import hello.servlet.domain.member.MemberRepository;

    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    import java.io.PrintWriter;

    @WebServlet(name = "memberFormServlet", urlPatterns = "/servlet/members/new-form")
    public class MemberFormServlet extends HttpServlet {

        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

            response.setContentType("text/html");
            response.setCharacterEncoding("utf-8");

            PrintWriter w = response.getWriter();
            w.write("<!DOCTYPE html>\n" +
                    "<html>\n" +
                    "<head>\n" +
                    " <meta charset=\"UTF-8\">\n" +
                    " <title>Title</title>\n" +
                    "</head>\n" +
                    "<body>\n" +
                    "<form action=\"/servlet/members/save\" method=\"post\">\n" +
                    " username: <input type=\"text\" name=\"username\" />\n" +
                    " age: <input type=\"text\" name=\"age\" />\n" +
                    " <button type=\"submit\">전송</button>\n" +
                    "</form>\n" +
                    "</body>\n" +
                    "</html>\n");
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161504552-4d7f05a9-e7dc-4c1c-8c24-7ebca98a9e64.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161504741-a288db97-5d1c-4bb7-9cc4-1a4fd101db1a.png)
    
    ```java
    package hello.servlet.web.servlet;

    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;

    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    import java.io.PrintWriter;

    @WebServlet(name = "memberSaveServlet", urlPatterns = "/servlet/members/save")
    public class MemberSaveServlet extends HttpServlet {

        private MemberRepository memberRepository = MemberRepository.getInstance();

        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            System.out.println("MemberSaveServlet.service");
            String username = request.getParameter("username");
            int age = Integer.parseInt(request.getParameter("age"));

            Member member = new Member(username, age);
            memberRepository.save(member);

            response.setContentType("text/html");
            response.setCharacterEncoding("utf-8");
            PrintWriter w = response.getWriter();
            w.write("<html>\n" +
                    "<head>\n" +
                    " <meta charset=\"UTF-8\">\n" +
                    "</head>\n" +
                    "<body>\n" +
                    "성공\n" +
                    "<ul>\n" +
                    " <li>id="+member.getId()+"</li>\n" +
                    " <li>username="+member.getUsername()+"</li>\n" +
                    " <li>age="+member.getAge()+"</li>\n" +
                    "</ul>\n" +
                    "<a href=\"/index.html\">메인</a>\n" +
                    "</body>\n" +
                    "</html>");
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161504889-d7d9977c-a881-45f7-b6b1-bebde1f6f9a9.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161504939-1d870565-d1f0-47ef-b2f5-86f26e1cb376.png)
    
    ```java
    package hello.servlet.web.servlet;

    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;

    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    import java.io.PrintWriter;
    import java.util.List;

    @WebServlet(name = "memberListServlet", urlPatterns = "/servlet/members")
    public class MemberListServlet extends HttpServlet {

        private MemberRepository memberRepository = MemberRepository.getInstance();

        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            List<Member> members = memberRepository.findAll();

            response.setContentType("text/html");
            response.setCharacterEncoding("utf-8");

            PrintWriter w = response.getWriter();
            w.write("<html>");
            w.write("<head>");
            w.write(" <meta charset=\"UTF-8\">");
            w.write(" <title>Title</title>");
            w.write("</head>");
            w.write("<body>");
            w.write("<a href=\"/index.html\">메인</a>");
            w.write("<table>");
            w.write(" <thead>");
            w.write(" <th>id</th>");
            w.write(" <th>username</th>");
            w.write(" <th>age</th>");
            w.write(" </thead>");
            w.write(" <tbody>");

            for (Member member : members) {
                w.write(" <tr>");
                w.write(" <td>" + member.getId() + "</td>");
                w.write(" <td>" + member.getUsername() + "</td>");
                w.write(" <td>" + member.getAge() + "</td>");
                w.write(" </tr>");
            }

            w.write(" </tbody>");
            w.write("</table>");
            w.write("</body>");
            w.write("</html>");
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161505105-60b3ab92-dba7-4ca5-9732-f958b716b8b1.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161505161-ccc0ef25-9a7b-4088-a500-34058b07bc33.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161505202-693eb502-268d-4d4f-b14f-6ce435916398.png)
    
    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
    <ul>
        <li><a href="basic.html">서블릿 basic</a></li>
        <li>서블릿
            <ul>
                <li><a href="/servlet/members/new-form">회원가입</a></li>
                <li><a href="/servlet/members">회원목록</a></li>
            </ul>
        </li>
        <li>JSP
            <ul>
                <li><a href="/jsp/members/new-form.jsp">회원가입</a></li>
                <li><a href="/jsp/members.jsp">회원목록</a></li>
            </ul>
        </li>
        <li>서블릿 MVC
            <ul>
                <li><a href="/servlet-mvc/members/new-form">회원가입</a></li>
                <li><a href="/servlet-mvc/members">회원목록</a></li>
            </ul>
        </li>
        <li>FrontController - v1
            <ul>
                <li><a href="/front-controller/v1/members/new-form">회원가입</a></li>
                <li><a href="/front-controller/v1/members">회원목록</a></li>
            </ul>
        </li>
        <li>FrontController - v2
            <ul>
                <li><a href="/front-controller/v2/members/new-form">회원가입</a></li>
                <li><a href="/front-controller/v2/members">회원목록</a></li>
            </ul>
        </li>
        <li>FrontController - v3
            <ul>
                <li><a href="/front-controller/v3/members/new-form">회원가입</a></li>
                <li><a href="/front-controller/v3/members">회원목록</a></li>
            </ul>
        </li>
        <li>FrontController - v4
            <ul>
                <li><a href="/front-controller/v4/members/new-form">회원가입</a></li>
                <li><a href="/front-controller/v4/members">회원목록</a></li>
            </ul>
        </li>
        <li>FrontController - v5 - v3
            <ul>
                <li><a href="/front-controller/v5/v3/members/new-form">회원가입</a></
                li>
                <li><a href="/front-controller/v5/v3/members">회원목록</a></li>
            </ul>
        </li>
        <li>FrontController - v5 - v4
            <ul>
                <li><a href="/front-controller/v5/v4/members/new-form">회원가입</a></
                li>
                <li><a href="/front-controller/v5/v4/members">회원목록</a></li>
            </ul>
        </li>
        <li>SpringMVC - v1
            <ul>
                <li><a href="/springmvc/v1/members/new-form">회원가입</a></li>
                <li><a href="/springmvc/v1/members">회원목록</a></li>
            </ul>
        </li>
        <li>SpringMVC - v2
            <ul>
                <li><a href="/springmvc/v2/members/new-form">회원가입</a></li>
                <li><a href="/springmvc/v2/members">회원목록</a></li>
            </ul>
        </li>
        <li>SpringMVC - v3
            <ul>
                <li><a href="/springmvc/v3/members/new-form">회원가입</a></li>
                <li><a href="/springmvc/v3/members">회원목록</a></li>
            </ul>
        </li>
    </ul>
    </body>
    </html>
    ```

***
  * JSP로 회원 관리 웹 애플리케이션 만들기
    
    ![image](https://user-images.githubusercontent.com/79301439/161511082-c6855c09-b413-4495-a645-980ca84c0b9d.png)
    
    ```gradle
    //JSP 추가 시작
    implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
    implementation 'javax.servlet:jstl'
    //JSP 추가 끝
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161511177-2189c564-85e4-46de-80f6-0590465aa3ee.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161511246-81fbe806-3dbb-4439-a680-0aa752cb6865.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161511334-60bab13b-5254-404b-a0d7-a9a423f5b49a.png)
    
    ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
    <form action="/jsp/members/save.jsp" method="post">
        username: <input type="text" name="username" />
        age: <input type="text" name="age" />
        <button type="submit">전송</button>
    </form>
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161511482-0e5483a8-a515-4d9e-97f0-345fa8a8abfc.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161511519-9241c884-5105-4742-ab10-3494e0b6118a.png)
    
    ```jsp
    <%@ page import="hello.servlet.domain.member.MemberRepository" %>
    <%@ page import="hello.servlet.domain.member.Member" %>
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <%
        //request, response 사용 가능
        MemberRepository memberRepository = MemberRepository.getInstance();

        System.out.println("save.jsp");
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);
        System.out.println("member = " + member);
        memberRepository.save(member);
    %>
    <html>
    <head>
        <meta charset="UTF-8">
    </head>
    <body>
    성공
    <ul>
        <li>id=<%=member.getId()%></li>
        <li>username=<%=member.getUsername()%></li>
        <li>age=<%=member.getAge()%></li>
    </ul>
    <a href="/index.html">메인</a>
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161511607-6f7b3cf0-0df3-413c-94df-7c39806ab6a5.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161511652-771e8df1-0b3e-49b2-9c0f-9a21c1de116a.png)
    
    ```jsp
    <%@ page import="java.util.List" %>
    <%@ page import="hello.servlet.domain.member.MemberRepository" %>
    <%@ page import="hello.servlet.domain.member.Member" %>
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <%
        MemberRepository memberRepository = MemberRepository.getInstance();
        List<Member> members = memberRepository.findAll();
    %>
    <html>
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
    <a href="/index.html">메인</a>
    <table>
        <thead>
        <th>id</th>
        <th>username</th>
        <th>age</th>
        </thead>
        <tbody>
        <%
            for (Member member : members) {
                out.write("    <tr>");
                out.write("        <td>" + member.getId() + "</td>");
                out.write("        <td>" + member.getUsername() + "</td>");
                out.write("        <td>" + member.getAge() + "</td>");
                out.write("    </tr>");
            }
        %>
        </tbody>
    </table>
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161511823-e278ccc8-916c-4d4d-bc31-3ab20ee90fa7.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161511901-2810b63f-e537-4cdc-aeb9-5eb3a0d25e0c.png)

***
  * MVC 패턴 - 개요
    
    ![image](https://user-images.githubusercontent.com/79301439/161517139-c0762410-5e57-4bd2-b01d-da645cbdd1e1.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161517454-33a2ad3b-4e1c-401c-9281-c9bc56bb9706.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161517501-9fe8dba4-d424-4639-b53f-b0bb4b9060f2.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161517611-bfa19b44-8806-4d38-837a-5f477e261607.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161517657-18565fba-0fea-4bda-ad25-f9e1df6cbe1d.png)

***
  * MVC 패턴 - 적용
    
    ![image](https://user-images.githubusercontent.com/79301439/161534698-2653a0be-ec6e-4eae-b318-e342b7c1e7c2.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161534760-e4eadbd1-a551-4d0c-8548-d08bc1550b67.png)
    
    ```java
    package hello.servlet.web.servletmvc;

    import javax.servlet.RequestDispatcher;
    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;

    @WebServlet(name = "mvcMemberFormServlet", urlPatterns = "/servlet-mvc/members/new-form")
    public class MvcMemberFormServlet extends HttpServlet {

        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            String viewPath = "/WEB-INF/views/new-form.jsp";
            RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath); //Controller에서 View로 이동할 때 사용
            dispatcher.forward(request, response);
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161534973-438441b4-c97a-44f0-aeb8-dfda8cd63cc8.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161535078-0c18a388-2dd8-47fc-96ae-488c2cb904f0.png)
    
    ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>

    <!-- 상대경로 사용, [현재 URL이 속한 계층 경로 + /save] -->
    <form action="save" method="post">
        username: <input type="text" name="username" />
        age: <input type="text" name="age" />
        <button type="submit">전송</button>
    </form>
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161535200-8d2c3196-80f9-4e49-bc71-e945e6c15787.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161535362-f216b559-e004-4bbf-90d4-23d78fea261e.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161535399-9521f3d3-2a28-42e0-9a99-0d9987c058ab.png)
    
    ```java
    package hello.servlet.web.servletmvc;

    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;

    import javax.servlet.RequestDispatcher;
    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;

    @WebServlet(name = "mvcMemberSaveServlet", urlPatterns = "/servlet-mvc/members/save")
    public class MvcMemberSaveServlet extends HttpServlet {

        private MemberRepository memberRepository = MemberRepository.getInstance();

        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

            String username = request.getParameter("username");
            int age = Integer.parseInt(request.getParameter("age"));

            Member member = new Member(username, age);
            memberRepository.save(member);

            //Model에 데이터를 보관한다.
            request.setAttribute("member", member);

            String viewPath = "/WEB-INF/views/save-result.jsp";
            RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
            dispatcher.forward(request, response);
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161535555-3e34140d-87fb-430e-bd35-c179c4573eb6.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161535609-13191340-c386-4753-b0b9-9e244192d84e.png)
    
    ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <meta charset="UTF-8">
    </head>
    <body>
    성공
    <ul>
        <li>id=${member.id}</li>
        <li>username=${member.username}</li>
        <li>age=${member.age}</li>
    </ul>
    <a href="/index.html">메인</a>
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161535722-2a96411f-611e-41da-b823-101152a51c84.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161535778-d11a1038-b079-432e-904b-ea5730baeaa0.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161535819-06f2eca3-4ae3-4c33-9059-73ad07332e61.png)
    
    ```java
    package hello.servlet.web.servletmvc;

    import hello.servlet.domain.member.Member;
    import hello.servlet.domain.member.MemberRepository;

    import javax.servlet.RequestDispatcher;
    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    import java.util.List;

    @WebServlet(name = "mvcMemberListServlet", urlPatterns = "/servlet-mvc/members")
    public class MvcMemberListServlet extends HttpServlet {

        private MemberRepository memberRepository = MemberRepository.getInstance();

        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

            List<Member> members = memberRepository.findAll();

            request.setAttribute("members", members);

            String viewPath = "/WEB-INF/views/members.jsp";
            RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
            dispatcher.forward(request, response);
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161535935-1bd5fd73-d8c4-425e-9379-7296cda75247.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161535972-50349a64-b00b-435d-92ca-1c8b79805743.png)
    
    ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
    <html>
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
    <a href="/index.html">메인</a>
    <table>
        <thead>
        <th>id</th>
        <th>username</th>
        <th>age</th>
        </thead>
        <tbody>
        <c:forEach var="item" items="${members}">
            <tr>
                <td>${item.id}</td>
                <td>${item.username}</td>
                <td>${item.age}</td>
            </tr>
        </c:forEach>
        </tbody>
    </table>
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/161536189-8cecbfb7-5218-45a6-b698-6505b1d459f4.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161536415-ccd522f0-81b5-46a9-92da-5560b1174089.png)

***
  * MVC 패턴 - 한계
    
    ![image](https://user-images.githubusercontent.com/79301439/161538272-78f02afb-2952-4c64-9724-bc2d945abe80.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161538331-495eb4a2-ed47-4299-8acd-daea084830b7.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161538377-621394b3-f247-4139-a81f-785680056842.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161538547-357571c8-4fdc-4c7e-8706-617163a8b05b.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/161538609-f1fe620c-022b-492d-91be-e5c925974185.png)
