---
title: "Tracking the monsoon, live"
date: 2026-07-06
description: "Season-to-date rainfall across five Indian cities since 1 June, pulled live from Open-Meteo and updated every 15 minutes."
tags: ["data", "weather", "india"]
---

India's southwest monsoon arrives in Kerala around 1 June and works its way north over the following weeks. The tracker below shows how much rain has actually fallen in five cities since the season opened. Data comes from the [Open-Meteo](https://open-meteo.com/) forecast API -- free, keyless, updated every few hours from model output and station observations.

<div class="rt-wrap">
  <div class="rt-head">
    <span class="rt-title">2026 Monsoon Season</span>
    <span class="rt-sub">Season-to-date rainfall from 1 June · refreshes every 15 min</span>
  </div>
  <div id="rt-body" class="rt-body">
    <div class="rt-loading">Fetching rainfall data&hellip;</div>
  </div>
  <div class="rt-foot">Last updated: <span id="rt-ts">--</span></div>
</div>

<style>
.rt-wrap{border:1px solid #a2a9b1;margin:1.6em 0;font-size:.9rem;background:#fff}
.rt-head{background:#f8f9fa;border-bottom:1px solid #a2a9b1;padding:9px 14px}
.rt-title{font-family:Georgia,serif;font-size:1rem;display:block}
.rt-sub{font-size:.78rem;color:#54595d;display:block;margin-top:2px}
.rt-loading{padding:14px;color:#54595d;font-size:.85rem}
.rt-row{display:grid;grid-template-columns:82px 1fr 72px;align-items:center;gap:10px;padding:9px 14px;border-bottom:1px solid #c8ccd1}
.rt-row:last-child{border-bottom:none}
.rt-city{font-weight:600;color:#202122}
.rt-track{height:9px;background:#f8f9fa;border:1px solid #c8ccd1;border-radius:1px;overflow:hidden;min-width:0}
.rt-fill{height:100%;background:#4a8fc7;border-radius:1px;transition:width .5s ease}
@media(prefers-reduced-motion:reduce){.rt-fill{transition:none}}
.rt-mm{text-align:right;font-family:ui-monospace,monospace;font-size:.82rem;color:#202122;white-space:nowrap}
.rt-nodata{color:#54595d;font-style:italic}
.rt-foot{background:#f8f9fa;border-top:1px solid #c8ccd1;padding:6px 14px;font-size:.76rem;color:#54595d}
@media(max-width:480px){.rt-row{grid-template-columns:76px 1fr 64px;gap:8px;padding:8px 10px}.rt-head,.rt-foot{padding-left:10px;padding-right:10px}}
</style>

<script>
(function () {
  var CITIES = [
    { name: 'Mumbai',    lat: 19.0760, lon: 72.8777 },
    { name: 'Pune',      lat: 18.5204, lon: 73.8567 },
    { name: 'Delhi',     lat: 28.6139, lon: 77.2090 },
    { name: 'Bengaluru', lat: 12.9716, lon: 77.5946 },
    { name: 'Chennai',   lat: 13.0827, lon: 80.2707 }
  ];

  function nowIST() {
    var now = new Date();
    return new Date(now.getTime() + (5.5 * 3600000) - (now.getTimezoneOffset() * 60000));
  }

  function seasonStart() {
    var d = nowIST();
    var year = d.getUTCFullYear();
    return d.getUTCMonth() < 5 ? (year - 1) + '-06-01' : year + '-06-01';
  }

  function todayIST() {
    var d = nowIST();
    var m = String(d.getUTCMonth() + 1).padStart(2, '0');
    var day = String(d.getUTCDate()).padStart(2, '0');
    return d.getUTCFullYear() + '-' + m + '-' + day;
  }

  function fetchCity(city) {
    var url = 'https://api.open-meteo.com/v1/forecast'
      + '?latitude=' + city.lat + '&longitude=' + city.lon
      + '&daily=precipitation_sum'
      + '&start_date=' + seasonStart() + '&end_date=' + todayIST()
      + '&timezone=Asia%2FKolkata';
    return fetch(url)
      .then(function (r) {
        if (!r.ok) throw new Error('HTTP ' + r.status);
        return r.json();
      })
      .then(function (json) {
        var vals = (json.daily && json.daily.precipitation_sum) || [];
        return Math.round(vals.reduce(function (s, v) { return s + (v || 0); }, 0));
      });
  }

  function fmtIST() {
    return new Date().toLocaleString('en-IN', {
      timeZone: 'Asia/Kolkata',
      day: 'numeric', month: 'short', year: 'numeric',
      hour: '2-digit', minute: '2-digit', hour12: false
    }) + ' IST';
  }

  function render(results) {
    var body = document.getElementById('rt-body');
    var ts   = document.getElementById('rt-ts');
    if (!body) return;

    var maxMm = 0;
    CITIES.forEach(function (c) {
      var r = results[c.name];
      if (r && !r.err && r.mm > maxMm) maxMm = r.mm;
    });

    body.innerHTML = CITIES.map(function (c) {
      var r = results[c.name];
      if (!r || r.err) {
        return '<div class="rt-row">'
          + '<span class="rt-city">' + c.name + '</span>'
          + '<div class="rt-track"></div>'
          + '<span class="rt-mm rt-nodata">no data</span>'
          + '</div>';
      }
      var pct = maxMm > 0 ? Math.round(r.mm / maxMm * 100) : 0;
      return '<div class="rt-row">'
        + '<span class="rt-city">' + c.name + '</span>'
        + '<div class="rt-track"><div class="rt-fill" style="width:' + pct + '%"></div></div>'
        + '<span class="rt-mm">' + r.mm + ' mm</span>'
        + '</div>';
    }).join('');

    if (ts) ts.textContent = fmtIST();
  }

  function fetchAll() {
    var results = {};
    var pending = CITIES.length;

    CITIES.forEach(function (city) {
      fetchCity(city)
        .then(function (mm) { results[city.name] = { mm: mm }; })
        .catch(function ()  { results[city.name] = { err: true }; })
        .finally(function () {
          pending -= 1;
          if (pending === 0) render(results);
        });
    });
  }

  fetchAll();
  setInterval(fetchAll, 15 * 60 * 1000);
}());
</script>

Each bar is scaled relative to the highest city total this season, so the widest bar is always the wettest city. Mumbai's coastal location typically puts it well ahead by mid-season; Delhi's numbers climb sharply once the monsoon crosses Rajasthan.
