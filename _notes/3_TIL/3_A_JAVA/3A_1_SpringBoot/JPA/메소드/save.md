---
created: ["2022-06-04 22:47"]
aliases: []
tags : [3.자기계발/A.Java/1.SpringBoot/JPA/메소드]
comments: true
date created: 목요일, 7월 7일 2022, 8:14:10 오후
date modified: 금요일, 7월 8일 2022, 8:15:21 오전
---

# Today I Learned
## Created : [[2022-06-04]]

## 내용

```Java
postRepository.save(Posts.builder().title(title).content(content).author("jojoldu@gmail.com").build());
```

- 테이블 posts에 insert/update 쿼리를 실행한다.
- id 값이 있다면 update가, 없다면 insert 쿼리가 실행된다.

## ⏱히스토리
	- 2022-06-04 22:47 최초 작성


📙URL :