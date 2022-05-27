2022-05-16 14:41
## Today I Learned
---
aliases : 스프링부트

tags : [[자료형]]

---

## Content

```
1. List<Object> list = new ArrayList<Object>(); 
2. ArrayList<Object> list = new ArrayList<>();
```
- 대부분의 ArrayList는 두번째 방법보다 첫번째 형태로 업캐스팅하여 선언 후 사용
- 그 이유는 객체지향 프로그래밍의 일환으로 다형성을 지원하기 위함(변경에 유연한 구조로 미리 설계)
- ArrayList만이 제공하는 기능을 쓰지 않을 때도 있어서 List로 선언을 해서 List로 제공하는 메소드까지 사용 가능하게 한다.
- 기존 코드를 바꾸지 않고 인터페이스 내에서 변경을 하기 쉽도록 하기 위함
- 객체는 인터페이스를 사용해 선언하는게 좋다.(결론이 이상하다)

## ⏱Log
-


📙출처 :
URL : [List 리스트 = ArrayList로 선언해 사용하는 이유](https://bibi6666667.tistory.com/m/236#:~:text=List%20list%20%3D%20new,%EB%A1%9C%20%EC%84%A0%EC%96%B8%ED%95%98%EC%97%AC%20%EC%82%AC%EC%9A%A9%EB%90%9C)