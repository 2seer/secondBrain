---
created: ["2022-06-04 22:40"]
aliases: []
tags : [3.자기계발/A.Java/1.SpringBoot/JPA]
comments: true
---

# Today I Learned
## Created : [[2022-06-04]]

## 내용
- ibatis나 MyBatis 등에서 Dao라고 불리는 DB Layer 접근자이다.
- JPA에선 Repository라 부르며 [[인터페이스]] 로 생성을 한다.
- 단순하게 인터페이스를 생성 후, `JpaRepository<Entity 클래스, PK 타입>를 상속`하면 기본적인 `CRUD 메소드`가 `자동으로 생성`된다.
- 다만, `Entity 클래스와 기본 Entity Repository는 함께 위치`해야 한다.(@Repository를 추가할 필요가 없다.)

## ⏱히스토리
	- 2022-06-04 22:40 최초 작성


📙URL :