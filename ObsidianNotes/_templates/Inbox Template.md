---
tags:
  - "#inbox"
Topics:
---

```dataview
TABLE Updated, length(file.inlinks) AS "Links", Topics
FROM #inbox 
SORT file.mtime DESC
```


