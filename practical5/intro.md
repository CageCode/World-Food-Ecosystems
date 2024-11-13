# Welcome to the 5th practical. 

In this practical, we will delve into suitability models to understand how different crops can thrive under varying environmental conditions. The Food and Agriculture Organization (FAO) provides a detailed description of Global Agro-Ecological Zones (GAEZ). They develop suitability and potential yield maps for up to 280 crops/land utilization types under varying input and management levels for historical, current, and future climate conditions. These models take into account factors such as climate conditions, soil classes, water availability etc. to provide a comprehensive view of agricultural productivity. 

These maps, are grounded on sophisticated suitability models. You can read about the development and the underlying models of these maps on the FAO's official site: [FAO GAEZ Data Portal](https://gaez.fao.org/pages/theme-details-theme-4).

<br />



Rather than using the FAO site directly, we will be utilizing the [Crop Niche Viewer](https://cartoscience.users.earthengine.app/view/crop-niche) in our Canvas Quiz exercises.

### Geospatial Parameters

What's that? A déjà vu? O yes it is, you have seen this table before and now it's your turn to fill it in. What would be appropriate geospatial parameters regarding suitability mapping? Well, that depends on the research question at hand. As discussed in the first lecture, the choices you make should align with the intended application. The goal is to reduce complexity to generate an answer that is both meaningful and accurate.

| Parameter  |  Value |
|---|---|
| Spatial extent | ? |
| Temporal extent | ? |
| Cartographic unit |  ?  |
| Dimensions | ? |
| Dimension description |  ? |
| Temporal resolution | ? |
| Spatial resolution | ? |
| Assumption / Hypothesis | ? |

> 📝 **Question 1**. Let's say you want to create a suitability map for grape production in The Netherlands. Which geospatial parameters would be fitting for the analysis? <br />
> <br />
> Explain which spatial and temporal extent and resolution would be suitable, which dimensions you would consider and which assumption(s) you would make for this analysis. Please provide your answer in bullet points in less than 100 words. <br />
<br />



### Agro-Ecological Zones by FAO
### Suitability Models: Agricultural Suitability and Potential Yields

For evaluating rain-fed land productivity, the FAO employs a water-balance model to ascertain the beginning and duration of periods with adequate water for crop growth. This model, combined with other climatic factors (such as radiation and temperature), is used in a robust crop growth framework to estimate potential biomass production and yield.

In contrast, for irrigated land productivity, each crop’s growth cycle length is aligned with periods that have temperatures favorable for growth. The estimated potential agro-climatic yields are then adjusted using various reduction factors that are either directly or indirectly associated with climate (e.g., pests and diseases), soil, and terrain conditions.


---

### Analyzing Suitability Models

In essence, the GAEZ v3.0 assessment provides a comprehensive and spatially explicit database of crop production potential and related constraint factors.

<br />

#### Agro–Climatic Yields

The constraint-free crop yields calculated in the AEZ biomass model reflect yield potentials concerning temperature, radiation, and moisture regimes prevailing in the respective grid cells. The model requires several crop characteristics, including:
- Length of growth cycle (days from emergence to full maturity)
- Length of yield formation period
- Maximum rate of photosynthesis at prevailing temperatures
- Leaf area index at maximum growth rate
- Harvest index
- Crop adaptability group
- Sensitivity of crop growth cycle length to heat provision
- Development stage-specific crop water requirements
- Coefficients of crop yield response to water stress

Agro-climatically attainable yields are calculated at crop/LUT level for three input levels (high, intermediate, and low) and three water supply system types (rain-fed, rain-fed with water conservation, and irrigation).

<br />

#### Climate Yield Constraints

The model not only provides estimates of agro-climatically attainable crop yields but also offers information on the climate-related constraints affecting crop yields. These constraints include:
- Temperature constraints
- Moisture constraints
- Yield constraints due to pests, diseases, and workability

Additionally, the model outputs crop water deficits (rain-fed conditions) or crop irrigation water requirements (irrigated conditions).

<br />

#### Crop Calendars

The results of biomass and yield calculation depend on the timing of the crop growth cycle (crop calendar). For each grid cell, the starting and ending dates of each crop/LUT growth cycle are optimized to achieve the best possible crop yields, separately for rain-fed and irrigated conditions. This process also ensures adaptation in simulations with year-by-year historical weather conditions and allows major crop calendar shifts and adaptations under climate changes scenarios.

<br />

#### Agro-Ecological Suitability and Productivity

Adequate agricultural exploitation of the climatic potentials and maintenance of land productivity depend significantly on soil fertility and ecologically sustained management of soils. Soil fertility pertains to the soil's ability to retain and supply nutrients and water, enabling crops to fully utilize the climatic resources of a given location. The fertility of soil is determined by both its physical and chemical properties. Understanding these factors and their interrelations is essential for effective utilization of climate, terrain, and crop resources for optimal use and production.

From the basic soil requirements of crops, various soil characteristics have been established related to crop yield, ranging from optimal to marginal conditions. Beyond certain critical ranges, crops cannot yield satisfactorily unless special management measures are implemented. Soil suitability classifications are informed by:
- Knowledge of crop requirements
- Prevailing soil conditions
- Applied soil management practices

In essence, soil suitability procedures assess to what extent soil conditions meet crop requirements under specified input and management circumstances.

Agro-ecological suitability and productivity are presented based on:
- Four input levels (high, intermediate, low, and mixed)
- Five water supply system types (rain-fed, rain-fed with water conservation, gravity irrigation, sprinkler irrigation, and drip irrigation)

These classifications are provided at the crop level (covering 49 crops) for baseline climate conditions (1961-1990) as well as future climate scenarios. Additionally, comprehensive crop summary tables by administrative units are available for viewing and download

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
var DEM = ee.Image("USGS/GTOPO30");
var BIO = ee.Image("WORLDCLIM/V1/BIO");

///////// loading variables
// for medium suitability
var prangeme = [800,2000]; //Precipitation suitability range for crop in mm
var trangeme = [200,300]; //temperature suitability range for crop in degrees C*10
var arangeme = [-100,2000]; //altitude suitability range for crop in m
var srangeme = [0,20]; //slope suitability range for crop in degrees

// for high suitability
var prangehi = [1000,2000]; //Precipitation suitability range for crop in mm
var trangehi = [200,300]; //temperature suitability range for crop in degrees C*10
var arangehi = [-100,1000]; //altitude suitability range for crop in m
var srangehi = [0,5]; //slope suitability range for crop in degrees
//selecting layers from imported Data
var prec = BIO.select('bio12')
var temp = BIO.select('bio01')
//Modifying layers for use in model
var slope = ee.Terrain.slope(DEM) // here the slope is calculated from the DEM
//reclassifying layers for suitability
var suitabilityme =
DEM.gte(arangeme[0]).and(DEM.lte(arangeme[1])).multiply(slope.gte(srangeme[0]).and(
slope.lte(srangeme[1]))).multiply(temp.gte(trangeme[0]).and(temp.lte(trangeme[1])))
.multiply(prec.gte(prangeme[0]).and(prec.lte(prangeme[1])))
var suitabilityhi =
DEM.gte(arangehi[0]).and(DEM.lte(arangehi[1])).multiply(slope.gte(srangehi[0]).and(
slope.lte(srangehi[1]))).multiply(temp.gte(trangehi[0]).and(temp.lte(trangehi[1])))
.multiply(prec.gte(prangehi[0]).and(prec.lte(prangehi[1])))
var suitability = suitabilityme.add(suitabilityhi)
//Modifying climatic layers for use in model
var temp = temp.add(0) // this line can be used to modify the temperature
var prec = prec.add(0) // this line can be used to modify the precipitation
//reclassifying layers for suitability with new climate
var suitabilityme2 =
DEM.gte(arangeme[0]).and(DEM.lte(arangeme[1])).multiply(slope.gte(srangeme[0]).and(
slope.lte(srangeme[1]))).multiply(temp.gte(trangeme[0]).and(temp.lte(trangeme[1])))
.multiply(prec.gte(prangeme[0]).and(prec.lte(prangeme[1])))
var suitabilityhi2 =
DEM.gte(arangehi[0]).and(DEM.lte(arangehi[1])).multiply(slope.gte(srangehi[0]).and(
slope.lte(srangehi[1]))).multiply(temp.gte(trangehi[0]).and(temp.lte(trangehi[1])))
.multiply(prec.gte(prangehi[0]).and(prec.lte(prangehi[1])))
var suitability2 = suitabilityme2.add(suitabilityhi2)
//Visualisation of the results
var suitmasked = suitability.updateMask(suitability.gt(0));
var suitmasked2 = suitability2.updateMask(suitability2.gt(0));
var vispar = {min: 1, max: 2, palette: ['FFFF00','00FF00']};
Map.addLayer(suitmasked,vispar, 'Current Suitability')
Map.addLayer(suitmasked2,vispar, 'Future Suitability')
///////// setting up the Legend
// set position of panel
var legend = ui.Panel({
style: {
position: 'bottom-left',
padding: '8px 15px'
}
});
// Create legend title
var legendTitle = ui.Label({
value: 'Suitability',
style: {
fontWeight: 'bold',
fontSize: '18px',
margin: '0 0 4px 0',
padding: '0'
}
});
// Add the title to the panel
legend.add(legendTitle);
// Creates and styles 1 row of the legend.
var makeRow = function(color, name) {
// Create the label that is actually the colored box.
var colorBox = ui.Label({
style: {
backgroundColor: '#' + color,
// Use padding to give the box height and width.
padding: '8px',
margin: '0 0 4px 0'
}
});
// Create the label filled with the description text.
var description = ui.Label({
value: name,
style: {margin: '0 0 4px 6px'}
});
// return the panel
return ui.Panel({
widgets: [colorBox, description],
layout: ui.Panel.Layout.Flow('horizontal')
});
};
// Palette with the colors
var palette =['00FF00','FFFF00'];
// name of the legend
var names = ['High Suitability','Medium Suitability'];
// Add color and and names
for (var i = 0; i < 2; i++) {
legend.add(makeRow(palette[i], names[i]));
}
// add legend to map (alternatively you can also print the legend to the console)
Map.add(legend);
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


