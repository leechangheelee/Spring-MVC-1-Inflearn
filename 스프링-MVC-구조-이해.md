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
