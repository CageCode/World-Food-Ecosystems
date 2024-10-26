## Step 3: Understanding the Gradient: how process influences form

In this exercise we will analyze data from the Earth Engine to gain insight in a real-life question: 
- How can we explain the remarkable gradient in land cover and related agricultural practices along the West-Coast of the USA (see video below)


<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/135052084-665539ec-8de3-4457-8cc4-f67af8c24812.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>




>WARNING: the amount of code below might be overwhelming, but don't worry: it is not the intention to be able to reproduce this from memory. The goal here is to understand what GEE can do and grasp the general working of the code below. 
***

<br />

### Step 3.1: What gradient?


Gradient analysis is a method to examine how certain variables change across a spatial or environmental gradient. All species occur in a characteristic, limited range of habitats; and within their range, they tend to be most abundant around their particular environmental optimum. The composition of biotic communities thus changes along environmental gradients. Gradients do not necessarily have physical reality as continua in either space or time, but sometimes they do. In our case, the gradients that we will observe are visible over a physical distance, running over an East-West transect in Oregon, USA.

Before we start investigating the gradients, it's important to consider the key factors for a geographical analysis. What factors would you consider when performing such an analysis?

> 🔍 **Recap 8**. What would be relevant factors to consider performing a geographical analysis? <br />

<br />
<details>
<summary>Answer Recap 8.</summary>
There are many factors that one can think of, including the spatial extent, spatial resolution, any time components involved, the type of unit for measuring variables, and the cartographic unit used for mapping, et cetera.
</details>
<br />

Since we are performing a *geographical* analysis, the concept of space plays an important role in our. First and foremost, we need to determine **spatial extent** of our variables of interest. For example: If you are analysing sea currents in the North Sea, but you focus on the Wadden Sea, then your spatial extent is off: you have zoomed in too much. On the other hand, if you research nutrient cycles in endemic flowers in the Dutch dunes and you use Europe as spatial extent, you have zoomed out too much.
Similar to this, the **spatial resolution** determines the unit of spatial measurement of the variables. It is the level of detail of the data, which can be in kilometers, meters or even centimeters for example. The dimension of the spatial resolution should be as large or as small as needed to represent the data accurately and show the features of interest. For a raster dataset, the spatial resolution is the dimension of the raster cells (pixels) also called the cell size. The cell size determines how coarse or fine the patterns or features in the raster will appear. The smaller the cell size, the smoother or more detailed the raster will be. However, the greater the number of cells, the longer it will take to process, and it will increase the demand for storage space. If a cell size is too large, information may be lost or subtle patterns may be obscured. For example, if the cell size is larger than the width of a road, the road may not exist within the raster dataset. In the diagram below, you can see how this simple polygon feature will be represented by a raster dataset at various cell sizes.

<div align="center">
  <img src="Spatial_Resolution.jpg" alt="Spatial Resolution" >
  <br />
  <em>Figure 1. Same shape on different spatial    <br />
    resolutions of a raster</em>
</div>

<br />
<br />

> 🔍 **Recap 9**. What would you consider the spatial resolution for a map of flooding risks in the Netherlands? And what would be a fitting resolution for tree classification from satellite imagery?<br />

<br />
<details>
<summary>Answer Recap 9.</summary>
A map of flooding risks in the Netherlands would probably represent the data well in kilometers. Tree classification might vary depending on the species, but since the average tree crown spans several meters, so a tree classification from satellite imagery should probably be in square meters or less.

</details>
<br />

***

### Step 3.2: Geospatial Parameters

The aforementioned factors are all types of geospatial parameters. It is good practice to list the geospatial parameters of interest with their respective units and descriptions that fit the scope of your analysis. This helps to figure out which data is appropriate to use and you'll be sure the analysis is performed accurately. Therefore, everytime you will see an analysis performed in these practicals, we will list the geospatial parameters of interest first. More-over, in these practicals we will typically already provide a completed version of the table you are expected to be able to fill in yourself for your own project, so that you have plenty of examples. The further we go ahead, the more '?' we introduce into the table: to fill them, you'll need to apply the principles you have learned thus far to address these gaps. 

Now we can think about the different parameters - or building blocks if you will - of the analysis, this raises questions such as:
- Which dimensions will we take into account (which processes will we consider)?
- How will we describe these dimensions?
- What spatial scale & extent is appropriate?
- What temporal scale & extent is fitting here?
- Do we have any hypotheses or assumptions about the analysis?

Our working hypothesis of today, is that the strong difference we see in vegetation and agricultural practices (check the video at the top of this page) are related to the gradients in elevation, temperature & precipitation. We will investigate this gradient over a transect in North-West USA. Thus the geospatial parameters that fit the scope of the analysis would be: 

<br />

