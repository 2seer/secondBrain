---
created: ["2022-06-05 23:55"]
aliases: []
tags : [3.자기계발/A.Java/1.SpringBoot/JPA, CreatedDate, LastModifiedDate, MappedSuperclass]
comments: true
---

# Today I Learned
## Created : [[2022-06-05]]
## Related tags : [[Getter]], [[@MappedSuperclass]], [[@EntityListeners]], [[@CreatedDate]], [[@LastModifiedDate]], [[@EnableJapAuditing]]
## 내용
- 보통 엔티티에는 해당 데이터의 생성시간과 수정시간을 포함하는데 반복되는 코드를 JPA Auditing을 사용함으로 해결할 수 있다.

BaseTimeEntity 
```Java
@Getter
@MappedSuperclass // JPA Entity 클래스들이 BaseTimeEntity를 상속할 경우 필드들도 칼럼으로 인식하도록 한다.
@EntityListeners(AuditingEntityListener.class) // BaseTimeEntity 클래스에 Auditing 기능을 포함시킨다.
public abstract class BaseTimeEntity {

	@CreatedDate // Entity가 생성되어 저장될 때 시간이 자동 저장된다.
	private LocalDateTime createdDate;
	
	@LastModifiedDate // 조회한 Entity의 값이 변경될 때 시간이 자동 저장된다.
	private LocalDateTime modifiedDate;
}
```

[[스프링 부트에서 JPA로 데이터베이스를 다뤄보자|Posts]] 클래스가 BaseTimeEntity를 상속 받도록 변경하고 Application 클래스에 활성화 어노테이션 하나를 추가하자.

@EnableJapAuditing // JPA Auditing 활성화

## ⏱히스토리
	- 2022-06-05 23:55 최초 작성


📙URL :