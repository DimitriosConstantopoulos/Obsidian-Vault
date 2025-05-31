---
date: <% moment(tp.file.title, 'YYYY-[W]ww').startOf('week').add(0,'days').format("YYYY-MM-DD") %>
tags:
  - "#type/weekly-note"
---

---
```calendar-nav
```
---

<%*
// Extract year and week from file title (expects format YYYY-WWW)
let NavTitleMatch = tp.file.title.match(/(\d{4})-W(\d{2})/);
if (!NavTitleMatch) return "Error: Title must be YYYY-WWW format";

let [_, NavYear, NavWeek] = NavTitleMatch;
NavWeek = parseInt(NavWeek);

// Calculate Monday and Sunday dates of the week
let NavMonday = moment().isoWeekYear(NavYear).isoWeek(NavWeek).startOf('isoWeek');
let NavSunday = NavMonday.clone().add(6, 'days');

// Generate daily links
let NavDailyLinks = [];
for (let i = 0; i < 7; i++) {
    let NavDate = NavMonday.clone().add(i, 'days');
    let NavDayName = NavDate.format('ddd');
    let NavDayNum = NavDate.format('Do').replace(/\d+/, '');
    NavDailyLinks.push(`[[Journal/01 Daily/${NavDate.format('YYYY/MM/YYYY-MM-DD')}|${NavDayName} ${NavDate.date()}${NavDayNum}]]`);
}

// Generate previous/next week links
let NavPrevWeek = NavMonday.clone().subtract(1, 'week');
let NavNextWeek = NavMonday.clone().add(1, 'week');
let NavWeekLinks = [
    `[[Journal/02 Weekly/${NavPrevWeek.format('YYYY')}/${NavPrevWeek.format('YYYY')}-W${NavPrevWeek.isoWeek().toString().padStart(2, '0')}|Week ${NavPrevWeek.isoWeek()}]]`,
    `[[Journal/02 Weekly/${NavNextWeek.format('YYYY')}/${NavNextWeek.format('YYYY')}-W${NavNextWeek.isoWeek().toString().padStart(2, '0')}|Week ${NavNextWeek.isoWeek()}]]`
];

// Generate monthly links
let NavMonths = [];
let NavStartMonth = NavMonday.format('YYYY-MM');
let NavEndMonth = NavSunday.format('YYYY-MM');

NavMonths.push(`[[Journal/03 Monthly/${NavYear}/${NavStartMonth}|${moment(NavStartMonth).format('MMMM YYYY')}]]`);
if (NavStartMonth !== NavEndMonth) {
    NavMonths.push(`[[Journal/03 Monthly/${NavYear}/${NavEndMonth}|${moment(NavEndMonth).format('MMMM YYYY')}]]`);
}

// Generate quarter links
let NavQuarters = [];
let NavStartQuarter = Math.ceil((NavMonday.month() + 1) / 3);
let NavEndQuarter = Math.ceil((NavSunday.month() + 1) / 3);

NavQuarters.push(`[[Journal/04 Quarterly/${NavYear}/${NavYear}-Q${NavStartQuarter}|Quarter ${NavStartQuarter}]]`);
if (NavStartQuarter !== NavEndQuarter) {
    NavQuarters.push(`[[Journal/04 Quarterly/${NavYear}/${NavYear}-Q${NavEndQuarter}|Quarter ${NavEndQuarter}]]`);
}

// Generate year link
let NavYearLink = `[[Journal/05 Yearly/${NavYear}|${NavYear}]]`;

// Output all navigation links
tR += `<span class="centered-navigation-links">${NavDailyLinks.join(' | ')}<br>${NavWeekLinks.join(' | ')}<br>${NavMonths.join(' | ')}<br>${NavQuarters.join(' | ')}<br>${NavYearLink}</span>`;
%>








## Pictures this Week

<%*
// =============================================
// WEEKLY REVIEW TEMPLATE (Monday-starting weeks)
// =============================================

// 1. GET YEAR AND WEEK FROM THE NOTE'S TITLE
// ------------------------------------------
// Example title: "2025-W19 Weekly Review" or "2025-W2 Weekly Review"
let titleMatch = tp.file.title.match(/(\d{4})-W?(\d{1,2})/);
let year = parseInt(titleMatch[1]);  // Extract the year (2025)
let week = parseInt(titleMatch[2]);  // Extract the week number (19 or 2)

// 2. FIND THE MONDAY FOR THIS WEEK
// --------------------------------
// Using moment.js to calculate dates:
let mondayOfWeek = moment()
  .isoWeekYear(year)    // Set to our specific year
  .isoWeek(week)        // Set to our specific week
  .startOf('isoWeek');  // Go to Monday of that week

// 3. CREATE THE PICTURES SECTION HEADER
// ------------------------------------
tR += `^picture-section\n\n`;  // This lets us embed just this part in other notes

// 4. SHOW EACH DAY OF THE WEEK (Monday-Sunday)
// -------------------------------------------
// We'll create a visual box for each day:
for (let dayIndex = 0; dayIndex < 7; dayIndex++) {
  // Calculate current day's date
  let currentDate = mondayOfWeek.clone().add(dayIndex, 'days');
  
  // Create a formatted title like "Monday, May 5"
  let dayTitle = currentDate.format('dddd, MMMM D');
  
  // Create the visual box (Obsidian callout syntax)
  tR += `> [!picture]- ${dayTitle}\n`;  // Box title
  tR += `>\n`;                         // Empty line inside box
  
  tR += `> ![[${currentDate.format("YYYY-MM-DD[]#YYYY-MM-DD")}]]\n\n`;
}
%>



