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

***
  * 상품 서비스 HTML
   
    ![image](https://user-images.githubusercontent.com/79301439/162597294-4bb4b107-b64f-4025-b877-2b0c682ca7de.png)
   
    ![image](https://user-images.githubusercontent.com/79301439/162597311-84ad035b-f630-4122-90da-c7ef6187ad5d.png)
   
    ![image](https://user-images.githubusercontent.com/79301439/162597315-58c363bf-0597-4c59-bb36-9b5e575538fa.png)
   
    ```html
    <!DOCTYPE HTML>
    <html>
    <head>
        <meta charset="utf-8">
        <link href="../css/bootstrap.min.css" rel="stylesheet">
    </head>
    <body>
    <div class="container" style="max-width: 600px">
        <div class="py-5 text-center">
            <h2>상품 목록</h2>
        </div>
        <div class="row">
            <div class="col">
                <button class="btn btn-primary float-end"
                        onclick="location.href='addForm.html'" type="button">상품
                    등록</button>
            </div>
        </div>
        <hr class="my-4">
        <div>
            <table class="table">
                <thead>
                <tr>
                    <th>ID</th>
                    <th>상품명</th>
                    <th>가격</th>
                    <th>수량</th>
                </tr>
                </thead>
                <tbody>
                <tr>
                    <td><a href="item.html">1</a></td>
                    <td><a href="item.html">테스트 상품1</a></td>
                    <td>10000</td>
                    <td>10</td>
                </tr>
                <tr>
                    <td><a href="item.html">2</a></td>
                    <td><a href="item.html">테스트 상품2</a></td>
                    <td>20000</td>
                    <td>20</td>
                </tr>
                </tbody>
            </table>
        </div>
    </div> <!-- /container -->
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162597340-45091dcc-9b9d-49ee-8638-471e3a9ccc01.png)
    
    ```html
    <!DOCTYPE HTML>
    <html>
    <head>
        <meta charset="utf-8">
        <link href="../css/bootstrap.min.css" rel="stylesheet">
        <style>
     .container {
     max-width: 560px;
     }
     </style>
    </head>
    <body>
    <div class="container">
        <div class="py-5 text-center">
            <h2>상품 상세</h2>
        </div>
        <div>
            <label for="itemId">상품 ID</label>
            <input type="text" id="itemId" name="itemId" class="form-control"
                   value="1" readonly>
        </div>
        <div>
            <label for="itemName">상품명</label>
            <input type="text" id="itemName" name="itemName" class="form-control"
                   value="상품A" readonly>
        </div>
        <div>
            <label for="price">가격</label>
            <input type="text" id="price" name="price" class="form-control"
                   value="10000" readonly>
        </div>
        <div>
            <label for="quantity">수량</label>
            <input type="text" id="quantity" name="quantity" class="form-control"
                   value="10" readonly>
        </div>
        <hr class="my-4">
        <div class="row">
            <div class="col">
                <button class="w-100 btn btn-primary btn-lg"
                        onclick="location.href='editForm.html'" type="button">상품 수정</button>
            </div>
            <div class="col">
                <button class="w-100 btn btn-secondary btn-lg"
                        onclick="location.href='items.html'" type="button">목록으로</button>
            </div>
        </div>
    </div> <!-- /container -->
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162597348-346b7dae-d905-467a-b7ef-a003b3f52aae.png)
    
    ```html
    <!DOCTYPE HTML>
    <html>
    <head>
        <meta charset="utf-8">
        <link href="../css/bootstrap.min.css" rel="stylesheet">
        <style>
     .container {
     max-width: 560px;
     }
     </style>
    </head>
    <body>
    <div class="container">
        <div class="py-5 text-center">
            <h2>상품 등록 폼</h2>
        </div>
        <h4 class="mb-3">상품 입력</h4>
        <form action="item.html" method="post">
            <div>
                <label for="itemName">상품명</label>
                <input type="text" id="itemName" name="itemName" class="form-control" placeholder="이름을 입력하세요">
            </div>
            <div>
                <label for="price">가격</label>
                <input type="text" id="price" name="price" class="form-control"
                       placeholder="가격을 입력하세요">
            </div>
            <div>
                <label for="quantity">수량</label>
                <input type="text" id="quantity" name="quantity" class="form-control" placeholder="수량을 입력하세요">
            </div>
            <hr class="my-4">
            <div class="row">
                <div class="col">
                    <button class="w-100 btn btn-primary btn-lg" type="submit">상품
                        등록</button>
                </div>
                <div class="col">
                    <button class="w-100 btn btn-secondary btn-lg"
                            onclick="location.href='items.html'" type="button">취소</button>
                </div>
            </div>
        </form>
    </div> <!-- /container -->
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162597367-c958e426-6cdd-4645-b2dc-528ce46907d1.png)
    
    ```html
    <!DOCTYPE HTML>
    <html>
    <head>
        <meta charset="utf-8">
        <link href="../css/bootstrap.min.css" rel="stylesheet">
        <style>
     .container {
     max-width: 560px;
     }
     </style>
    </head>
    <body>
    <div class="container">
        <div class="py-5 text-center">
            <h2>상품 수정 폼</h2>
        </div>
        <form action="item.html" method="post">
            <div>
                <label for="id">상품 ID</label>
                <input type="text" id="id" name="id" class="form-control" value="1"
                       readonly>
            </div>
            <div>
                <label for="itemName">상품명</label>
                <input type="text" id="itemName" name="itemName" class="form-control" value="상품A">
            </div>
            <div>
                <label for="price">가격</label>
                <input type="text" id="price" name="price" class="form-control"
                       value="10000">
            </div>
            <div>
                <label for="quantity">수량</label>
                <input type="text" id="quantity" name="quantity" class="form-control" value="10">
            </div>
            <hr class="my-4">
            <div class="row">
                <div class="col">
                    <button class="w-100 btn btn-primary btn-lg" type="submit">저장
                    </button>
                </div>
                <div class="col">
                    <button class="w-100 btn btn-secondary btn-lg"
                            onclick="location.href='item.html'" type="button">취소</button>
                </div>
            </div>
        </form>
    </div> <!-- /container -->
    </body>
    </html>
    ```
