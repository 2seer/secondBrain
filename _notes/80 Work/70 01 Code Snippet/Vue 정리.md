---
created: ["2022-07-18 10:02"]
tags: [Vue]
---
1. 푸터 부분에 vue.js를 임포트한다.
<script src="/js/vue.min.js" th:attrappend="src='?ver='+${@environment.getProperty('cache.version')}"></script>

2. vue를 조작하려는 dom에 id를 부여한다. 
	- ex) id="search"
3. vue에 관한 js를 작성한다.
```javascript
var product = new Vue({  
    el: "#id",  
    data: {  
        element: [], 
        id: [], 
    },    
    filters: {  
	    upperCased: function (data) {  
	        return data != null ? data.toUpperCase() : '';  
	    },  
	    comma: function (data) {  
	        return data !== null ? parseInt(data).toLocaleString('ko-KR') : 'N/A';  
	    }
	},
    methods: {  
        init: function() {  
            var $this = this;  
            this.api();
        },  
        api: function() {
	        var $this = this;
	        $(".loading").show();
	        $.ajax({  
			    type: 'GET',  
			    url: '/url/api/v1'  
			})  
			    .done(function (res) {
				})
				.fail(function (xhr, status, error) {  
				    if (error && error.responseText) {  
				        alert(error.responseText);  
				    } else {  
				        alert('처리중 오류가 발생했습니다.');  
				    }
				})  
				.always(function () {  
				    $(".loading").hide();  
				});
        },
        function: function(id, name) {  
            if (confirm('')) {  
                window.opener.postMessage({  
                    id: id,  
                    name: name  
                }, '*');  
                window.close();  
            }        
        },    
	    mounted: function() {  
	        this.init(); 
	    }
	});
```
## ⏱히스토리
	- 2022-07-18 10:02 최초 작성

## 의견
	-


📙URL :