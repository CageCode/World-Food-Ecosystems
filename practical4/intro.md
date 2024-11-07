# Welcome to the 4th practical. 

In this practical we will look at the amazing patterns and relationships between our abiotic and biotic world. We will use geodiversity as a composite measure for our abiotic world, which includes the variety of physical and chemical features of the Earth's surface. In addition, we will also take climate data into consideration during the analysis. For our biotic world, we will focus on the richness and rarity of species, encompassing both the diversity and uniqueness of life forms present in an ecosystem. The research question for this practical is:
- How do global trends in biodiversity differ over abiotic gradients on a landscape level?

Let's delve into the interactions between these two realms, start by creating a new script in Google Earth Engine: Click on *NEW* at the top-left of GEE and choose *file*, give it an appropriate name like: *prac4_geodiversity*.

<br />

## Step 1: Describing data

<br />

### Problem setting



<br />

How would you go about analysing biodiversity on this global scale given the information above? Can you complete the following table and give the answers on the question marks?

| Parameter  |  Value |
|---|---|
| Spatial extent | ? |
| Temporal extent | Static analysis. We are conducting an analysis on current (climatic) data. |
| Cartographic unit |  ?  |
| Dimensions | Biodiversity, geodiversity, climate |
| Dimension description |  Biodiversity is expressed in richness & rarity. Geodiversity consists of slope, hydrology, soil and lithology maps. For climate data we will use annual precipitation and mean temperature. |
| Temporal resolution | ? |
| Spatial resolution | ? |
| Assumption / Hypothesis | ? |

<br />

*** 

<br />

### Accessing the data

The data for this practical has already been preprocessed, so you only need to upload it to Google Earth Engine and perform the analysis. You can find the data in the .zip file provided on Canvas for this tutorial. Once you have downloaded the data, moved it to a desired folder and unzipped it there you are good to go.

<br />

### Biodiversity

You have seen multiple ways of calculating biodiversity in the lectures. One of those is to calculate the species richness, often defined as the number of species in a region. Another way might be to calculate the rarity of a species. This is somewhat harder to define, but says how common it is to observe a species. One way of calculating species rarity is by dividing the species occurence in an area by the total amount of area where this species is present. 

