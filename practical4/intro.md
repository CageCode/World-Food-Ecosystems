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

<br />

<div align="center">
  <img src="MapOfLife.jpg" alt="Map Of Life" >
  <br />
  <em>Figure 1. A random selection of species in the Map of Life database.</em>
</div>

<br />

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

> 🔍 **Review 1**. At first glance, what stands out to you about and between the richness and rarity maps? <br />

<br />
<details>
<summary>Answer Review 1. (click on this to show/hide the answer)</summary>
There can be many right answers to this question. Some striking patterns include the high amount of species richness in the tropics. Yet, these biomes do not always show high species rarity. Instead, species rarity is more pronounced on islands and in mountainous areas.
</details>
<br />



<nav>
  <ul>
    <li><strong>Step 1: Problem Description</strong></li>
    <li><a href="geodiversity.html">Step 2: Geodiversity</a></li>
    <li><a href="relationship.html">Step 3: Data Analysis</a></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>


