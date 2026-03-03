---
tags: jobs
target: 50
---

## This Week

```dataview
TABLE WITHOUT ID
  length(filter(file.tasks, (t) => t.completed)) + " / " + this.target AS "Applied / Target",
  this.target - length(filter(file.tasks, (t) => t.completed)) AS "Remaining"
WHERE file.name = "Jobs"
```

> Log each application as a task. Check it off once submitted.

- [ ]
