# Step 3: Extracting information from GEE

Let us see where the measurements of the salinity are taken on earth. To achieve that, we need to load in the points data. This data is located in the .zip file that you have downloaded and probably contains extensions that you are not familiar with, but which allow GEE (and other GIS-software) to process the data. In order to get the data in GEE, perform the following steps:

> Click on the *Assets* tab in the top-left corner of Google Earth Engine <br />
> Click on the red box called *New* <br />
> Select *Shape files* under Table Upload <br />
> Press on *Select* under Source Files <br />
> Select the five *lakes* files (the .cpg, .dbf, .prj, .shp, .shx -files), you can select multiple files by clicking each of them while holding the control-key (cntrl on keyboard) at the same time <br />
> Click on *Upload*, the points will be uploaded to GEE, this might take a while <br />
> ... <br />
> Now click on the *Tasks* tab in the top-**right** corner of GEE <br />
> When it is loaded, you will see it the Point data is in the ingest-table, open it <br />
> Scroll down, click on *View Asset* and press *Import* <br />
> Good job! The point data is now in GEE at the top of your script <br />

Rename the variable by clicking on the name *table* and give it a logical name (we will use 'lakes' in the script). Now we can visualize the layer - the point data - to the map with the following code: 

```javascript
Map.addLayer(lakes,{'color':'purple','opacity':0.5}, "lakes");
```

<br />

Now, let's extract the mean water deficit for the first epoch, and the biome classification, this for each point. We have done this before, so below are some clues for the code which - based on your work last week - you should be able to adjust to make it work. 

```javascript
// load the image collection of TERRACLIMATE, and filter the correct dates
var dataset = ee.ImageCollection('IDAHO_EPSCOR/TERRACLIMATE')
                  .filter(ee.Filter.date('1980-01-01', '1990-01-01'));
// from the image collection, select the band containing the deficit information.
var deficit = ?.select(?);
print(deficit); //this is still an image collection, so we'll need to reduce it and take the mean (so that we have one mean layer instead of all the monthly layers in the collection
//let's calculate the monthly mean deficit over the first epoch
var meandeficit = ?.mean();
var deficitVis = {
  min: 0,
  max: 1500.0,
  palette: ['blue', "green", 'red'],
};
// plot the mean deficit map:
Map.addLayer(?, ?, 'deficit');

/// now we extract the values for deficit for each of the points. This is very similar to what we did in practical 5

var lakes_deficit80s = meandeficit.reduceRegions(lakes, ee.Reducer.mean( ),4638.3); // the 4638 is the resolution of the layer of terraclimate (see also in the catalogue)
Export.table.toDrive({
  collection: lakes_deficit80s,
  description:'lakes_deficit80s',
  fileFormat: 'CSV'
}); //we write the elevation values per camera location to a csv --> download this csv file

```
>Now, do the same for the Biome map: extract the values of the biome class for each point. 

This is similar to the water deficit, with the exception that the OpenLand Biome map is already an image and that you don't have to take the mean, but the mode (the most common pixel value) and that the resolution of the openland biome map is 1000m. Also export this csv file. 

### You now have two csv files in your google drive: one containing the information on the deficit in the 80s, one containing information on the biomes can be imported in R

We now go back to R to do the [data analysis](https://liesjacobs.github.io/worldfoodecosystems2023/practical3/Analysis.html)



<nav>
  <ul>
    <li><a href="intro.html">Step 1: Problem description</a></li>
    <li><a href="API.html">Practical 6: Exercise 1</a></li>
    <li><strong>Practical 6: Exercise 2</strong></li>
    <li><a href="Analysis.html">Practical 6: Exercise 3</a></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>
