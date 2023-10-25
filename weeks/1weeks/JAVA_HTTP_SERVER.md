# Java HTTP Server

Non-Blocking I/O 를 내부적으로 사용하는 고수준의 HTTP Server API

## 흐름알아가기

### 1. 서버객체를 준비

```java
InetSocketAddress address = new InetSocketAddress(8080);
int backlog = 0;

HttpServer httpServer = HttpServer.create(address, backlog);
httpServer.start();
```

### 2. 이제 응답해줄 서버 Path 설정하기 (Handler 등록)

```java
httpServer.createContext("/", (exchange) -> {
  // TODO: Implement    
});
```

여기서 이제, request 를 처리해야하는데 여러가지들이 존재한다.

- Method 구하기 : `exchange.getRequestMethod()`
- 주소 구하기 : `exchange.getRequestURI()`
- Header 구하기 : `exchange.getRequestHeaders()`
- Body 구하기 : `exchange.getRequestBody()`

### 3. 응답하기

```java
String content = "Hello World";
byte[] bytes = content.getBytes();

exchange.sendResponseHeaders(200, bytes.length);

OutputStream outputStream = exchange.getResponseBody(); 
outputStream.write();
outputStream.flush();
```

`sendResponseHeader` 로 상태와 길이를 보내주고, `getResponseBody` 로 Body 를 구한뒤, 응답을 보내주면 된다.
