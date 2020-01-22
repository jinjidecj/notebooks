# jQuery 效果

## 隐藏、显示

```javascript
$(selector).hide(speed,callback);
$(selector).show(speed,callback);

可选的 speed 参数规定隐藏/显示的速度，可以取以下值："slow"、"fast" 或毫秒。
可选的 callback 参数是隐藏或显示完成后所执行的函数名称。
```

```js
$(selector).toggle();
切换hide()和show()方法
```

## 淡入淡出

```js
$(selector).fadeIn(speed,callback);
```

```
$(selector).fadeOut(speed,callback);
```

```
$(selector).fadeToggle(speed,callback);
```

```js
$(selector).fadeTo(speed,opacity,callback);
opacity 将淡入淡出效果设置为给定的不透明度 0到1 之间
```

## 滑动

```
$(selector).slideDown(speed,callback);//向下滑动
$(selector).slideUp(speed,callback);
$(selector).slideToggle(speed,callback);
```

## 动画

```js
$(selector).animate({params},apeed,callback);
```

```js
$("button").click(function(){
  var div=$("div");
  div.animate({left:'100px'},"slow");
  div.animate({fontSize:'3em'},"slow");
});
```

## stop()

```js
$(selector).stop(stopAll,goToEnd);
```

# jQuery Html

## 获取

