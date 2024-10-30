## Step 2: Exploring the functionalities
Now we will explore the functionalities of Google Earth Engine, a cloud-based platform for spatial data analysis. It enables users to access, visualize, and analyze vast amounts of data, facilitating research on topics such as land cover change, climate monitoring and disaster response to name a few. In this course we use GEE to quickly access various datasources and inspect them easily.

If at any point your lost on the basic JavaScript commands, go [back](intro.html)
</br >
</br >

### Step 2.1: Loading the biome map of the world

First we will load a biome map of the world which represents different types of biomes across the world, showing zones with characteristic life forms and environmental conditions. Biomes are classified based on factors such as temperature, precipitation, and the dominant forms of life like vegetation. Examples include tropical rainforests, deserts, tundras, and savannas.
The biome map that we will load using JavaScript comes in the form of a raster, which means it consists of pixels organized in a grid. Each pixel has the same spatial size and contains a value that represents some type of data. 

Copy the following piece of code in the 'script' panel of google earth engine. 

```javascript

// first we retrieve the Image (the raster dataset) from the server and declare the variable
var biomes = ee.Image("OpenLandMap/PNV/PNV_BIOME-TYPE_BIOME00K_C/v01");

// but what have we done exactly? Let's see what it is in this 'Image'. 
print(biomes);

```

Save the script (e.g. name it 'BiomePlotting') and run it. 


> 🔍 **Recap 4**. You should see some basic information about the image *printed* in the console: how many bands does this Image have? (tip: try clicking on the Image name in the console) <br />
> <br />
> a) 1. <br />
> b) 5. <br />
> c) 8. <br />
> d) 16. <br />

<br />
<details>
<summary>Answer Recap 4.</summary>
Answer A. There is only one band available. This indicates that the Biome map consists of a single raster layer, meaning there are no multiple bands present. In contrast, for other types of data, such as average temperature per month, you would typically expect to see 12 bands — one raster layer for each month.
</details>
<br />

***

### Step 2.2: Visualize Biomes

Now that we loaded the Biome map, you can plot the biome map on the baselayer of GEE and discovering its contents. The pixel values in this raster represent different biome categories. Each pixel's value corresponds to a specific biome type, and the names of these biome categories are stored within the metadata of the dataset. 

Let's plot the map with the following code:

```javascript
// First we need to define how we want to handle the visualisation: we need to *declare a variable* that describes how/what we want to plot. 
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


//after we have set the parameters for the visualisation (declared through the variable 'visualization'we can actually plot: 
Map.setCenter(4,53,7);
Map.addLayer(biomes, visualization, "Potential distribution of biomes");

```

<br />

#### TIP: for each of the functions, there is a documentation provided by earth-engine: https://developers.google.com/earth-engine/apidocs

Let's understand the code above through the following questions. 

> 🔍 **Recap 5**. Why is the minimum set at 1, and the maximum at 32? (hint: check the properties)? <br />

<br />
<details>
<summary>Answer Recap 5.</summary>
If you look in the properties, you will see 20 different classes. However, underneath 'biome_type_class_values' you will see that the data ranges from 1 (index 0) to 32 (index 19).
</details>
<br />

> 🔍 **Recap 6**. What does the palette do? (hint: try changing some of the letters in these strings)? <br />

<br />
<details>
<summary>Answer Recap 6.</summary>
The palette colors the different biome types, of which there are 20.
</details>
<br />

> 🔍 **Recap 7**. Try clicking on the biome map in the visualisation of the bottom panel: what happens underneath the tab: 'Inspector'? <br />

<br />
<details>
<summary>Answer Recap 7.</summary>
The inspector shows the value of the raster cell for the visualized map. You are thus 'inspecting' the raster.
</details>
<br />

<br />

***
### Graded Questions

It’s time to explore the Biome map and answer the first few questions on Canvas. You can find these questions in the quiz associated with this practical.

> 📝 **Question 1**. You have used the OpenLandMap/PNV/PNV_BIOME-TYPE_BIOME00K_C/v01 dataset to make a biome map of the world.
> What is the dominant pixel value in the Netherlands? <br />
> <br />
> (tip: click on some pixels in the Netherlands on the map and then check the value in the 'inspector' panel on the right)

<br />

> 📝 **Question 2**. What does the dominant pixel value from question 1 stand for? 
> <br />
> (hint: look up the legend by browing to the dataset in the earth engine catalogue)

<br />

> 📝 **Question 3**. This biome map shows... (tip: check the paper by Hengl et al attached in this module)
> <br />
> • The actual vegetation
> • The vegetation under the scenario of no climate change
> • The vegetation if there would be no urbanisation/human infrastructure
> • The vegetation if there would be no human interventions

<br />

Now that you know the basic functionalities of the GUI (Graphical User Interface), let's try an actual application. Continue with step 3!


<nav>
  <ul>
    <li><a href="intro.html">Step 1: JavaScript</a></li>
    <li><strong>Step 2: Google Earth Engine</strong></li>
    <li><a href="understandinggradients.html">Step 3: Gradient Analysis</a></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>

