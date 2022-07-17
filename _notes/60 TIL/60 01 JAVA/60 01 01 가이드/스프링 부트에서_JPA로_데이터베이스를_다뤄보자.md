---
created: ["2022-06-04 22:55"]
aliases: []
tags : [3.자기계발/A.Java/1.SpringBoot/가이드]
comments: true
date created: 목요일, 7월 7일 2022, 8:15:41 오후
date modified: 금요일, 7월 8일 2022, 8:15:20 오전
---

# Today I Learned
## Created : [[2022-06-04]]

## 내용
- Request 데이터를 받을 [[Dto]], API 요청을 받을 [[@Controller]], 트랜잭션, 도메인 기능 간의 순서를 보장하는 [[Service]]가 필요하다.
- 여기에서 많은 사람들이 오해하는 것이 Service에서 비즈니스 로직을 처리해야 한다는 것([[스프링 웹 계층]] 참조)인데 비즈니스 처리를 담당해야할 곳은 Domain이다.
  (기존에 서비스로 처리하던 방식을 [[트랜잭션 스크립트]]라 한다.)

Posts
```Java
@NoArgsConstructor
@Entity
public class Posts extends BaseTimeEntity {
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

PostsController
```Java
@RequiredArgsConstructor
@Controller
public class IndexController {
	private final PostsService postsService;

	@GetMapping("/")
	public String index(Model model) { // 서버 템플릿 엔진에서 사용할 수 있는 객체를 저장할 수 있다.
		model.addAttribut("list", postsService.findAllDesc());
	}

	@GetMapping("/posts/update/{id}")
	public String postsUpdate(@PathVariable Long id, Model model) {
		PostsResponseDto dto = postsService.findById(id);
		model.addAttribute("posts", dto);
		return "posts-update";
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

	@DeleteMapping("/api/v1/posts/{id}")
	public Long delete(@PathVariable Long id) {
		postsService.delete(id);
		return id;
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

	@Transactional(readOnly = true)
	// readOnly = true만 주면 트랜잭션 범위는 유지하되, 조회 기능만 남겨두어 조회 속도가 개선된다.
	public List<PostsListResponseDto> findAllDesc() {
		return postsRepository.findAllDesc().stream()
			.map(PostListResponseDto::new)
			// .map(posts -> new PostListResponseDto(posts))
			.collect(Collectors.toList());
	}

	@Transactional
	public void delete(Long id) {
		Posts posts = postsRepository.findById(id).orElseThrow(() ->new IllegalArgumentException("해당 게시글이 없습니다. id="+id));
		postsRepository.delete(posts);
		// JpaRepository에서 delete 메서드를 지원
	}
}
```

PostsSaveRequestDto
 - [[_notes/60 TIL/60 01 JAVA/60 01 09 JPA/Entity|Entity]] 클래스를 Request/Response 클래스로 사용해서는 안된다
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
	public PostsSaveRequestDto(String title, String content, String author){
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

PostsListResponseDto
```Java
@Getter
public class PostsListResponseDto {
	private Long id;
	private String title;
	private String author;
	private LocalDateTime modifiedDate;

	public PostsListResponseDto(Posts entity) {
		this.id = entity.getId();
		this.title = entity.getTitle();
		this.author = entity.getAuthor();
		this.modifiedDate = entity.getModifiedDate();
	}
}

```

PostsRepository
```Java
public interface PostsRepository extends JpaRepository<Posts, Long> {
	@Query("SELECT p FROM posts p ORDER BY p.id DESC")
	List<Posts> findAllDesc();
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
	@Test
	public void BaseTimeEntity_등록() {
		// given
		LocalDateTime now = LocalDateTime.of(2022,6,6,0,0,0);
		postsRepository.save(Posts.builder()
			.title("title")
			.content("content")
			.author("author")
			.build());

		// when
		List<Posts> postsList = postsRepository.findAll();

		// then
		Posts posts = postsList.get(0);
		System.out.println(">>>>>>>> createDate = " + posts.getCreatedDate()+", modifiedDate = " + posts.getModifiedDate());
		assertThat(posts.getCreatedDate()).isAfter(now);
		assertThat(posts.getModifiedDate()).isAfter(now);
	}
}
```

index.js
index 객체 내에서만 function이 유효하게 하여 JS와 겹치지 않게 유효범위를 만들어 사용한다.
```javascript
var main = {
	init : function() {
		var _this = this;
		$('#btn_save').on('click', function() { // btn_save란 id를 가진 HTML 엘리먼트에 click 이벤트가 발생했을 때 save function을 실행하도록 이벤트를 등록한다.
			_this.save();
		});

		$('#btn_update').on('click', function() {
			_this.update();
		});
		
		$('#btn_delete').on('click', function() {
			_this.delete();
		});
	},
	save : function() {
		var data = {
			title : $('#title').val(),
			author: $('#author'),val(),
			content: $('#content').val()
		};
		$.ajac({
			type : 'POST',
			url : '/api/v1/posts',
			dataType : 'json',
			contentType : 'application/json; charset=utf-8',
			data: JSON.stringify(data)
		}).done(function() {
			alert('글이 등록되었습니다.');
			window.location.href = '/';
		}).fail(function (error) {
			alert(JSON.stringify(error));
		});
	},
	update : function() {
		var data = {
			title : $('#title').val(),
			content: $('#content').val()
		};
		$.ajac({
			type : 'PUT',
			url : '/api/v1/posts' + id,
			dataType : 'json',
			contentType : 'application/json; charset=utf-8',
			data: JSON.stringify(data)
		}).done(function() {
			alert('글이 수정되었습니다.');
			window.location.href = '/';
		}).fail(function (error) {
			alert(JSON.stringify(error));
		});
	},
	delete : function() {
		var data = {
			id : $('#id').val(),
		};
		$.ajac({
			type : 'DELETE',
			url : '/api/v1/posts' + id,
			dataType : 'json',
			contentType : 'application/json; charset=utf-8'
		}).done(function() {
			alert('글이 삭제되었습니다.');
			window.location.href = '/';
		}).fail(function (error) {
			alert(JSON.stringify(error));
		});
	}
};
main.init();
```

## ⏱히스토리
	- 2022-06-04 22:55 최초 작성


📙URL :