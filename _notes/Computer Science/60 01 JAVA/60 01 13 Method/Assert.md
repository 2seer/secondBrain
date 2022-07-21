---
created: ["2022-07-17 16:42"]
tags: [Today_I_Learned]
---

# 요약 
- IF 문을 줄이는 역할만 하는 것이 아니라 프로젝트 규칙을 적용하고 공통을 재사용

# 설명 
- 인수를 검증하고 조건에 맞지 않는 경우 IllegalArgumentException 또는 IllegalStateException을 발생

# 예제
```Java
if(user == null) {
    throw new IllegalArgumentException("사용자 정보가 존재하지 않습니다.");
}
```

```Java
Assert.notEmpty(user, "사용자 정보가 존재하지 않습니다.");
```

## ⏱히스토리
	- 2022-07-17 16:42 최초 작성


📙URL : [Spring Boot - Assert 사용 예제](https://eblo.tistory.com/63)