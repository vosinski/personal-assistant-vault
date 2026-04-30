---
tags:
  - "#inbox"
Topics:
---
```dataview
TABLE Updated, length(file.inlinks) AS "Links", Topics
FROM #atom  
SORT file.mtime DESC
```


