---
date: <% moment(tp.file.title, 'YYYY-MM').startOf('month').add(0, 'days').format("YYYY-MM-DD") %>
tags:
  - "#type/monthly-note"
---


---
```calendar-nav
```


<%*
// MONTHLY TEMPLATE - With full navigation
let NavMonthRef = moment(tp.file.title, "YYYY-MM");
let NavWeekLinks = [];
let NavWeekTracker = NavMonthRef.clone().startOf('month').startOf('isoWeek');

// Ordinal suffix function
const getOrdinalSuffix = (num) => {
    const j = num % 10, k = num % 100;
    if (j == 1 && k != 11) return num + "st";
    if (j == 2 && k != 12) return num + "nd";
    if (j == 3 && k != 13) return num + "rd";
    return num + "th";
};

// Build weekly links
while (NavWeekTracker.isSameOrBefore(NavMonthRef.endOf('month'))) {
    let NavWeekEnd = NavWeekTracker.clone().add(6, 'days');
    if (NavWeekTracker.isSameOrBefore(NavMonthRef.endOf('month'))) {
        let NavStartDay = getOrdinalSuffix(NavWeekTracker.date());
        let NavEndDay = getOrdinalSuffix(NavWeekEnd.date());
        NavWeekLinks.push(
            `[[Journal/02 Weekly/${NavWeekTracker.isoWeekYear()}/${NavWeekTracker.isoWeekYear()}-W${NavWeekTracker.isoWeek().toString().padStart(2,'0')}|` +
            `W${NavWeekTracker.isoWeek()} (${NavStartDay}-${NavEndDay})]]`
        );
    }
    NavWeekTracker.add(1, 'week');
}

// Previous/Next Month links
let NavPrevMonth = NavMonthRef.clone().subtract(1, 'month');
let NavNextMonth = NavMonthRef.clone().add(1, 'month');
let NavMonthLinks = [
    `[[Journal/03 Monthly/${NavPrevMonth.format('YYYY')}/${NavPrevMonth.format('YYYY-MM')}|${NavPrevMonth.format('MMMM YYYY')}]]`,
    `[[Journal/03 Monthly/${NavNextMonth.format('YYYY')}/${NavNextMonth.format('YYYY-MM')}|${NavNextMonth.format('MMMM YYYY')}]]`
];

// Quarter link
let NavQuarterNum = Math.ceil((NavMonthRef.month() + 1) / 3);
let NavQuarterLink = `[[Journal/04 Quarterly/${NavMonthRef.format('YYYY')}/${NavMonthRef.format('YYYY')}-Q${NavQuarterNum}|Quarter ${NavQuarterNum}]]`;

// Year link
let NavYearLink = `[[Journal/05 Yearly/${NavMonthRef.format('YYYY')}|${NavMonthRef.format('YYYY')}]]`;

// Output all links
tR += `<span class="centered-navigation-links">${NavWeekLinks.join(' | ')}<br>${NavMonthLinks.join(' | ')}<br>${NavQuarterLink}<br>${NavYearLink}</span>`;
%>







## Month
<%*
// Extract year and month from title (format: 'YYYY-MM Monthly Review')
let [year, month] = tp.file.title.split('-').map(Number);
let start_of_month = moment([year, month - 1]); // months are 0-indexed in Moment.js
let end_of_month = start_of_month.clone().endOf('month');

// --- Pictures Section ---
tR += `^picture-section\n\n`; // Anchor for transclusion

// Group days by week
let currentWeek = null;
let days_in_month = end_of_month.date(); // number of days in month

for (let day = 1; day <= days_in_month; day++) {
    let currentDate = start_of_month.clone().date(day);
    let weekNumber = currentDate.week();
    
    // Week change detection
    if (weekNumber !== currentWeek) {
        currentWeek = weekNumber;
        // Close previous week callout (except first iteration)
        if (day > 1) tR += `\n`; 
        
        // Start new week callout
        let weekStart = currentDate.clone().startOf('week');
        let weekEnd = currentDate.clone().endOf('week');
        tR += `> [!picture]- Week ${weekNumber} (${weekStart.format('MMM D')} - ${weekEnd.format('MMM D')})\n`;
        tR += `>\n`; // Start callout content
    }
    
    // Add image (indented under week callout)
    tR += `> ![[${currentDate.format("YYYY-MM-DD[]#YYYY-MM-DD")}]]\n`;
}
%>

