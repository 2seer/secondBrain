---
created: 2022-05-08 08:58
modified: <%+ tp.file.last_modified_date() %>
author: JS.Park 
link:
title: Obsidian Plugin 정리
aliases: 
owner: JS.Park
from: obsidian
src: 💭 Thoughts  
type: permenent  
status: synthesizing 
tags: 정보/메모/제텔카스텐 
cssclass: 
---

# [[Obsidian Link-Tag-Folder]]

# Obsidian plugin 
- 이미지 관리
	- <mark class="hltr-red">Excalidraw</mark>[](https://github.com/zsviczian/obsidian-excalidraw-plugin)  
		- 그림, https://www.youtube.com/c/VisualPKM
		- MD → 메타, 그림 설명 추가 → <mark class="hltr-red">Dataview</mark> , <mark class="hltr-red">Search</mark> 에서 조회
			- https://www.youtube.com/watch?v=8payWdWI2cQ
		- IPad, Mac 미러링 → 애플 펜슬 그림 그리기 가능
	- <mark class="hltr-red">Diagrams</mark> [](https://github.com/zapthedingbat/drawio-obsidian) 
		- [draw.io](https://github.com/zapthedingbat/drawio-obsidian)
		- SVG → Excalidraw에서 활용 가능

- 링크 꾸미기
	- <mark class="hltr-red">Supercharged Links</mark> [](https://github.com/mdelobelle/obsidian_supercharged_links) - 노트의 메타데이터(YAML), 태그에 따라 노트의 링크에 아이콘을 추가이나 색을 변경
	- <mark class="hltr-red">Style Settings plugin</mark>  [](https://github.com/mgmeyers/obsidian-style-settings)- 링크의 색, 그림 변경  
	- <mark class="hltr-red">Various Completion</mark> [](https://github.com/tadashi-aikawa/obsidian-various-complements-plugin) - 자동완성

- 웹 컨텐츠 저장
	- <mark class="hltr-red">Local REST API</mark> [](https://github.com/coddingtonbear/obsidian-local-rest-api)
		- Obsidian [REST API](https://coddingtonbear.github.io/obsidian-local-rest-api/) , Enable Insecure HTTP Server : On, API Key → Obsidian Web 복사
	- Chrome 확장프로그램 
		- <mark class="hltr-red">Obsidian Web</mark> 설치 [URL]((https://chrome.google.com/webstore/detail/obsidian-web/edoacekkjanmingkbkgjndndibhkegad) 
		- 관리 메뉴 →복사한 API 붙여넣기
 		- 하단의 Templates 수정
	- <mark class="hltr-red">Local images</mark> [](https://github.com/aleksey-rezvov/obsidian-local-images) 외부 이미지를 로컬 이미지로 저장
	- <mark class="hltr-red">Clear unused images </mark> [](https://github.com/ozntel/oz-clear-unused-images-obsidian) 사용하지 않는 이미지 삭제
	- <mark class="hltr-red">Dataview</mark> [](https://github.com/blacksmithgu/obsidian-dataview)- 최근 5개 사이드 바에 저장
		````
			```dataview
			list 
			from "00.임시메모/clipping"
			sort date desc
			limit 10
			```
		````
	- <mark class="hltr-red">Database Folder</mark>  특정 폴더 혹은 특정 테크를 Notion Database 형태로 조회

- 일정 노트
	- 노트 작성
		- <mark class="hltr-blue">Daily Note</mark>  → Core Plugin
			- 당일 노트
			- 사이드바 버튼
			- 저장경로, 탬플릿 파일 경로 설정
		- <mark class="hltr-red">Calender</mark> [](https://github.com/liamcain/obsidian-calendar-plugin)
			- 특정일 노트, 주간노트
			- 사이드바 달력
		- <mark class="hltr-blue">Templates</mark> →  <mark class="hltr-red">Templater</mark> 
			- [탬플릿](https://silentvoid13.github.io/Templater/) 파일 생성 
			- Calender 연계하여 날짜 입력 할때 사용
		````
		제목날짜 다음날	2001-01-02
		제목날짜 전날	2000-12-31
		````
	- 노트 검색
		- <mark class="hltr-blue">Search</mark>  → Core Plugin
			- <mark class="hltr-red">Vantage - Advanced Search Builder</mark>  
				- Core plugin 검색 키워드 생성
		- <mark class="hltr-red">Text expander</mark> → Search  키워드 → 노트 저장 → Template 표시 
			- Auto Exapnder : on → 문서 오픈 재검색, 결과 업데이트
			````
			```expander
			file:( 2001-01-02 OR 2001-01-03 OR 2001-01-04 OR 2001-01-05 OR 2001-01-06 OR 2001-01-07 OR 2001-01-08 OR 2001-01-09) path:("40 🗂 Planer") section:("## Meeting" "-")
			#### [[$filename]]
			!$header:"Meeting"
			```
			````
		- <mark class="hltr-red">Dataview</mark> 
			- dataviewjs → 할일, 기한이 지난일, 예정된 할일 보는 
			````
				```dataviewjs
				const myTasks = dv.pages('"40 🗂 Planer"').file.tasks.where(t => !t.completed);
				
				const overDue = myTasks.filter(t=>t.due).filter(t=> moment(t.due.toString()).isBefore(moment(),"day")).sort(t=>t.created);
				
				const today = myTasks.filter(t=>t.due).filter(t=> moment(t.due.toString()).isSame(moment(),"day")).sort(t=>t.created);
				
				const upcomming = myTasks.filter(t=>t.due).filter(t=>moment(t.due.toString()).isAfter(moment(),"day")).sort(t=>t.due);
				
				const undated = myTasks.filter(t=>!t.due).sort(t=>t.created)
				
				if (overDue.length > 0) {
					dv.header(3,"Over Due");
					dv.taskList(overDue, false);
				}
				
				if (today.length > 0) {
					dv.header(3,"Today");
					dv.taskList(today, false);
				}
				
				if (upcomming.length > 0) {
					dv.header(3,"Upcomming");
					dv.taskList(upcomming, false);
				}
				
				if (undated.length>0) {
					dv.header(3,"Check it");
					dv.taskList(undated, false);
				}
				```
			````
			- inline dataviewjs, dataview → Reading view에서만 작동, Live view에서는 작동안함
	- 일정 생성
		- <mark class="hltr-red">QuickAdd</mark>  → 노트에 기록하는 반복 작업, 자동 매크로
- Vault 링크
	- MD menu → Copy Obsidian URL
	- <mark class="hltr-red">ADVANCED URI</mark> 
