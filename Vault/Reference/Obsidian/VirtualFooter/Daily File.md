
```dataviewjs
const fileDate = dv.current().file.name

// ONLY show on the files with a date name
if (/^\d{4}-\d{2}-\d{2}$/.test(fileDate)) {

dv.el('div', new Date(fileDate + 'T12:00').toLocaleDateString('en-US', { weekday: 'long' }))

const filter = p => {
    if (!p.file.name.startsWith('Sprint')) return false
    const start = p.file.frontmatter.startDate
    const end = p.file.frontmatter.endDate
    return start && fileDate >= start && end && fileDate <= end
}
const sprints = [
    ...dv
        .pages('"Reference/Projects/Completed/Sprints"')
        .where(filter),
    ...dv
        .pages('"Projects"')
        .where(filter),
]
if (sprints.length > 0) {
    dv.el("div",
        sprints.map(p => `**${p.file.link}**\n🛫 ${p.file.frontmatter.startDate} ⏳${p.file.frontmatter.endDate}`).join("\n")
    );
} else {
    dv.el('div', '# NO SPRINT YET')
}
// For two-week sprints, this shows a per-day status, like this:
// ✅ Mon | 👋 Tue | 🔲 Wed | 🔲 Thu | 🔲 Fri
// 🔲 Mon | 🔲 Tue | 🔲 Wed | 🔲 Thu | 🔲 Fri ^progress
const start = sprints[0]?.file.frontmatter.startDate
const end = sprints[0]?.file.frontmatter.endDate
const now = new Date()
const startDate = new Date(start + 'T12:00')
const endDate = new Date(end + 'T12:00')
const weekDays = { 1: 'Mon', 2: 'Tue', 3: 'Wed', 4: 'Thu', 5: 'Fri' }
const firstWeek = []
let days = []
for (var i = 0; i <= 14; i++) {
	const day = new Date(startDate.getTime() + i * 24 * 60 * 60 * 1000)
	if (day.getDay() > 0 && day.getDay() < 6 && day.getDate() <= endDate.getDate()) {
		let line = ''
		if (now.getDate() > day.getDate()) line += '✅ '
		else if (now.getDate() === day.getDate()) line += '✋ '
		else line += '🔲 '
		line += weekDays[day.getDay()]
		days.push(line)
	}
	if (day.getDay() === 6 && !firstWeek.length) {
		firstWeek.push(...days)
		days = []
	}
}
const progress = [ firstWeek, days ].map(lines => lines.join(' | ')).join('\n')
dv.el('div', progress)
dv.el('div', '---')

// These are three different task lists, split up this way because then
// it makes the "done" stuff to show up at the bottom, and that feels like
// a nice way to sort.

// Tasks in the current sprint
dv.el('div', `\`\`\`tasks
path regex matches /^Projects\/Sprint .+\.md/
tags does not include #waiting-for
(no due date) OR (due date on or before ${fileDate})
(not done) OR (done ${fileDate})
hide edit button
hide task count
hide backlink
show tree
sort by start
\`\`\``)

// Tasks incomplete and due today or before today (exclude today and sprint)
dv.el('div', `\`\`\`tasks
((due in or before ${fileDate}) AND (not done))
filename does not include ${fileDate}
(path regex does not match /^Projects\/Sprint .+\.md/) OR (done ${fileDate})
group by function task.file.filenameWithoutExtension
hide edit button
hide task count
show tree
\`\`\``)

// Tasks completed today (exclude todays note though)
dv.el('div', `\`\`\`tasks
done {{query.file.filenameWithoutExtension}}
filename does not include ${fileDate}
path regex does not match /^Projects\/Sprint .+\.md/
group by function task.file.filenameWithoutExtension
hide edit button
hide task count
\`\`\``)
}
```
