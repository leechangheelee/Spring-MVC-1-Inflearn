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

***
  * 상품 목록 - 타임리프
    
    ![image](https://user-images.githubusercontent.com/79301439/162598849-29a69783-e0d0-40b6-b548-49b4f8dffb3a.png)
    
    ```java
    package hello.itemservice.web.basic;

    import hello.itemservice.domain.item.Item;
    import hello.itemservice.domain.item.ItemRepository;
    import lombok.RequiredArgsConstructor;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RequestMapping;

    import javax.annotation.PostConstruct;
    import java.util.List;

    @Controller
    @RequestMapping("/basic/items")
    @RequiredArgsConstructor
    public class BasicItemController {

        private final ItemRepository itemRepository;

        @GetMapping
        public String items(Model model) {
            List<Item> items = itemRepository.findAll();
            model.addAttribute("items", items);
            return "basic/items";
        }

        /**
         * 테스트용 데이터 추가
         */
        @PostConstruct
        public void init() {
            itemRepository.save(new Item("itemA", 100000, 10));
            itemRepository.save(new Item("itemB", 200000, 20));
        }
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162599007-ab33a52a-3aa9-4626-bf5e-097cedb98689.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162598883-5e188a93-df7f-4903-a13f-21f311528b0a.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162598890-79aeccb0-d14c-4010-8c33-916d9d392426.png)
    
    ```html
    <!DOCTYPE HTML>
    <html xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
        <link th:href="@{/css/bootstrap.min.css}"
                href="../css/bootstrap.min.css" rel="stylesheet">
    </head>
    <body>
    <div class="container" style="max-width: 600px">
        <div class="py-5 text-center">
            <h2>상품 목록</h2>
        </div>
        <div class="row">
            <div class="col">
                <button class="btn btn-primary float-end"
                        onclick="location.href='addForm.html'"
                        th:onclick="|location.href='@{/basic/items/add}'|"
                        type="button">상품
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
                <tr th:each="item : ${items}">
                    <td><a href="item.html" th:href="@{/basic/items/{itemId}(itemId=${item.id})}" th:text="${item.id}">회원id</a></td>
                    <td><a href="item.html" th:href="@{|/basic/items/${item.id}|}" th:text="${item.itemName}">상품명</a></td>
                    <td th:text="${item.price}">10000</td>
                    <td th:text="${item.quantity}">10</td>
                </tr>
                </tbody>
            </table>
        </div>
    </div> <!-- /container -->
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162598931-633b25eb-3b15-49f9-a881-0de94e9aff43.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162598947-5dba4c8c-f854-4db0-9794-d0f9d1a318f9.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162598954-856e939d-1cd4-4fae-91a7-64255abbae8a.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162598968-1f4683d5-9708-4969-83aa-da3532b5834c.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162598973-30d61dcd-bdad-4294-acac-8287f30bc016.png)

***
  * 상품 상세
    
    ![image](https://user-images.githubusercontent.com/79301439/162600060-06e8ecd9-a90e-43c7-9ff8-c84aee2b7199.png)
    
    ```java
    @GetMapping("/{itemId}")
    public String item(@PathVariable Long itemId, Model model) {
        Item item = itemRepository.findById(itemId);
        model.addAttribute("item", item);
        return "basic/item";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162600079-b58019dc-e0c6-475a-98d9-48ffa556fed5.png)
    
    ```html
    <!DOCTYPE HTML>
    <html xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
        <link th:href="@{/css/bootstrap.min.css}"
                href="../css/bootstrap.min.css" rel="stylesheet">
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
                   value="1" th:value="${item.id}" readonly>
        </div>
        <div>
            <label for="itemName">상품명</label>
            <input type="text" id="itemName" name="itemName" class="form-control"
                   value="상품A" th:value="${item.itemName}" readonly>
        </div>
        <div>
            <label for="price">가격</label>
            <input type="text" id="price" name="price" class="form-control"
                   value="10000" th:value="${item.price}" readonly>
        </div>
        <div>
            <label for="quantity">수량</label>
            <input type="text" id="quantity" name="quantity" class="form-control"
                   value="10" th:value="${item.quantity}" readonly>
        </div>
        <hr class="my-4">
        <div class="row">
            <div class="col">
                <button class="w-100 btn btn-primary btn-lg"
                        onclick="location.href='editForm.html'"
                        th:onclick="|location.href='@{/basic/items/{itemId}/edit(itemId=${item.id})}'|"
                        type="button">상품 수정</button>
            </div>
            <div class="col">
                <button class="w-100 btn btn-secondary btn-lg"
                        onclick="location.href='items.html'"
                        th:onclick="|location.href='@{/basic/items}'|"
                        type="button">목록으로</button>
            </div>
        </div>
    </div> <!-- /container -->
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162600097-14930c4f-cca5-489a-b6e9-1ad82f72088c.png)

