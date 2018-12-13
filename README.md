前端AJAX只能呼叫**同源**的問題

我們可以使用Jsonp或跨域资源共享(CORS)來解決，但這個必須在後端來實作

如果要在前端解決我們可以使用  PostMessage  

## **簡介：**

瀏覽器將CORS請求分成兩類：簡單請求（simple request）和非簡單請求（not-so-simple request）。

只要同時滿足以下兩大條件，就屬於簡單請求。

1. 請求方法是以下三種方法之一：
    * HEAD
    * GET
    * POST

2. HTTP Header資訊不超出以下幾種：

    ```
    Accept
    Accept-Language
    Content-Language
    Last-Event-ID
    Content-Type：只限於三個值
       * application/x-www-form-urlencode
       * multipart/form-data
       * text/plain
    ```


 

在這次範例中我們製作兩個Html頁面

希望使用js在Iframe傳遞資訊

 
---
## `postMessage.html` 頁面中 

我們創建一個Iframe 來源指向 `listener.html` 頁面

使用 `window.frames[0].postMessage($txt_val, '*');` 將資料推到 頁面Iframes上

```html
<html>

<head>
  <title></title>
  <script src="./lib/jquery-3.2.1.min.js"></script>
  <script type="text/javascript">
    //將資料提交到Iframe裡面
    function postMessage() {
      var $txt_val = $('#txt_val').val();
      //第一個參數傳出去的值,第二個參數限定的網域
      window.frames[0].postMessage($txt_val, '*');
    }
    $(function () {
      //增加接收的方法 使用匿名labda方法
      //event參數裡面有data屬性代表接收資料
      window.addEventListener('message', (event) => {
        $('#txt_show').html(event.data);
      });
    });
  </script>
</head>

<body>
  <input id="txt_val" type="text" />
  <button onclick="postMessage()">提交</button>
  <div id="txt_show"></div>
  <iframe src="listener.html"></iframe>
</body>

</html>
```

----

## `listener.html` 頁面中 


最主要由 `window.addEventListener("message", receiveMessage);` 來接收資訊

`window.addEventListener` 方法:

第一個參數是`'message'`
第二個是接收的方法(裡面會有一個變數可以使用變數裡面的data來獲取資料)
 
```html
<html>

<head>
    <title></title>
    <script src="./lib/jquery-3.2.1.min.js"></script>
    <script type="text/javascript">
        function receiveMessage(event) {
            //event參數裡面有data屬性代表接收資料
            $('#txt_show').html(event.data);
        }
        $(function () {
            window.addEventListener("message", receiveMessage);
        });
        function postMessage()
        {
            var $val = $('#txt_val').val();
            //第一個參數傳出去的值,第二個參數限定的網域
            window.parent.postMessage($val,'*');
        }
    </script>
</head>

<body>
    <div id="txt_show"></div>
    <button onclick="postMessage();">提交</button>
    <input id="txt_val" type="text" value="從子頁面傳出的資料!!" />
</body>

</html>
 ```

 同理 `window.parent.postMessage($val,'*');` 也可將資料push至父視窗中