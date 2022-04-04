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
