---
tags:
  - "#inbox"
Topics:
---

## Fleeting Notes
```dataview
TABLE Updated, length(file.inlinks) AS "Links", Topics
FROM #fleetingnote 
SORT file.mtime DESC
```

## Source Notes
```dataview
TABLE Updated, length(file.inlinks) AS "Links", Topics
FROM #Source  
SORT file.mtime DESC
```

## Notes With Ideas
```dataview
TABLE Updated, length(file.inlinks) AS "Links", Topics
FROM #idea  
SORT file.mtime DESC
```