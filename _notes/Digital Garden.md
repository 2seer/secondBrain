---
title: Digital Garden
---

### Welcome!

# 최근 변경된 문서
```dataview
LIST WITHOUT ID
    dateformat(file.mtime, "yyyy.MM.dd") + " - " + file.link
FROM !"_notes/4. dailyNote" and !"_notes/1. 문서/템플릿 양식"
SORT file.mtime 
DESC LIMIT 25
```

## 2. 지식큐레이션
```dataview
LIST WITHOUT ID
    dateformat(file.mtime, "yyyy.MM.dd") + " - " + file.link
FROM "_notes/2. knowledgeCuration" 
SORT file.mtime 
DESC LIMIT 25
```

## 3. 스터디
```dataview
LIST WITHOUT ID
    dateformat(file.mtime, "yyyy.MM.dd") + " - " + file.link
FROM "_notes/3. study" 
SORT file.mtime 
DESC LIMIT 25
```
Appendix
[[Appendix|목차]]

