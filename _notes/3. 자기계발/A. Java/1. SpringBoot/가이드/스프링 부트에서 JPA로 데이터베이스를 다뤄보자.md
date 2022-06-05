---
created: ["2022-06-04 22:55"]
aliases: []
tags : [3.자기계발/A.Java/1.SpringBoot/가이드]
comments: true
---

# Today I Learned
## Created : [[2022-06-04]]

## 내용
- Request 데이터를 받을 [[Dto]], API 요청을 받을 [[@Controller]], 트랜잭션, 도메인 기능 간의 순서를 보장하는 [[Service]]가 필요하다.
- 여기에서 많은 사람들이 오해하는 것이 Service에서 비즈니스 로직을 처리해야 한다는 것([[스프링 웹 계층]] 참조)인데 비즈니스 처리를 담당해야할 곳은 Domain이다.
  (기존에 서비스로 처리하던 방식을 [[트랜잭션 스크립트]]라 한다.)
-  등록, 수정, 삭제 기능을 만들자

Posts
```Java
@NoArgsConstructor
@Entity
public class Posts {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;

	@Column(length = 500, nullable = false)
	private String content;

	private String author;

	@Builder
	public Posts(String title, String content, String author) {
		this.title = title;
		this.content = content;
		this.author = author;
	}

	public void update(String title, String content) {
		this.title = title;
		this.content = content;
	}
}
```

PostsApiController
```Java
@RequiredArgsConstructor
@RestController
public class PostsApiController {
	private final PostsService postService;
	// 등록
	@PostMapping("/api/v1/posts")
	public Long save(@RequestBody PostsSaveRequestDto requestDto) {
		return postService.save(requestDto);
	}

	// 수정
	@PutMapping("/api/v1/posts/{id}")
	public PostsResponseDto findById(@PathVariable Long id) {
		return postsService.findBy(id);
	}
}
```

PostService
update 기능에서 데이터베이스에 쿼리를 날리는 부분이 없는 것은 JPA의 [[영속성 컨텍스트]] 때문
JPA의 핵심 내용은 엔티티가 영속성 컨텍스를 포함되어 있냐 아니냐로 갈린다.
```Java
@RequiredArgsConstructor
@Service
public class PostsService {
	private final PostsRepository postsRepository;

	@Transactional
	public Long save(PostsSaveRequestDto requestDto) {
		return postsRepository.save(requestDto.toEntity()).getId();
	}

	@Transactional
	public Long update(Long id, PostsUpdateRequestDto requestDto) {
		Posts posts = postsRepository.findById(id).orElseThrow(() -> new IllegalArgumentException("해당 게시글이 없습니다. id="+id));
		posts.update(requestDto.getTitle(), requestDto.getContent());
		return id;
	}
}
```

PostsSaveRequestDto
 - [[Entity]] 클래스를 Request/Response 클래스로 사용해서는 안된다
    Request와 Response용 Dto는 view를 위한 클래스라 자주 변경이 필요하므로, View Layer와 DB Layer의 역할 분리를 철저하게 하는 것이 좋다. 
    (실제로 Controller에서 결괏값으로 여러 테이블을 조인해서 줘야할 경우가 빈번)
    즉 Entity 클래스과 Controller에서 쓸 Dto는 분리해서 사용해야 한다.
```Java
@Getter
@NoArgsConstructor
public class PostsSaveRequestDto {
	private String title;
	private String content;
	private String author;

	@Builder
	public PostsSaveRequestDto(String title, String content, String author) {
		this.title = title;
		this.content = content;
		this.author = author;
	}

	public Posts toEntity() {
		return Posts.builder()
			.title(title)
			.content(content)
			.author(author)
			.build();
	}
}
```

PostsResponseDto
Entity의 필드 중 일부만 사용하므로 생성자로 Entity를 받아 필드에 값을 넣는다.
```Java
@Getter
public class PostsSaveResponseDto {
	private Long id;
	private String title;
	private String content;
	private String author;

	public PostsResponseDto(Posts entity) {
		this.id = entity.getId();
		this.title = entity.getTitle();
		this.content = entity.getContent();
		this.author = entity.getAuthor();
	}
}
```

PostsUpdateRequestDto

```Java
@Getter
@NoArgsConstructor
public class PostsUpdateRequestDto {
	private String title;
	private String content;

	@Builder
	public PostsUpdateRequestDto(String title, String content) {
		this.title = title;
		this.content = content;
	}
}
```

PostApiControllerTest
Api Controller를 테스트하는데에는 @WebMvcTest의 경우 JPA가 작동하지 않기에 @SpringBootTest와 TestRestTemplate를 사용하면 된다.
```Java
@RunWith(SpringRunner.class)
@SpringBootTest(webEnviroment = SpringBootTest.webEnviroment.RANDOM_PORT)
public class PostsApiControllerTest {
	@LocalServerPort
	private int port;

	@Autowired
	private TestRestTemplate restTemplate;

	@Autowired
	private PostsRepository postRepository;

	@After
	public void tearDown() throws Exception {
		postRepository.deleteAll();
	}

	@Test
	public void Posts_등록된다() throws Exception {
		//given
		String title = "title";
		String content  = "content";
		PostsSaveRequestDto requestDto = PostsSaveRequestDto.builder()
			.title(title)
			.content(content)
			.author("author")
			.build();
		String url = "http://localhost:" + port + "/api/v1/posts";

		// when
		ResponseEntity<Long> responseEntity = restTemplate.postForEntity(url, requestDto, Long.class);

		// then
		assertThat(responseEntity.getStatusCode()).isEqualTo(HttpStatus.OK);
		assertThat(responseEntity.getBody()).isGreaterThan(0L);

		List<Posts> all = postsRepository.findAll();
		assertThat(all.get(0).getTitle()).isEqualTo(title);
		assertThat(all.get(0).getContent()).isEqualTo(content);
	}
	@Test
	public void Posts_수정된다() throws Exception {
		// given
		Posts savedPosts = postsRepository.save(Posts.builder()
			.title("title")
			.content("content")
			.author("author")
			.build());
		Long updateId = savedPosts.getId();
		String expectedTitle = "title2";
		String expectedContent = "content2";

		PostsUpdateRequestDto requestDto = PostsUpdateRequestDto.builder()
			.title(expectedTitle)
			.content(expectedContent)
			.build();
		String url = "http://localhost:"+port+"/api/v1/posts/"+updateId;
		HttpEntity<PostsUpdateRequestDto> requestEntity = new HttpEntity<>(requestDto);
		
		//when
		ResponseEntity<Long> responseEntity = restTemplate.exchange(url, HttpMethod.PUT, requestEntity, Long.class);
		
		//then
		assertThat(responseEntity.getStatusCode()).isEqualTo(HttpStatus.OK);
		assertThat(responseEntity.getBody()).isGraterThan(0L);
		List<Posts> all = postsRepository.findAll();
		assertThat(all.get(0).getTitle()).isEqualTo(expectedTitle);
		assertThat(all.get(0).getContent()).isEqualTo(expectedContent);
	}
}
```

## ⏱히스토리
	- 2022-06-04 22:55 최초 작성


📙URL :