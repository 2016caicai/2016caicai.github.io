---
layout: post
title: websockect初体验
categories: [node ]
tags: [node ]
description: node
---
## html文件
```
    <div class="vertical-center">
        <div class="container">
            <ul id="messages" class="list-unstyled"></ul>
            <hr/>
            <form role="form" id="chat_form" onsubmit="sendMessage(); return false;">
                <div class="form-group">
                    <input class="form-control" type="text" id="message" name="message"
                           placeholder="Type text to echo in here" value="" autofocus/>
                </div>
                <button type="button" id="send" class="btn btn-primary"
                        onclick="sendMessage();">
                    Send Message
                </button>

            </form>
            <div class="form-group"><span>nikename：</span><input id="name" type="text" /> <button class="btn btn-sm btn-info" onclick="changName();">change</button></div>
        </div>
    </div>
```
    ## js文件
    ```
    //建立连接
    var ws = new WebSocket("ws://192.168.2.242:8181");
    var nickname = "";
    ws.onopen = function (e) {
        console.log('Connection to server opened');
    }
    //显示
    function appendLog(type, nickname, message) {
        if (typeof message == "undefined") return;
        var messages = document.getElementById('messages');
        var messageElem = document.createElement("li");
        var preface_label;
        if (type === 'notification') {
            preface_label = "<span class=\"label label-info\">*</span>";
        } else if (type == 'nick_update') {
            preface_label = "<span class=\"label label-warning\">*</span>";
        } else {
            preface_label = "<span class=\"label label-success\">"
                    + nickname + "</span>";
        }
        var message_text = "<h2>" + preface_label + "&nbsp;&nbsp;"
                + message + "</h2>";
        messageElem.innerHTML = message_text;
        messages.appendChild(messageElem);
    }
    //收到消息处理
    ws.onmessage = function (e) {
        var data = JSON.parse(e.data);
        nickname = data.nickname;
        appendLog(data.type, data.nickname, data.message);
        console.log("ID: [%s] = %s", data.id, data.message);
    }
    //监听关闭消息
    ws.onclose = function (e) {
        appendLog("Connection closed");
        console.log("Connection closed");
    }
    //发送消息
    function sendMessage() {
        var messageField = document.getElementById('message');
        if (ws.readyState === WebSocket.OPEN) {
            ws.send(messageField.value);
        }
        messageField.value = '';
        messageField.focus();
    }
    //修改名称
    function changName() {
        var name = $("#name").val();
        if (ws.readyState === WebSocket.OPEN) {
            ws.send("/nick " + name);
        }
    }
    
```
## 服务器端

```
    var WebSocket = require('ws');
var WebSocketServer = WebSocket.Server;
//var WebSokectServer=require('ws').Server;
var wss=new WebSocketServer({port:8181});
var uuid=require('node-uuid');
var clients = [];
var clientIndex = 1;
//建立连接
wss.on('connection',function(ws) {
    var client_uuid = uuid.v4();
    var nickname = "user" + clientIndex;
    clientIndex += 1;
    clients.push({"id": client_uuid, "ws": ws, "nickname": nickname});
    console.log('client [%s] connected', client_uuid);
    var connect_message = nickname + " has connected";
    wsSend("notification", client_uuid, nickname, connect_message);
    ws.on('message', function (message) {
        if (message.indexOf('/nick') === 0) {
            var nickname_array = message.split(' ');
            if (nickname_array.length >= 2) {
                var old_nickname = nickname;
                nickname = nickname_array[1];
                var nickname_message = "Client " + old_nickname + " changed to " + nickname;
                wsSend("nick_update", client_uuid, nickname, nickname_message);
            }
        } else {
            wsSend("message", client_uuid, nickname, message);
        }
    });
    //监听连接关闭
    var closeSocket = function(customMessage) {
        for (var i = 0; i < clients.length; i++) {
            if (clients[i].id == client_uuid) {
                var disconnect_message;
                if (customMessage) {
                    disconnect_message = customMessage;
                } else {
                    disconnect_message = nickname + " has disconnected";
                }
                wsSend("notification", client_uuid, nickname, disconnect_message);
                clients.splice(i, 1);
            }
        }
    };
    ws.on('close', function () {
        closeSocket();
    });
})
//推送消息
function wsSend(type, client_uuid, nickname, message) {
    for (var i = 0; i < clients.length; i++) {
        var clientSocket = clients[i].ws;
        if (clientSocket.readyState === WebSocket.OPEN) {
            clientSocket.send(JSON.stringify({
                "type": type,
                "id": client_uuid,
                "nickname": nickname,
                "message": message
            }));
        }
    }
}
```

[来源](http://www.cnblogs.com/stoneniqiu/p/5402311.html)
