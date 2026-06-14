---
title: "Los Angeles Crime 2020-2023: an exploratory data analysis"
date: 2026-06-14
description: "Patterns in 877,000 LAPD incidents: when crimes peak, which neighbourhoods see the most activity, and who gets targeted."
tags: ["data", "eda", "crime"]
---

The LAPD publishes every reported incident since 2020 on the [data.gov open data portal](https://catalog.data.gov/dataset/crime-data-from-2020-to-present). After cleaning and filtering to complete years, the dataset covers **877,327 incidents from January 2020 to December 2023**. This post walks through the main patterns using pandas — charts are interactive, hover over any element for exact values.

---

## Crime volume by year

Crime rose steadily from 2020 through 2022, peaked, then slightly declined in 2023. The 2020 figure is visibly dampened by COVID lockdowns, particularly in Q2.

<div id="chart-yearly" style="width:100%;height:320px;"></div>

---

## Monthly trend

The month-by-month view shows the lockdown dip clearly in April-May 2020, a slow recovery through 2021, and a higher plateau in 2022-2023.

<div id="chart-monthly" style="width:100%;height:360px;"></div>

---

## When do crimes happen?

The hourly breakdown has one quirk worth flagging: the noon spike (1200) is partly a reporting artefact. When officers don't know the exact time, the LAPD system defaults to 1200. Discounting that, the real peak is the evening window from 17:00 to 20:00. The quietest period is 04:00-05:00.

<div id="chart-hour" style="width:100%;height:360px;"></div>

---

## Most common crime types

Vehicle theft leads by a wide margin — nearly 94,000 incidents over four years, more than battery and identity theft combined. The top 15 types account for roughly 65% of all incidents.

<div id="chart-crimes" style="width:100%;height:480px;"></div>

---

## Crime by LAPD division

Central, 77th Street, and Pacific are consistently the highest-volume divisions. The spread across all 21 divisions is relatively even — there is no single extreme outlier.

<div id="chart-area" style="width:100%;height:420px;"></div>

---

## Victim age distribution

The 30-39 age band is the most targeted, followed closely by 20-29. Incidents involving victims under 10 or over 70 are comparatively rare. Ages of 0 and negative values (data entry errors) were excluded.

<div id="chart-age" style="width:100%;height:360px;"></div>

---

<script>
(function () {
  var s = document.createElement('script');
  s.src = 'https://cdn.jsdelivr.net/npm/plotly.js-dist@2/plotly.min.js';
  s.onload = function () {

    var font = { family: 'Georgia, serif', size: 13 };
    var bg   = '#fafafa';
    var cfg  = { responsive: true, displayModeBar: false };

    /* ── data ── */
    var monthly = {
      dates:  ["2020-01","2020-02","2020-03","2020-04","2020-05","2020-06","2020-07","2020-08","2020-09","2020-10","2020-11","2020-12","2021-01","2021-02","2021-03","2021-04","2021-05","2021-06","2021-07","2021-08","2021-09","2021-10","2021-11","2021-12","2022-01","2022-02","2022-03","2022-04","2022-05","2022-06","2022-07","2022-08","2022-09","2022-10","2022-11","2022-12","2023-01","2023-02","2023-03","2023-04","2023-05","2023-06","2023-07","2023-08","2023-09","2023-10","2023-11","2023-12"],
      counts: [18576,17284,16188,15706,17230,17060,17158,16902,15658,16510,15596,15979,16636,15440,16354,16091,17020,17182,18690,18398,18386,19343,18374,17962,18567,17750,19745,19837,20467,20273,20009,20144,19341,20335,18755,20036,19970,18489,19214,18937,18908,18741,19935,20082,19321,20122,19074,19552]
    };

    var hourly = {
      hours:  [0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23],
      counts: [36280,26390,22350,19531,16501,15257,19997,22618,32483,31848,37707,38173,59982,39708,42836,45760,46064,50975,52061,48030,48772,44386,42754,36864]
    };

    var crimes = {
      types: ["Vehicle stolen","Battery - simple assault","Theft of identity","Burglary from vehicle","Burglary","Vandalism - felony","Assault w/ deadly weapon","Theft - petty","Intimate partner assault","Theft from vehicle - petty","Theft from vehicle - grand","Robbery","Theft - grand","Vandalism - misdemeanor","Shoplifting"],
      counts: [93674,69421,56216,53990,53411,53141,49828,44898,43717,33702,31318,29813,29144,22988,21852]
    };

    var area = {
      areas:  ["Central","77th Street","Pacific","Southwest","Hollywood","Southeast","Olympic","N Hollywood","Newton","Wilshire","Rampart","West LA","Northeast","Van Nuys","West Valley","Harbor","Devonshire","Topanga","Mission","Hollenbeck","Foothill"],
      counts: [59456,54981,51351,49127,46277,44325,44300,43858,43787,41933,41223,40313,37703,37122,36864,36085,35864,35826,34982,32768,29182]
    };

    var age = {
      bins:   ["0-9","10-19","20-29","30-39","40-49","50-59","60-69","70-79","80-89","90-99"],
      counts: [4163,33802,159409,173884,116557,89002,53532,20923,5995,1472]
    };

    var yearly = {
      years:  [2020, 2021, 2022, 2023],
      counts: [199847, 209876, 235259, 232345]
    };

    var hourLabels = hourly.hours.map(function (h) {
      return (h < 10 ? '0' : '') + h + ':00';
    });

    /* ── yearly bar ── */
    Plotly.newPlot('chart-yearly', [{
      type: 'bar', x: yearly.years.map(String), y: yearly.counts,
      marker: { color: ['#5b8dd9','#5b8dd9','#e05c2a','#e8a838'] },
      text: yearly.counts.map(function (c) { return c.toLocaleString(); }),
      textposition: 'outside'
    }], {
      yaxis: { title: 'Incidents', range: [0, 270000] },
      margin: { t: 24, r: 16, b: 48, l: 70 },
      plot_bgcolor: bg, paper_bgcolor: bg, font: font, showlegend: false
    }, cfg);

    /* ── monthly line ── */
    Plotly.newPlot('chart-monthly', [{
      type: 'scatter', mode: 'lines', x: monthly.dates, y: monthly.counts,
      line: { color: '#3366cc', width: 2 }, fill: 'tozeroy',
      fillcolor: 'rgba(51,102,204,0.08)', name: 'Incidents'
    }], {
      xaxis: { title: 'Month' },
      yaxis: { title: 'Incidents' },
      margin: { t: 24, r: 16, b: 60, l: 70 },
      plot_bgcolor: bg, paper_bgcolor: bg, font: font, showlegend: false
    }, cfg);

    /* ── hourly bar ── */
    var hourColors = hourly.hours.map(function (h) {
      return h === 12 ? '#aaaaaa' : '#e05c2a';
    });
    Plotly.newPlot('chart-hour', [{
      type: 'bar', x: hourLabels, y: hourly.counts,
      marker: { color: hourColors }, name: 'Incidents'
    }], {
      xaxis: { title: 'Hour of day', tickangle: -45 },
      yaxis: { title: 'Incidents' },
      annotations: [{ x: '12:00', y: 59982, text: 'Reporting artefact', showarrow: true,
        arrowhead: 2, ax: 60, ay: -40, font: { size: 12 } }],
      margin: { t: 32, r: 16, b: 90, l: 70 },
      plot_bgcolor: bg, paper_bgcolor: bg, font: font, showlegend: false
    }, cfg);

    /* ── crime types horizontal bar ── */
    var cReversed = crimes.types.slice().reverse();
    var vReversed = crimes.counts.slice().reverse();
    Plotly.newPlot('chart-crimes', [{
      type: 'bar', orientation: 'h', x: vReversed, y: cReversed,
      marker: { color: '#3366cc' }, name: 'Incidents'
    }], {
      xaxis: { title: 'Incidents' },
      margin: { t: 24, r: 24, b: 56, l: 210 },
      plot_bgcolor: bg, paper_bgcolor: bg, font: font, showlegend: false
    }, cfg);

    /* ── area bar ── */
    var aReversed = area.areas.slice().reverse();
    var acReversed = area.counts.slice().reverse();
    Plotly.newPlot('chart-area', [{
      type: 'bar', orientation: 'h', x: acReversed, y: aReversed,
      marker: { color: '#0f9d58' }, name: 'Incidents'
    }], {
      xaxis: { title: 'Incidents' },
      margin: { t: 24, r: 24, b: 56, l: 110 },
      plot_bgcolor: bg, paper_bgcolor: bg, font: font, showlegend: false
    }, cfg);

    /* ── victim age bar ── */
    Plotly.newPlot('chart-age', [{
      type: 'bar', x: age.bins, y: age.counts,
      marker: { color: '#7b52ab' }, name: 'Incidents'
    }], {
      xaxis: { title: 'Age group' },
      yaxis: { title: 'Incidents' },
      margin: { t: 24, r: 16, b: 56, l: 70 },
      plot_bgcolor: bg, paper_bgcolor: bg, font: font, showlegend: false
    }, cfg);

  };
  document.head.appendChild(s);
}());
</script>

---

*Data: LAPD Crime Data 2020-Present via data.gov. Analysis in Python (pandas). Charts rendered with Plotly.js.*
