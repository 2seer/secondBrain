```dataview
List 
where type = "webclip"
	and (contains(file.folder, "00") or contains(file.folder, "10") or contains(file.folder, "30")) 
sort file.cday desc
limit 5
```
