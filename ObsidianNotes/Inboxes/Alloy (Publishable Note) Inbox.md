---
tags:
  - "#inbox"
Topics:
---
```dataview
TABLE Updated, length(file.inlinks) AS "Links", Topics
FROM #alloy  
SORT file.mtime DESC
```