+ text() 文本内容
+ html() 所选元素的内容，包括hteml标记
+ val() 表单字段的值
+ attr(“href") 获取属性

## 设置

在上面提到的四个函数里设置内容

```js
$("#btn1").click(function(){
  $("#test1").text("Hello world!");
});
$("#btn2").click(function(){
  $("#test2").html("<b>Hello world!</b>");
});
$("#btn3").click(function(){
  $("#test3").val("Dolly Duck");
});
```

```js
$("#btn1").click(function(){
  $("#test1").text(function(i,origText){
    return "Old text: " + origText + " New text: Hello world!
    (index: " + i + ")";
  });
});

$("#btn2").click(function(){
  $("#test2").html(function(i,origText){
    return "Old html: " + origText + " New html: Hello <b>world!</b>
    (index: " + i + ")";
  });
});
```

```js
$("button").click(function(){
  $("#w3s").attr({
    "href" : "http://www.w3school.com.cn/jquery",
    "title" : "W3School jQuery Tutorial"
  });
});
```

```js
$("button").click(function(){
  $("#w3s").attr("href", function(i,origValue){
    return origValue + "/jquery";
  });
});
```

## 添加

+ append() 结尾插入内容
+ prepend() 开头插入内容
+ after() 被选元素之后插入内容
+ before() 被选元素之前插入内容

```js
$("p").append("Some appended text.");
$("p").prepend("Some prepended text.");
```

```js
function appendText(){
    var txt1="<p>Text.</p>";              // 以 HTML 创建新元素
    var txt2=$("<p></p>").text("Text.");  // 以 jQuery 创建新元素
    var txt3=document.createElement("p");
    txt3.innerHTML="Text.";               // 通过 DOM 来创建文本
    $("body").append(txt1,txt2,txt3);        // 追加新元素
}
```

```js
$("img").after("Some text after");
$("img").before("Some text before");
```

## 删除

+ remove() 删除被选元素
+ empty() 从被选元素中删除子元素

```js
$("#div1").remove();
$("#div1").empty();
$("p").remove(".italic");
```

## CSS类

+ addClass() 添加一个或者多个类
+ removeClass() 删除一个或者多个类
+ toggleClass() 添加和删除一个类的切换操作
+ css() 设置或者返回样式属性 css("propertyname");

```js
$("#div1").addClass("important blue");

$("h1,h2,p").addClass("blue");
$("div").addClass("important");

$("h1,h2,p").removeClass("blue");

$("h1,h2,p").toggleClass("blue");
//返回属性
$("p").css("background-color");
//设置属性
$("p").css("background-color","yellow");

$("p").css({"background-color":"yellow","font-size":"200%"});
```

## 尺寸

+ width() 方法设置或返回元素的宽度（不包括内边距、边框或外边距）。
+ height() 方法设置或返回元素的高度（不包括内边距、边框或外边距）。
+ innerWidth() 方法返回元素的宽度（包括内边距）。
+ innerHeight() 方法返回元素的高度（包括内边距）。
+ outerWidth() 方法返回元素的宽度（包括内边距和边框）。
+ outerHeight() 方法返回元素的高度（包括内边距和边框）。
+ outerWidth(true) 方法返回元素的宽度（包括内边距、边框和外边距）。
+ outerHeight(true) 方法返回元素的高度（包括内边距、边框和外边距）。

# jQuery 遍历

+ parent() 方法返回被选元素的直接父元素。该方法只会向上一级对 DOM 树进行遍历。
+ parents() 方法返回被选元素的所有祖先元素，它一路向上直到文档的根元素 (<html>)。
+ parentsUntil() 方法返回介于两个给定元素之间的所有祖先元素。

```js
$("span").parents("ul");//回所有 <span> 元素的所有祖先，并且它是 <ul> 元素
$("span").parentsUntil("div");//返回介于 <span> 与 <div> 元素之间的所有祖先元素
```

+ children() 方法返回被选元素的所有直接子元素。该方法只会向下一级对 DOM 树进行遍历。
+ find() 方法返回被选元素的后代元素，一路向下直到最后一个后代。

```js
$("div").children();//返回每个 <div> 元素的所有直接子元素
$("div").children("p.1");//返回类名为 "1" 的所有 <p> 元素，并且它们是 <div> 的直接子元素
$("div").find("span");//返回属于 <div> 后代的所有 <span> 元素
$("div").find("*");//返回 <div> 的所有后代
```

+ siblings() 方法返回被选元素的所有同胞元素。

+ next() 方法返回被选元素的下一个同胞元素。

+ nextAll() 方法返回被选元素的所有跟随的同胞元素。

+ nextUntil() 方法返回介于两个给定参数之间的所有跟随的同胞元素。

+ prev(), prevAll() 以及 prevUntil() 方法的工作方式与上面的方法类似，只不过方向相反而已：它们返回的是前面的同胞元素（在 DOM 树中沿着同胞元素向后遍历，而不是向前）。

+ last() 方法返回被选元素的最后一个元素。

+ eq() 方法返回被选元素中带有指定索引号的元素。

  索引号从 0 开始，因此首个元素的索引号是 0 而不是 1。下面的例子选取第二个 <p> 元素（索引号 1）：

  ```js
  $("p").eq(1);
  ```

+ filter() 方法允许您规定一个标准。不匹配这个标准的元素会被从集合中删除，匹配的元素会被返回。

  下面的例子返回带有类名 "intro" 的所有 <p> 元素：

  ```js
  $("p").filter(".intro");
  ```

+ not() 方法返回不匹配标准的所有元素。

  提示：not() 方法与 filter() 相反。

  下面的例子返回不带有类名 "intro" 的所有 <p> 元素：

  ```js
  $("p").not(".intro");
  ```

# AJAX

+ load()方法从服务器加载数据，并把返回的数据放入被选元素中。

  ```js
  $(selector).load(URL,data,callback);
  ```

  可选的 callback 参数规定当 load() 方法完成后所要允许的回调函数。回调函数可以设置不同的参数：

  - *responseTxt* - 包含调用成功时的结果内容

  - *statusTXT* - 包含调用的状态

  - *xhr* - 包含 XMLHttpRequest 对象

    ```js
    $("button").click(function(){
      $("#div1").load("demo_test.txt",function(responseTxt,statusTxt,xhr){
        if(statusTxt=="success")
          alert("外部内容加载成功！");
        if(statusTxt=="error")
          alert("Error: "+xhr.status+": "+xhr.statusText);
      });
    });
    ```

    

+ $.get() 方法通过 HTTP GET 请求从服务器上请求数据。

  ```js
  $.get(URL,callback);
  ```

  必需的 *URL* 参数规定您希望请求的 URL。

  可选的 *callback* 参数是请求成功后所执行的函数名。

  下面的例子使用 $.get() 方法从服务器上的一个文件中取回数据：

  ```js
  $("button").click(function(){
    $.get("demo_test.asp",function(data,status){
      alert("Data: " + data + "\nStatus: " + status);
    });
  });
  ```

+ $.post() 方法通过 HTTP POST 请求从服务器上请求数据。

  ```js
  $.post(URL,data,callback);
  ```

  必需的 *URL* 参数规定您希望请求的 URL。

  可选的 *data* 参数规定连同请求发送的数据。

  可选的 *callback* 参数是请求成功后所执行的函数名。

  下面的例子使用 $.post() 连同请求一起发送数据：

  ```js
  $("button").click(function(){
    $.post("demo_test_post.asp",
    {
      name:"Donald Duck",
      city:"Duckburg"
    },
    function(data,status){
      alert("Data: " + data + "\nStatus: " + status);
    });
  });
  ```

+ ajax()方法

  ```js
  var jsonObj = {"userid":data};
  $.ajax({
  	type: 'POST',
  	url: "getUserById",
  	dataType: "json",
  	data: JSON.stringify(jsonObj),
      contentType : "application/json",
      success: function(data) {
  		$.cookie("username",data.userName);
  	},
      error: function() {
      }
  });
  ```

  