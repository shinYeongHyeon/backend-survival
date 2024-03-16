# CRUD
1. Create 
   - POST
2. Read
   - GET
3. Update
   - PUT : 전체
   - PATCH : 일부
4. Delete
   - DELETE

종종 Bulk Update,Delete 가 필요할 수도 있는데, 이럴때는 그냥 collection 을 활용하기도 하고, API 문서에 정확히 기록해둠.  
e.g. `DELETE /posts`

## CQS (Command Query Separation)
Command 와 Query 로 CRUD 를 나눌 수 있다.
1. Command: Create, Update, Delete / 상태가 변하며, 안전하지 않다.
2. Query: Read / 상태가 변하지 않으며, 안전하다. 분산/캐시 등이 수월하다.

## 로그인/로그아웃

리소스로 어떻게 표현할 수 있을까 ?  
추상적인 개념을 "session" 을 도입하고는 한다.  

1. POST `/session` : 로그인
2. DELETE `/session` : 로그아웃

추가적으로 내 정보를 확인하고 싶을 때는, `GET /session` 을 쓰거나 통상적으로 `GET /users/me` 를 쓴다.