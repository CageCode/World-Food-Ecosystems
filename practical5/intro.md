# Welcome to the 5th practical. 

In this practical, we will delve into suitability models to understand how different crops can thrive under varying environmental conditions. The Food and Agriculture Organization (FAO) provides a detailed description of Global Agro-Ecological Zones (GAEZ). They develop suitability and potential yield maps for up to 280 crops/land utilization types under varying input and management levels for historical, current, and future climate conditions. These models take into account factors such as climate conditions, soil classes, water availability etc. to provide a comprehensive view of agricultural productivity. 

These maps, are grounded on sophisticated suitability models. You can read about the development and the underlying models of these maps on the FAO's official site: [FAO GAEZ Data Portal](https://gaez.fao.org/). 

<br />

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

The constraint-free crop yields calculated in the AEZ biomass model reflect yield potentials concerning temperature, radiation, and moisture regimes prevailing in the respective grid cells. The model requires several crop characteristics, to give you an idea of what kind of parameters are used, here is a list of some of the data:
- Length of growth cycle (days from emergence to full maturity)
- Length of yield formation period
- Maximum rate of photosynthesis at prevailing temperatures
- Leaf area index at maximum growth rate
- Harvest index
- Crop adaptability group
- Sensitivity of crop growth cycle length to heat provision
- Development stage-specific crop water requirements
- Coefficients of crop yield response to water stress

Adequate agricultural exploitation of the climatic potentials and maintenance of land productivity depend significantly on soil fertility and ecologically sustained management of soils. Soil fertility pertains to the soil's ability to retain and supply nutrients and water, enabling crops to fully utilize the climatic resources of a given location. The fertility of soil is determined by both its physical and chemical properties. Understanding these factors and their interrelations is essential for effective utilization of climate, terrain, and crop resources for optimal use and production. From the basic soil requirements of crops, various soil characteristics have been established related to crop yield, ranging from optimal to marginal conditions. Beyond certain critical ranges, crops cannot yield satisfactorily unless special management measures are implemented.

Agro-ecological suitability and productivity are presented based on:
- Four input levels (high, intermediate, low, and mixed)
- Five water supply system types (rain-fed, rain-fed with water conservation, gravity irrigation, sprinkler irrigation, and drip irrigation)

These classifications are provided at the crop level (covering 49 crops) for baseline climate conditions (1961-1990) as well as future climate scenarios. Additionally, comprehensive crop summary tables by administrative units are available for viewing and download

***

<br />

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

<br />

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

<br />

