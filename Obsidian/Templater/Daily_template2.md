---
aliases: 
tags: 
AutoNoteMover: disable
---
日: [[<% tp.date.now("YYYY-MM-DD-dd", -1, tp.file.title, "YYYY-MM-DD-dd") %>]] | [[<% tp.date.now("YYYY-MM-DD-dd", 1, tp.file.title, "YYYY-MM-DD-dd") %>]]
週: [[<% tp.date.now("YYYY-MM-DD-dd", -7, tp.file.title, "YYYY-MM-DD-dd") %>]] | [[<% tp.date.now("YYYY-MM-DD-dd", 7, tp.file.title, "YYYY-MM-DD-dd") %>]]
月: [[<% tp.date.now("YYYY-MM-DD-dd", "P-1M", tp.file.title, "YYYY-MM-DD-dd") %>]] | [[<% tp.date.now("YYYY-MM-DD-dd", "P1M", tp.file.title, "YYYY-MM-DD-dd") %>]]
年: [[<% tp.date.now("YYYY-MM-DD-dd", "P-1Y", tp.file.title, "YYYY-MM-DD-dd") %>]] | [[<% tp.date.now("YYYY-MM-DD-dd", "P1Y", tp.file.title, "YYYY-MM-DD-dd") %>]]


<% tp.file.cursor() %>
<%* app.commands.executeCommandById("obsidian-silhouette:insert-tasks") -%>

<div style="border-top: solid medium green; border-bottom: solid medium green; text-align: center;"><font size="6"> <strong>
    朝 🌄
</strong></font></div>



<div style="border-top: solid medium green; border-bottom: solid medium green; text-align: center;"><font size="6"> <strong>
    昼 🍵
</strong></font></div>



<div style="border-top: solid medium green; border-bottom: solid medium green; text-align: center;"><font size="6"> <strong>
    夜 🌙
</strong></font></div>





Task (Alt+T) Neta (Alt+I) Want (Alt+W) Memo (Alt+Q)
## Task / Neta / Want
```tasks
hide task count
hide created date
hide done date
(tags include #Task) OR \
(tags include #Want) OR \
(tags include #Neta)
(path includes _notes_private/Task/Task) OR \
(path includes _notes_private/Neta/Neta) OR \
(path includes _notes_private/Want/Want)
(heading includes {{date:MM}}) OR \
(heading includes {{date-1M:MM}}) OR \
(heading includes {{date-2M:MM}})
```

## Memo

