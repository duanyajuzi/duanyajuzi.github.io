---
layout: post
title: websocket初探
date: 2016-08-30 16:46:29
categories: blog
author:     "jarvis"
catalog:    true
tags:
    - websocket
---
> websocket是h5开始支持一种新型通信协议.java从1.7版本开始也加入了对websocket的支持.普通的http协议建立连接,需要三次握手,而websocket建立连接只需要一次握手动作.

#### 服务端
在开发工具idea上建立一个普通的javaWeb项目,加入maven包管理,加入websocket的jar包

```xml
<dependency>
  <groupId>javax.websocket</groupId>
  <artifactId>javax.websocket-api</artifactId>
  <version>1.1</version>
</dependency>
```

编写java端websocket服务,新建一个java类

``` java
import javax.websocket.*;
import javax.websocket.server.PathParam;
import javax.websocket.server.ServerEndpoint;

/**
 * @author jarvis on 2016/8/3.
 * @version v1.0
 */
@ServerEndpoint("/wsserver/{username}")
public class WsServer {
    /**
     * 连接时触发
     */
    @OnOpen
    public void onOpen(@PathParam("username") String username) {
        System.out.print(username + "连接到服务器");
    }

    /**
     * 连接关闭触发
     */
    @OnClose
    public void onClose() {
        System.out.println("连接关闭");
    }

    /**
     * 收到消息触发
     *
     * @param message
     * @param session
     */
    @OnMessage
    public void onMessage(@PathParam("username") String username, String message, Session session) {
        System.out.println();
        System.out.println(username + "说:" + message);

    }

    /**
     * 异常时触发
     *
     * @param throwable
     */
    @OnError
    public void onError(Throwable throwable) {
        System.out.println("websocket异常" + throwable.getMessage());
    }

}
```

后台从方法名就可以看出各个方法的大概作用

#### 客户端

```html
<%@ page contentType="text/html;charset=utf-8" language="java" %>
<!DOCTYPE html>
<html lang="en">
<head>
    <title>ws</title>
    <meta charset="UTF-8">
    <script src="http://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js"></script>
    <script src="https://cdn.socket.io/socket.io-1.4.5.js"></script>
</head>
<body>
<h2>websocket小试</h2>
输入用户名:<input id="username">
<br>
请输入:&nbsp;&nbsp;<input id="info"><br>
<button id="connect">连接服务器</button>
<button id="send" onclick="send()">发送</button>
<br>
<span id="message"></span>
</body>
<script>
    var ws = null;
    $("#connect").click(function () {
        var username = $("#username").val();
        if ('WebSocket' in window) {
            ws = new WebSocket("ws://localhost:9999/ws/wsserver/" + username)
        } else {
            alert("websocket not support ")
        }
        //连接发生错误的回调方法
        ws.onerror = function () {
            setInfo("error");
        };

        //连接成功建立的回调方法
        ws.onopen = function (event) {
            console.log(ws)
            setInfo("连接成功");
        }

        //接收到消息的回调方法
        ws.onmessage = function (event) {
            setInfo(event.data);
        }

        //连接关闭的回调方法
        ws.onclose = function () {
            setInfo("close");
        }

        //监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。
        window.onbeforeunload = function () {
            ws.close();
        }

        //将消息显示在网页上
        function setInfo(info) {
           $('#message').html(info + '<br/>');
        }

        //关闭连接
        function closeWebSocket() {
            ws.close();
        }

    })
    //发送消息
    function send() {
        var message = $('#info').val();
        ws.send(message);
    }
</script>
</html>


```

#### 测试

![开始连接](/images/2016/08/web_con.png)

![后台](/images/2016/08/server_con.png)

![聊天信息](/images/2016/08/server_chat.png)
