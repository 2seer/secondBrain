---
created: ["2022-06-06 23:52"]
aliases: []
tags: []
comments: true
---

# Today I Learned
## 작성일 : [[2022-06-06]]
## 분야 :
## 키워드 : #Enumerated, #NoArgsConstructor
## 상태 :  #작성중, #연결중 
## 내용
- 2.0에서는 1.5 방식에서와 달리 client 인증 정보만 입력을 하면 된다. (모두 enum값으로 대체)
- CommonOAtu2Provieder라는 enum에서 구글, 깃허브, 페이스북, 옥타(2.7.0 이후 폐지)의 기본 설정값을 모두 제공해준다(이외의 소셜로그인인 경우 직접 다 추가를 해야함)

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
```Java
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

UserRepository
1. findByEamil : 소셜 로그인으로 반환되는 값 중 email을 통해 이미 생성된 사용자인지 처음 가입하는 사용자인지 판단하기 위한 메소드
```Java
public interface UserRepository extends JpaRepository<User, Long> {
	Optional<User> findByEmail(String email); // 1
}
```

스프링 시큐리티 설정
``comepile('org.springframework.boot:spring-boot-starter-oauth2-client')
소셜 로그인 등 클라이언트 입장에서 소셜 기능 구현시 필요한 의존성

SecurityConfig
1. [[EnabledWebSecuiry]] 
	1. Spring Security 설정들을 활성화시킨다.
2. .csrf().disable().headers().freamOptions().disable()
	1. h2-console 화면을 사용하기 위해 해당 옵션들을 disable 한다.
3. [[authorizeRequests]]
	1. URL별 권한 관리를 설정하는 옵션의 시작점이다. 저게 선언되야 [[antMatchers]] 옵션을 사용할 수 있다.
4. [[antMatchers]] 
	1. 권한 관리 대상을 지정하는 옵션이다
	2. URL, HTTP 메서드 별로 관리가 가능하다
	3. `"/"` 등 지정된 URL들은 permitAll() 옵션을 통해 전체 열람 권한을 주었다.
	4. `"/api/v1/**"` 주소를 가진 API는 USER권한만 가진 사람만 사용하도록 하였다.
5. anyRequest
	1. 설정된 값들 이외 나머지 URL들을 나타낸다.
	2. 여기서는 authenticated()를 추가하들은 모두 인증된 사용자(로그인한 사용자)들에게만 허용하게 한다.
6. logout().logsoutSuccessUrl("/")
	1. 로그아웃 기능에 대한 여러 설정의 진입점이다.
	2. 로그아웃 성공시 / 주소로 이동한다.
7. oauth2Login
	1. OAuth 2 로그인에 대한 여러 설정의 진입점이다.
8. userInfoEndPoint
	1. OAuth 2 로그인 성공 이후 사용자 정보를 가져올 때의 설정들을 담당한다.
9. userService
	1. 소셜 로그인 성공 후 후속 조치를 진행할 UserService 인터페이스의 구현체를 등록한다.
	2. 리소스 서버(즉, 소셜 서비스들)에서 사용자 정보를 가져온 상태에서 추가로 진행하고자 하는 기능을 명시할 수 있다.
```Java
@RequiredArgsConstructor
@EnabledWebSecuiry // 1
public class SecurityConfig extends WebSecurityConfigurerAdapter {
	private final CustomOAuth2UserService customOAuth2UserService;

	@Ovveride
	protected void configure(HttpSecurity http) thorws Exception {
		http.csrf().disable().headers().freamOptions().disable() // 2
			.and()
			.authorizeRequests() // 3
			.antMatchers("/", "/css/**", "/images/**", "/js/**", "/h2-console/**").permitAll()
			.antMatchers("/api/v1/**").hasRole(Role.USER.name()) // 4
			.anyRequest().authenticated() // 5
			.and()
			.logout().logsoutSuccessUrl("/") // 6
			.and()
			.oauth2Login().userInfoEndPoint().userService(customOAuth2UserService); // 9
	}
}
```

CustomOAuth2UserService
1. registrationId
	1.  현재 로그인 진행 중인 서비스를 구분하는 코드
	2. 하나만 사용하면 불필요한 값이지만 이후 다른 소셜 로그인 연동시 구분하기 위해 사용
2. userNameAttributeName
	1. OAuth2 로그인을 진행시 키가 되는 필드값을 이야기(PK)
	2. 구글의 경우 기본적으로 코드를 지원하지만(sub) 다른 소셜 로그인의 경우 지원을 하지 않는다.
3. OAuthAttirbutes
	1. OAuth2UserService를 통해 가져온 OAuth2User의 Attirbute를 담을 클래스
4. SessionUser
	1. 세션에 사용자 정보를 저장하기 위한 Dto 클래스
```Java
@RequiredArgsConstructor
@Service
public class CustomOAuth2UserService implements OAuth2UserService<OAuth2UserRequest, OAuth2User> {
	private final UserRepository userRepository;
	private final HttpSession httpSession;

