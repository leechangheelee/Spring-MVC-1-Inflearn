## **스프링 MVC - 웹 페이지 만들기**

![image](https://user-images.githubusercontent.com/79301439/162224596-d5d36f72-6c8f-4ce8-bc62-3a9c1e1c8fae.png)

***
  * 프로젝트 생성
    
    ![image](https://user-images.githubusercontent.com/79301439/162344101-6c988ace-ecb6-4000-bea5-fbc72d48de08.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162344161-21d2f1a8-4aa8-4c71-95b6-128c5ee97616.png)
    
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
    
    ![image](https://user-images.githubusercontent.com/79301439/162344272-f5c159d6-cd91-4352-afe5-acaf64974c53.png)
    
    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
    <ul>
        <li>상품 관리
            <ul>
                <li><a href="/basic/items">상품 관리 - 기본</a></li>
            </ul>
        </li>
    </ul>
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162344383-29257520-1756-4db7-8c76-172600a7dc09.png)

***
  * 요구사항 분석
    
    ![image](https://user-images.githubusercontent.com/79301439/162346949-bbf326df-e855-4bc9-9b85-10e6aa27abc2.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162346968-fff85c66-5d16-4ab2-9a87-61509deefd1d.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162346990-af2cc690-0dbf-4357-8132-31a4e3d727a9.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162347013-6e566c42-275c-4ff4-8f06-faaba6af8635.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162347037-1e617e21-1238-47ba-99e8-77f0390e70c7.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162347113-2d272ce6-d30e-465d-8eb6-6e3af53a716b.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162347145-8747690b-37cb-4c2f-99fd-889338fceaa3.png)

***
  * 상품 도메인 개발
    
    ![image](https://user-images.githubusercontent.com/79301439/162381149-5356cc5b-5097-4bb0-9062-6c91851099ec.png)
    
    ```java
    package hello.itemservice.domain.item;

    import lombok.Data;

    @Data
    public class Item {

        private Long id;
        private String itemName;
        private Integer price;
        private Integer quantity;

        public Item() {
        }

        public Item(String itemName, Integer price, Integer quantity) {
            this.itemName = itemName;
            this.price = price;
            this.quantity = quantity;
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162381229-4ff2172d-8d4c-4f93-91b7-cfc6cb05ce39.png)
    
    ```java
    package hello.itemservice.domain.item;

    import org.springframework.stereotype.Repository;

    import java.util.ArrayList;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;

    @Repository
    public class ItemRepository {

        private static final Map<Long, Item> store = new HashMap<>();
        private static Long sequence = 0L;

        public Item save(Item item) {
            item.setId(++sequence);
            store.put(item.getId(), item);
            return item;
        }

        public Item findById(Long id) {
            return store.get(id);
        }

        public List<Item> findAll() {
            return new ArrayList<>(store.values());
        }

        public void update(Long itemId, Item updateParam) {
            Item findItem = findById(itemId);
            findItem.setItemName(updateParam.getItemName());
            findItem.setPrice(updateParam.getPrice());
            findItem.setQuantity(updateParam.getQuantity());
        }

        public void clearStore() {
            store.clear();
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162381321-870b95c0-08a9-49d3-be91-d60f3986cb93.png)
    
    ```java
    package hello.itemservice.domain.item;

    import org.junit.jupiter.api.AfterEach;
    import org.junit.jupiter.api.Test;

    import java.util.List;

    import static org.assertj.core.api.Assertions.*;

    class ItemRepositoryTest {

        ItemRepository itemRepository = new ItemRepository();

        @AfterEach
        void afterEach() {
            itemRepository.clearStore();
        }

        @Test
        void save() {
            //given
            Item item = new Item("itemA", 10000, 10);

            //when
            Item savedItem = itemRepository.save(item);

            //then
            Item findItem = itemRepository.findById(item.getId());
            assertThat(findItem).isEqualTo(savedItem);
        }

        @Test
        void findAll() {
            //given
            Item item1 = new Item("item1", 10000, 10);
            Item item2 = new Item("item2", 20000, 20);

            itemRepository.save(item1);
            itemRepository.save(item2);

            //when
            List<Item> result = itemRepository.findAll();

            //then
            assertThat(result.size()).isEqualTo(2);
            assertThat(result).contains(item1, item2);
        }

        @Test
        void updateItem() {
            //given
            Item item = new Item("item1", 10000, 10);

            Item savedItem = itemRepository.save(item);
            Long itemId = savedItem.getId();

            //when
            Item updateParam = new Item("item2", 20000, 30);
            itemRepository.update(itemId, updateParam);

            Item findItem = itemRepository.findById(itemId);

            //then
            assertThat(findItem.getItemName()).isEqualTo(updateParam.getItemName());
            assertThat(findItem.getPrice()).isEqualTo(updateParam.getPrice());
            assertThat(findItem.getQuantity()).isEqualTo(updateParam.getQuantity());
        }
    }
    ```
