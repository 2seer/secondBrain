2022-05-15 19:17
## Today I Learned
---
aliases : 스프링부트

tags : #Test

---

## Content
```java
private MockMvc mvc
// 웹 API를 테스트할 때 사용
mvc.perform(get('/hello')
// MockMvc를 통해 /hello 주소로 HTTP GET 요청을 한다.
.andExpect(status().isOk())
// HTTP Header의 Status가 200인지 아닌지를 검증한다.
.andExpect(content().string(hello))
// 응답 본문의 내용이 hello가 맞는지를 검증한다.
```
## ⏱Log
-


📙출처 : [[스프링 부트와 AWS로 혼자 구현하는 웹 서비스]]
URL :