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
javascript
```Javascript
let item = {};  
item.id = parseInt(id);  
item.name = name;  
item.enabled = enabled;  
item = JSON.stringify(item);  
jsnString.push(JSON.parse(item));

var data = new FormData();  
data.append("menuId", menuId);  
data.append("jsnString", JSON.stringify(jsnString));

$.ajax({  
    type: 'PUT',  
    url: '/test/api/',  
    data: data,  
    processData: false,  
    contentType: false,  
    cache: false  
})
```


jsonString 으로 List형 DTO 변환하기
```Java

@PatchMapping(value = "/test1")
public ResponseEntity<Object> modAuditCategory(@Valid Dto req, HttpServletRequest request) throws JsonProcessingException {  
    ObjectMapper objectMapper = new ObjectMapper();  
    if (req.getJsnString() != null) {  
        List<Dto> list = objectMapper.readValue(req.getJsnString(), new TypeReference<List<Dto>>() {});
		for (int i = 0; i < list.size(); i++) {  
		    list.get(i).setMenuId(req.getMenuId());  
		    service.modUser(list.get(i), request);  
		}  
    }    
    return ResponseEntity.noContent().build();  
}
```




## ⏱히스토리
	- 2022-06-24 16:54 최초 작성
