# Welcome to the 2nd practical. 


In the previous practical, you have gained familiarity with Google Earth Engine and JavaScript code. 

Today we are going to focus on abiotic factors and how plants adapt to their environment, taking salinization as an example.

<br />

## Step 1: The case-study

The case we are investigating today is the salinization of fresh water lakes. In recent year, a global dataset of surface water salinity - with measurements between 1980 and 2019 - was published [here](https://www.nature.com/articles/s41467-021-24281-8). The paper reports on the dataset and how it was established. In this practical we will analyze the dataset to answer following questions: 
- has salinity - as measured by the electrical conductivity (EC) increased or descreased in global freshwater lakes? 
- Is salinity of the water linked to rainfall and temperature? 
- Are increases/decreases in salinity different across different biomes? 
- Which local drivers can influence salinity trends? 

<br />

### Problem simplification

How would you go about to perform such an analysis? Keep in mind the questions that we asked in the first practical:
- Which dimensions will we take into account (which processes will we consider)?
- How will we describe these dimensions?
- What spatial scale & extent is appropriate?
- What temporal scale & extent is fitting here?
- Do we have any hypotheses or assumptions about the analysis?

<br />

If you have an answer on these questions, you can simplify and define the research question and determine the geospatial parameters: 

<br />

| Parameter  |  Value |
|---|---|
| Spatial extent | Global |
| Temporal extent | We will compare averages over 1980-1990 with averages over 2005-2015 |
| Cartographic unit |  Point data (measurement points of salinity in the database. Each seperate point is considered to be a location (regardless if two points are taken in the same water body) |
| Dimensions | We focus on (i) a quanitified rainfall deficit and (ii) the biome map |
| Dimension description | the WorldCLIM dataset (precipitation and temperature) and the OpenLand Biome map |
| Temporal resolution | Only measurement stations that have >5y of measurement in both epochs are considered |
| Spatial resolution | Both the climate data (WorldClim) as the biome map have a resolution of 1 sq. km.  |
| Assumption / Hypothesis | ? |

<br />


### Data description

Now that we have defined the problem and indicated the geospatial paramters, we can describe the data we'll use:

| Dataset      | Type | Source     |Access point     |
| :---        |    :---    |          :---  |         :---  |
| EC sampling points    | Points (vector)      | Thorslund et al. 2020  | [here](https://doi.pangaea.de/10.1594/PANGAEA.913939?format=html#download)  |
| WorldCLIM  | Raster        | ?  |Google Earth Engine Catalogue    |
| Biome map| Raster       | OpenLand potential Biomes     |Google Earth Engine Catalogue|

***

<br />

The datasets by Thorslund et al. are very large and need to be pre-processed as we want to compare data from 1980-1990 to that from 2005-2015 for those stations for which an average can be reliably calculated. 

To make this exercise feasible, this preprocessing has already been done: the aggregated csv file and its conversion into a shapefile as well as the original file can be found [here](https://canvas.uva.nl/files/11814098/download?download_frd=1). 

**Now we are ready for the [next step](API.html): we'll first explore the original and the pre-processed data in R**

<br />

<nav>
  <ul>
    <li><strong>Step 1: Describing the problem</strong></li>
    <li><a href="API.html">Step 2: Exercise 1</a></li>
    <li><a href="Mapping.html">Step 3: Exercise 2</a></li>
    <li><a href="Analysis.html">Step 4: Exercise 3</a></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>

