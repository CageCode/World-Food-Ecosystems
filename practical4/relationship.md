# Data Analysis

<br />


```R
setwd("C:/Users/danie/Documents/World Food Ecosystems")

# Load in data
geo <- read.csv("points_geo.csv", header = TRUE, sep = ",")
rich <- read.csv("points_rich.csv", header = TRUE, sep = ",")
rare <- read.csv("points_rare.csv", header = TRUE, sep = ",")
temp <- read.csv("points_temp.csv", header = TRUE, sep = ",")
prec <- read.csv("points_prec.csv", header = TRUE, sep = ",")
ghm <- read.csv("points_ghm.csv", header = TRUE, sep = ",")

# Create dataframe from data
df <- data.frame(geo$mean, rich$mean, rare$mean, temp$mean, prec$mean, ghm$mean)
df <- na.omit(df)
colnames(df) <- c("geo","rich","rare","temp","prec","ghm")


# Multiple linear regression richness
# With only geodiversity
richgeo <- lm(rich ~ geo, data = df)
summary(richgeo)
plot(rich ~ geo, data=df)
abline(richgeo)

# With precipitation and temperature
richgeo_tempprec <- lm(rich ~ geo + temp + prec, data = df)
summary(richgeo_tempprec)
plot(rich ~ geo, data=df)
plot(rich ~ temp, data=df)
plot(rich ~ prec, data=df)


# Multiple linear regression rarity
# With only geodiversity
raregeo <- lm(rare ~ geo, data = df)
summary(raregeo)
plot(rare ~ geo, data=df)
abline(raregeo)

# With precipitation and temperature
raregeo_tempprec <- lm(rare ~ geo + prec + temp, data = df)
summary(raregeo_tempprec)
plot(rare ~ geo, data=df)
plot(rare ~ temp, data=df)
plot(rare ~ prec, data=df)
```



<br />
<br />


<nav>
  <ul>
    <li><strong>Step 1: Problem Description</strong></li>
    <li><a href="geodiversity.html">Step 2: Geodiversity</a></li>
    <li><a href="relationship.html">Step 3: Data Analysis</a></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>


