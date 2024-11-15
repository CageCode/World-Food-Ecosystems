# Welcome to the 6th practical. 

Today we are going to investigate crop diversity around the world!
This practical is inspired by the publication of Leff et al. which dates back 20 years (to 2004), which focuses on the diversity in crops around the world. We will base ourselves on their methods, but we will work with very recent global crop datasets at very high spatial resolution and we will consider many more crops than considered by Leff et al. We'll analyze this crop data and calculate key indices such as the Shannon diversity index for agriculture (isn't that for biodiversity you say?). Furthermore, you'll investigate the vulnerability of food systems of different countries to climate change. Ready to get started? Let’s dive into the croplands!

This practical session is entirely in R and is intended to also demonstrate the ease with which R can handle spatial data. Start by creating a new script in R-Studio: Click on *New-file* at the top-left of R-studio and choose *R Script*, give it an appropriate name like: *prac6_agriculture*.

<br />

## Step 1: Describing data

<br />

### Problem setting

As various regions around the world experience the increasing effects of climate change, the stability of food production systems is at risk. Among the multifaceted impacts of climate change, two pressing concerns in the agricultural domain are the vulnerability of crops to extreme climate events and changing disease vectors that cause crop failures.
One buffer against such effects could be agricultural diversity, i.e. the amount of different crops cultivated in a certain area. Such diverse cropping systems can potentially buffer against the impacts of climate extremes by spreading risk across different species and varieties, which may respond differently to adverse conditions. Conversely, regions with limited crop diversity might face heightened vulnerability due to their reliance on a narrow range of crops, which could be decimated by specific climate threats.
One could wonder how the agricultural diversity differs around the world. 

<br />