***
  * 상품 등록 폼
    
    ![image](https://user-images.githubusercontent.com/79301439/162614026-73f2c6c8-9634-48ad-8792-66410dc62dc6.png)
    
    ```java
    @GetMapping("/add")
    public String addForm() {
        return "basic/addForm";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162614053-5188e2e5-0017-4287-b2d2-285fd41e3863.png)
    
    ```html
    <!DOCTYPE HTML>
    <html xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
        <link th:href="@{/css/bootstrap.min.css}"
                href="../css/bootstrap.min.css" rel="stylesheet">
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
        <form action="item.html" th:action method="post">
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
                            onclick="location.href='items.html'"
                            th:onclick="|location.href='@{/basic/items}'|"
                            type="button">취소</button>
                </div>
            </div>
        </form>
    </div> <!-- /container -->
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162614095-e5e93ffd-a76e-4d6f-8cca-99e417a42b7e.png)

***
  * 상품 등록 처리 - @ModelAttribute
    
    ![image](https://user-images.githubusercontent.com/79301439/162615093-9e4e9c21-4fa3-4f5f-99ff-d3f54d67718d.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162615100-6b61f7cc-5786-4459-9fdc-979d5b2ad8b9.png)
    
    ```java
    @PostMapping("/add")
    public String addItemV1(@RequestParam String itemName,
                       @RequestParam int price,
                       @RequestParam Integer quantity,
                       Model model) {

        Item item = new Item();
        item.setItemName(itemName);
        item.setPrice(price);
        item.setQuantity(quantity);

        itemRepository.save(item);

        model.addAttribute("item", item);

        return "basic/item";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162615169-d62d678e-dcd8-4aee-b8b0-a24d1d7d5d6b.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162615183-df8e75a3-b079-4590-9230-88dc0c02f861.png)
    
    ```java
    /**
     * @ModelAttribute("item") Item item
     * model.addAttribute("item", item); 자동 추가
     */
    @PostMapping("/add")
    public String addItemV2(@ModelAttribute("item") Item item, Model model) {
        itemRepository.save(item);
        //model.addAttribute("item", item); //자동 추가, 생략 가능

        return "basic/item";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162615286-04e091f4-08ee-4310-9acb-6f6e1a544417.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162615330-6716f8eb-bb47-421a-9f52-21a61650df25.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162615355-7a118986-4971-47ac-be76-e91ab31e919e.png)
    
    ```java
    /**
     * @ModelAttribute name 생략 가능
     * model.addAttribute(item); 자동 추가, 생략 가능
     * 생략시 model에 저장되는 name은 클래스명 첫글자만 소문자로 등록 Item -> item
     */
    @PostMapping("/add")
    public String addItemV3(@ModelAttribute Item item) {
        itemRepository.save(item);
        return "basic/item";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162615422-b38a4948-e494-4f7e-86a4-ea82d544c9f3.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162615438-83b41f70-f97a-4c79-a042-e0fce4633e5b.png)
    
    ```java
    /**
     * @ModelAttribute 자체 생략 가능
     * model.addAttribute(item) 자동 추가
     */
    @PostMapping("/add")
    public String addItemV4(Item item) {
        itemRepository.save(item);
        return "basic/item";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162615477-289c2024-81c8-4412-9b0e-9cd91ea1f03d.png)

***
  * 상품 수정
    
    ![image](https://user-images.githubusercontent.com/79301439/162616526-b1bbbb09-5861-457f-8b81-f78fd31bdeee.png)
    
    ```java
    @GetMapping("/{itemId}/edit")
    public String editForm(@PathVariable Long itemId, Model model) {
        Item item = itemRepository.findById(itemId);
        model.addAttribute("item", item);
        return "basic/editForm";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162616554-303effc5-193b-477d-9ac4-e94971ce024b.png)
    
    ```html
    <!DOCTYPE HTML>
    <html xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
        <link th:href="@{/css/bootstrap.min.css}"
                href="../css/bootstrap.min.css" rel="stylesheet">
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
        <form action="item.html" th:action method="post">
            <div>
                <label for="id">상품 ID</label>
                <input type="text" id="id" name="id" class="form-control" value="1" th:value="${item.id}"
                       readonly>
            </div>
            <div>
                <label for="itemName">상품명</label>
                <input type="text" id="itemName" name="itemName" class="form-control" value="상품A" th:value="${item.itemName}">
            </div>
            <div>
                <label for="price">가격</label>
                <input type="text" id="price" name="price" class="form-control"
                       value="10000" th:value="${item.price}">
            </div>
            <div>
                <label for="quantity">수량</label>
                <input type="text" id="quantity" name="quantity" class="form-control" value="10" th:value="${item.quantity}">
            </div>
            <hr class="my-4">
            <div class="row">
                <div class="col">
                    <button class="w-100 btn btn-primary btn-lg" type="submit">저장
                    </button>
                </div>
                <div class="col">
                    <button class="w-100 btn btn-secondary btn-lg"
                            onclick="location.href='item.html'"
                            th:onclick="|location.href='@{/basic/items/{itemId}(itemId=${item.id})}'|"
                            type="button">취소</button>
                </div>
            </div>
        </form>
    </div> <!-- /container -->
    </body>
    </html>
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162616577-e2102b67-f52d-4983-98bf-94bb9025c7f0.png)
    
    ```java
    @PostMapping("/{itemId}/edit")
    public String edit(@PathVariable Long itemId, @ModelAttribute Item item) {
        itemRepository.update(itemId, item);
        return "redirect:/basic/items/{itemId}";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162616645-afc89784-976a-4fe2-8e7d-435c608912c0.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162616658-240ff8d1-8d12-4753-862d-aef2d5af59af.png)

***
  * PRG Post/Redirect/Get
    
    ![image](https://user-images.githubusercontent.com/79301439/162617182-1d99f1b7-8dfb-464a-901a-93a5b9270852.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162617202-cc0fe6f3-7de3-44e5-9a81-74e7d0125181.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162617213-2c133246-e76e-4f38-ab26-4cf790b3eda3.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162617241-820a6a0f-1edc-4864-a657-b00c51b87de5.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162617255-d75a7e05-2bca-400a-9dc9-d26eafac0219.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162617265-ff9f34ba-02da-4352-acdd-dbbf0c264bd3.png)
    
    ```java
    /**
     * PRG - Post/Redirect/Get
     */
    @PostMapping("/add")
    public String addItemV5(Item item) {
        itemRepository.save(item);
        return "redirect:/basic/items/" + item.getId();
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162617322-fdb874ef-b918-4a25-90ed-7255a032844d.png)

***
  * RedirectAttributes
    
    ![image](https://user-images.githubusercontent.com/79301439/162617990-2ac40e01-cffb-42f1-978e-4b5f943221c9.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162618001-37133bad-04e8-44f5-8532-4e0b37fbd1c1.png)
    
    ```java
    /**
     * RedirectAttributes
     */
    @PostMapping("/add")
    public String addItemV6(Item item, RedirectAttributes redirectAttributes) {
        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true);
        return "redirect:/basic/items/{itemId}";
    }
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162618077-a885b4af-ca6c-4a41-ab8b-49384512b9ca.png)
    
    ![image](https://user-images.githubusercontent.com/79301439/162618104-85591ca3-65eb-4331-8840-413880d4c9d6.png)
    
    ```html
    ...
    
    <div class="container">    
        <div class="py-5 text-center">
            <h2>상품 상세</h2>
        </div>

        <!-- 추가 -->
        <h2 th:if="${param.status}" th:text="'저장 완료'"></h2> 
    
    ...
    ```
    
    ![image](https://user-images.githubusercontent.com/79301439/162618160-c27fac1d-11af-4099-92c7-fee2ddfaca50.png)
