---
date: <% moment(tp.file.title, 'YYYY-[Q]Q').startOf('quarter').add(0, 'days').format("YYYY-MM-DD") %>
tags:
  - "#type/quarterly-note"
---


---
```calendar-nav
```
---

<%*
try {
  // Extract year and quarter from title
  const [NavYear, NavQuarter] = tp.file.title.match(/(\d{4})-Q(\d)/).slice(1);
  const NavQuarterStart = moment(`${NavYear}-${(NavQuarter - 1) * 3 + 1}-01`);

  // Generate current quarter's months
  const NavMonths = [];
  for (let i = 0; i < 3; i++) {
    const NavMonthDate = NavQuarterStart.clone().add(i, 'months');
    NavMonths.push(
      `[[Journal/03 Monthly/${NavYear}/${NavYear}-${NavMonthDate.format('MM')}|${NavMonthDate.format('MMMM YYYY')}]]`
    );
  }

  // Generate previous/next quarter links
  const NavPrevQuarter = NavQuarterStart.clone().subtract(3, 'months');
  const NavNextQuarter = NavQuarterStart.clone().add(3, 'months');
  const NavQuarterLinks = [
    `[[Journal/04 Quarterly/${NavPrevQuarter.format('YYYY')}/${NavPrevQuarter.format('YYYY')}-Q${Math.ceil((NavPrevQuarter.month() + 1)/3)}|Quarter ${Math.ceil((NavPrevQuarter.month() + 1)/3)} ${NavPrevQuarter.format('YYYY')}]]`,
    `[[Journal/04 Quarterly/${NavNextQuarter.format('YYYY')}/${NavNextQuarter.format('YYYY')}-Q${Math.ceil((NavNextQuarter.month() + 1)/3)}|Quarter ${Math.ceil((NavNextQuarter.month() + 1)/3)} ${NavNextQuarter.format('YYYY')}]]`
  ];

  // Generate year link
  const NavYearLink = `[[Journal/05 Yearly/${NavYear}|${NavYear}]]`;

  // Output all sections
  tR += `<span class="centered-navigation-links">${NavMonths.join(' | ')}<br>${NavQuarterLinks.join(' | ')}<br>${NavYearLink}</span>`;

} catch (e) {
  tR += "⚠️ Use in quarterly notes (title format: YYYY-Q1)";
}
%>





# Quarter

<%*
// Extract year and quarter from title (format: 'YYYY-QQ')
let titleParts = tp.file.title.split('-');
let year = parseInt(titleParts[0]);
let quarter = parseInt(titleParts[1].replace('Q', ''));

// Calculate quarter dates
let start_of_quarter = moment(year, 'YYYY').quarter(quarter).startOf('quarter');
let end_of_quarter = moment(year, 'YYYY').quarter(quarter).endOf('quarter');
let days_in_quarter = end_of_quarter.diff(start_of_quarter, 'days') + 1;

// Correct Season Mapping (Northern Hemisphere)
const monthToSeason = (month) => {
    if ([2, 3, 4].includes(month)) return "spring";    // Mar-May
    else if ([5, 6, 7].includes(month)) return "summer";  // Jun-Aug
    else if ([8, 9, 10].includes(month)) return "autumn";  // Sep-Nov
    else return "winter";  // Dec-Feb
};

// --- Pictures Section ---
tR += `^picture-section\n\n`; // Anchor at top for transclusion

let currentMonth = null;
for (let i = 0; i < days_in_quarter; i++) {
    let currentDate = start_of_quarter.clone().add(i, 'days');
    
    // Month change detection
    if (currentDate.month() !== currentMonth) {
        currentMonth = currentDate.month();
        // Close previous month callout (except first iteration)
        if (i > 0) tR += `\n`; 
        
        // Start new month callout with correct season
        let season = monthToSeason(currentDate.month());
        tR += `> [!${season}]- ${currentDate.format('MMMM YYYY')}\n`;
        tR += `>\n`; // Start callout content
    }
    
    // Add image (indented under month callout)
    tR += `> ![[${currentDate.format("YYYY-MM-DD[]#YYYY-MM-DD")}]]\n`;
}

// No need to close final callout - Markdown handles it
%>

> [! highlight]- Stand-out Days
> ```dataview
TABLE aliases
WHERE aliases != null
AND length(aliases) >= 1
AND date >= date(<% moment(tp.file.title, 'YYYY-[Q]Q').startOf('quarter').add(0, 'days').format("YYYY-MM-DD") %>)
AND date <= date(<% moment(tp.file.title, 'YYYY-[Q]Q').startOf('quarter').add(3, 'months').add(-1, 'days').format("YYYY-MM-DD") %>)

> [! highlight]- Highlights!
> ```dataview
TASK
FROM ""
WHERE contains(text, "#log/highlight")
AND date >= date(<% moment(tp.file.title, 'YYYY-[Q]Q').startOf('quarter').add(0, 'days').format("YYYY-MM-DD") %>)
AND date <= date(<% moment(tp.file.title, 'YYYY-[Q]Q').startOf('quarter').add(3, 'months').add(-1, 'days').format("YYYY-MM-DD") %>)
GROUP BY file.name AS filename
SORT filename ASC

> [! Calendar]- Monthly Reviews
> ```dataview
> TASK
> WHERE contains(text, "#log/month-review")
AND date >= date(<% moment(tp.file.title, 'YYYY-[Q]Q').startOf('quarter').add(0, 'days').format("YYYY-MM-DD") %>)
AND date <= date(<% moment(tp.file.title, 'YYYY-[Q]Q').startOf('quarter').add(3, 'months').add(-1, 'days').format("YYYY-MM-DD") %>)
GROUP BY file.name AS filename
SORT filename ASC

---
- [ ] #log/quarter-review


---
## Wheel Of Life

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

- [.] **Soul**: 5 #log/quarterly-soul 
	- Things to do to improve 'Soul'

- [.] **Career/work**: 5 #log/quarterly-career
	- Things to do to improve 'Career'

- [.] **Relationships**: 5 #log/quarterly-relationships 
	- Things to do to improve 'Relationships'

- [.] **Health**: 5 #log/quarterly-health 
	- Things to do to improve 'Health'

- [.] **Personal Growth**: 5 #log/quarterly-personal-growth 
	- Things to do to improve 'Personal Growth'

- [.] **Fun**: 5 #log/quarterly-fun 
	- Things to do to improve 'Soul'

- [.] **Social**: 5 #log/quarterly-social 
	- Things to do to improve 'Social'

- [.] **Finance**: 5 #log/quarterly-finance 
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
endDate: <% moment(tp.file.title, 'YYYY-[Q]Q').startOf('quarter').add(3, 'months').add(-1, 'days').format("YYYY-MM-DD") %>
summary: 
	template: 
		" - Average Day Rating:  {{average()}} 
		\n - Average sleep rating: {{average(dataset(1))}} 
		\n - Average sleep hours: {{average(dataset(2))}} 
		\n - Average Healthy Food Rating: {{average(dataset(3))}}"
```