What would be suitable geospatial parameters for such analysis? In this case, we are interested in the vulnerability on a country level. Thus we need global maps of agricultural output, in this case we will use agricultural maps from the Spatial Production Allocation Model [SPAM](https://mapspam.info/), which contains various crop data aggregated to a 10 sq.km. resolution. 


| Parameter  |  Value |
|---|---|
| Spatial extent | Global |
| Temporal extent | This is a static analysis, however, the vulnerability of climate change is projected to a few decades into the future |
| Cartographic unit |  Raster & Shapefile  |
| Dimensions |  ?  |
| Dimension description |   ?  |
| Temporal resolution | Crop data is updated to 2020, the yields are yearly estimates. |
| Spatial resolution | Crop data is aggregated to 10 sq.km. |
| Assumption / Hypothesis |  ?  |

<br />


*** 

<br />

### Ready R

One of the first things we need to do is install the packages needed to handle spatial data, keep in mind that we only need to install the packages once, therefore, copy the code below without the '#-comment' to your console and let R-studio install the package before moving to the next package:

```R
# Install the following packages one by one (this step is only necessary once).
# install.packages("terra")  # for handling spatial data
# install.packages("sf")
# install.packages("ggplot2")
# install.packages('dplyr', repos = 'https://cloud.r-project.org')
# install.packages("exactextractr")
# install.packages("plotly") 

# Load necessary libraries
library(terra)
library(sf)
library(ggplot2)
library(dplyr)
library(exactextractr)
library(plotly)
```

<br />

### Crop Area

Make sure you downloaded the files for this practical and unzipped them in your World Food Ecosystem folder (you don't secretly just dump these files randomly in your Downloads or Documents folder do you? 🧐). One of the folders in the *"WFE_prac6"* directory is called *"spam2010V1r0_global_physical_area"*, this dataset comes from the SPAM 2020 database and show the global physical area occupied by various crops. Check the contents of the directory, the documentation of the dataset can be found [here](https://www.dropbox.com/scl/fi/z468wh0d8abfohol991r5/Readme.txt?rlkey=el1w3zeykz909aadrx2xuck36&e=1&dl=0). Each file represents the total area per pixel occupied by a specific crop, with a total of 46 crops included.

In R, we can point to the directory that contains all the TIFF (raster) files for different crops. First set your working directory to your own path:

```R
# Set the working directory
setwd("?/WFE_prac6")

# Specify the directory containing the tiff files
directory <- "spam2020V1r0_global_physical_area"
```

> 🔍 **Review 1**. What type of crops does the SPAM 2020 database contain? <br />
> <br />
> • Cereals <br />
> • Seeds <br />
> • Fruits <br />
> • Vegetables <br />
> • All of the above <br />


<br />
<details>
<summary>Answer Review 1. (click on this to show/hide the answer)</summary>
All of the above. There are 46 crops ranging from Bananas to Yams.
</details>
<br />

<br />

Following this, we can list all the files that end with *_A* (an extension indicating the total area) and *.tif*. These TIFF files represent the physical area of all cropping systems, both irrigated and rainfed. In R it is possible to use the function *list.files()* to list all the files of a directory:

```R
# List files ending with _a.tif
tiff_files <- list.files(directory, pattern = "_A\\.tif$", full.names = TRUE)
```

<br />

Now we read all the tif files into raster files, this we do by applying (lappy) the function rast (which reads raster files) to all the elements in are list, called tiff_files. 
In R, the lapply function is used to apply a function to each element of a list. The lapply function is particularly useful for performing repetitive operations on the elements of a list without needing to write explicit loops.

```R
# Read all the tiff files into a list of raster objects
# i.e. we apply the function (rast) to all the tiff files
raster_list <- lapply(tiff_files, rast)

# You can now work with this list of rasters, e.g. plot bean
# cultivation (which is the 4th raster in raster_list)
plot(raster_list[[4]], main = names(raster_list[[4]]))
```

<br />

> 📝 **Question 1**. Instead of Cassava, plot the Pigeon Pea from the TIFF files. In which country is the Pigeon Pea mostly cultivated? <br />
> Hint: Use the documentation (check the link earlier at Crop Area) and investigate the *raster_list* in R. <br />
> <br />
> • China <br />
> • India <br />
> • USA <br />
> • Nigeria <br />
> • Egypt <br />

<br />

These raster files, encapsuled in the *raster_list* denotes the crop area in every grid cell. However, we might also simply want to know whether a pixel contains the cultivation of a specific crop or not. The following code checks whether or not there is any area cultivated per cell.

```R
# To convert all the raster files to binary files we need to write our own function

# Function to convert each raster into a binary raster
binary_raster <- function(r) {
  # Apply the condition: 1 if value > 0, otherwise 0
  r[is.na(r)] <- 0
  r_binary <- ifel(r > 0, 1, 0)  # `ifel` from terra package
  return(r_binary)
}

# Now we can Apply the binary raster function to each raster in the list
binary_raster_list <- lapply(raster_list, binary_raster)
```

<br />

Rather than just plotting the raster, it would be helpful to include an outline of the world. The folder you downloaded contains a shapefile with the boundaries of all countries on Earth. Import this shapefile into R with the following code:

```R
# Let's load a shapefile with all the countries
shapefile_path <- "world-administrative-boundaries/world-administrative-boundaries.shp"
shapefile_data <- st_read(shapefile_path)

# Transform the cassava raster to a data.frame in order to plot it together with the shapefile
raster_df <- as.data.frame(raster_list[[4]], xy = TRUE, na.rm = TRUE)

# Plot both the raster and the country outlines
ggplot() +
     geom_raster(data = raster_df, aes(x = x, y = y, fill = spam2020_v1r0_global_A_CASS_A)) +
     geom_sf(data = shapefile_data, fill = NA, color = "black") +
     coord_sf(xlim = c(-180, 180), ylim = c(-90, 90)) +
     scale_fill_viridis_c() +
     theme_minimal() +
     labs(title = "Binary raster of cassava",
          fill = "Raster Value")
```

> 📝 **Question 2**. Instead of Cassava, plot Wheat from the TIFF files with the country overlay. Which of the following statement is true?
> <br />
> • A lot of wheat is produced in the North of Brazil. <br />
> • Wheat in Argentina is mostly produced in the South. <br />
> • There is almost no wheat production in China.  <br />
> • The South-East of India barely produces any wheat. <br />

***

<br />

### Agricultural Richness

Given the binary files that we calculated earlier, the 'richness' of agricultural crops can be determined by simply adding all binary rasters together:

```R
# add all the binary rasters to just get a count of the amount of crops in a certain pixel: 
raster_stack_bin <- rast(binary_raster_list)
summed_rasterbin <- sum(raster_stack_bin, na.rm = TRUE)
plot(summed_rasterbin, main = "Summed bin Raster (NAs Ignored)")
```

<br />

> 📝 **Question 3**. Given this map, what is the maximum amount of different crops cultivated in a grid cell? <br />
<br />

Similarly, we can also calculate the total area of cropland in each pixel. Instead of adding the binary files, we just sum the original rasters that contains the crop area for each crop.

```R
# add all the raw rasters to get a total area of cropland
raster_stack <- rast(raster_list)
summed_raster <- sum(raster_stack, na.rm = TRUE)

croparea_df <- as.data.frame(summed_raster, xy = TRUE, na.rm = TRUE)

ggplot() +
    geom_raster(data = croparea_df, aes(x = x, y = y, fill = sum)) +
    geom_sf(data = shapefile_data, fill = NA, color = "black") +
    coord_sf(xlim = c(-180, 180), ylim = c(-90, 90)) +
    scale_fill_viridis_c(limits = c(0, 10000)) +
    theme_minimal() +
    labs(title = "Total crop area per cell in hectares",
         fill = "Crop area in ha")

#the summed raster file is now the SUMFik. We need this to calculate the Fi,k as defined by Leff et al, i.e. the relative crop fraction for each of the crops.
```

> 📝 **Question 4**. Leff et al (2004) talks about "...major crop belts throughout the world." Given the figure you made above, which areas would you indicate as major crop belts? Select all the answers that show a high amount of crop area. <br />
<br />

<br />

Now that we have the total crop area, we can calculate the relative crop fraction for each of the crops. In the paper by Leff et al. (2004) this is being indicated as *Fi,k*. i.e. the crop area of a certain crop in a pixel compared to the total crop area in that pixel (the latter is denoted by SUMFik, or summed_raster in our R code). Let's calculate Fi,k for all crops in all pixels:

```R 
#now we can calculate the relative fraction of all the files
# we will have to write a Function to divide each raster by the summed raster, handle NAs in the summed raster
divide_raster <- function(r, summed) {
  result <- r / summed
  result[is.na(summed)] <- NA  # Ensure that pixels with NA in summed raster remain NA
  return(result)
}

Fik_list <- lapply(raster_list, divide_raster, summed = summed_raster)
plot(Fik_list[[4]], main = "relative fraction")
```

> 📝 **Question 5**. Given this map, what is the maximum amount of different crops cultivated in a grid cell? <br />
<br />

<br />

It is time to step away from the Agricultural Commodity Diversity index as defined by Leff. Instead, we will calculate our own diversity index, inspired by the Shannon index for biodiversity. Remember the formula for the Shannon Index? It calculates the diversity of species in a particular community:

$H = -\sum_{i=1}^{n} p_i \log(p_i)$

where:
H: Shannon index
Σ: Greek symbol for “sum”
ln: Natural log
pi: The proportion of community i

<br />

The lower the value of H, the lower the diversity and vica versa. Whereas a value of H = 0 indicates a community that only has one species. In the lectures you learned that *pi* is the proportion a certain species and H representing the diversity in species. In this case we are going to apply the formula to calculate agricultural diversity, in which *pi* is the proportion of a certain crop. Originally Claude Shannon created the index to represent entropy in the field of information theory. Nowadays it is widely used by biologist to indicate biodiversity. This is a fine example of the benefit of interdisciplinary collaboration, in which the knowledge of one discipline can very well create innovative approaches in another!

Now calculate the Shannon Index for our crop dataset:


```R
#now we can start calculating the shannons index
# Function to calculate Fik * log(Fik) while handling NA values
calculate_log_fraction <- function(r) {
  # Replace NA with 0 before calculating log (to avoid -Inf)
  r[is.na(r)] <- 0
  # Calculate Fik * log(Fik), handling log(0) as 0
  result <- ifel(r > 0, r * log(r), 0)  # Use ifel to avoid log(0) issue
  return(result)
}
# Apply the calculation to each raster in Fik_list
log_fraction_list <- lapply(Fik_list, calculate_log_fraction)
log_fraction_stack <- rast(log_fraction_list)
Shannon <- sum(log_fraction_stack, na.rm = TRUE)*-1
plot(Shannon, main = "shannon index")
```

<br />

This Shannon map now shows the agricultural diversity expressed as a ‘Shannon’s index’. Now we can couple this to a countries vulnerability to the effects of climate change on food security. For this, we base ourselves on the gain-new ranking, which [ranks countries](https://gain-new.crc.nd.edu/ranking/vulnerability/food) in terms of their vulnerability to climate change with a focus on the dimension of food. 
The Food score captures a country’s vulnerability to climate change, and includes metrics of sensitivity, exposure and adaptive capacity. Indicators include: projected change of cereal yields, projected population growth, food import dependency, rural population, agriculture capacity, and child malnutrition. Pag 16 of the ND-gain Technical Report gives you an idea about the variables available, we will look at overall vulnerability of countries to climate change regarding food.

```R  
food <- read.csv("nd_gain_countryindex_2024/resources/vulnerability/food.csv")
```

<br />

11.	this is a CSV file, but of course, we also want to check the spatiality (we want to link these values to the the crop diversity. To do this, we will need to import a shapefile of the world, so that we can merge the csv (non-spatial dataset) with the shapefile (a spatial dataset). Later on, this shapefile can then be used to extract the raster values we calculated earlier.

```R 
#now we have to couple the 'food' variable with the shapefile_data
head(shapefile_data)

# We see that the iso3 and ISO3 comes back in both: let's merge them. 
merged_data <- shapefile_data %>%
  left_join(food, by = c("iso3" = "ISO3"))
```

<br />

12.	Finally, now that we have a shapefile with all the vulnerability scores, we can extract the zonal statistics (e.g. the median Shannon index) for each country;

```R
#now we extract zonal statistics
library(exactextractr)
zonal_stats <- exact_extract(Shannon, merged_data, fun = "median")
merged_data$shannon_median <- zonal_stats
```

<br />

13.	This final data can now be used for visualization and statistics.

In this case we are not interested in a regression analysis, because we are not predicting 

```R 
#question for quiz: how much of the variation in the  food vulnerability index can be explained by shannon median index. 
plot(merged_data$X2022, merged_data$shannon_median)

data_to_plot <- merged_data %>%
  select(name, continent, shannon_median, X2022) %>% na.omit()
#remove NAs

interactive_plot <- plot_ly(data = data_to_plot,
                            x = ~shannon_median,        # X-axis
                            y = ~X2022,                 # Y-axis
                            type = 'scatter',           # Scatter plot
                            mode = 'markers',           # Use markers
                            color = ~continent,
                            text = ~paste("Name:", name), # Tooltip text
                            hoverinfo = 'text', # Show tooltip on hover
                            marker = list (size = 10))        
interactive_plot <- interactive_plot %>%
  layout(title = "Interactive Plot of Zonal Stats vs X2022",
         xaxis = list(title = "Shannon Median"),
         yaxis = list(title = "X2022"),
         legend = list(title = list(text = "continent")))

# Show the plot
interactive_plot
htmlwidgets::saveWidget(interactive_plot, "interactive_plot.html")
#questions: which countries have a realtively high vulnerability score and a low shannon index?
```

<br />


















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


```R

```





<br />
<br />

**Continue with the next step, in which you will calculate agricultural richness and diversity**

<nav>
  <ul>
    <li><strong>Step 1: Crop Area</strong></li>
    <li><a href="agriculture.html">Step 2: Agricultural Diversity</a></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>


