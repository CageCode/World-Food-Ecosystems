## Step 3: Understanding the Gradient: how process influences form

In this exercise we will analyze data from the Earth Engine to gain insight in a real-life question: 
How can we explain the remarkable gradient in land cover and related agricultural practices along the West-Coast of the USA (see video below)


<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/135052084-665539ec-8de3-4457-8cc4-f67af8c24812.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>




>WARNING: the amount of code below might be overwhelming, but don't worry: it is not the intention to be able to reproduce this from memory. The goal here is to understand what GEE can do and grasp the general working of the code below. 
***

</br >

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
Similar to this, the **spatial resolution** determines the unit of spatial measurement of the variables. It 

The dimension of the cells can be as large or as small as needed to represent the surface conveyed by the raster dataset and the features within the surface, such as a square kilometer, square foot, or even square centimeter. The cell size determines how coarse or fine the patterns or features in the raster will appear. The smaller the cell size, the smoother or more detailed the raster will be. However, the greater the number of cells, the longer it will take to process, and it will increase the demand for storage space. If a cell size is too large, information may be lost or subtle patterns may be obscured. For example, if the cell size is larger than the width of a road, the road may not exist within the raster dataset. In the diagram below, you can see how this simple polygon feature will be represented by a raster dataset at various cell sizes.

<div align="center">
  <img src="Spatial_Resolution.jpg" alt="Spatial Resolution" >
  <br />
  <em>Figure 2. Overview of the purchasing costs of each square kilometer of forest</em>
</div>

> 🔍 **Recap 9**. What would you consider the spatial resolution for flooding risk in the Netherlands? And what would be a fitting resolution for tree classification from satellite imagery?<br />

<br />
<details>
<summary>Answer Recap 9.</summary>
There are ma

</details>
<br />


> step 1: make a new Script in Earth engine, and give it an appropriate name (e.g. transects): the first time you make a script you'll be asked to enter your username and repository name




<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/131994092-5ed947ec-1aa9-4277-bfa1-db014ac2458f.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>



Now we can think about the building blocks of the analysis (see slide 45, course 1): 
- what Geographic scale – spatial unit is appropriate
- what is the temporal scale we will work on
- What are the boundary conditions or assumptions
- Which dimensions will we take into account (which processes will we consider)
- How will we describe the dimensions


| building block  |  decision |
|---|---|
| Geographic scale |  Transect (line) |
| temporal scale |  all data input should span same timespan |
| Assumption | The different landcovers on such a short transect are due to either a steep temperature gradient, or a steep precipitation gradient, either can be induced by the topographic barrier |
| Dimensions | We'll use winter and summer temperature and yearly precipitation |
| Dimension description | ERA5 for temperature, CHIRPS for precipitation, a local DEM for topography |


>TIP: If you are looking for data, or data types, you could use the search bar above to find datasets and even examples of code to import/plot/...

<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/131996471-05d9d1b8-0a0f-4e1d-82da-d9cdc1ba0bd2.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>





***

> step 2: Define the spatial unit, and import the relevant layers



The spatial unit in our case is a 'Line': we can define this using the function ee.Geometry.LineString, which just needs the beginning and end coordinates as (LONG,LAT) pairs. 

Next, we can plot the transect on the map. 

```javascript
// defining the variable 'transect' as the gradient we want to investigate:  
var endpoint = [-118.821944, 46.527222];  
var startpoint = [-123.416667, 46.783333];
var transect = ee.Geometry.LineString([endpoint, startpoint]);
Map.addLayer(transect, {color: 'FF0000'}, 'transect'); //Map is the name GEE gives to the lower panel that shows 'the map':-)
```



Now, we can import the Image(collections) we need and filter them

```javascript
// Daily mean 2m air temperature
var era5_2mt = ee.ImageCollection('ECMWF/ERA5/DAILY')
                   .select('mean_2m_air_temperature')
                   .filter(ee.Filter.date('2013-12-21', '2014-09-23'));
print(era5_2mt);
    
//import elevation:
var elevation = ee.Image('USGS/NED');  // elevation is here simply one image (so no image collection) because it only contains 1 acquisition (unlike landsat imagery)

// let's add precipitation ass well (mm/pentad, or mm/5days), similar as temperature
var chirps = ee.ImageCollection("UCSB-CHG/CHIRPS/PENTAD"); //rainfall (from the chirps dataset) is available every 5 days: so it is an image collection
var chirpsprecip = chirps.filterBounds(transect)  //again, we filter for location: only the transect
    .select(['precipitation'], ['previp'])   //again, we select only the band of interest (precipitation) and we give it our own name
    .set('system:time_start', chirps.get('system:time_start')); //this is a line that defines the startdate of the image collection
    
```


