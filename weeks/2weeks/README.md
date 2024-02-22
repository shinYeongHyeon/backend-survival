# RestAPI

## API
Application Programming Interface 의 약어로, 어떻게 작동하는지를 알려주는게 아닌, 객체가 정의하는 연산의 모든 시그니처들을 응답으로 알려준다 정도로 이해하면 된다.
소통을 위한 것으로, `어떤 것을 주면 어떤걸 알려줄게`
예시로, 키보드에서 `a` 키를 누르면 `a` 가 화면에 보여질텐데, 이 것이 내부적으로 어떻게 동작하는지는 모르지만 a 를 누르면 a 를 화면에 띄어줄게를 약속한  API 인것이다.

## Rest (Representational State Transfer)
1. Starting with the Null Style
2. Client-Server
3. Stateless
4. Cache
5. **Uniform Interface**
    1. 일반적 소프트웨어 공학의 원칙을 적용한 것
    2. 구현이 독립적이기 때문에, 따로 발전 시킬 수 있음
    3. Standard 한 Form
        1. Resource 표현시 URI 로 표현한다. => URI 로 식별할 수 있다.
        2. 표현으로 Resource 를 조작한다.
        3. 메시지는 자기서술적이기 때문에 여러 레이어에서 처리/변환이  가능하다
            1. 메시지가 무엇인줄 알아야 처리가 가능한데, 이를 URI 로 설명하는 것
            2. `application/json` 이 아니라 `application/dns+json`
                1. dns 를 조작할 거고, json 으로 보내줄게
            3. 까다로운 부분
        4. Hypermedia as the Engine of Application State (HATEOAS) ???
            1. 이렇게 표현하려면 액션에 대해 표현이 되어야 한다.
            2. 한마디로 응답으로 링크를 건내주는 것
            3. 가장 까다로운 부분
            4. 효율적인 문제로, 표현에 링크를 넣지 않고 클라이언트가 개발자가 API 문서를 활용해 처리 한다.
            5. 예를 들어 어떤 응답을 주고 상세하게 보고 싶어할 경우에 응답으로 상세 API 링크를 내려주는 것
6. Layered Architecture
7. Code-On-Demand

## 자원과 표현

### 자원 (Resource)
추상적인 것
특정 시점의 스냅샷이 아닌, 모든 시간에 통용되는 엔티티의 집합. 객체 지향에서 말하는 Entity 라고 생각하면 편함, 내가 키가 커지던 작아지던 나는 나다.

### 표현 (Presentation)
Data + Metadata ...
사실상 HTTP 메시지라고 보면 된다. 가령, 리소스를 어떻게 조작할 것을 HTTP Method 로 표현하고, 무엇을 조작할지 Content-Type 과 Body로 표현
