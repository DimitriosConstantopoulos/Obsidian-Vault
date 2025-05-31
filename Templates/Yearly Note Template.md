---
date: <% moment(tp.file.title, 'YYYY').startOf('year').add(0, 'days').format("YYYY-MM-DD") %>
tags:
  - "#type/yearly-note"
---

---
```calendar-nav
```
---

<%*
try {
  // Extract year from filename
  const NavYear = tp.file.title.match(/\d{4}/)?.[0];
  if (!NavYear) throw new Error("Not a yearly note");
  
  // Generate current year's quarters
  const NavQuarters = [];
  for (let q = 1; q <= 4; q++) {
    NavQuarters.push(`[[Journal/04 Quarterly/${NavYear}/${NavYear}-Q${q}|Quarter ${q}]]`);
  }

  // Generate previous/next year links
  const NavPrevYear = (parseInt(NavYear) - 1).toString();
  const NavNextYear = (parseInt(NavYear) + 1).toString();
  const NavYearLinks = [
    `[[Journal/05 Yearly/${NavPrevYear}|${NavPrevYear}]]`,
    `[[Journal/05 Yearly/${NavNextYear}|${NavNextYear}]]`
  ];

  // Output all sections
  tR += `<span class="centered-navigation-links">${NavQuarters.join(' | ')}<br>${NavYearLinks.join(' | ')}</span>`;
  
} catch (e) {
  tR += "⚠️ Please use in yearly notes (title format: YYYY)";
}
%>




## Year

<%*
// Extract year from title (format: 'YYYY Year in Review')
let year = parseInt(tp.file.title.split(' ')[0]);

// Correct Season Mapping (Northern Hemisphere)
const monthToSeason = (month) => {
    if ([2, 3, 4].includes(month)) return "spring";    // Mar-May
    else if ([5, 6, 7].includes(month)) return "summer";  // Jun-Aug
    else if ([8, 9, 10].includes(month)) return "autumn";  // Sep-Nov
    else return "winter";  // Dec-Feb
};

// --- Pictures Section ---
tR += `^picture-section\n\n`; // Anchor at top for transclusion

// Process each quarter
for (let quarter = 1; quarter <= 4; quarter++) {
    // Start quarter callout
    tR += `> [!picture]- Q${quarter} ${year}\n`;
    tR += `> \n`; // Start callout content
    
    // Get the months for this quarter
    let start_of_quarter = moment(year, 'YYYY').quarter(quarter).startOf('quarter');
    let end_of_quarter = moment(year, 'YYYY').quarter(quarter).endOf('quarter');
    let days_in_quarter = end_of_quarter.diff(start_of_quarter, 'days') + 1;
    
    let currentMonth = null;
    for (let i = 0; i < days_in_quarter; i++) {
        let currentDate = start_of_quarter.clone().add(i, 'days');
        
        // Month change detection
        if (currentDate.month() !== currentMonth) {
            currentMonth = currentDate.month();
            // Close previous month callout (except first iteration)
            if (i > 0) tR += `> \n`; 
            
            // Start new month callout with correct season
            let season = monthToSeason(currentDate.month());
            tR += `> > [!${season}]- ${currentDate.format('MMMM YYYY')}\n`;
            tR += `> > \n`; // Start callout content
        }
        
        // Add image (indented under month callout)
        tR += `> > ![[${currentDate.format("YYYY-MM-DD[]#YYYY-MM-DD")}]]\n`;
    }
    tR += `\n`; // Space between quarters
}
%>

> [! highlight]- Stand-out Days
> ``` dataview
TABLE aliases
WHERE aliases != null
AND length(aliases) >= 1
AND date >= date(<% moment(tp.file.title,).startOf('year').add(0, 'days').format("YYYY-MM-DD") %>)
AND date <= date(<% moment(tp.file.title, 'YYYY').startOf('year').add(1,'years').add(-1, 'days').format("YYYY-MM-DD") %>)

> [! highlight]- Highlights!
> ```dataview
TASK
FROM ""
WHERE contains(text, "#log/highlight")
AND date >= date(<% moment(tp.file.title,).startOf('year').add(0, 'days').format("YYYY-MM-DD") %>)
AND date <= date(<% moment(tp.file.title, 'YYYY').startOf('year').add(1,'years').add(-1, 'days').format("YYYY-MM-DD") %>)
GROUP BY file.name AS filename
SORT filename ASC

> [! Calendar]- Monthly Reviews
> ```dataview
> TASK
> WHERE contains(text, "#log/month-review")
AND date >= date(<% moment(tp.file.title,).startOf('year').add(0, 'days').format("YYYY-MM-DD") %>)
AND date <= date(<% moment(tp.file.title, 'YYYY').startOf('year').add(1,'years').add(-1, 'days').format("YYYY-MM-DD") %>)
GROUP BY file.name AS filename
SORT filename ASC

---
- [ ] #log/year-review


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

- [.] **Soul**: 5 #log/yearly-soul 
	- Things to do to improve 'Soul'

- [.] **Career/work**: 5 #log/yearly-career
	- Things to do to improve 'Career'

- [.] **Relationships**: 5 #log/yearly-relationships 
	- Things to do to improve 'Relationships'

- [.] **Health**: 5 #log/yearly-health 
	- Things to do to improve 'Health'

- [.] **Personal Growth**: 5 #log/yearly-personal-growth 
	- Things to do to improve 'Personal Growth'

- [.] **Fun**: 5 #log/yearly-fun 
	- Things to do to improve 'Soul'

- [.] **Social**: 5 #log/yearly-social 
	- Things to do to improve 'Social'

- [.] **Finance**: 5 #log/yearly-finance 
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
    initMonth: <% moment(tp.file.title, 'YYYY').format('YYYY-MM') %>
    circleColorByValue: true
    todayRingColor: white
    selectedRingColor: white
```

#### Averages

```tracker
searchType: frontmatter 
searchTarget: log-day-rating, log-sleep-ratings, log-sleep-hours, log-healthy-food-rating 
datasetName: log-day-rating, log-sleep-ratings, log-sleep-hours, log-healthy-food-rating 
startDate: <% moment(tp.file.title, 'YYYY').startOf('year').add(0, 'days').format("YYYY-MM-DD") %>
endDate: <% moment(tp.file.title, 'YYYY').startOf('year').add(3, 'year').add(-1, 'days').format("YYYY-MM-DD") %>
summary: 
	template: 
		" - Average Day Rating:  {{average()}} 
		\n - Average sleep rating: {{average(dataset(1))}} 
		\n - Average sleep hours: {{average(dataset(2))}} 
		\n - Average Healthy Food Rating: {{average(dataset(3))}}"
```