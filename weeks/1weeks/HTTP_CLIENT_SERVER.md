# HTTP Client & Server

**TCP/IP 를 기반으로 HTTP 가 올라간다.**  
최근에는 HTTP/3 에서 UDP 를 이용한 QUIC 프로토콜을 사용하기도 하지만, 기본적으로 HTTP/2 에서는 SPDY 프로토콜을 기반으로 한다.

[💡 HTTP/n 알아보러가기 👉](./HTTPn.md)

## TCP/UDP
전송 계층에 하는 가장 대표적인 프로토콜들이다. 
- TCP : 연결이 필요하고, 전달 및 순서를 보장된다.
- UDP : 연결이 필요하지 않고, 전달 및 순서를 보장하지 않는다.

## Socket

### Socket vs Socket API
**Socket** 은 네트워크 Socket 이며 컴퓨터 네트워크를 경유하는 프로세스 간 통신의 종착점이고, 그 연결간에 어떻게 통신할 것이냐를 결정하는게 [**Socket API**](https://en.wikipedia.org/wiki/Berkeley_sockets) 이다.  
Socket 은 기본적으로 파일과 유사하게 다룰 수 있고, Java 에서는 키보드 입력,화면 출력, 파일 IO 등과 마찬가지로 Steam 으로 다룰 수 있다. **(NOT Stream API)**

## TCP 의 통신순서
1. Listen : 접속 요청을 받기 위한 소켓을 **서버**가 연다.
2. Connect : Client 는 소켓을 만들고, 서버에 접속을 요청한다.
3. Accept : 서버는 접속 요청을 받고, 클라이언트와 통신할 소켓을 **따로** 만든다.
4. Send & Receive : 소켓을 통해 데이터를 주고 받는다. (반복)
5. Close : 통신 완료 후 소켓을 닫는다. (Client 는 Receive 로 인지)

### Client 관점에서의 흐름 알아보기
제일 먼저, Host 를 알아야 한다.  
Host 는 IP 혹은 [domain 이름](#domain-이름)으로 사용이 가능하다.  

그 다음으로는 port 를 알아야 한다.  
port는 기본적으로는 80 port 이며, HTTPS 의 경우 443이 된다.  

IP와 Port만 알면 기본적으로 서버에 접속할 수 있다.  
```java
Socket socket = new Socket({host}, {port});
```

이 다음으로는 Request 를 하면 되는데, 아래와 같은 형태가 된다.   
```java
String message = """
GET / HTTP/1.1
Host: example.com

""";

// NOTE: 방법1, 직접 작성
OutputStreamWriter writer = new OutputStreamWriter(socket.getOutputStream());
writer.write(message);
writer.flush(); // NOTE: 내부적으로 buffer 가 있기에, 잊지 않기 !

// NOTE: 방법2, Socket 활용
OutoutStream outputStream = socket.getOutputStream();
outputStream.write(message.getBytes());
```

그 뒤는 이제 응답을 받을 차례인데, Socket에서 Input Stream 을 통해 얻을 수 있다.  
배열을 준비 한 뒤 받으면 되는데, 준비한 배열보다 응답이 큰 경우 여러번 읽는 것이 필요하다.  

```java
InputStream inputStream = socket.getInputStream();

byte[] bytes = new byte[1_000_000];
int readSize = inputStream.read(bytes); // NOTE: 얼마나 읽었는지를 return 해준다.

byte[] data = Arrays.copyOf(bytes, readSize); // NOTE: 받은 만큼만 짜르는 행위
String responseText = new String(data); // NOTE: DONE!

socket.close();
```

`responseText` 는 아래와 같은 형태가 될 것이다.

```markdown
HTTP/1.1 200 OK
Accept-Ranges: bytes
...중략...
<--빈줄-->
Body 내용
```

추가적으로, 데이터를 받을때 Reader 를 쓰면 편하다

```java
InputStream inputStream = socket.getInputStream();
InputStreamReader reader = new InputStreamReader(inputStream);

CharBuffer charBuffer = CharBuffer.allocate(1_000_000);

reader.read(charBuffer); // NOTE: 알아서 사이즈 별로...
charBuffer.flip(); // NOTE: flip 을 해줘야만 string이 정상적으로 나온다.
String text = charBuffer.toString();

socket.close();
```

`socket.close()` 을 통해 종료를 하긴 했으나, `try-with-resources` 를 써도 된다.  
try 영역을 벗어나면, 자동으로 Close가 된다.

```java
try (Socket socket = new Socket("example.com", 80)) {
    // ...Request~Response
} catch {}
```

### Server 관점에서의 흐름 알아보기
내 서버를 띄우는 것이기 때문에 port 만 정하면 된다.  
```java
ServerSocket listener = new ServerSocket({port});
```

여기까지 하면, 이제 `Listen` 할 준비가 완료된 것이고, Client 요청만 오면 된다.  
Client에서 접속하게 되면 통신용 소켓을 따로 만들어서 돌려준다.  
```java
Socket socket = listener.accept();
```

요청이 들어오기 까지 **기다리고 있다가**, 요청이 오면 위 라인으로 받는다 !

> I/O 에서 이렇게 기다리고 있는 것을 `Blocking`이라고 한다.  
> 모든 I/O 에서 Blocking 동작이 있지만, `accept` 처럼 요청이 없는 경우에는 영원이 기다릴 수 있다.  
> 그렇기에 멀티스레드, 비동기, 이벤트기반처리 따위 가 필요한 이유이다.  

요청을 받았으면 이제 Request 를 처리해야 하는데, Read 한 뒤에 처리하면 된다.  
Client 에서 Read 하는 것과 완전히 동일하기에 생략한다.

이제는 응답할 차례 인데, 클라이언트와 동일하게 응답 메시지를 만들어서 전송하면 된다.    
(마지막 줄에 New Line을 잊으면 안돼요!)
```java
String message = """
HTTP/1.1 200 OK

Hello, world!
""";

Writer writer = new OutputStreamWriter(socket.getOutputStream());
writer.write(message);
writer.flush();

socket.close();
listener.close();
```

이렇게 되면, 응답까지 내려가게 되나, 한번의 요청으로 서버가 끝나게 된다.  
이를 위해서는 Accept 를 계속해주면 된다.  
```java
while (true) {
    Socket socket = listener.accept();
    
    // ..중략

    socket.close();
}
```

여기까지 Simple 하게 할 수 있는 영역이고, message 에 조금 더 있써빌러티를 넣어주자.  
```java
String responseBody = "Hello, world";
byte[] bytes = responseBody.getBytes();
String message = "" +
    "HTTP/1.1 200 OK\n" +
    "Content-Type: text/html; charset=UTF-8\n" + 
    "Content-Length: " + bytes.length + "\n" +
    "\n" + body;
```

> Content-Length 로 정확한 크기를 알 수 있기 때문에, body 이후에 New Line을 넣어주지 않아도 된다.

---
###### domain 이름
`naver.com` 과 같이 IP 형태가 아니지만, DNS에 의해 IP를 쉽게 부르게 해주는 별명이라고 생각하면 된다.

###### """
java 에서 큰따옴표 3개는 여러줄을 표현할 때 쓴다.
