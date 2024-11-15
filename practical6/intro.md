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

<br />



**Continue with the next step, in which you will calculate agricultural richness and diversity**

<nav>
  <ul>
    <li><strong>Step 1: Crop Area</strong></li>
    <li><a href="agriculture.html">Step 2: Agricultural Diversity</a></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>


