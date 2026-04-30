---
tags:
  - "#dailynote"
---

## 

Tasks

## Ideas (Fleeting Note)
1. 

## Favourite Moments

## Journal 

## Notes Created Today
```dataview  
List FROM ""  
WHERE file.cday = date("<%tp.date.now('YYYY-MM-DD')%>")  
SORT file.ctime asc
```

## Notes Updated Today

```dataview  
List  
FROM ""  
WHERE file.mday = date("<%tp.date.now('YYYY-MM-DD')%>") SORT file.mtime asc
```