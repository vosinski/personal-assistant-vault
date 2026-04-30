---
tags:
  - "#inbox"
Topics:
---
```dataview
TABLE Updated, length(file.inlinks) AS "Links", Topics
FROM #molecule  
SORT file.mtime DESC
```


