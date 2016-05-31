---
date: 2015-01-09T17:00:00Z
tags: ["D3.js", "Data Visualization"]
title: Geovisualization with D3.js and DataMaps
---

Before I started working on this data visualization project, I *really* wanted to make a map. I didn't have any data to visualize yet, but I was already thinking about all the things that I could do with a map. **That is probably not the best way to start a visualization project.** Maps are great, but only when geography provides a meaningful way to present the data. Fortunately, I found an interesting talk from [Noah Iliinsky](https://www.youtube.com/watch?v=c6eUyxHGmJ4&list=LLvvC8JVsbLNhi_zFjFeDzyw&index=2) that slapped some sense into me.

After building my [first visualization](http://www.uncompiled.org/datavis-militarization/military-spending.html), I felt more comfortable with D3.js and began exploring different ways to present that data. My original visualization only displays the 15 countries with the highest amount of military spending, but another way to present the same data is using a choropleth (which is a fancy Greek word for a color-coded map).

### How Do You Make a Choropleth?

Mike Bostock has a really great tutorial on [how to make a map](http://bost.ocks.org/mike/map/) from scratch, so I won't go over that. Instead, I built my map on top of [DataMaps](http://datamaps.github.io/), which provides a D3.js map encapsulated in a single Javascript file.

To use DataMaps, you simply include D3.js, topojson.js, and datamaps.js in your HTML:

{{< highlight html >}}
<script src="/js/d3.min.js"></script>
<script src="/js/topojson.min.js"></script>
<script src="/js/datamaps.world.min.js"></script>

<div id="container">
  <!-- the map will appear here -->
</div>

<script>
  var map = new Datamap({
    element: document.getElementById('container')
  });
</script>
{{< / highlight >}}

In this example, the Datamap is bound to the DIV element called 'container'. Now that you have a map, you need to bind some data to make it interesting. In DataMaps, countries are represented by their [ISO 3166-1 alpha-3](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) country code. Since my data source (SIPRI) did not use this format, I wrote a quick script to convert the Country names to the 3-character code officially assigned by the ISO 3166 Maintenance Agency. Once I had the data in the desired format, I saved it as a Tab-Separated Values (TSV) file and imported it into my visualization:

{{< highlight javascript >}}
d3.tsv("data/milexp-global.tsv", function (error, data) {
  data.forEach(function(d) {
    jsonMilExp[ d.country ] = {
      fillKey: quantize(d.percentGDP),
      percentGDP: d.percentGDP
    };
    map.updateChoropleth(jsonMilExp);
  });
});
{{< / highlight >}}

This function reads the input data and populates an array of objects that contain the fill color (fillKey) and the country's military spending as a percentage of their GDP. The fillKey value is generated by the d3.scale.quantize function, which maps the input (domain) into equal buckets.

{{< highlight javascript >}}
var quantize = d3.scale.quantize()
  .domain([0, 11.3])
  .range(d3.range(7).map(function(i) { return "q" + i; }));
{{< / highlight >}}

For the quantize function, I chose to map the domain to the values "q0" through "q6", which represent variables used for the color scale. You could map the domain directly to RGB or hexidecimal color values, but this allows me to reference the colors in other places, such as when I generate the legend.

### Voila! Here's a Map!

> [2013 Global Military Expenditure as Percentage of GDP](http://www.uncompiled.org/datavis-militarization/military-spending-gdp.html)

A darker color on the map means that the country spends a larger percentage of their GDP on the military.

That's pretty much all there is to working with maps in D3.js. Here's a recap:

* **Step 1: Draw the map.** You can draw the map directly from GeoJSON/TopoJSON geography data or use a package like DataMaps.
* **Step 2: Bind the data.** Import your data source (CSV, TSV, JSON).
* **Step 3: Present the data.** Choose how you want to present the data. Choropleths are popular, but you may decide to overlay other elements, such as circles or text.