---
created: ["2022-06-24 16:54"]
aliases: []
tags: []
comments: true
---

# jsonString to DTO
## 작성일 : [[2022-06-24]]
## 상위 문서 : [[7.work/]]
## 분야 : #업무 
## 키워드 : #ObjectMapper, #TypeRefernce, #CodeSnippet
## 상태 :  #작성중, #연결중 


## ObjectMapper
---
jsonString 으로 List형 DTO 변환하기
```Java

@PatchMapping(value = "/test1")
public ResponseEntity<Object> modAuditCategory(@Valid Dto req, HttpServletRequest request) throws JsonProcessingException {  
    ObjectMapper objectMapper = new ObjectMapper();  
    if (req.getJsnString() != null) {  
        Dto[] list = objectMapper.readValue(req.getJsnString(), Dto[].class);  
        for (Dto dto : list) {  
            dto.setMenuId(req.getMenuId());  
            memberService.mod(dto, request);  
        }    
    }    
    return ResponseEntity.noContent().build();  
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
### ordinal 메서드의 사용

## 참고문헌
---
- 1


## 주석
---
- 1


## ⏱히스토리
	- 2022-06-24 16:54 최초 작성
