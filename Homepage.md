---
cssclass: myhome wordcountTable

banner_x: 0.62858
banner_y: 0.38648
banner: "https://i.pinimg.com/originals/de/7b/d6/de7bd6a32ee6aba8ab6da671aa4df54b.gif"
banner_icon: 🏖️
---

%% MAP OF CONTENTS %%

%% 导航 %%

# Rainbell
- **AGENDA**
	- `$='[['+moment().format("YYYY-MM-DD")+'|오늘의 메모]]'`
	- `$='[['+moment().format("YYYY-M월계획")+'|월간 노트]]'`
	- `$='[['+moment().format("YYYY년도Q분기별계획")+'|분기별 노트]]'`
	- `$='[['+moment().format("YYYY년연간계획")+'|연간 노트]]'`
	- [[00. 5개년계획|5개년 계획]]
- **LIFE**
	- [[00. 书单|도서관]]
	- [[体重问题|체중]]
	- [달력](obsidian://advanced-uri?vault=%E5%85%AB%E5%96%9C&filepath=00.%2520Homepage.md&commandid=big-calendar%253Aopen-big-calendar)
	- [Memo](obsidian://advanced-uri?vault=%E5%85%AB%E5%96%9C&commandid=obsidian-memos%253Aopen-memos)
	- [Reeder](reeder://)
- **STUDY**
	- [[MCIT主页|MCIT]]
	- [[뮤직 홈|음악]]
	- [[기타 리소스]]
	- [[환승역]]
- **AVES**
	- [[00. 의료기록 홈페이지|병력]]

<br>

%% MUCIC AND BIRTHDAY COUNTDOWN %%


```jsx:
<ShowI></ShowI>
```

%% FLEX VIEW OF DIARY, AGENDA, A PICTURE, AND PARROT LOG %%


````ad-col2
```dataviewjs
dv.paragraph(`![[${moment().format("YYYY-MM-DD#일기")}]]`)
```

```dataviewjs
dv.paragraph(`![[${moment().format("YYYY-MM-DD#일정")}]]`)
```

![[parrotnobackground.png#center|200]]

```dataviewjs
dv.paragraph(`![[${moment().subtract(1, 'days').format("YYYY-MM-DD#鸟崽")}]]`)
```

<br>

````

%% A COMMENTTED OFF WAY OF DISPLAYING STATIONARY FLEX VIEW OF DIARY, AGENDA ON A SPECIFIC DAY %%


%%



```ad-col2
![[2022-05-27]]

![[2021-12-30-#일정]]

![[Pasted image 20211221152043.png#center|200]]


```

%%

%% CURRENTLY READING SECTION %%

**CURRENTLY READING** ![[Pasted image 20211210214132.png#right|150]] 



<p align = "center">Genghis Khan: His Conquests, His Empire, His Legacy<br>
Frank McLynn</p>

*A definitive and sweeping account of the life and times of the world's greatest conqueror -- Genghis Khan -- and the rise of the Mongol empire in the 13th century...*

<p align = "left">  🔗 More</p> 
     


<br>
<br>

%% READING NOTES SECTION %%

**READING NOTES**

%%
```dataview
TABLE
  author AS "作者",
  genre  AS "类型",
  status AS "进度",
  rating AS "评分",
  description AS "简介"
FROM [[reading]]
SORT rating DESC
```

%% DVJS VERSION OF READING NOTES SECTION %%

%% DVJS版本 %%

%%


```dataviewjs
dv.table(["제목", "작가", "유형", "상태"], dv.pages("#reading")
    .map(b => [b.file.link, b.author, b.genre, b.status]))
```

<br>
<br>

%% OBSIDIAN ACTIVITY SECTION %%

**OBSIDIAN ACTIVITY**
```dataviewjs

let ftMd = dv.pages("").file.sort(t => t.cday)[0]
let total = parseInt([new Date() - ftMd.ctime] / (60*60*24*1000))
let totalDays = "您已使用 *Obsidian* "+total+" 天，"
let nofold = '!"misc/templates"'
let allFile = dv.pages(nofold).file
let totalMd = "공동제작 "+
	allFile.length+" 篇==笔记=="
let totalTag = allFile.etags.distinct().length+" 个==标签=="
let totalTask = allFile.tasks.length+" 个==待办==。 "
dv.paragraph(
	totalDays+totalMd+"、"+totalTag+"、"+totalTask
)

```
```ActivityHistory
/
```






