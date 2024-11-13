# Welcome to the 5th practical. 

In this practical, we will delve into suitability models to understand how different crops can thrive under varying environmental conditions. The Food and Agriculture Organization (FAO) provides a detailed description of Global Agro-Ecological Zones (GAEZ). They develop suitability and potential yield maps for up to 280 crops/land utilization types under varying input and management levels for historical, current, and future climate conditions. These models take into account factors such as climate conditions, soil classes, water availability etc. to provide a comprehensive view of agricultural productivity. 

These maps, are grounded on sophisticated suitability models. You can read about the development and the underlying models of these maps on the FAO's official site: [FAO GAEZ Data Portal]([https://gaez.fao.org/pages/theme-details-theme-4](https://gaez.fao.org/)). 

### Geospatial Parameters

What's that? A déjà vu? O yes it is, you have seen this table before and now it's your turn to fill it in. What would be appropriate geospatial parameters regarding suitability mapping? Well, that depends on the research question at hand. As discussed in the first lecture, the choices you make should align with the intended application. The goal is to reduce complexity to generate an answer that is both meaningful and accurate.

<br />

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

<br />

> 📝 **Question 1**. Let's say you want to create a suitability map for grape production in The Netherlands. Which geospatial parameters would be fitting for the analysis? <br />
> <br />
> Explain which spatial and temporal extent and resolution would be suitable, which dimensions you would consider and which assumption(s) you would make for this analysis. Please provide your answer in bullet points in less than 100 words. <br />


***

<br />

### Agro-Ecological Zones

Agro-ecalogical zoning (AEZ), as applied in FAO studies, defines zones on the basis of combinations of soil, landform and climatic characteristics. The particular parameters used in the definition focus attention on the climatic and edaphic requirements of crops and on the management systems under which the crops are grown. 

***

### Analyzing Suitability Models

Let's see what these suitability maps look like. Luckily for us, the paper by Peter et al., 2020, introduces a suitability mapper in Google Earth Engine. We will be utilizing this [Crop Niche Viewer](https://cartoscience.users.earthengine.app/view/crop-niche) in this practical. Go to this website and perform the following steps.

Fill in the following settings for the parameters on the left: 

1) Select region: Nigeria
2) Choose temporal range: 2019-2020
3) Choose season duration: 04-01 -  07-01
4) Select crop from FAO ECOCROP database: SweetPotato and select 'Use FAO ECOCROP parameters'
5) Set rainfall range during growth cycle: 10-20 millimeters
6) Set temperature range during growth cycle: 15-30 degrees Celsius
7) Click 'Click to map!' (displaying your results might take a few seconds up to half a minute)

Use the visualized suitability map to answer the following question:

> 📝 **Question 2**. Which region in Nigeria (zoom in on the map if needed) is best suited to grow Sweet Potatoes in the months April, May and June under the conditions you set above? <br />
<br />

Now change the map a little: Use the same settings 1) to 5) as above, but change 6) Set temperature range during growth cycle to: 20-35 degrees Celsius.

> <br />

> 📝 **Question 3**. Study on your map which region in Nigeria (zoom in on the map if needed) is best suited to grow Sweet Potatoes in the months April, May and June under the new conditions you just set. Is this region different from the region you picked in Question 13 and how can this be explained?
<br />



***

### Suitability Maps

Let's build a suitability mapper ourselves! 
For this suitability mapper, we are going to consider elevation, slope, mean temperature and annual precipitation. First we have to retrieve this data, import this data yourself, for the DEM use the GTOPO30 dataset and for the climate data use the WorldCLIM Bio dataset.

Make sure you give the variables the names DEM and BIO respectively to make sure it will work with the code later on. Fill in the question marks in the following code:

```javascript
var DEM = ?
var BIO = ?
```

<br />

Now that you imported the datasets, select the correct bands for both the temperature and the precipitation. Again, change the question marks to the correct band:

```javascript
//selecting layers from imported Data
var prec = BIO.select('?')
var temp = BIO.select('?')

//Modifying layers for use in model
var slope = ee.Terrain.slope(DEM) // here the slope is calculated from the DEM
```

<br />

Once we have that going, We can now specify the ranges within our climate data that are suitable for our crop of interest. In this case we are going to model rice. From the FAO website, it becomes clear that a precipitation between 800 and 2000 is prefered, together with a temperature between 20 and 30 degrees Celcius. The altitude should be below 1000 meters in most cases to be suitable.

```javascript
///////// initializing suitability parameters
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
```

<br />

Now we can copy the following code. This code is somewhat harder to understand, but check it out of you want to try. The first part calculates the high en medium suitability. Then these suitability classes are visualized, and a legend is added at the end for extra insight.

```javascript
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

<br />

Run the code if you copied it, does it visualize the suitability in yellow and green? If yes, good job, then everything went well. 

<br />

> 📝 **Question 4**. Run the model, the model was prepared for Rice Suitability. <br />
> <br />
> Study your web map. Which areas are suitable for rice cultivation? <br />
> Pick the best answer from the answers below (see Canvas). <br />



> 📝 **Question 5**. Compare suitability map for rice you just made with the average regional output map for rice on The Decolonial Atlas. <br />
> <br />
> Why are the rice producing areas  not necessarily the ones that are most suitable? You can select multiple answers. <br />
> Select ALL the answers that are correct!<br />






The climate is changing, and we are uncertain if we will exceed the 2.0-degree threshold of global warming. In the summit of Glasgow (November 2021), an international agreement to phase down coal and a roadmap to limit global warming to 1.5 degrees was achieved. This warming will also have an effect on the suitability of crops around the globe.

In our code we can change the average temperature so we can get an idea on how this will affect rice cultivation. Find & change in the following line of code the value 0 to 20 (check if you understand why you fill in 20 instead of 2 here):

> var temp = temp.add(0) // this line can be used to modify the temperature

After you found and changed this line of code, run the code again. How does this simulation reflect climate change? What complexities of climate change did we overlook when implementing this code? 

> 📝 **Question 6**. By simulating climate change in GEE in this way, we...  <br />
> <br />

> 📝 **Question 7**. Modify the code to account for our scenario with climate change. <br />
> <br />
> Do you see a generally positive effect or negative effect for rice cultivation? <br />












In essence, the GAEZ v3.0 assessment provides a comprehensive and spatially explicit database of crop production potential and related constraint factors.

<br />



(https://gaez.fao.org/pages/theme-details-theme-4)

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


