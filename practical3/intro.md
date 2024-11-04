# Welcome to the 3rd practical. 

In this practical we will be using a combination of Google Earth Engine and R to understand biodiversity patterns. It is essential that you found your way trough the [earlier practicals](../) as these practical sessions intentionally have an increasing complexity. 


Start by creating a new script in Google Earth Engine: Click on *NEW* at the top-left of GEE and choose *file*, give it an appropriate name like: *prac3_biodiversity*.

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
We have processed the camera trap data, so that it is compiled into a shape-file that you can download from canvas: download the folder and unzip in your working directory. 

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

<br />

**NDVI-Map:** <br />
The points are relatively close together, the NDVI datasets that are readily available in the Google Earth Catalogue have a rough spatial resolution (e.g. the based on [MODIS satellites has a 500m resolution](https://developers.google.com/earth-engine/datasets/catalog/MODIS_061_MOD13A1). 

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


<br />

> 🔍 **Review 1**. We have now used only one satellite acquisition to represent/calculate NDVI for the whole year. Based on what you know/find about the local climate, can you justify this? <br />

<br />
<details>
<summary>Answer Review 1. (click on this to show/hide the answer)</summary>

</details>
<br />

> 🔍 **Review 2**. The cloud cover is still quite high, given the fact that we need to calculate NDVI at a reasonable spatial resolution (note also how close the points are to each other), which other satellite missions for which the data is available on GEE can we use? <br />
> <br />
> a) MODIS <br />
> b) SENTINEL-1 <br />
> c) SENTINEL-2 <br /> 
> d) All of the above options (_a_, _b_, and _c_) are correct. <br /> 

<br />
<details>
<summary>Answer Review 2. (click on this to show/hide the answer)</summary>

</details>
<br />

<iframe width="640px" height="480px" src="https://forms.office.com/Pages/ResponsePage.aspx?id=zcrxoIxhA0S5RXb7PWh05Vl3_L7XnVBBlpWSqA8whj9UNFUwM05BS0E2S0NVSVg2OTdJNDhZMk1CVy4u&embed=true" frameborder="0" marginwidth="0" marginheight="0" style="border: none; max-width:100%; max-height:100vh" allowfullscreen webkitallowfullscreen mozallowfullscreen msallowfullscreen> </iframe>

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