| Parameter  |  Value |
|---|---|
| Spatial extent | North-West USA |
| Temporal extent | We are performing a static analysis (so not over a period of time), yet keep in mind that the data does come from a certain moment in time, which should span the same timespan |
| Cartographic unit |  Transect (line) |
| Dimensions | We'll consider elevation, temperature and precipitation |
| Dimension description | elevation: the SRTM DEM at 90m cellsize, temperature: long term averages extracted from worldclim at ca. 900m cellsize, precipitation: ? (<- these are possible data sources you can use for the mentioned dimensions, but there are more out there) |
| Temporal resolution | We are looking at landscapes, climate data would be appropriate (>30 year averages) |
| Spatial resolution | We are looking at landscapes, thus a resolution on the order of kilometers would be suitable |
| Hypothesis | The different landcovers on such a relative short transect are due to either a steep temperature gradient, or a steep precipitation gradient, either can be induced by the topographic barrier |


> TIP: If you are looking for data, or data types, you could use the search bar on the top of GEE to find datasets and even examples of code to import or plot these datasets!

***

<br />

### Step 3.3: Drawing the transect

Let's see if we can draw a nice transect. First make a new Script in Google Earth engine, and give it an appropriate name: *Prac1_Gradient_Analysis*. The first time you make a script you'll be asked to enter your username and repository name.

<br />

<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/131994092-5ed947ec-1aa9-4277-bfa1-db014ac2458f.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

<br />

The spatial unit in our case is a 'Line': we can define this using the function `ee.Geometry.LineString`, which just needs the beginning and end coordinates as (LONG,LAT) pairs. 

Next, we can plot the transect on the map. 

```javascript
// defining the variable 'transect' as the gradient we want to investigate:  
var endpoint = [-121, 45.1];    // The first input here is the longitude and the second one the latitude
var startpoint = [-124, 45.1];
Map.setCenter(-122, 45.1, 8); 
var transect = ee.Geometry.LineString([endpoint, startpoint]);
Map.addLayer(transect, {color: 'FF0000'}, 'transect'); //Map is the name GEE gives to the lower panel that shows 'the map':-)
```

<br />

Now, eventually, for points along the transect we want to see how elevation, temperature and precipitation changes when going from left to right on the transect. This means: we are working towards a plot where we have longitude on the X-axis and elevation / precipitation / temperature on the y-axis. 

One of the easiest ways to do this, is to first create an Image with several bands: one for longitude (or latitude, if you want to investigate a vertical gradient), one for elevation, one band for yearly temperature, and one band for yearly precipitation. 