> 📝 **Question 5**. Compare suitability map for rice you just made with the average regional output map for rice on [The Decolonial Atlas](https://decolonialatlas.wordpress.com/2016/10/09/agricultural-maps-of-the-world) and the [FAO database](https://gaez.fao.org/pages/theme-details-theme-4) that shows yields per crop type. <br />
> <br />
> Why are the rice producing areas not necessarily the ones that are most suitable? You can select multiple answers. <br />
> Select ALL the answers that are correct!<br />

Another interesting website to 
<br />

The climate is changing, and we are uncertain if we will exceed the 2.0-degree threshold of global warming. In the summit of Glasgow (November 2021), an international agreement to phase down coal and a roadmap to limit global warming to 1.5 degrees was achieved. This warming will also have an effect on the suitability of crops around the globe.

In our code we can change the average temperature so we can get an idea on how this will affect rice cultivation. Find & change in the following line of code the value 0 to 20 (check if you understand why you fill in 20 instead of 2 here):

> var temp = temp.add(0) // this line can be used to modify the temperature

After you found and changed this line of code, run the code again. How does this simulation reflect climate change? What complexities of climate change did we overlook when implementing this code? 

<br />

> 📝 **Question 6**. By simulating climate change in GEE in this way, we...  <br />
> <br />

<br />

> 📝 **Question 7**. Modify the code to account for our scenario with climate change. <br />
> <br />
> Do you see a generally positive effect or negative effect for rice cultivation? <br />

***

<br />

### Creating your own suitability map

Now that you have some feeling for creating a suitability map, you can now use your skills to make your own! To find information about different crops we can use a multitude of platforms. One of those platforms that provide information about different crops is Feedpedia.

Feedipedia is an open access information system on animal feed resources that provides information on nature, occurrence, chemical composition, nutritional value and safe use of nearly 1400 worldwide livestock feeds. Most of the livestock feeds are also used for human consumption. Use the Feedipedia database at [https://www.feedipedia.org/](https://www.feedipedia.org/). to answer the following questions.

> HINT: Look at the Feeds Data Sheets via the tab 'About Feedipedia'.
> NOTE: Some crops may not be listed in the form suitable for human consumption, but if you open a derived product the information should be listed there.

Let's start of by investigating this website a little bit:

<br />

> 📝 **Question 8**. What is the optimal maximum average daily temperature for Potato (Solanum tuberosum)? <br />
<br />

> 📝 **Question 9**. Until which altitude can coffee (Coffea arabica  L.) be grown in the subtropics? <br />
<br />



Next you will choose one of the following crops to create a suitability map for:

- Maize
- Rye (Secale cereale)
- Sunflower
- Sugarcane 
- Coffee (Arabica/Robusta)
- Potato
- Date
  
Look up the suitability factors for your crop within the Feedipedia database. Try to find factors for high and medium suitability. If you cannot find everything, use google or make an educated guess (some slope factors might be difficult to find).

Important: medium and high suitability ranges must overlap and not exclude! For example: Medium suitability range in elevation for soybean would be 0 - 2000 m and high would be 500-1000 m. Also, remember to fill in temperature values as degrees celcius *10.

Fill in the following table for your chosen crop:

| Factor	| Medium Suitability Range |	High Suitability Range |
|---|---|---|
| Annual Average Temperature | | |	
| Annual Average Precipitation | | |		
| Elevation	| e.g. 0 - 2000 m	| eg. 500 - 1000 m |
| Slope	| eg. 0 - 5 degrees	| eg. 0 - 1 degrees |

When you found the suitability parameters for your crop, insert the values into your Google Earth Engine code and run the model.

Hint: to include that elevations are below sea level (such as The Netherlands), choose a negative number for you lower elevation range.

> 📝 **Question 10**. Add the name of your crop to the legend title in the code were it says: ```value: 'Suitability'```. For instance ```'Suitability Sunflower'```. Then make a screenshot of your created suitability map (including the legend). Upload this map on Canvas at this question.  <br />
> Try to Google how to make a screenshot if you do not know how (on windows there is an application called Snippy for instance). Make sure the image can be opened by us (such as a .jpg format). <br /> 
<br /> 

Let's investigate the distribution of your chosen crop and how vulnerable it is to changes in temperature and precipitation. Apply the following scenarios in your code by changing the ```var temp``` and ```var prec``` just as you did earlier in the practical. Use the scenarios underneath to get a feel for this vulnerability.

| Scenarios | 
|---|
| + 2.0 degrees temperature | same precipitation |
| - 2.0 degrees temperature | same precipitation |
| same temperature | + 200 mm precipitation |
| same temperature | - 200 mm precipitation |
| + 2.0 degrees temperature | + 200 mm precipitation |
| - 2.0 degrees temperature | - 200 mm precipitation |

> 📝 **Question 11**. Describe in 5-10 sentences the differences between the regional output and your suitability map for your chosen crop and how vulnerable your crop is to climate change (which regions/biomes will become more/less suitable).  <br /> 


***

Now the model that we build is only a relatively simple suitability mapper. The article from Peter et al., 2020, published their Google Earth Engine code. You can find the code they used underneath. If you want, you can copy and paste this into GEE and play around with it yourself :)

```Javascript
/* ----------------------------------------------------------------------------------------------------------------------
# CropSuit-GEE
Authors: Brad G. Peter (bpeter@ua.edu), Joseph P. Messina, and Zihan Lin
Organizations: BGP, JPM - University of Alabama; ZL - Michigan State University
Last Modified: 06/28/2020

To cite this code use:
Peter, B. G.; Messina, J. P.; Lin, Z., 2019, "Web-based GIS for spatiotemporal crop climate niche mapping", 
https://doi.org/10.7910/DVN/UFC6B5, Harvard Dataverse, V1
-------------------------------------------------------------------------------------------------------------------------
This is a Google Earth Engine crop climate suitability geocommunication and map export tool designed to support 
agronomic development and deployment of improved crop system technologies. This content is made possible by the 
support of the American People provided to the Feed the Future Innovation Lab for Sustainable Intensification through 
the United States Agency for International Development (USAID). The contents are the sole responsibility of the authors 
and do not necessarily reflect the views of USAID or the United States Government. Program activities are funded by 
USAID under Cooperative Agreement No. AID-OAA-L-14-00006.
-------------------------------------------------------------------------------------------------------------------------
Summarization of input options: There are 14 user options available. The first is a country of interest selection using a 
2-digit FIPS code (link available below). This selection is used to produce a rectangular bounding box for export; however,
other geometries can be selected with minimal modification to the code. Options 2 and 3 specify the complete temporal range
for aggregation (averaged across seasons; single seasons may also be selected). Options 4–7 specify the growing season for 
calculating total seasonal rainfall and average season temperatures and NDVI (NDVI is for export only and is not used in 
suitability determination). Options 8–11 specify the climate parameters for the crop of interest (rainfall and temperature 
max/min). Option 12 enables masking to agriculture, 13 enables exporting of all data layers, and 14 is a text string for 
naming export files.
-------------------------------------------------------------------------------------------------------------------------

••••••••••••••••••••••••••••••••••••••••••• USER OPTIONS ••••••••••••••••••••••••••••••••••••••••••••••••••••••••••••• */

// CHIRPS data availability: https://developers.google.com/earth-engine/datasets/catalog/UCSB-CHG_CHIRPS_PENTAD
// MOD11A2 data availability: https://developers.google.com/earth-engine/datasets/catalog/MODIS_006_MOD11A2

var country = 'MI' // [1] https://en.wikipedia.org/wiki/List_of_FIPS_country_codes
var startRange = 2001 // [2]
var endRange = 2017 // [3]
var startSeasonMonth = 11 // [4]
var startSeasonDay = 1 // [5]
var endSeasonMonth = 4 // [6]
var endSeasonDay = 30 // [7]
var precipMin = 750 // [8]
var precipMax = 1200 // [9]
var tempMin = 22 // [10]
var tempMax = 32 // [11]

var maskToAg = 'TRUE' // [12] 'TRUE' (default) or 'FALSE'
var exportLayers = 'TRUE' // [13] 'TRUE' (default) or 'FALSE'
var exportNameHeader = 'crop_suit_maize' // [14] text string for naming export file

// ••••••••••••••••••••••••••••••••• NO USER INPUT BEYOND THIS POINT ••••••••••••••••••••••••••••••••••••••••••••••••••••

// Access precipitation and temperature ImageCollections and a global countries FeatureCollection
var region = ee.FeatureCollection('USDOS/LSIB_SIMPLE/2017')
  .filterMetadata('country_co','equals',country)
var precip = ee.ImageCollection('UCSB-CHG/CHIRPS/PENTAD').select('precipitation')
var temp = ee.ImageCollection('MODIS/006/MOD11A2').select(['LST_Day_1km','LST_Night_1km'])
var ndvi = ee.ImageCollection('MODIS/006/MOD13Q1').select(['NDVI'])

// Create layers for masking to agriculture and masking out water bodies
var waterMask = ee.Image('UMD/hansen/global_forest_change_2015').select('datamask').eq(1)
var agModis = ee.ImageCollection('MODIS/006/MCD12Q1').select('LC_Type1').mode()
  .remap([1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17],
         [0,0,0,0,0,0,0,0,0,0,0,1,0,1,0,0,0])
var agGC = ee.Image('ESA/GLOBCOVER_L4_200901_200912_V2_3').select('landcover')
  .remap([11,14,20,30,40,50,60,70,90,100,110,120,130,140,150,160,170,180,190,200,210,220,230],
         [1,1,1,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0])
var cropland = ee.Image('USGS/GFSAD1000_V1').neq(0)
var agMask = agModis.add(agGC).add(cropland).gt(0).eq(1)

// Modify user input options for processing with raw data
var years = ee.List.sequence(startRange,endRange)
var bounds = region.geometry().bounds()
var tMinMod = (tempMin+273.15)/0.02
var tMaxMod = (tempMax+273.15)/0.02

// This expression is used to account for growing seasons that overlap the from one year to the next (e.g., Nov-Apr)
var yearMod = ee.Algorithms.If(endSeasonMonth-startSeasonMonth<0,1,0) 

// Calculate seasonal rainfall (sum) and average temperature/NDVI
var bySeason = ee.ImageCollection(years.map(function (y) {
  var startSeason = ee.Date.fromYMD(y, startSeasonMonth,startSeasonDay)
  var endSeason = ee.Date.fromYMD(ee.Number(y).add(yearMod),endSeasonMonth,endSeasonDay)
  var p = precip.filterDate(startSeason,endSeason).sum().rename('p')
  var tDay = temp.select('LST_Day_1km').filterDate(startSeason,endSeason).mean()
  var tNight = temp.select('LST_Night_1km').filterDate(startSeason,endSeason).mean()
  var tMean = tDay.add(tNight).divide(2).rename('t')
  // NDVI is used for data presentation/export only and is not used in the suitability determination
  var nMean = ndvi.mean().rename('n')
  return p.addBands(tMean).addBands(nMean).set('start_date',startSeason).set('end_date',endSeason)
}))

// Average the seasonal data across the temporal range selected
var bySeasonMean = bySeason.mean()
var precipMean = bySeasonMean.select('p').rename('precip_mean')
var tempMean = bySeasonMean.select('t').multiply(0.02).subtract(273.15).rename('temp_mean');
var ndviMean = bySeasonMean.select('n').rename('ndvi_mean')

// Parse into boolean suitable or not suitable categories based on the user input parameters 
var pBool = precipMean.gt(precipMin).add(precipMean.gt(precipMax)).eq(1).rename('precip_suit')
var tBool = bySeasonMean.select('t').gt(tMinMod).add(bySeasonMean.select('t').gt(tMaxMod)).eq(1).rename('temp_suit')
var cBool = pBool.multiply(tBool).rename('combined_suit')

// Clip layers to agriculture if the user selects 'TRUE'
if (maskToAg == 'TRUE') {
  var maskTool = function (i) {
    return i.updateMask(agMask).updateMask(waterMask)
  }
  precipMean = maskTool(precipMean)
  tempMean = maskTool(tempMean)
  ndviMean = maskTool(ndviMean)
  pBool = maskTool(pBool)
  tBool = maskTool(tBool)
  cBool = maskTool(cBool)
}

// Adds layers to map for visualization      
var colors = ['262626','89d82e']
Map.setOptions('HYBRID').centerObject(bounds)
Map.addLayer(precipMean,{},'precip_mean',false)
Map.addLayer(tempMean,{},'temp_mean',false)
Map.addLayer(ndviMean,{},'ndvi_mean',false)
Map.addLayer(pBool,{palette:colors},'precip_boolean',false)
Map.addLayer(tBool,{palette:colors},'temp_boolean',false)
Map.addLayer(cBool,{palette:colors},'combined_boolean',true)
var empty = ee.Image().byte()
var regionVis = empty.paint({
  featureCollection: bounds,
  width: 3
})
Map.addLayer(regionVis,{palette:'white'},country)

// Prints metadata to console for additional evaluation
print('Country bounds: '+ee.Feature(region.first()).get('country_na').getInfo())
print('Years: '+startRange+'-'+endRange)
print('Season: '+startSeasonMonth+'-'+startSeasonDay+' to '+endSeasonMonth+'-'+endSeasonDay)
print('precipMin = '+precipMin+'; precipMax = '+precipMax)
print('tempMin = '+tempMin+'; tempMax = '+tempMax)
print(bySeason)

// Enables export of all layers if the user selects true
if (exportLayers == 'TRUE') {
  var exportTool = function (i,scale,desc) {
    return Export.image.toDrive({
      image: i,
      description: exportNameHeader+desc+'_'+country,
      fileNamePrefix: exportNameHeader+desc+country,
      scale: scale,
      maxPixels: 1e13,
      region: bounds
    })
  }
  exportTool(precipMean,5000,'_precip_mean')
  exportTool(tempMean,1000,'_temp_mean')
  exportTool(ndviMean,250,'_ndvi_mean')
  exportTool(pBool,5000,'_precip_suit_boolean')
  exportTool(tBool,1000,'_temp_suit_boolean')
  exportTool(cBool,1000,'_combined_suit_boolean')
  print('Check tasks tab for export')
}

// Histogram display options (combined suitability grid)
var panel = ui.Panel({
  layout: ui.Panel.Layout.flow('vertical'),
  style: {position:'bottom-left',border:'1px solid gray',padding:'2px',width:'225px'}
})
var histOptions = {
  title: 'Combined suitability',
  fontSize: 11,
  legend: {position:'none'},
  opacity:1,
  hAxis: {textPosition:'none',gridlineColor:'none',baselineColor:'none'},
  series: {0:{color:colors[0]},1:{color:colors[1]}}
}
var histogram = ui.Chart.image.histogram({
  image: cBool.updateMask(cBool.eq(0)).addBands(cBool.updateMask(cBool.eq(1))),
  region: bounds,
  scale: 1000
}).setOptions(histOptions)

Map.add(panel)
panel.add(histogram)

// Legend creation
var makeRow = function(color, name) {
  var colorBox = ui.Label({
    style: {
      backgroundColor: '#' + color,
      padding: '8px',
      margin: '0 0 0 0',
      }
  })
  var description = ui.Label({
    value: name,
    style: {margin: '0 0 2px 4px', fontSize: '13px'}
  })
  return ui.Panel({
    widgets: [colorBox, description],
    layout: ui.Panel.Layout.Flow('horizontal'),
    style: {margin:'0 0 0 15px'}
  })
}
var names = ['Not suitable','Suitable']
for (var i = 0; i < 2; i++) {
  panel.add(makeRow(colors[i], names[i]));
}
panel.add(ui.Label({
  value:'See console tab for metadata and tasks tab for exports',
  style:{margin:'5px 15px 20px 15px'}
}))

/* ------------------------------------------------------------------------------------------------------------------- */
```

<br />





