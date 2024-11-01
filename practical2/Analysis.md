# Step 4: Analysis

<br />

Now that we have extracted the biome classification and climate variables for our lake points, we can import them in R and start the analysis. First import the data in Rstudio.

```r
#loading the data: adjust the path name and the file name to your specific file: 
lakes_prec <- read.csv("lakes_prec.csv")
lakes_biomes <- read.csv("lakes_biomes.csv")
lakes_temp <- read.csv("lakes_temp.csv")
```

<br />

Now we can plot the data and ask ourselves the following question: what is the relation between precipitation and salinity.

```r
# Plot this graph, and adjust the labels of the axis
plot(lakes_prec80s$EC19801990 ~ lakes_prec$mean)
```

<br />

It is hard to see any relationship because of the two outliers that we noticed earlier. We'll save you the trouble of figuring out why these places have such high EC values. We have already done that for you, it turns out it is because these water readings were taken after road salt was spread on the roads in winter. We have to remove these outliers in order to perform any meaningful analysis. The easiest way is to merge the three datasets and remove it only once instead of repeating it three times. The following code does that for you:

```r
lakesall<-lakes_prec

colnames(lakesall)[9]<-"annualprec"

lakesall<-merge(lakesall, lakes_temp, by = 'system.index')
colnames(lakesall)[18]<-"meanannualtemp"


lakesall<-merge(lakesall, lakes_biomes, by = 'system.index')
colnames(lakesall)[27]<-"biome"

lakesall<-lakesall[,c("EC19801990", "EC20052015", "biome", "annualprec", "meanannualtemp")]


#now let's filter out the extremely high values (explain that this is potentially due to road treatment)
filtered_lakesall <- lakesall[lakesall$EC19801990 <= 8000, ]
```

***

<br />

### Inspect the relationship

Now that we filtered the data, we can investigate the relationships. Make visualizations of the salinity and the three variables: precipitation, temperature and biomes. Make sure to change the "?" in the following code with correct R-code:

```r
# Visualize the relationship with precipitation
plot(filtered_lakesall$EC19801990 ~ filtered_lakesall$annualprec)

# Visualize the relationship with mean annual temperature
plot(filtered_lakesall$EC19801990 ~ filtered_lakesall$?)

# Visualize the relationship with the biomes
boxplot(filtered_lakesall$EC19801990 ~ filtered_lakesall$biome)
```

> 📝 **Question 2**. Why does biome 16 have such high EC-values compared to the other biomes?

<br />

Interesting! The EC-values seems to decrease with increasing precipitation and increase with higher temperatures, does that make sense you think?
Let's determine if these relationships are also statistically significant, create a linear model that explains the salinity by the temperature and precipitation.

```r
#simple linear model
linmod <- lm(filtered_lakesall$EC19801990 ~ filtered_lakesall$annualprec + filtered_lakesall$meanannualtemp)
summary(linmod)
```

<br />

> 📝 **Question 3**. Are these predictor variables annual precipitation and mean annual temperature significant in this model?

<br />

> 📝 **Question 4**. How much variation is explained by this model?

***

<br />

### Human Influence

Now the precipitation and temperature clearly have a great influence on the salinity of the water. However, humans are also able to substantially change the abiotic nature of their surroundings. As we have seen earlier, the outliers in our model were due to the application of road salt to keep the roads snow-free.

> 🔍 **Review 5**. Which other human activities would change the salinity levels of the environment.

<br />
<details>
<summary>Answer Review 5.</summary>
There are many right answers to this question. To name a few of these activities: Mining releases a lot of mine effluent that often contains high EC levels, agriculture can increase the salinity through irrigation (often in warm areas), wastewater might increase the salinity etc.
</details>

<br />

> 📝 **Question 5**. Read the paper published in Nature by Thorslund et al. 2001, which human drivers have a significant impact on the salinity according to their research? <br />
> Tip: Figure 4 and table 1 can help you out with this question.

***

<br />

### Salinization increase: in which biomes are the lakes situated with the most increase in salinity? 

This is slightly different from the plot/model above, as biomes are a categorical variable (they represent different classes). A way in which we can visualize this is to plot the difference in salinity for each biome seperately in a boxplot: 

```r
#now which biomes have seen increase in salt? 
boxplot(lakes_biomes$DIFF ~ lakes_biomes$mode, outline=F)
abline(a=0, b=0, col="purple") #why do we add this line?
```

<br />

All lakes in biome 13 see an increase in salinity. Which biome is 13? How many lakes are there in this biome? Where are they located? 

The biome codes can be found in the data catalogue in GEE when searching for the original dataset we used to extract the biome information (openLandMap).

To find out how many lakes are in biome 13 and where they are located, let's first filter them out. Based on the codes above, you should be able to complete the code below: 

```r
biome13<-?
```

<br />

In R there is also quite some nice visualization possibilities: let's plot the points on a open street map background and color the dots according to the difference in salinity. 

```r
#install.packages('leaflet') 
library(dplyr) #install.packages("dplyr") if your version of R does not have it installed yet
library(leaflet) #install.packages("leaflet") if your version of R does not have it installed yet
library(viridis) #install.packages("viridis") if your version of R does not have it installed yet
pal <- colorNumeric(palette = "YlOrRd", domain=biome13$DIFF)
leaflet(biome13) %>% addTiles() %>%
  addCircleMarkers(lng = biome13$X, 
             lat = biome13$Y, 
             color = ~pal(biome13$DIFF))
```

<br />

The resulting visualization should look something like this: 
![fig2](https://user-images.githubusercontent.com/89069805/186885770-32c49b56-6c3b-495d-92e7-82fed71cece6.png)

Do you have any idea why the salinity of the lakes in biome 13 all went up? What pattern do you observe here?
Now we could go even further and investigate the spatial and temporal trends in human activities and salinization and download this data as well. However, that's it for today... you made it to the end of this practical. Congrats!

<br />

<nav>
  <ul>
    <li><a href="intro.html">Step 1: Problem description</a></li>
    <li><a href="API.html">Step 2: Exploring</a></li>
    <li><a href="Mapping.html">Step 3: Mapping</a></li>
    <li><strong>Step 4: Data analysis</strong></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>