> [! highlight]- Stand-out Days
>  ```dataview
TABLE aliases
WHERE aliases != null
AND length(aliases) >= 1
AND date >= date(<% moment(tp.file.title, 'YYYY-MM').startOf('month').format("YYYY-MM-DD") %>)
AND date <= date(<% moment(tp.file.title, 'YYYY-MM').endOf('month').format("YYYY-MM-DD") %>)


> [! highlight]- Highlights!
> ```dataview
TASK
FROM ""
WHERE contains(text, "#log/highlight")
AND date >= date(<% moment(tp.file.title, 'YYYY-MM').startOf('month').format("YYYY-MM-DD") %>)
AND date <= date(<% moment(tp.file.title, 'YYYY-MM').endOf('month').format("YYYY-MM-DD") %>)
GROUP BY file.name AS filename
SORT filename ASC

> [! Calendar]- Weekly Reviews
> ```dataview
> TASK
> WHERE contains(text, "#log/week-review")
AND date >= date(<% moment(tp.file.title, 'YYYY-MM').startOf('month').add(0, 'days').format("YYYY-MM-DD") %>)
AND date <= date(<% moment(tp.file.title, 'YYYY-MM').startOf('month').add(1, 'months').add(-1, 'days').format("YYYY-MM-DD") %>)
GROUP BY file.name AS filename
SORT filename ASC

---
- [ ] #log/month-review


---
## Wheel of Life

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

- [.] **Soul**: 5 #log/monthly-soul 
	- Things to do to improve 'Soul'

- [.] **Career/work**: 5 #log/monthly-career
	- Things to do to improve 'Career'

- [.] **Relationships**: 5 #log/monthly-relationships 
	- Things to do to improve 'Relationships'

- [.] **Health**: 5 #log/monthly-health 
	- Things to do to improve 'Health'

- [.] **Personal Growth**: 5 #log/monthly-personal-growth 
	- Things to do to improve 'Personal Growth'

- [.] **Fun**: 5 #log/monthly-fun 
	- Things to do to improve 'Soul'

- [.] **Social**: 5 #log/monthly-social 
	- Things to do to improve 'Social'

- [.] **Finance**: 5 #log/monthly-finance 
	- Things to do to improve 'finance'

---
## Statistics
### Calendar Graph
```tracker
searchType: frontmatter
searchTarget: log-day-rating, log-sleep-rating, log-sleep-hours, log-healthy-eating
datasetName: Day-Rating, Sleep-Rating, Sleep-Hours, Healthy-eating
month:
    startWeekOn: 'Sun'
    threshold: 0.99, 0.99, 7.51, 0.99
    color: green
    initMonth: <% moment(tp.file.title, 'YYYY-[Q]Q').format('YYYY-MM') %>
    circleColorByValue: true
    todayRingColor: white
    selectedRingColor: white
```

#### Averages

```tracker
searchType: frontmatter 
searchTarget: log-day-rating, log-sleep-ratings, log-sleep-hours, log-healthy-food-rating 
datasetName: log-day-rating, log-sleep-ratings, log-sleep-hours, log-healthy-food-rating 
startDate: <% moment(tp.file.title, 'YYYY-[Q]Q').startOf('quarter').add(0, 'days').format("YYYY-MM-DD") %> 
endDate: <% moment(tp.file.title, 'YYYY-[Q]Q').startOf('quarter').add(3, 'month').add(-1, 'days').format("YYYY-MM-DD") %> 
summary: 
	template: 
		" - Average Day Rating:  {{average()}} 
		\n - Average sleep rating: {{average(dataset(1))}} 
		\n - Average sleep hours: {{average(dataset(2))}} 
		\n - Average Healthy Food Rating: {{average(dataset(3))}}"
```