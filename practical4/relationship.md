# Data Analysis

<br />

Now we are going to do the data analysis in R. First we have to set the working directory to the correct folder and import the data. Force yourself to import the data through code (with the read.csv function) instead of manually importing the data. The following code combines the different datasets into one dataframe (*df*) which we will use from there on.

```R
# Set the working directory to YOUR folder
setwd("C:/Users?/?")

# Load in the biodiversity, geodiversity and climate data
geo <- read.csv("points_geo.csv", header = TRUE, sep = ",")
rich <- read.csv("points_rich.csv", header = TRUE, sep = ",")
rare <- read.csv("points_rare.csv", header = TRUE, sep = ",")
temp <- read.csv("points_temp.csv", header = TRUE, sep = ",")
prec <- read.csv("points_prec.csv", header = TRUE, sep = ",")

# Create dataframe from data
df <- data.frame(geo$mean, rich$mean, rare$mean, temp$mean, prec$mean)
df <- na.omit(df)
colnames(df) <- c("geo","rich","rare","temp","prec")
```

<br />

### Biodiversity over geodiversity



```R
# Relationship between geodiversity and species richness
richgeo <- lm(rich ~ geo, data = df)
summary(richgeo)
plot(rich ~ geo, data=df)
abline(richgeo)

# Relationship between geodiversity and pecies rarity
raregeo <- lm(rare ~ geo, data = df)
summary(raregeo)
plot(rare ~ geo, data=df)
abline(raregeo)
```


```R
# Relationship between temperature / precipitation and species richness
richtemp <- lm(rich ~ temp, data = df)
summary(richtemp)
plot(rich ~ temp, data=df)
abline(richtemp)

richprec <- lm(rich ~ prec, data = df)
summary(richprec)
plot(rich ~ prec, data=df)
abline(richprec)
```

<br />

> 📝 **Question **. Look at the plot between precipitation and species richness. Do you think a linear model is fitting for this data?
> <br />
> • Yes, there is a clear significant relationship between the two variables. <br />
> • No, the relationship between the variables appears to be more logarithmic.  <br />
> • Yes, the R-squared value shows that the majority of the variation is explained by this model. <br />
> • No, the slope coefficient (~0.02) is too small to confirm a linear relationship. <br />



Now also investigate the relationship between temperature and precipitation with species rarity. Write this code yourself by copying the code that analyzes the species richness with the climate data and adjusting it to species rarity.

> 📝 **Question **. 
> <br />
> • In soil and geology only <br />
> • In geomorphology and soil only <br />
> • In geology and geomorphology and soil <br />
> • In all components of geodiversity <br />


```R
# With precipitation and temperature
richgeo_tempprec <- lm(rich ~ geo + temp + prec, data = df)
summary(richgeo_tempprec)

# With precipitation and temperature
raregeo_tempprec <- lm(rare ~ geo + temp + prec, data = df)
summary(raregeo_tempprec)
```



<br />
<br />


<nav>
  <ul>
    <li><a href="geodiversity.html">Step 1: Problem Description</a></li>
    <li><a href="geodiversity.html">Step 2: Geodiversity</a></li>
    <li><strong>Step 3: Data Analysis</strong></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>


