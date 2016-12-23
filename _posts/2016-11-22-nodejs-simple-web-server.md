---
layout: post
title: 간단한 웹 서버 생성하기
category: Node.js
tag: [Node.js, web server]
---

Node.js로 만드는 간단한 웹 서버입니다.

웹 서버를 만들어 실행한 뒤, 클라이언트의 접속이나 요청으로 발생하는 이벤트는 다음과 같습니다.

* connection : 클라이언트가 접속할 때 발생합니다. IP 주소나 port를 알 수 있습니다.
* request : 클라이언트의 request 입니다. response를 통해 클라이언트에게 응답할 수 있습니다.


<pre class="prettyprint" style="font-size:0.7em;">
var http = require("http");

var server = http.createServer();
var port = 3000;

server.listen(port, function() {
    console.log("Simple web server is started... (port : %d)", port);
});

server.on("connection", function(socket) {
    var addr = socket.address();
    console.log("Client(%s, %d) is connected.", addr.address, addr.port);
});

server.on("request", function(request, response) {
    console.log("Client is requested");
    console.dir("request");

    response.writeHead(200, {"Content-Type" : "text/plain"});
    response.end("Hello, SnowDeer's Node.js\n");
});

server.on("close", function() {
    console.log("Simple web server is closed.");
});
</pre>

<br>

그럼 여기에, Response에 이미지를 실어보내는 코드도 작성해 보겠습니다.

<pre class="prettyprint" style="font-size:0.7em;">
var http = require("http");
var fs = require("fs");

var server = http.createServer();
var port = 3000;

server.listen(port, function() {
    console.log("Simple web server is started... (port : %d)", port);
});

server.on("connection", function(socket) {
    var addr = socket.address();
    console.log("Client(%s, %d) is connected.", addr.address, addr.port);
});

server.on("request", function(request, response) {
    console.log("Client is requested");
    console.dir("request");

    var filename = "image.png";
    fs.readFile(filename, function (error, data) {
        response.writeHead(200, {"Content-Type" : "image/png"});
        response.write(data);
        response.end();
    });
});

server.on("close", function() {
    console.log("Simple web server is closed.");
});
</pre>

웹브라우저에서 접속하면 다음과 같은 화면이 출력됩니다.

![image -fullwidth]({{ site.baseurl }}/assets/2016-11-22-nodejs-simple-web-server/1.png)