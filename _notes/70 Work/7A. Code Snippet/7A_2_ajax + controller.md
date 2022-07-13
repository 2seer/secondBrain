---
created: ["2022-06-23 22:26"]
aliases: []
tags: []
comments: true
date created: 목요일, 7월 7일 2022, 8:14:43 오후
date modified: 금요일, 7월 8일 2022, 8:15:19 오전
---

# Ajax + Controller
## 작성일 : [[2022-06-23]]
## 상위 문서 : [[7.work/]]
## 분야 : #업무
## 키워드 : #CodeSnippet
## 상태 : #작성중, #연결중


## [[RequestParam Annotation|@RequestParam]]
---
- 용도 : jsonString 을 사용하거나 dto를 통해 서비스를 호출할 정도가 아닌 경우 즐겨서 사용
javascript
```javascript
$.ajax({  
    url: './api/test/',  
    type: 'POST',  
    data:  
        {  
            parameter1: parameter1,  
            parameter2: parameter2,  
            parameter3: parameter3  
        },
	success: function() {
	},
	error: function(e) {
	}
});
```
```javascript
$.ajax({  
    type: 'PATCH',  
    url: '/ap/test/',  
    contentType: 'application/json',  
    data: {  
        jsnString: JSON.stringify(jsnString)  
    },})  
    .done(function () {  
        alert('');  
        location.reload();  
    })    .fail(function (xhr, status, error) {  
        if (error && error.responseText) {  
            alert(error.responseText);  
        } else {  
            alert('처리중 오류가 발생했습니다.');  
        }    })    .always(function () {  
        $(".ajax_load").hide();  
        jsonString = [];  
    });
```

java
```java
public void test(@RequestParam String parameter1,  
				 @RequestParam String parameter2,  
				 @RequestParam(value = "parameter3", required = false) String parameter3) {
```
 

## [[Valid Annotation|@Valid]] [[DTO]]
---
- 용도 : DTO에 담기 위해 사용
javscript
```Javascript
var data = new FormData();  
data.append("id", menuId);  

$(".ajax_load").show();  
$.ajax({  
    type: 'POST',  
    url: '/member/api/',  
    data: data,  
    processData: false,  
    contentType: false,  
    cache: false  
})
```

java 
```java
@RestController  
@RequestMapping("/member/api")  
@RequiredArgsConstructor  
public class MemberApiController {  
    private final MenuService memberService;
  
    @PostMapping("/")  
    public ResponseEntity<Object> addMmeber(@Valid MemberDto req, HttpServletRequest request) {  
        return ResponseEntity.noContent().build();  
    }
}
```


## Examples
----
### : 가장 단순한 형태
```Java
	enum Season { WINTER, SPRING, SUMMER, FALL }
```

### : 데이터와 메서드가 있는 형태

### : switch와 함께 사용하기

## 안티패턴
---
### Ordinal 메서드의 사용

## 참고문헌
---
- 1


## 주석
---
- 1


## ⏱히스토리
	- 2022-06-23 22:26 최초 작성
