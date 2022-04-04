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
