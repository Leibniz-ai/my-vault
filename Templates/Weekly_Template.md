# 周复盘

> [!abstract] 本周寄语
> 

---

> [!quote]+ 💡 本周灵感 
> ```dataview
> LIST rows.L.text
> FROM "MyLife/Daily"
> FLATTEN date(this.file.name, "kkkk-'W'WW") as week_start
> WHERE file.day >= week_start AND file.day <= week_start + dur(6 days)
> FLATTEN file.lists as L
> WHERE contains(meta(L.section).subpath, "灵感")
> GROUP BY file.link
> ```

> [!todo]+ 📝 总结与反思 
> ```dataview
> LIST rows.L.text
> FROM "MyLife/Daily"
> FLATTEN date(this.file.name, "kkkk-'W'WW") as week_start
> WHERE file.day >= week_start AND file.day <= week_start + dur(6 days)
> FLATTEN file.lists as L
> WHERE contains(meta(L.section).subpath, "总结与反思")
> GROUP BY file.link
> ```

> [!example]+ 📊 状态评分表
> ```dataview
> TABLE score as "评分"
> FROM "MyLife/Daily"
> FLATTEN date(this.file.name, "kkkk-'W'WW") as week_start
> WHERE file.day >= week_start AND file.day <= week_start + dur(6 days)
> SORT file.day ASC
> ```

---