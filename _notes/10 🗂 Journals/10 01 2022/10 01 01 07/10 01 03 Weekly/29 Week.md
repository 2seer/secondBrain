---
aliases: [""]
publish: false
tags:
 - "#calendar/weekly/2022"

banner: 
banner_icon: 🗓️
---
  

# 2022-07 Week 29

  

[[2022 Week 28|↶ Previous Week]] | [[2022 Week 30|Following Week ↷]]

  

> [!METADATA]-

> Created:: [[2022-07-18]] 18:42

> Updated:: 2022-07-18 18:42

> ID:: 20220718184215

  

**Table of Contents:**

```toc

style: number

```

  

___

  

## Memos

- [[2022-07-17|일요일]]

![[2022-07-17#^memo-link]]

- [[2022-07-18|월요일]]

![[2022-07-18#^memo-link]]

- [[2022-07-19|화요일]]

![[2022-07-19#^memo-link]]

- [[2022-07-20|수요일]]

![[2022-07-20#^memo-link]]

- [[2022-07-21|목요일]]

![[2022-07-21#^memo-link]]

- [[2022-07-22|금요일]]

![[2022-07-22#^memo-link]]

- [[2022-07-23|토요일]]

![[2022-07-23#^memo-link]]

  

## Work Log

- [[2022-07-17|월요일]]

![[2022-07-17#^work-link]]

- [[2022-07-18|화요일]]

![[2022-07-18#^work-link]]

- [[2022-07-19|수요일]]

![[2022-07-19#^work-link]]

- [[2022-07-20|목요일]]

![[2022-07-20#^work-link]]

- [[2022-07-21|금요일]]

![[2022-07-21#^work-link]]

- [[2022-07-22|토요일]]

![[2022-07-22#^work-link]]

- [[2022-07-23|일요일]]

![[2022-07-23#^work-link]]

  

## Overview

### Week Statistics

```dataviewjs

const daysPath = dv.current().file.folder;

const attributes = {
	'panic': {
		label: 'Panic',
		average: 10,
	},
	'money-spent': {
		label: 'Money Spent',
		backgroundColor: 'rgba(85, 174, 229, 0.2)',
		borderColor: 'rgba(85, 174, 229, 1)',
		average: 20,
	},
	'prayer': {
		label: 'Prayer',
		backgroundColor: 'rgba(255, 211, 101, 0.2)',
		borderColor: 'rgba(255, 211, 101, 1)',
		average: 5,
	},

	'steps': {
		label: 'Steps',
		backgroundColor: 'rgba(141, 82, 188, 0.2)',
		borderColor: 'rgba(141, 82, 188, 1)',
		average: 10000,
	},
	'hours-worked': {
		label : 'Hours Worked',
		backgroundColor: 'rgba(143, 208, 50, 0.2)',
		borderColor: 'rgba(143, 208, 50, 1)',
		average: 6
	},
};

const date = "2022-07-18";
customJS.DvCharts.renderWeeklyChart({
	dv,
	context: this,
	daysPath: '02 Personal/02.01 Periodic Notes/2022/Daily/07 7월',
	attributes,
	type: 'average',
	date
})

```

  

```dataview

TABLE WITHOUT ID

link(file.name) as "Day",

feeling AS "💭",

money-spent AS "💸",

panic AS "🌪️",

prayer AS "🕋",

steps AS "👣",

hours-worked AS "✏️"

FROM "02 Personal/02.01 Periodic Notes"

WHERE week = [[2022 Week 29]]

SORT file.name ASC

```

  

### Habits

```dataview

TABLE WITHOUT ID
	file.link AS "Day",
	coffee AS "☕",
	exercise AS "🏋️",
	reading AS "👓",
	revision AS "🔁",
	shower AS "🚿",
	typing AS "⌨️"
	FROM "02 Personal/02.01 Periodic Notes"
	WHERE week = [[2022 Week 29]]
SORT file.name ASC

```

  


### Weather

```dataview

TABLE WITHOUT ID
	file.link AS Day,
	weather AS ☁️,
	(temperature + " °C") AS 🌡️,
	(feels-like + " °C") AS 💭,
	wind-direction AS 🧭,
	(wind-speed + " km h⁻¹") AS 🍃,
observed AS 🕓
FROM "02 Personal/02.01 Periodic Notes"
WHERE week = [[2022 Week 29]]
SORT file.name ASC
```