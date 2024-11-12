# Welcome to the 5th practical. 

In this practical, we will delve into suitability models to understand how different crops can thrive under varying environmental conditions.  The Food and Agriculture Organization (FAO) provides a detailed description of Global Agro-Ecological Zones (GAEZ). They develop suitability and potential yield maps for up to 280 crops/land utilization types under varying input and management levels for historical, current, and future climate conditions. These models take into account factors such as climate conditions, soil moisture, radiation, and temperature to provide a comprehensive view of agricultural productivity. 

These maps, are grounded on sophisticated suitability models. You can read about the development and the underlying models of these maps on the FAO's official site: [FAO GAEZ Data Portal](https://gaez.fao.org/pages/theme-details-theme-4).

Rather than using the FAO site directly, we will be utilizing the [Crop Niche Viewer](https://cartoscience.users.earthengine.app/view/crop-niche) in our Canvas Quiz exercises.

### Suitability Models: Agricultural Suitability and Potential Yields

For evaluating rain-fed land productivity, the FAO employs a water-balance model to ascertain the beginning and duration of periods with adequate water for crop growth. This model, combined with other climatic factors (such as radiation and temperature), is used in a robust crop growth framework to estimate potential biomass production and yield.

In contrast, for irrigated land productivity, each crop’s growth cycle length is aligned with periods that have temperatures favorable for growth. The estimated potential agro-climatic yields are then adjusted using various reduction factors that are either directly or indirectly associated with climate (e.g., pests and diseases), soil, and terrain conditions.


<br />

## Step 1: Describing data

<br />

### Problem setting

As introduced in class, we will use camera trap data: these are cameras where each time an animal passes, a picture is taken and AI is used to identify the animal. After some data cleaning and quality checks, the total amount of unique species (species richness) can be calculated for each camera (so each location). The question in this practical will be: for a dataset collected in Costa Rica: which gradients in species richness exist? Based on what we saw in class, we assume here that if there are gradients, they might depend on NDVI. Additionally, because the dataset is acquired on the flanks of a volcano, we will also check the effect of elevation. 

<br />

Now think about the geospatial parameters, how would you go about analysing biodiversity over gradients in Costa Rica? Can you complete the following table and give the answers on the question marks?

| Parameter  |  Value |
|---|---|
| Spatial extent | ? |
| Temporal extent | ? |
| Cartographic unit |  Point data (camera trap observations)  |
| Dimensions | We'll consider the elevation and NDVI
| Dimension description | Elevation: ? , temperature: NDVI: calculated from the LANDSAT 8 mission  |
| Temporal resolution | Static, the observation data is simplified to 2019(!) |
| Spatial resolution | ? |
| Assumption / Hypothesis | Species richness depends on the amount of energy available. Here we will use NDVI as a proxy for net primary production. Additionally, we assume that richness decreases with temperature, and thus will decrease when elevations are higher |

<br />

(!) In reality the data was collected intermittently since 2007 but for the purposes of this practical, the data are aggregated and strongly simplified. Dealing with the temporal dimension of this dataset and with the particularities of data cleaning for camera trapping lies beyond the scope of this course. We *assume*, for the purposes of this exercise, the data is suitable for analysis against a landsat image of the year 2019 (see later). 


And we'll use following data structures and sources:

| Dataset      | Type | Source     |Access point     |
| :---        |    :---    |          :---  |         :---  |
| TEAM-camera data Costa Rica     | vector       | downloadable from canvas, original source: https://www.wildlifeinsights.org/team-network  |
| NDVI    | ?        | LANDSAT 8    |Google Earth Engine: see below     |
| ?  | ?        | ?      | ?    |


*** 

<br />

### Accessing the data

**Species Richness:** <br />
We have processed the camera trap data, so that it is compiled into a shape-file that you can download from canvas: download and unzip the file (CAMERAS.zip) in your working directory. 

Then you can load the shapefile into google earth engine so that you can do analysis with/on it: 

<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/179978055-1c284a74-4951-41b8-83f5-b790c58c450c.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

<br />

Now it needs a few minutes to load (you can check the progress in the right panel 'tasks'). 

Once it is finished, we can load it and visualize it on the map. We will also draw a square around it which will be useful to calculate the NDVI. Loading the camera points and drawing the region of interest around them is shown here: 


<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/179978970-7b2b90c7-3ac5-402a-ac69-7da81db11365.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

> 📝 **Question 1**. The data we use of the camera traps is basically:
> <br />
> • Abundance data<br />
> • Species richness data, without consideration of abundance <br />
> • A weighted combination of abundance and species richness <br />

***

<br />

**NDVI-Map:** <br />
The camera traps are relatively close together, the NDVI datasets that are readily available in the Google Earth Catalogue have a rough spatial resolution (e.g. the based on [MODIS satellites has a 500m resolution](https://developers.google.com/earth-engine/datasets/catalog/MODIS_061_MOD13A1). 

> 🔍 **Review 1**. Why would a readily available NDVI with a resolution of 500m be too rough? <br />

<br />
<details>
<summary>Answer Review 1. (click on this to show/hide the answer)</summary>
Because many camera traps would then fall within the same rastercell of the NDVI-map. This means there is a mismatch in scale, analyzing this data would give you the same NDVI value for different camera traps.
</details>
<br />

We will thus have to make it ourselves based on satellites with a better spatial resolution. One of the options is the LANDSAT 8 mission which collects spectral information in the red and near-infrared spectrum (the bands we need to calculate NDVI) at 30m resolution. All the information on this product is given [here](https://developers.google.com/earth-engine/datasets/catalog/LANDSAT_LC08_C02_T1_L2).


```javascript

/// You should have the script ready where you have imported the table (the camera points) and drawn a rectangle around them 
// let's also visualize the points; 
Map.addLayer(table, {color: 'FF0000'},'qgis shapefile');
//and center the map around the rectangle we made: 
Map.centerObject(clip);

//Now we can start calculating NDVI based on the landsat 8 reflectances in the red and near infrared bands (use the link above to find out which bands these are) 
var landsat = ee.ImageCollection("LANDSAT/LC08/C02/T1_L2");
print(landsat); // if you want to print this to the console, you'll get an error: indeed the imageCollection is way too big
//so, let's first select on the region and the dates of interest (only within the clip, and the whole year of 2019)
var landsat = ee.ImageCollection("LANDSAT/LC08/C02/T1_TOA").filterBounds(clip).filterDate('2019-01-01', '2019-12-31');
print(landsat); //great this works
// in the tropics, and in mountaineous areas, clouds can be an issue when calculating NDVI. So let's sort the image collection according to cloud cover, and then take the image with the least clouds
var bestlandsat = ee.Image(landsat.sort('CLOUD_COVER').first());
print(bestlandsat);

// now let's visualize this image: 
// Define the visualization parameters.
var vizParams = {
  bands: ['B4', 'B3', 'B2'],
  min: 0,
  max: 0.3
};

// Center the map and display the image.
Map.addLayer(bestlandsat, vizParams, 'true color composite');

//now let's calucalte ndvi of this image
// Compute the Normalized Difference Vegetation Index (NDVI): we need to define the nir and red bands: find out which ones these are and replace the X and Y below with the correct band numbers: 
var nir = bestlandsat.select('BX');
var red = bestlandsat.select('BY');
var ndvi = nir.subtract(red).divide(nir.add(red)).rename('NDVI'); // this is (NIR-RED)/(NIR+RED)


// Now let's visualize this ndvi map we made:                   
var ndvizoom = ndvi.clip(clip); //only cut out the region that what we need
var ndviParams = {min: -1, max: 1, palette: ['blue', 'white', 'green']}; // set visualization parameters
Map.addLayer(ndvizoom, ndviParams, 'NDVI image'); //plot the NDVI


// GREAT! now we can simply extract the values of NDVI for the different points and export it to a csv: 
var camera_ndvi = ndvizoom.reduceRegions(table, ee.Reducer.mean( ),30); // the '30' here indicates the resolution at which you want to collect the data, i.e. in this case the resolution of the dem , in this case 90m
Export.table.toDrive({
  collection: camera_ndvi,
  description:'camera_ndvi',
  fileFormat: 'CSV'
}); //we write the elevation values per camera location to a csv
// after running this it should appear in your 'tasks' here to the right: click on 'run': after finalization it will appear in your google drive
```

***

<br />

### Starting the quiz

Based on the previous code, by investigating the data and your knowledge from the lectures, please answer the following questions (on Canvas).

<br />

> 📝 **Question 2**. Statement: The Landsat product we use as a proxy for vegetation has only one band: the NDVI.
> <br />
> • True <br />
> • False <br />

<br />

> 📝 **Question 3**. How is the NDVI we use calculated? Which bands are used? Provide the formula indicating how it is calculated.

<br />


> 📝 **Question 4**. We have now used only one satellite acquisition to represent/calculate NDVI for the whole year. Based on what you know/find about the local climate, can you justify this? <br />

<br />

> 📝 **Question 5**. The cloud cover is still quite high, given the fact that we need to calculate NDVI at a reasonable spatial resolution (note also how close the points are to each other), which other satellite missions for which the data is available on GEE can we use to calculate NDVI at a comparable or better resolution as LANDSAT 8? <br />
> <br />
> • MODIS <br />
> • SENTINEL-1 <br />
> • SENTINEL-2 <br /> 
> • All of the other options are correct <br /> 


<br />
<br />

**Now we are ready for the [next step](QGIS.html): extracting the elevation data for each point**

<nav>
  <ul>
    <li><strong>Step 1: Problem Description</strong></li>
    <li><a href="QGIS.html">Step 2: Extracting Data</a></li>
    <li><a href="Rstudio.html">Step 3: Data Analysis</a></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>


