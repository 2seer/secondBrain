---
created: ["2022-06-06 23:52"]
aliases: []
tags: []
comments: true
---

# Today I Learned
## Created : [[2022-06-06]]
## Related tags :  [[@Enumerated]]

## 내용
- 2.0에서는 1.5 방식에서와 달리 client 인증 정보만 입력을 하면 된다. (모두 enum값으로 대체)
- CommonOAtu2Provieder라는 enum에서 구글, 깃허브, 페이스북, 옥타의 기본 설정값을 모두 제공해준다(이외의 소셜로그인인 경우 직접 다 추가를 해야함)

application-oauth.properties
profile=xxx라는 식으로 호출하면 해당 properties의 설정들을 가져올 수 있다.
```Java
spring
	security
		oauth2
			client
				registration
					google
						client-id=클라이언트 ID
						client-secret=클라이언트 보안 비밀
						schope=profile, email # 기본 값이라 별도 등록을 안해도 된다.
```

application.properties에 추가
```java
spring.profiles.inclue=oath
```

.gitignore 추가
```java
application-oauth.properties
```

User
```
@Getter
@NoArgsConstructor
@Entity
public class User extens BaseTimeEntity {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;

	@Column(nullable = false)
	private String name;

	@Column(nullable = false)
	private String email;

	@Column
	private String picture;

	@Enumerated(EnumType.STRING)
	@Column(nullable = false)
	private Role role;

	@Builder
	public User(String name, String email, String picture, Role role) {
		this.name = name;
		this.email;
		this.pricture = picture;
		this.role = role;
	}

	public User update(String name, String picture) {
		this.name = name;
		this.picture = picture;
		return this;
	}

	public String getRoleKey() {
		return this.role.getKey();
	}
}

```

Role
권한 코드에 항상 `ROLE_` 이 앞에 있어야만 한다.
```java
@Getter
@RequiredArgsConstructor
public enum Role {
	GUEST("ROLE_GUEST", "손님");
	USER("ROLE_USER", "일반 사용자");

	private final String key;
	private final String title;
}
```



## ⏱히스토리
	- 2022-06-06 23:52 최초 작성


📙URL :