## Highlights and Reviews

>[! highlight]- Highlights!
>```dataview
TASK
FROM ""
WHERE icontains(text, "#log/highlight")
AND (date <= (date(this.date) + dur(6 days))) and (date >= (date(this.date)))
GROUP BY file.name as filename

>[! Calendar]- Daily Reviews! 
>```dataview 
TASK 
FROM "" 
WHERE icontains(text, "#log/day-review") AND (date <= (date(this.date) + dur(6 days))) and (date >= (date(this.date))) 
GROUP BY file.name as filename 
SORT filename ASC

---
- [ ] #log/week-review



---

```chart
type: polarArea
labels: [Soul, Career/Work, Love/Relationships, Health/Fitness, Personal Growth, Fun/Recreation, Social, Finance]
series:
  - label: 'Wheel of Life'
    data: [2, 3, 5, 7, 4, 5, 9, 6]
tension: 0.2
width: 88%
labelColors: true
fill: true
beginAtZero: true
max: 10
legendPosition: right
```


- [.] **Soul**: 5 #log/weekly-soul 
	- Things to do to improve 'Soul'

- [.] **Career/work**: 5 #log/weekly-career
	- Things to do to improve 'Career'

- [.] **Relationships**: 5 #log/weekly-relationships 
	- Things to do to improve 'Relationships'

- [.] **Health**: 5 #log/weekly-health 
	- Things to do to improve 'Health'

- [.] **Personal Growth**: 5 #log/weekly-personal-growth 
	- Things to do to improve 'Personal Growth'

- [.] **Fun**: 5 #log/weekly-fun 
	- Things to do to improve 'Soul'

- [.] **Social**: 5 #log/weekly-social 
	- Things to do to improve 'Social'

- [.] **Finance**: 5 #log/weekly-finance 
	- Things to do to improve 'finance'


---
## Statistics
### Calendar Graph

```tracker
searchType: frontmatter
searchTarget: log-day-rating, log-sleep-rating, log-sleep-hours, log-healthy-eating
datasetName: Day-Rating, Sleep-Rating, Sleep-Hours, Healthy-eating
startDate: <% moment(tp.file.title, 'YYYY-[W]ww').startOf('month').add(0, 'days').format("YYYY-MM-DD") %>
endDate: <% moment(tp.file.title, 'YYYY-[W]ww').startOf('month').add(1, 'months').add(-1, 'days').format("YYYY-MM-DD") %>
month:
    startWeekOn: 'Sun'
    threshold: 0.99, 0.99, 7.51, 0.99
    color: green
    initMonth: <% moment(tp.file.title, 'YYYY-[W]ww').format('YYYY-MM') %>
    circleColorByValue: true
    todayRingColor: white
    selectedRingColor: white
```


### Averages


```tracker
searchType: frontmatter
searchTarget: log-day-rating
datasetName: log-day-rating
startDate: "<% moment(tp.file.title,'YYYY-[W]ww').startOf('week').add(0, 'days').format("YYYY-MM-DD") %>"
endDate: "<% moment(tp.file.title,'YYYY-[W]ww').startOf('week').add(6, 'days').format("YYYY-MM-DD") %>"
summary:
    template: " - Average Day Rating: {{average()}}"
```

```tracker
searchType: frontmatter
searchTarget: log-sleep-ratings
datasetName: log-sleep-ratings
startDate: "<% moment(tp.file.title,'YYYY-[W]ww').startOf('week').add(0, 'days').format("YYYY-MM-DD") %>"
endDate: "<% moment(tp.file.title,'YYYY-[W]ww').startOf('week').add(6, 'days').format("YYYY-MM-DD") %>"
summary:
    template: " - Average Sleep Rating: {{average()}}"
```

```tracker
searchType: frontmatter
searchTarget: log-healthy-food-rating
datasetName: log-healthy-food-rating
startDate: "<% moment(tp.file.title,'YYYY-[W]ww').startOf('week').add(0, 'days').format("YYYY-MM-DD") %>"
endDate: "<% moment(tp.file.title,'YYYY-[W]ww').startOf('week').add(6, 'days').format("YYYY-MM-DD") %>"
summary:
    template: " - Average Healthy Food Rating: {{average()}}"
```

```tracker
searchType: frontmatter
searchTarget: log-sleep-hours
datasetName: log-sleep-hours
startDate: "<% moment(tp.file.title,'YYYY-[W]ww').startOf('week').add(0, 'days').format("YYYY-MM-DD") %>"
endDate: "<% moment(tp.file.title,'YYYY-[W]ww').startOf('week').add(6, 'days').format("YYYY-MM-DD") %>"
summary:
    template: " - Average Sleep Hours: {{average()}}"
```


```tracker
searchType: frontmatter 
searchTarget: log-day-rating, log-sleep-ratings, log-sleep-hours, log-healthy-food-rating 
datasetName: log-day-rating, log-sleep-ratings, log-sleep-hours, log-healthy-food-rating 
startDate: <% moment(tp.file.title,'YYYY-[W]ww').startOf('week').add(0, 'days').format("YYYY-MM-DD") %> 
endDate: <% moment(tp.file.title,'YYYY-[W]ww').startOf('week').add(6, 'days').format("YYYY-MM-DD") %> 
summary: 
	template: 
		" - Average Day Rating:  {{average()}} 
		\n - Average sleep rating: {{average(dataset(1))}} 
		\n - Average sleep hours: {{average(dataset(2))}} 
		\n - Average Healthy Food Rating: {{average(dataset(3))}}"
```