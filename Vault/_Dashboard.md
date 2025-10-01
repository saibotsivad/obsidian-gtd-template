
This is something I'm still working on, but I think it would be a really good quick access dashboard.

---

###### Horizons
[[Vision]] | ...etc | [[Tasks]]
###### Calendar
[[2025]] | [[Journal/2025/2025Q2]] | [[2025M09]] | [[Reference/Projects/Sprint 2025-09|Sprint 32]] | [[2025W32]] | [[Journal/2025/02/2025-09-29]]
*(implications here, required reviews: yearly, quarterly, monthly, sprint, weekly)*
###### Agendas
[[People]] | [[Meetings]]
###### Projects
```dataview
LIST
FROM "Projects" AND -"Projects/Someday Maybe"
SORT ASC
```
###### Project Next Steps
```dataview
TASK
FROM "Projects" AND -"Projects/Someday Maybe"
WHERE !completed
WHERE !cancelled
WHERE contains(tags, "next-step")
GROUP BY file.name
```
