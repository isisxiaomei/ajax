# 1. ajax

## 1.1 ajax 简介

- **作用**：ajax 可以跟后台交换数据，实现网页局部更新
  - 注册时检测用户名已存在
  - 登录时检测用户名和密码错误
  - 删除数据时可以将 id 发送到后台服务器，服务器在数据库中删除

## 1.2 iframe

- **背景**：在没有 ajax 之前局部更新需要用到 iframe
- **定义**：能够将另一个 HTML 页面嵌入到当前页面中。

```html
<!-- 示例1：刷新的是 iframe页面而不是整个大网页面-->
<form
  action="http://yapi.demo.qunar.com/mock/18150/test/ajax.html"
  method="get"
  target="myframe"
>
  姓名：<input type="text" name="userName" /><br />
  密码：<input type="password" name="passwd" />
  <input type="submit" value="登录" />
</form>
<iframe name="myframe"></iframe>
```

## 1.3 ajax 基础操作
+ XMLHttpRequest 术语缩写为XHR，中文可以解释为可扩展超文本传输请求。
```html
<!-- 示例1： -->
<script>
  window.onload = () => {
    var btn = document.getElementById("btn");
    var respInfo = document.getElementById("respInfo");

    btn.onclick = function() {
      var userName = document.getElementById("userName").value;
      var passwd = document.getElementById("passwd").value;

      // 第一步：创建XMLHttpRequest请求对象
      var xhr = new XMLHttpRequest();
      // 第二步：准备发送open(method, url, 默认true异步-false同步)
      xhr.open(
        "get",
        `http://yapi.demo.qunar.com/mock/18150/test/ajax.html?userName=${userName}&passwd=${passwd}`,
        true
      );
      // 第三步：执行发送动作；get方式默认发送null；post方式sent(post数据)
      xhr.send(null);
      // 第四步：响应的回调函数
      xhr.onreadystatechange = () => {
        // readyState==4 表示接受到数据 status==200表示数据时正常的
        if (xhr.readyState == 4 && xhr.status == 200) {
          respInfo.innerHTML = xhr.responseText;
        }
      };
    };
  };
</script>

<form>
  姓名：<input type="text" name="userName" id="userName" /><span
    id="respInfo"
  ></span
  ><br />
  密码：<input type="password" name="passwd" id="passwd" />
  <input type="button" value="登录" id="btn" />
</form>
```
### 1.3.1 XMLHttpRequest对象的兼容处理
+ XMLHttpRequest对象IE6不支持；
+ IE6支持的是`ActiveXObject("Microsoft.XMLHTTP");`
```js
// 兼容处理方式1：
var xhr = null;
xhr = window.XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject("Microsoft");

// 兼容处理方式2：
try{
    xhr = new XMLHttpRequest();
} catch(e) {
    xhr = new ActiveXObject("Microsoft");
}
```
### 1.3.2 准备发送
+ open()
    - 参数1：请求方法
    - 参数2：请求地址
    - 参数3：同步异步标志位，默认true为异步；
+ 如果是get请求需要将参数直接连接在`请求地址`上，并且如果是中文需要encodeURI()进行编码防止乱码（因为IE有时候不支持这里的参数是中文）

```js
// 示例1：
xhr.open("get", "http://ip:port/ajax.html?userName=${userName}&passwd=${passwd}`, true)
// 示例2
var param = "userName="+userName+"&passwd="+passwd;
xhr.open('get', "http://ip:port/ajax.html?"+encodeURI(param), true);
```
### 1.3.3 发送阶段
+ get请求：`send(null)` send必须添加null为参数
+ post请求：`send(post数据)`, 并且post数据不需要转码; 但是必须在send之前设置请求头信息，否则数据无法传递
+ 设置请求头：`setRequestHeader(headerKey,value)`
```js
// 示例1
var param = "userName="+userName+"&passwd="+passwd;
xhr.open('post', "http://ip:port/ajax.html", true);
xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
xhr.send(param);
```
### 1.3.4 响应回调

+ 注意：onreadystatechange事件被触发 5 次（0 - 4）；每当readyState的属性改变时，**浏览器**调用该函数就会调用该函数
+ readyState:
  - 0：表示对象已经创建，否则报错
  - 1：表示调用open方法和send方法
  - 2：表示已经接收完全部响应数据
  - 3：表示正在解析数据——根据服务器端响应头部返回的MIME类型把数据转换成能通过responseBody，responseText或responseXML的属性存取的格式
  - 4：表示已经解析完成；可以调用属性获取数据
+ reponseXML缺点：格式便签不利于传输；解析不方便等于解析xml而不是解析数据
+ reponseXML解析：可以通过代码dom获取标签的方式进行解析

### 1.3.5 XMLHttpRequest对象常用属性和方法
![](image/../../image/../ajax/image/Snipaste_2019-10-26_23-06-55.png)
![](image/../../image/../ajax/image/方法.png)



