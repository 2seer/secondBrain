---
banner: "![[daily log.jpg]]"
banner_y: 0.43
banner_icon: 🖋️
type: dashboard
description: A Dashboard for Daily Logs
---

---
**Tags**:: #dashboard
**Links**::[[📰 Dashboard]]

---

# 🖋 Daily Log

```button
name New
type command
action Create new note
color purple
```
^button-s2mb

---

```dataview
TABLE WITHOUT ID
	("[[" + name + "]]") AS "File",
	effectiveness AS "Effectiveness",
	기분 AS "🌈 I feel"
	
FROM #daily
WHERE file.name != "🗓 Daily Note"
SORT file.name DESC
```
