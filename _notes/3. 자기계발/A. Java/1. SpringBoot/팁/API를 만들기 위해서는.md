---
created: ["2022-06-04 22:55"]
aliases: []
tags : [3.자기계발/A.Java/1.SpringBoot/팁]
comments: true
---

# Today I Learned
## Created : [[2022-06-04]]

## 내용
- Request 데이터를 받을 [[Dto]], API 요청을 받을 [[@Controller]], 트랜잭션, 도메인 기능 간의 순서를 보장하는 [[Service]]가 필요하다.
- 여기에서 많은 사람들이 오해하는 것이 Service에서 비즈니스 로직을 처리해야 한다는 것([[스프링 웹 계층]] 참조)인데 비즈니스 처리를 담당해야할 곳은 Domain이다.
  (기존에 서비스로 처리하던 방식을 [[트랜잭션 스크립트]]라 한다.)
-  등록, 수정, 삭제 기능을 만들자

PostApiController
```Java
@RequiredArgsConstructor
@RestController
public class PostApiController {
	private final PostsService postService;
	// 등록
	@PostMapping("/api/v1/posts")
	public Long save(@RequestBody PostsSaveRequestDto requestDto) {
		return postService.save(requestDto);
	}
}
```

PostService
```Java
@RequiredArgsConstructor
@Service
public class PostsService {
	private final PostsRepository postsRepository;

	@Transactional
	public Long save(PostsSaveRequestDto requestDto) {
		return postsRepository.save(requestDto.toEntity()).getId();
	}
}
```

PostsSaveRequestDto
## ⏱히스토리
	- 2022-06-04 22:55 최초 작성


📙URL :