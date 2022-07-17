---
created: ["2022-06-04 23:29"]
aliases: []
tags: []
comments: true
date created: 목요일, 7월 7일 2022, 8:14:25 오후
date modified: 금요일, 7월 8일 2022, 8:15:21 오전
---

# Today I Learned
## Created : [[2022-06-04]]

## 내용
- [[@Autowired]], [[setter]], [[생성자]] 중에 가장 권장하는 방식은 생성자로 주입받는 방식이다.
  (생성자로 Bean 객체를 받도록 하면 @Autowired와 동일한 효과를 볼 수 있고, @Autowired는 권장하지 않는다.)
- 생성자는 [[RequiredArgsConstructor]]를 사용하면 해결되는데 그 이유는 해당 클래스의 의존성 관계가 변경될 때마다 생성자 코드를 계속해서 수정하는 번거로움을 해결하기 위함이다.

## ⏱히스토리
	- 2022-06-04 23:29 최초 작성


📙URL :