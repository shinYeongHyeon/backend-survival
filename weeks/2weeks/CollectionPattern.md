# Collection Pattern

대부분, 여러 리소스를 하나의 그룹으로 묶을 수 있다. 
게시물을 예로 들어보자

**Pattern을 쓰지 않는 경우**
- /the-first-post
- /test-post

**Pattern을 쓰는 경우**
- /posts
- /posts/the-first
- /posts/test
- /posts/:id

**Post ID 는 리소스의 ID가 아니다!**
- Resource ID = URI
- Post ID
  - Resource ID 를 구성하는 요소중 하나 이고, posts 그룹 내 식별자

그룹명은 복수형으로  사용해야 한다. 개별 요소를 찾는 `posts/1` 의 경우에도 복수형으로 사용해야 한다.  
특정 게시물에 댓글을 찾는다고 가정하면 `posts/:id/comments` 와 같이 사용해야 한다.  
더 나아가, 코멘트 하나의 정보까지 찾는다고 하면 `posts/:id/comments/:id` 가 된다.

그러나, 그룹이 아닌 경우라면 그냥 단수형을 써도 된다.
`/session` : 세션은 하나만 유지되고, 다른 세션을 참고할 일도 없다.