Today we will use the biodiversity metrics provided by the [Map of life](https://mol.org/). This website uses the latest approaches and technologies to deliver detailed information on the distribution of species and their change. In Map of Life, the species richness is estimated by summing the predicted presence of all studied species. Rarity maps are similarly summed, but each species presence in a cell is weighted by the total number of positive cells in the study area for that species. The all-taxa richness and rarity layers are averaged over the normalized richness & rarity of all 7 taxonomic groups. 

<div align="center">
  <img src="MapOfLife.jpg" alt="Map Of Life" >
  <br />
  <em>Figure 1. A random selection of species in the Map of Life database.</em>
</div>


Let's examine how biodiversity, measured in terms of richness and rarity, varies across different regions of the world. To begin, we need to load the required data into Google Earth Engine (GEE) by creating new assets. You have loaded data into GEE before, so this will not be new for you, perform these same steps as you did in earlier practicals:
- Click on the *“Assets”* tab on the top-left panel.
- Press the *“New”* button to create a new asset.
- Select *“GeoTIFF”* under Image upload.
- Select the *"richall"* dataset from the data that you downloaded, perform previous steps again to also upload the *"rareall"*  dataset.

The biodiversity datasets will be visible under the tab *"Tasks"* in the top-right panel of GEE.
- Click on the newly created assets and tap the *"View asset"* button.
- Press on *"Import"* in the popup.
- The datasets will be visible in the middle panel on GEE, give it the appropriate names *"richall"* and *"rareall"* respectively.

<br />

Well done! You've successfully loaded the biodiversity data into GEE. If you used the suggested names for the imported data, you can visualize the richness and rarity of all species with the following code. Note that a mask is applied to remove all NoData values from the map, ensuring that the oceans are not colored.

```javascript
// Define NoData value (0 in this example)
var nodataValue = 0;

// Create a mask for NoData
var raremask = rareall.updateMask(rareall.neq(nodataValue));
var richmask = richall.updateMask(richall.neq(nodataValue));

// visualize biodiversity maps
Map.addLayer(richmask, { min: 0, max: 100, palette: ['red', 'yellow', 'green']}, 'richness');
Map.addLayer(raremask, { min: 0, max: 100, palette: ['red', 'yellow', 'green']}, 'rarity');

Map.centerObject(richmask, 2);
```

<br />

As you can see in the code and if you inspect the map, the values for both richness and rarity range from 0 to 100. This is because the values are normalized: a score of 100 represents the maximum value for richness or rarity, while a score of 0 represents the minimum.

<br />

### Geodiversity
> Geodiversity focuses on the resource giving potential of components of the geosphere in terms of their overall resource potential, the temporal variation in resource availability and the variation of resource availability over space.

In the following code, you will compute a geodiversity map of the world using standardized and harmonized environmental datasets. We will create this geodiversity index by combining geological, soil, hydrological, and topographical datasets within grid cells of 10 × 10 km. A geological dataset derived from the Global Lithological Map database [Hartmann & Moosdorf, 2012](https://doi.pangaea.de/10.1594/PANGAEA.788537) is used to compute a lithological index, based on the number of the lithological formations in each grid cell. A soil index for each grid cell based on the number of soil types was derived from the SoilGrids repository [Hengl et al., 2017](https://soilgrids.org/).  For the hydrological index the total river length per grid cell was calculated using the data from [Lehner, Verdin, & Jarvis, 2011](https://agupubs.onlinelibrary.wiley.com/doi/10.1029/2008EO100001). The slope index was based on a Digital Elevation Model elevation database [Yamazaki et al., 2017](https://hydro.iis.u-tokyo.ac.jp/~yamadai/MERIT_DEM/) and shows the standard deviation of the slope for each grid cell. 

Let's import these global datasets in order to compute the geodiversity index. Similarly as the as for the biodiversity maps, go to the *Assets* tab in GEE and import the raster (TIF) files of the slope, river, soil & lithology maps one by one. While Google Earth Engine is processing this data, you can already glimpse over the following questions:

> 🔍 **Review **. W <br />

<br />
<details>
<summary>Answer Review . (click on this to show/hide the answer)</summary>
Because many camera traps would then fall within the same rastercell of the NDVI-map. This means there is a mismatch in scale, analyzing this data would give you the same NDVI value for different camera traps.
</details>
<br />


> 📝 **Question **. Seijmonsbergen et al. (2018 & see lecture on geodiversity) introduce **time** as a key factor in their study on the geodiversity of the Hawaiian Archipelago. In which component(s) of geodiversity is time a key factor?
> <br />
> • In soil and geology only <br />
> • In geomorphology and soil only <br />
> • In geology, geomorphology, hydrology and in soil <br />
> • In geology and geomorphology and soil <br />


The Amazonian Lowland in Ecuador and the Wilhelmina Mountains in Surinam have the highest geodiversity classes
The lowlands and mountains in Surinam and Ecuador have relatively high geodiversity
The lowlands and mountains in Surinam and Ecuador have predominantly moderate geodiversity
The mountains in Ecuador and in Surinam have higher geodiversity classes than their lowlands



Study the geodiversity map of Niger. it is characterized by the very low and low geodiversity classes, probably related to the absence of surface water in combination with large plains, without too much topographical variation. Soil formation, in the absence of vegetation and water, will likely be restricted as well.

Karst processes and fluvial processes
Weathering processes and mass movement
Aeolian processes and weathering processes
Fluvial processes and mass movement processes


All indices were reclassified into five precentile break-classes and combined into a summed geodiversity index at 10 × 10 km resolution.


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


