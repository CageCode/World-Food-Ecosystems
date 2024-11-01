# Step 3: Extracting information from GEE

Let us see where the measurements of the salinity are taken on earth. To achieve that, we need to load in the points data. This data is located in the .zip file that you have downloaded and probably contains extensions that you are not familiar with, but which allow GEE (and other GIS-software) to process the data. In order to get the data in GEE, perform the following steps:

> • Click on the *Assets* tab in the top-left corner of Google Earth Engine <br />
> • Click on the red box called *New* <br />
> • Select *Shape files* under Table Upload <br />
> • Press on *Select* under Source Files <br />
> • Select the five *lakes* files (the .cpg, .dbf, .prj, .shp, .shx -files), you can select multiple files by clicking each of them while holding the control-key (cntrl on keyboard) at the same time <br />
> • Click on *Upload*, the points will be uploaded to GEE, this might take a while <br />
> ... <br />
> • Now click on the *Tasks* tab in the top-**right** corner of GEE <br />
> • When it is loaded, you will see it the Point data is in the ingest-table, open it <br />
> • Scroll down, click on *View Asset* and press *Import* <br />
> • Good job! The point data is now in GEE at the top of your script <br />

Rename the variable by clicking on the name *table* and give it a logical name (we will use 'lakes' in the script). Now we can visualize the layer - the point data - to the map with the following code: 

```javascript
Map.addLayer(lakes,{'color':'purple','opacity':0.5}, "lakes");
```

> 🔍 **Review 3**. In which country are most measurements taken?

<br />
<details>
<summary>Answer Review 3.</summary>
Most of the data is located within the United States of America.
</details>

***

<br />

### Visualizing data

As we have defined earlier, we want to see if the salinity is related to precipitation and temperature. But this time we are using the BIO Variables V1 data for precipitation and temperature. The following code will load the Bioclim dataset and visualize the precipitation.

```javascript
// load the image collection of WorldCLIM
var bioclim = ee.Image("WORLDCLIM/V1/BIO")
print(bioclim);

// from the image collection, select the band containing the deficit information.
var prec = bioclim.select('bio12');
print(prec); 
var precVIS = {
  min: 0,
  max: 3000.0,
  palette: ['red', 'green', "blue"],
};
// plot the mean deficit map:
Map.addLayer(prec, precVIS, 'precbioclim');
```

<br />

We also want to load en visualize the temperature data, copy and change the code above in order to retrieve and plot the temperature data.

> 🔍 **Review 4**. Which band contains the temperature data in the WorldCLIM/BIO dataset?

<br />
<details>
<summary>Answer Review 4.</summary>
Search for the WorldCLIM/bio dataset in GEE and click on bands. We want to use the annual temperature. Make sure you don't forget to check the scale.
</details>

<br />

After you loaded the temperature data, let's extract the mean water deficit for the first epoch, and the biome classification, this for each point. In the previous practical we have extracted data to a transect, which is a line. The following code will extract it for the point data: Lakes. We then save the data to a csv (comma-seperated value file) and upload it to Google Drive, from where we can download it and analyze it with Rstudio.

```javascript
var lakes_temp = temp.reduceRegions(lakes, ee.Reducer.mean( ),927);
// the 927 is the resolution of the layer of terraclimate (see also in the catalogue)

Export.table.toDrive({
  collection: lakes_temp,
  description:'lakes_temp',
  fileFormat: 'CSV'
}); 
```

<br />

You will see that the csv-files are now ready to be uploaded to your Google Drive when you press on the *tasks* tab in the top-right corner of GEE. Press RUN and then RUN again to get the data in your Google Drive account, this might take a while. When it is ready, open your google drive and find the data there, did it work?
Now perform the same steps for the precipitation data. Copy the code above and change it in order to process the precipitation data, upload that one to Google Drive as well. Last but not least, use the following code to 

```javascript
// and finally we extract the biomes
var biomes = ee.Image("OpenLandMap/PNV/PNV_BIOME-TYPE_BIOME00K_C/v01")

var lakes_biomes = biomes.reduceRegions(lakes, ee.Reducer.median( ),1000); // why median here?
Export.table.toDrive({
  collection: lakes_biomes,
  description:'lakes_biomes',
  fileFormat: 'CSV'
});
```

<br />

> 🔍 **Review 4**. Why do we use median in the reduce function to extract the biomes?

<br />
<details>
<summary>Answer Review 4.</summary>
Because the biome map consists of classes and do not represent a continuous scale such as temperature or precipitation.
</details>

<br />

### You now have three csv files in your google drive: two containing the climate information on temperature and precipitation and one containing information on the biomes, all can be imported in R

If you uploaded all the data to your Google Drive and downloaded it from there to your laptop, you can continue. We now go back to Rstudio to do the [data analysis](Analysis.html)

<br />


<nav>
  <ul>
    <li><a href="intro.html">Step 1: Problem description</a></li>
    <li><a href="API.html">Step 2: Exploring</a></li>
    <li><strong>Step 3: Mapping</strong></li>
    <li><a href="Analysis.html">Step 4: Data analysis</a></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>