	@Override
	public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException {
		OAuth2UserService<OAuth2UserRequest, OAuth2User> delegate = new DefaultOAuth2UserService();
		OAuth2User oAuth2User = delegate.loadUser(userRequest);
		String registrationId = userRequest.getClientRegistration().getRegistrationId(); // 1
		String userNameAttributeName = userRequest.getClientRegistration()
												  .getProviderDetails()
												  .getUserInfoEndpoint()
												  .getUserNameAttributeName(); // 2
		OAuthAttributes attributes = OAuthAttributes
										.of(registrationId, userNameAttributeName, oAuth2User.getAttributes()); // 3
		User user = saveOrUpdate(attributes);
		httpSession.setAttribute("user", new SessionUser(user)); // 4
		return new DefaultOAuth2User(Collection.singleton(new SimpleGrantedAuthority(user.getRoleKey())),
														attributes.getAttibutes(), 
														attributes.getNameAttributeKey());
														
	}
	private User saveOrUpdate(OAuthAttributes attributes) {
		User user = userRepository.findByEmail(attributes.getEmail())
								.map(entity -> entitry.update(attributes.getName(), attributes.getPicture()))
								.orElse(attributes.toEntity());
		return userRepository.save(user);
	}
}
```

OAuthAttributes
1. of()
	1. OAuth2User에서 반환하는 사용자 정보는 Map이기 때문에 값 하나하나를 반환해야만 한다.
2. toEntity()
	1. User 엔티티를 생성한다.
	2. OAuthAttributes에서 엔티티를 생성하는 시점은 처음 가입할 때이다
	3. 가입할 때의 기본 권한을 GUEST로 주기 위해서 role 빌더값에는 Role.GUEST를 사용
	4. OAuthAttributes 클래스 생성이 끝났으면 같은 패키지에 SessionUser 클래스를 생성
User 클래스를 그대로 사용하였으면 직렬을 구현하지 않았다는 오류가 생겼을 것
```Java
@Getter
public class OAuthAttributes {
	private Map<String, Object> attributes;
	private String nameAttributeKey;
	private String name;
	private String email;
	private String pricture;

	@Builder
	public OAuthAttributes(Map<String, Object> attributes, String nameAttributeKey, String name, String email, String picture) {
		this.attributes = attributes;
		this.nameAttributeKey = nameAttributeKey;
		this.name = name;
		this.email = email;
		this.picture = picture;
	}

	// 1
	public static OAuthAttirbutes of(String registrationId, String userNameAttributeName, Map<String, Object> attributes) {
		return ofGoogle(userNameAttributeName, attributes);
	}

	private static OAuthAttributes ofGoogle(String userNameAttributeName, Map<String, Object> attributes) {
		return OAuthAttributes.builder()
			.name((String) attributes.get("name"))
			.email((String)) attributes.get("email"))
			.picture((String) attributes.get("picture"))
			.nameAttributeKey(userNameAttributeName)
			.build();
	}
	public User toEntity() {
		return User.builder()
			.name(name)
			.email(email)
			.picture(picture)
			.role(Role.GUEST)
			.build();
	}
}
```

같은 코드가 반복되는(세션값을 가져오는 부분)을 어노테이션으로 개선하기
LoginUser
1. @[[Target]](ElementType.PARAMETER)
	1. 이 어노테이션이 생성될 수 있는 위치로 지정한다.
	2. PARAMETER로 지정하였으니, 메소드의 파라미터로 선언된 객체에서만 사용할 수 있다.
	3. 이 외에도 클래스 선언문에 쓸 수 있는 TYPE 등이 있다.
2. @[[interface]]
	1. 이 파일을 어노테이션 클래스로 지정한다.(LoginUser라는 이름을 가진 어노테이션이 생성)
```Java
@Target(ElementType.PARAMETER) // 1
@Retention(RetentionPolicy.RUNTIME)
public @interface LoginUser { // 2
}
```

LoginUserArgumentResolver
1. supportsParameter()
	1. 컨트롤러 메서드의 특정 파라미터를 지원하는지 판단한다.
	2. @LoginUser 어노테이션이 붙어있고 SessionUser.class인 경우 true를 반환한다.
2. resolveArgument()
	1. 파라미터에 전달할 객체를 생성한다.(세션에서 객체를 가져온다)
```Java
@RequireArgsConstructor
@Component
public class LoginUserArgumentResolver implements HandlerMethodArgumentResolver {
	private final HttpSession httpSession;

	@Override
	public boolean supportsParameter(MethodParameter parameter) { // 1
		boolean isLoginUserAnnotation = parameter.getParameterAnnotation(LoginUser.class) != null;
		boolean isUserClass = SessionUser.class.equals(parameter.getParameterType());
		return isLoginUserAnnotation && isUserClass;
	}
	@Override // 2
	public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer,
								  NativeWebRequest webReqeust, WebDataBinderFactory binderFactory) throws Exception {
		return httpSession.getAttribute("user");
	}

}
```

WebConfig
```Java
@RequireArgsConstructor
@Configuration
public class WebConfig implements WebMbcConfigurer {
	private final LoginUserArgumentResolver loginUserArgumentResolver;

	@Override
	public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers) {
		argumentResolvers.add(loginUserArgumentResolver);
	}
}
```

IndexController
1. 기존에 (User) httpSession.getAttribute("user")로 가져오던 세션 정보 값이 개선
```Java
@RequireArgsConstructor
@Controller
public class IndexController {
	private final PostService postsService;

	@GetMapping("/")
	public String index(Model model, @LoginUser SessionUser user) { // 1
	model.addAttribute("posts", postsService.findAllDesc());
	if (user != null) {
		model.addAttribute("userName", user.getName());
	}
	return "index";
}
```

## ⏱히스토리
	- 2022-06-06 23:52 최초 작성
	- 2022-06-11 UserRepository, SecurityConfig, CustomOAuth2UserService 작성
	- 2022-06-12 OAuthAttributes, LoginUser, LoginUserArgumentResolver 작성


📙출처 :