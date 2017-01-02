---
date: 2015-01-02T22:00:00Z
tags: ["Data Visualization", "D3.JS"]
title: Bias in Data Visualization
---

Data is everywhere. It's being consumed by every smart phone whenever we receive e-mails and notifications, but we're also producing it faster than ever. Every Facebook post, every Instagram picture, and every geotagged location produces even more data, but what does all of this information mean? As part of a commitment to teach myself something new every month, I decided to jump into the world of data visualization.

### Data Visualization

Data Visualization is an incredibly interesting field to me because it combines a lot of different fields and skills. I used Ben Fry's Ph.D disseration on [Computational Information Design](http://benfry.com/phd/dissertation-110323c.pdf) to provide me with some background knowledge.  In it, he describes data visualization as an intersection of:

* Computer Science
* Mathematics, Statistics, and Data Mining
* Graphic Design
* Information Visualization and Human Computer Interaction

Computer science is used to acquire (often large unstructured sets of) data, parse it and transform it into something useable. From there, it can be filtered based on specific data points and algorithms can be applied to discern patterns and provide mathematical or statistical context.  At that point, the raw data is transformed into a simple representation that is understandable by humans, such as a bar graph or a pie chart. This is where graphic design and usability becomes especially important, as a good visualization needs to tell a story or answer an important question. One complication of data visualization is in how the story is told. There is always an amount of subjectivity in every visualization because you need to exclude some data to make the rest of it understandable. Is the excluded data truly irrelevant or does it obscure a trend? That's what I'm going to explore.

### Acquiring the Data

Before I started this project, I knew that I was going to use it as an excuse to learn [D3.js](http://d3js.org/) and how to dynamically generate [SVG](http://en.wikipedia.org/wiki/Scalable_Vector_Graphics), but I immediately found my first barrier. *What am I going to visualize? Where am I going to get the data?*

That's an important question. My first thought was using [US Census data](http://www.census.gov/data/data-tools.html), but there were already so many visualizations on that data. I was checking [Data.gov](http://www.data.gov/) for publically available government data when I got the idea to do a visualization on government spending, specifically military spending. In recent years, there has been a lot of talk about the size of the US defense budget, which has led to [decreased military spending](http://www.defenseone.com/management/2014/03/obama-requests-smaller-500-billion-defense-budget/79813/), but I'm not here to talk about politics, so let's jump straight into the data.

The first thing that I noticed about [America's staggering defense budget](http://www.washingtonpost.com/blogs/wonkblog/wp/2013/01/07/everything-chuck-hagel-needs-to-know-about-the-defense-budget-in-charts/) is this infographic:

<amp-img layout="responsive" width="375" height="200"
  src="http://img.washingtonpost.com/blogs/wonkblog/files/2013/01/4A8078449E794DFB8CC33ADD00A6F1AF.gif">

This image shows that in 2011, the United States spent more on its defense budget than the **next 13 countries combined**. Whether it was intentional or not, this chart paints the United States of America as the premier warmonger in the entire world. Sure, it tells a story, but is it an honest one? Fortunately, the article points to the [Stockholm International Peace Research Institute](http://www.sipri.org/) as the source. Once I acquired the data set from their website, I noticed that by presenting the defense budget as the only data point, it removed the correlation between military spending and the relative wealth of each country. Although the United States defense budget *is* staggering, I wanted to know what it looks like in relation to our [gross domestic product](http://en.wikipedia.org/wiki/Gross_domestic_product).

In the spirit of open data, GDP information can be obtained directly from the:

* [United Nations Statistics Division](http://unstats.un.org/unsd/snaama/dnltransfer.asp?fID=2)
* [World Bank](http://data.worldbank.org/indicator/NY.GDP.MKTP.CD)
* [International Monetary Fund](http://www.imf.org/external/pubs/ft/weo/2014/02/weodata/weorept.aspx?sy=2012&ey=2019&scsm=1&ssd=1&sort=country&ds=.&br=1&pr1.x=41&pr1.y=3&c=512%2C668%2C914%2C672%2C612%2C946%2C614%2C137%2C311%2C962%2C213%2C674%2C911%2C676%2C193%2C548%2C122%2C556%2C912%2C678%2C313%2C181%2C419%2C867%2C513%2C682%2C316%2C684%2C913%2C273%2C124%2C868%2C339%2C921%2C638%2C948%2C514%2C943%2C218%2C686%2C963%2C688%2C616%2C518%2C223%2C728%2C516%2C558%2C918%2C138%2C748%2C196%2C618%2C278%2C624%2C692%2C522%2C694%2C622%2C142%2C156%2C449%2C626%2C564%2C628%2C565%2C228%2C283%2C924%2C853%2C233%2C288%2C632%2C293%2C636%2C566%2C634%2C964%2C238%2C182%2C662%2C453%2C960%2C968%2C423%2C922%2C935%2C714%2C128%2C862%2C611%2C135%2C321%2C716%2C243%2C456%2C248%2C722%2C469%2C942%2C253%2C718%2C642%2C724%2C643%2C576%2C939%2C936%2C644%2C961%2C819%2C813%2C172%2C199%2C132%2C733%2C646%2C184%2C648%2C524%2C915%2C361%2C134%2C362%2C652%2C364%2C174%2C732%2C328%2C366%2C258%2C734%2C656%2C144%2C654%2C146%2C336%2C463%2C263%2C528%2C268%2C923%2C532%2C738%2C944%2C578%2C176%2C537%2C534%2C742%2C536%2C866%2C429%2C369%2C433%2C744%2C178%2C186%2C436%2C925%2C136%2C869%2C343%2C746%2C158%2C926%2C439%2C466%2C916%2C112%2C664%2C111%2C826%2C298%2C542%2C927%2C967%2C846%2C443%2C299%2C917%2C582%2C544%2C474%2C941%2C754%2C446%2C698%2C666&s=NGDPD&grp=0&a=)

### Visualizing the Data

Once I had the data, this part was actually pretty easy, despite never using D3 before. Once I sat down to learn D3, I was a bit overwhelmed by the number of visualization options that I could do. If you're curious, this [D3 gallery](https://github.com/mbostock/d3/wiki/Gallery) provides numerous examples of what can be done with the library.  I originally wanted to do something cartographic using publically available GeoJSON data, but for my first visualization project, I decided to stick to a bar chart:

> [Global Military Spending for 2013](http://www.uncompiled.org/datavis-militarization/military-spending.html)

Using the SIPRI data set for 2013, I present the top 15 countries with the highest military spending, which clearly shows the United States as the leader (spending $640B on the military budget). However, my visualization also factors in the nominal GDP of each country and then sorts their military budgets as a percentage of GDP. After this calculation is performed, the information is dynamically resorted and shows that the United States is no longer the biggest military spender. With the addition of this information, the United States no longer appears to be as warmongering as the earlier chart.

While visualizations are never purely objective, I'd like to think that this picture paints a more honest look at the data.

*If you have comments on my visualization or find discrepancies with the data, please let me know so that I can fix it.*