Once we have this image, we can collect the various variables (longitude, elevation, temperature...) along the transect with the `ee.Reducer.toList()` function that is explained on [this page](https://developers.google.com/earth-engine/guides/charts_array).

But first things first, let's make this Image with the different bands. 

```javascript
//To create a raster with longitudes and latitudes, we can use the following function: 
var latLonImg = ee.Image.pixelLonLat();
//you can check this by printing the newly created dataset to the console: 
print(latLonImg); 
//check in the console here on the right: the image has two bands: which ones?
```

<br />

> 🔍 **Recap 10**. What is the name of the first band in the Image latLonImg? And what is the name of the second one?

<br />
<details>
<summary>Answer Recap 10.</summary>
The first one is longitude and the second one is latitude. You can check the Console > Image > bands to see this.
</details>
<br />

If you want to know exactly what a certain function does, you can either google it, or consult it on the platform itself this way: 


<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/179742254-e5a09908-89c3-426c-9c7c-9d79af16d7a1.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

<br />

```javascript
//let's Map one of those two: 
var vizParams = {
  bands: ['longitude'],
  min: -180,
  max: 180,
  palette: ['00FFFF', '0000FF'],
  opacity: 0.5
};

Map.addLayer(latLonImg,vizParams, 'longitude');

```

***

<br />

### Step 3.4: Import the data

Now we can search elevation, temperature and precipitation data on Google Earth Engine. You can easily search for data using the searchbar at the top in GEE or you can browse the entire catalog [here](https://developers.google.com/earth-engine/datasets/catalog). Here is an example of how to browse for data:

<br />

<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/131996471-05d9d1b8-0a0f-4e1d-82da-d9cdc1ba0bd2.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

<br />

To load elevation and climate data into GEE, we can use the following resources:

| Dataset      | Type | Source     |Access point     |
| :---        |    :---    |          :---  |         :---  |
| WORLDCLIM   | Raster        | University of California, Berkeley     | https://developers.google.com/earth-engine/datasets/catalog/WORLDCLIM_V1_MONTHLY?hl=en     |
| SRTM DEM 90 meters  | Raster       | ? | https://developers.google.com/earth-engine/datasets/catalog/CGIAR_SRTM90_V4 |


<br />

Use the links above to find out what the source is of the SRTM DEM:

> 🔍 **Recap 11**. What is the source of the SRTM DEM? 

<br />
<details>
<summary>Answer Recap 11.</summary>
The source is NASA/CGIAR, the dataset is released by NASA and subsequently post-processed to create a seamless map.
</details>

<br />

Now that we know what is inside of this new image, we can continue: we will import a thematic layer (e.g. the elevation) and add the longitude and latitude bands to that image: this allows us to have, for each long-lat combination a band value (e.g. for temperature or elevation or ...)

```javascript
// Import a digital surface model
var elevImg =ee.Image("CGIAR/SRTM90_V4");
//What is in the image (which are the bands, how many bands are there?)-->
print(elevImg);

```

<br />

hmmm, we only need the elevation, which band is that? Again, to find information about the datasets, you can use the search bar above: 

<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/179746069-76558d59-a5bd-44a4-95ff-83070baced21.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

<br />

```javascript
// let's now select the band we want and add the latlon bands to it: 
var elevation = elevImg.select('elevation');
var elevationlatlon = elevation.addBands(latLonImg);
// does it now have the bands you are interested in? 
print(elevationlatlon); // how many bands are there now?

//let's also add this image to the map below: let's plot the band  elevation
var vizParams2 = {
  bands: ['elevation'],
  min: 0,
  max: 1800,
  palette: ['00FFFF', '0000FF'],
  opacity: 0.5
};
Map.addLayer(elevationlatlon,vizParams2, 'elevation');
```

<br />

GREAT! now we have an image which already has the coordinates as pixel values and the elevation, meaning we can try to attempt to use ee.Reducer.to.List and plot the elevation changes according to longitude changes along the profile: 

```javascript
// Reduce elevation and coordinate bands by transect line; get a dictionary with
// band names as keys, pixel values as lists.
var elevTransect = elevationlatlon.reduceRegion({
  reducer: ee.Reducer.toList(),
  geometry: transect,
  scale: 1000,  //we use a scale (or a resolution) of 1000m, this seems weird when we have a DEM of 90m, but we do this because the resolution of the other datasets (e.g. temperature) is way less detailed. You are often restricted by the resolution of the layer lowest quality.
});
print(elevTransect); //Check what is inside this object: their seems to be 3 lists, which ones, and does this make sense to you? 
     
```


***

<br />

### Step 3.5: Plot the gradients

Now that we have an object that contains a list with the longitudes and the elevations, we can plot the elevations and the longitudes on one plot: 


```javascript
// Get longitude and elevation value lists from the reduction dictionary.
var lon = ee.List(elevTransect.get('longitude'));
var elev = ee.List(elevTransect.get('elevation'));

// Sort the longitude and elevation values by ascending longitude.
var lonSort = lon.sort(lon);
var elevSort = elev.sort(lon);

// Define the chart and print it to the console.
var chart = ui.Chart.array.values({array: elevSort, axis: 0, xLabels: lonSort})
                .setOptions({
                  title: 'Elevation Profile Across Longitude',
                  hAxis: {
                    title: 'Longitude',
                    viewWindow: {min: -124.50, max: -120}, 
                    titleTextStyle: {italic: false, bold: true}
                  },
                  vAxis: {
                    title: 'elevation(m)',
                    titleTextStyle: {italic: false, bold: true}
                  },
                  colors: ['1d6b99'],
                  lineSize: 5,
                  pointSize: 0,
                  legend: {position: 'none'}
                });
print(chart);

```

<br />

*Note that you can click on the small arrow on the right upper corner of your graph to maximize the window and even download the data!

<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/179748619-4537869a-90f1-476b-a46c-23dd07a9500e.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

<br />

***
GREAT! Now that we have done this for an image (the image with the elevation band), let's try to do this for an ImageCollection: An image collection is a series of images bundles together, for example because they contain the same type of data but at different timesteps. An image collection for precipitation could for example consist of an image for each day, whereby each image contains the daily precipitation per pixel. Before you can analyze this, you will thus have to convert the collection to a single image (e.g. by summing all the values to get the total precipitation, or by averaging to get e.g. the average temperature). 

This is exactly what we will do next. After the reduction of the ImageCollection to the image, the exercise is very similar to the one of elevation vs. longitude. 


```javascript
// we will use this dataset for the temperature (and perhaps also for precipitation?)
var climatevar = ee.ImageCollection('WORLDCLIM/V1/MONTHLY');
print(climatevar); // Ok this dataset contains several images with multiple bands representing monthly means: we'll need to select the right band and average them to get an annual mean: 
var temperature = climatevar.select('tavg');
print(temperature); // is there now only one band per image? YES
// However, there are still 12 features (for the 12 months): select the first one and plot it on the map. Like this: 
var tempjan = temperature.first();
print(tempjan);
var vizParams = {
  min: -100,
  max : 250,
  palette: ['00FFFF', '0000FF'],
  opacity: 0.5
};
Map.addLayer(tempjan, vizParams, 'mean temperature januari'); 
// Now click on Amsterdam on the map: what is the average monthly value for this city as depicted in the inspector? can this be correct?
// Why is this value so high? (tip: search in the searchbar for the worldclim temperature dataset and look in the band description )
// now we can take the average temperature over all the months: 
var average_T = temperature.reduce('mean').multiply(0.1); //why do we multiply with 0.1? (again, search in the searchbar for the worldclim temperature dataset)

print(average_T);
     
```

<br />

Right, now we have an actual image with the band we want: the exercise is now very similar to the one with elevation: 

```javascript
// Ok: now we can repeat the same exercise to get the values along the transect
var average_T_withlatlon = average_T.addBands(latLonImg);
print(average_T_withlatlon);

// Reduce elevation and coordinate bands by transect line; get a dictionary with
// band names as keys, pixel values as lists.
var tempTransect = average_T_withlatlon.reduceRegion({
  reducer: ee.Reducer.toList(),
  geometry: transect,
  scale: 1000, //why 1000? (tip: search the openlandmap dataset in the search bar above)
});
print(tempTransect); 


// Get longitude and temperature value lists from the reduction dictionary.
var lon = ee.List(tempTransect.get('longitude'));
var temp = ee.List(tempTransect.get('tavg_mean'));

// Sort the longitude and elevation values by ascending longitude.
var lonSort = lon.sort(lon);
var tempSort = temp.sort(lon);

// Define the chart and print it to the console.
var chart = ui.Chart.array.values({array: tempSort, axis: 0, xLabels: lonSort})
                .setOptions({
                  title: 'Temperature Profile Across Longitude',
                  hAxis: {
                    title: 'Longitude',
                    viewWindow: {min: -124.50, max: -120},
                    titleTextStyle: {italic: false, bold: true}
                  },
                  vAxis: {
                    title: 'temperature (C)',
                    titleTextStyle: {italic: false, bold: true}
                  },
                  colors: ['1d6b99'],
                  lineSize: 5,
                  pointSize: 0,
                  legend: {position: 'none'}
                });
print(chart);

```

<br />

Well done! You have succesfully retrieved the elevation and temperature data for the transect at a 1 kilometer resolution. This makes it possible to see the relationship between the two on a graph. Are you able to answer the following question about the code you just evaluated? Can you do the same for the precipitation?

<br />

> 📝 **Question 4**. The temperature data, whose mean temperature of January you have visualized on the map in GEE, have very large values: why is that? 
> <br />
> tip: look at the source link given in the table on GitHub that describes the data

<br />

> 📝 **Question 5**. Given the code earlier, you should now have the ability to build a plot showing how Precipitation changes across Longitude.
> <br />
> Copy the code that you used to create the temperature plot, then change the code so that it extracts the precipitation.
> Build this plot using the transect you have used in the practical.
> Make the figure complete by adding a title and labels for the axis.
> Indicate the coast range, willamette valley and western cascades on this precipitation profile based on information from the paper you read for this class.
> Upload the final plot to the quiz.

<br />

### Step 3.6: Vegetation & gradients

Let's see how the gradients in the abiotic nature correspond with the land cover in this area. Plot the biome map we made earlier on the same map, simply by copy-pasting what we already did before (this code is the same as in step 2):

```javascript
var dataset = ee.Image("OpenLandMap/PNV/PNV_BIOME-TYPE_BIOME00K_C/v01");
var visualization = {
  bands: ['biome_type'],
  min: 1.0,
  max: 32.0,
  palette: [
    "1c5510","659208","ae7d20","000065","bbcb35","009a18",
    "caffca","55eb49","65b2ff","0020ca","8ea228","ff9adf",
    "baff35","ffba9a","ffba35","f7ffca","e7e718","798649",
    "65ff9a","d29e96",
  ]
};
Map.addLayer(dataset, visualization, "Potential distribution of biomes");
```

You can manually investigate the relationship between the precipitation / elevation / temperature plots and the biomes, or you can try to extract the biome values from the plot as well (are you able to adjust the code to do this for the biome map?)


**Congrats: you are now able to reduce an image collection to an image and make nice plots: can you do the same for precipitation? **




<nav>
  <ul>
    <li><a href="intro.html">Step 1: JavaScript</a></li>
    <li><a href="exploring.html">Step 2: Google Earth Engine</a></li>
      <li><strong>Step 3: Gradient Analysis</strong></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>