***


> step 3: prepare the data for plotting on a graph

Now that we have all the image(collections), we can 'reduce' the data, so that we have the winter and summer temperature along the transect, the total precipitation and the topography, and we can combine all this data into one *new image*. 

```javascript
//selecting a year worth of data for CHIRPS and sum all the data (so total yearly precipitation)
var chirpsprecipselect = chirpsprecip.filterDate('2013-12-21', '2014-12-21') //we filter on a range of dates
                          .reduce(ee.Reducer.sum()) //we reduce, by taking the sum
                          .select([0], ['yearprec']); //we select the relevant band (the first band only, or that with index 0) and we give it a name
                          
// Calculate bands for seasonal temperatures and elevations; 
// Calculate bands for seasonal temperatures and elevations; 
var summer = era5_2mt.filterDate('2014-06-21', '2014-09-23')
    .reduce(ee.Reducer.mean())
    .select([0], ['summer']);
var winter = era5_2mt.filterDate('2013-12-21', '2014-03-20')
    .reduce(ee.Reducer.mean())
    .select([0], ['winter']);

//join all the data into one new image: 
// we first make a list describing the distance from the startingpoint:
var startingPoint = ee.FeatureCollection(ee.Geometry.Point(startpoint));
var distance = startingPoint.distance(450000);
//now we add all the bands to the distance list:
var image = distance.addBands(elevation).addBands(winter).addBands(summer).addBands(chirpsprecipselect); //addBands is here a function, that takes the bands defined above as input

```



OK: now we have all ingredients to sort the information and plot it all in one chart 

```javascript
// Extract band values along the transect line: convert the image information into an array (list).
var array = image.reduceRegion(ee.Reducer.toList(), transect, 1000)
                 .toArray(image.bandNames());

// Sort points along the transect by their distance from the starting point.
var distances = array.slice(0, 0, 1);
array = array.sort(distances);

// Create arrays for plotting in a figure.
var elevationAndTempAndPrecip = array.slice(0, 1);  // For the Y axis.
// Project distance slice to create a 1-D array for x-axis values.
var distance = array.slice(0, 0, 1).project([1]);


```
> step 3: prepare the data for plotting on a graph  (note: you do not need to be able to code this yourself)


```javascript
// Generate and style the chart.
var chart = ui.Chart.array.values(elevationAndTempAndPrecip, 1, distance)
    .setChartType('LineChart')
    .setSeriesNames(['Elevation', 'Winter 2014', 'Summer 2014', 'Precipitation 2014'])
    .setOptions({
      title: 'Elevation, temperatures and precipitation along transect',
      vAxes: {
        0: {
          title: 'Average seasonal temperature '
        },
        1: {
          title: 'Elevation (meters, blue) or precipitation (mm, Green)',
          baselineColor: 'transparent'
        }
      },
      hAxis: {
        title: 'Distance from starting points (m)'
      },
      interpolateNulls: true,
      pointSize: 0,
      lineWidth: 1,
      // Our chart has two Y axes: one for temperature and one for elevation/precip.
      // Y axis, which we do here:
      series: {
        0: {targetAxisIndex: 1},
        1: {targetAxisIndex: 0},
        2: {targetAxisIndex: 0}, 
        3: {targetAxisIndex: 1},
      }
    });

print(chart);
Map.setCenter(-121, 46, 7);
Map.addLayer(elevation, {min: 4000, max: 0});
Map.addLayer(transect, {color: 'FF0000'});

```



*Note that you can click on the small arrow on the right upper corner of your graph to maximize the window




<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/131997467-23ba1c44-9af8-487b-a8f7-6c4409bdab17.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>


## **Congrats: you can now analyze multiple layers at the same time on the same graph: is the result what you expected**? 

The question from the forms was: How can you use this graph to explain agricultural suitability along this transect?

> 📝 **Question 4**. The temperature data, whose mean temperature of January you have visualized on the map in GEE, have very large values: why is that? 
> <br />
> (tip: look at the source link given in the table on GitHub that describes the data)

<br />

<nav>
  <ul>
    <li><a href="intro.html">Step 1: JavaScript</a></li>
    <li><a href="exploring.html">Step 2: Google Earth Engine</a></li>
      <li><strong>Step 3: Gradient Analysis</strong></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>
