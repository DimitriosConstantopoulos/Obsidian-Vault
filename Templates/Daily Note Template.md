---
date: <% moment(tp.file.title, 'YYYY-MM-DD').format("YYYY-MM-DD") %>
aliases:
tags:
  - "#type/daily-note"
log-sleep-hours:
log-sleep-ratings:
log-healthy-food-rating:
log-day-rating:
---


## Navigation
---
```calendar-nav
```
<%*
try {
    const dateStr = tp.file.title.match(/\d{4}-\d{2}-\d{2}/)?.[0];
    if (!dateStr) throw new Error();
    
    const currentDate = moment(dateStr);

    // Format daily links (year/month folders)
    const formatLink = (date, label) => `[[Journal/01 Daily/${date.format("YYYY/MM")}/${date.format("YYYY-MM-DD")}|${label}]]`;

    // Yesterday and Tomorrow links
    const yesterdayLink = formatLink(currentDate.clone().subtract(1, 'days'), "Yesterday");
    const tomorrowLink = formatLink(currentDate.clone().add(1, 'days'), "Tomorrow");

    // Weekly link with year folder path
    const weekYear = currentDate.isoWeekYear();
    const weekNum = currentDate.isoWeek();
    const weekStr = `${weekYear}-W${weekNum.toString().padStart(2, '0')}`;
    const weekLink = `[[Journal/02 Weekly/${weekYear}/${weekStr}|Week ${weekNum}]]`;

    // Output all links within the same centered span, week link on next line but no extra spacing
    tR += `<span class="centered-navigation-links">${yesterdayLink} | ${tomorrowLink}<br>${weekLink}</span>`;
} catch (e) {}
%>



---
## Daily Quote

<%*
const quotesFile = "Journal Help/Quotes.md";

// Step 1: Get date from frontmatter or title
const noteDateStr = tp.file.frontmatter?.date || tp.file.title;
const noteDate = new Date(noteDateStr + "T00:00:00");

// Step 2: Determine if it's a weekend (Sat=5 or Sun=6), with Monday = 0
const rawDay = noteDate.getDay(); // Sunday=0 ... Saturday=6
const day = (rawDay + 6) % 7;
const isWeekend = day >= 5; // Saturday (5) or Sunday (6)

// Step 3: Read Quotes.md content
const file = app.vault.getAbstractFileByPath(quotesFile);
if (!file) {
  tR += "⚠️ Quote file not found.";
  return;
}
const content = await app.vault.read(file);

// Step 4: Extract quotes using regex
// Matches: callout + quote lines + optional tag line + block ID line
const regex = /> \[!quote\]\+.*?\n(?:> .*\n)*> *#([^\n]+)\n\^([^\n]+)/gm;
const matches = [];
let match;

while ((match = regex.exec(content)) !== null) {
  const tags = match[1].split(/[,\s]+/).filter(tag => tag.length > 0);
  const blockId = match[2];

  const isValid =
    (isWeekend && tags.some(tag => tag.includes("weekend"))) ||
    (!isWeekend && tags.some(tag => tag.includes("daily")));

  if (isValid) matches.push(blockId);
}

// Step 5: Embed a random valid quote
if (matches.length > 0) {
  const pick = matches[Math.floor(Math.random() * matches.length)];
  tR += `![[Quotes#^${pick}]]`;
} else {
  tR += "⚠️ No matching quotes found for this day.";
}
%>

---

## Tasks

<%*
/* Unique variable prefix: dt_ (daily tasks) */
const dt_filePath = "Journal Help/Recurring Tasks.md"; 
const dt_targetFile = app.vault.getAbstractFileByPath(dt_filePath);
const dt_fileContent = await app.vault.read(dt_targetFile);
const dt_filteredTasks = dt_fileContent.split("\n").filter(
  dt_line => dt_line.includes("#task-daily")
);

let dt_calloutOutput = `> [!daily]- Daily Tasks\n`;
dt_filteredTasks.forEach(dt_task => {
  dt_calloutOutput += `> ${dt_task.replace("#task-daily", "").trim()}\n`;
});
tR += dt_calloutOutput;
%>


>[! journal]- Tasks
>```dataview
>TASK
>WHERE !completed
>AND icontains(text, "[[<% moment(tp.file.title, 'YYYY-MM-DD').format("YYYY-MM") %>-")
>AND icontains(text, "#task")
>GROUP BY file.name as filename
>```

>[! journal]- Recurring Tasks
>```dataview
>TASK
>WHERE !completed
>AND icontains(text, "[[<% moment(tp.file.title, 'YYYY-MM-DD').format("YYYY-MM") %>-")
>AND icontains(text, "#task")
>AND icontains(text, "#reccuring")
>GROUP BY file.name as filename
>```

>[! reminder]- Reminders
>```dataview
>TASK
>WHERE !completed
>AND icontains(text, "[[<% moment(tp.file.title, 'YYYY-MM-DD').format("YYYY-MM") %>-")
>AND icontains(text, "#reminder")
>GROUP BY file.name as filename
>```

### Set Tasks



---
## Information (FIND NEW NAME) 

>[! journal]- On This Day...
>```dataview
LIST
FROM "Journal/01 Daily"
WHERE dateformat(file.day, "MM-dd") = dateformat(this.file.day, "MM-dd")

>[! calendar]- Notes Created This Day
>```dataview
TABLE created, updated as modified, tags, summary
FROM "" AND !"Journal" AND !"Templates"
WHERE icontains(dateformat(file.ctime, "YYYY-MM-DD"), dateformat(this.file.day, "YYYY-MM-DD"))


- `**` Morning brian-dump/word-vomit here `**`


## Pictures
#### <% tp.file.title %>



---
## Log Pages

- 08:00
	- [x] First  log here... (see this as a side adventure or side quest from your main storyline for the day)



---
## Evening Review

- [.] #log/daily-food
- [ ] #log/day-review

