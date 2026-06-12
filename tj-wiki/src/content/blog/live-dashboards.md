---
title: "Embedding live dashboards in posts"
date: 2026-06-12
description: "How to drop interactive and real-time Plotly charts into any post with a single HTML block."
tags: ["data", "meta"]
---

Posts on this site can host interactive charts using [Plotly.js](https://plotly.com/javascript/) loaded from a CDN. No extra build config, no new dependencies — drop an HTML block into any markdown file and push. Two patterns below: a static interactive chart, and one that streams data in real time.

## Category breakdown

Hover over bars for exact values. Click a legend entry to toggle that series.

<div id="chart-bar" style="width:100%;height:360px;"></div>

## Live metric feed

Simulates a real-time order-rate metric updating every second with a random walk. To wire this up to actual data, swap the `setInterval` body for a `fetch()` call to any JSON API.

<div id="chart-live" style="width:100%;height:360px;margin-top:1rem;"></div>

<script>
(function () {
  var s = document.createElement('script');
  s.src = 'https://cdn.jsdelivr.net/npm/plotly.js-dist@2/plotly.min.js';
  s.onload = function () {

    /* ── Bar chart ── */
    var cats = ['Electronics', 'Fashion', 'Home & Kitchen', 'Beauty', 'Sports'];
    Plotly.newPlot('chart-bar', [
      { name: 'Q1', type: 'bar', x: cats, y: [4200, 6800, 3100, 2900, 1800],
        marker: { color: '#3366cc' } },
      { name: 'Q2', type: 'bar', x: cats, y: [5100, 7200, 3600, 3400, 2300],
        marker: { color: '#e05c2a' } }
    ], {
      title: { text: 'Orders by category  (Q1 vs Q2)', font: { size: 15 } },
      yaxis: { title: 'Orders (thousands)' },
      barmode: 'group',
      margin: { t: 48, r: 16, b: 80, l: 60 },
      font: { family: 'Georgia, serif', size: 13 },
      plot_bgcolor: '#fafafa',
      paper_bgcolor: '#fafafa'
    }, { responsive: true, displayModeBar: false });

    /* ── Live chart ── */
    var N = 60;
    var xs = Array.from({ length: N }, function (_, i) { return i - N + 1; });
    var ys = Array.from({ length: N }, function () { return 420 + Math.random() * 60; });

    var layout = {
      title: { text: 'Order rate  (live)', font: { size: 15 } },
      xaxis: { title: 'Seconds ago', range: [-(N - 1), 0] },
      yaxis: { title: 'Orders / min', range: [300, 580] },
      margin: { t: 48, r: 16, b: 60, l: 60 },
      font: { family: 'Georgia, serif', size: 13 },
      plot_bgcolor: '#fafafa',
      paper_bgcolor: '#fafafa'
    };

    Plotly.newPlot('chart-live',
      [{ x: xs, y: ys, type: 'scatter', mode: 'lines',
         line: { color: '#0f9d58', width: 2 }, name: 'Orders / min' }],
      layout,
      { responsive: true, displayModeBar: false }
    );

    setInterval(function () {
      var last = ys[ys.length - 1];
      var next = Math.max(310, Math.min(570, last + (Math.random() - 0.48) * 18));
      ys.push(next);
      ys.shift();
      Plotly.react('chart-live',
        [{ x: xs, y: ys, type: 'scatter', mode: 'lines',
           line: { color: '#0f9d58', width: 2 }, name: 'Orders / min' }],
        layout
      );
    }, 1000);

  };
  document.head.appendChild(s);
}());
</script>

## How it works

Every `.md` file on this site supports raw HTML blocks. Astro passes them through to the browser unchanged, so a CDN `<script>` tag runs exactly as it would on any static page.

The pattern is always the same:

1. Add a `<div id="my-chart">` where you want the chart to appear.
2. At the bottom of the post, load Plotly and call `Plotly.newPlot`.
3. For live data, use `setInterval` (simulation) or `fetch` (real API).

## Connecting to a real API

Replace the `setInterval` body with a fetch call:

```javascript
setInterval(function () {
  fetch('https://your-api.example.com/metric/latest')
    .then(function (r) { return r.json(); })
    .then(function (d) {
      ys.push(d.value);
      ys.shift();
      Plotly.react('chart-live', [{ x: xs, y: ys, ... }], layout);
    });
}, 5000);
```

Any endpoint that returns JSON works. Public APIs (weather, finance, transport) need no auth and are a quick way to get genuinely live data on a static site.
