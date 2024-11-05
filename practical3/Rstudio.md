# Step 3: Analysing biodversity over elevation & NDVI

So... we want to import the csv files we just made and downloaded in GEE to R, where we will do some basic data processing. 

The camera_elevation.csv and camera_ndvi.csv should be in your google drive: these files you can download by navigating to [google drive](https://www.google.com/drive/). Download both files and move them to a directory in which you want to do your work for this practical. 

For example: 

<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/183601893-9722fa4c-c801-41c9-932b-008a2568924e.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

<br />


### Great: so now we can shift to Rstudio

Open Rstudio. If all goes well, it should look something like this: 

![Rstudio_Home](https://user-images.githubusercontent.com/89069805/131488428-fe3591d5-2cd0-4107-8dd1-84b4aafe883b.png)

With on the left the console (where you execute single commands), on the top right a panel where you can see the variables in the 'environment'. In the bottom right you can see the plots you make, but also e.g. the packages that are installed, or the help function. 

We now have to do two things: 

1. Import the two dataset
2. Build an R script to do some basic analysis 

<br />

### Importing the data and building a script

The first thing you need to do is build an empty R file where you will later put the commands you'll use for analysis. 

<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/131489386-bf1b4aee-c1bc-42d3-a1fa-afc8397c0b7e.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

<br />

We can add simple code, as illustrated in the video below 

<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/131489891-e0210044-50ad-4361-9fea-1b8e095dbbc7.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

<br />

Now let's code ourselves: let's first import the data. 

```r
# this is the file where we will list the commands needed for the analysis
# using a hashtag = comments 

#first let's see in which directory R is working now: 
getwd()

#this is probably not the directory you want (the directory where you stored your csv files). 
#so we need to change this. 
#in my case this is: 
setwd("C:/Users/lhjacob/OneDrive - UvA/UvA-Education/teaching/WFE/coursedocs2022/PRACT2") #note that R requires forward slashes in your path name

#check if it worked: 
getwd()


#let's now import the data, starting with the ndvi dataset: 
ndvi<-read.csv("camera_ndvi.csv")
#check out the dataset: which two columns are of interest to us? 
View(ndvi)

# now we can build simple code, to do some analysis
# first, let's plot
plot(x=ndvi$mean, y=ndvi$rich, xlab="NDVI", ylab = "species richness")

# then build a regression: 
regression_ndvi <- lm(ndvi$rich~ndvi$mean, data = ndvi)
summary(regression_ndvi)
# is the relationship significant? 

# plot regression line on scatterplot
abline(regression_ndvi, col="red", lwd=2)

```

<br />

After the execution, you should get something like this: 
![ndviplot](https://user-images.githubusercontent.com/89069805/183605802-32e69e96-8cb2-4453-8422-e46762f9ce77.png)

<br />

If you get a similar plot (it is probably not exactly the same), well done!
Let's warm up our own coding skills: repeat the above exercise, but now using the elevation data that you have gathered yourself. 

To start you on the right path, you can use the code below as a starting point. 
A *'?'* means you'll have to fill in the blank based on what you have learned above (so in analogy to the NDVI exercise) . 

```r

#let's now import the data, starting with the elevation dataset: 
elevation <-read.csv("?")
#check out the dataset: which two columns are of interest to us? 
View(?)

# now we can build simple code, to do some analysis
# first, let's plot
plot(x=?, y=?, xlab="?", ylab = "species richness")

# then build a regression: 
? <- lm(?~?, data = ?)
summary(?)
# is the relationship significant? 

# plot regression line on scatterplot
abline(?, col="red", lwd=2)

```

***

<br />

### More Quizzz-Time!

> 📝 **Question 6**. When looking at the two linear models: one with NDVI as a predictor for species richness and one with elevation as a predictor for species richness, which statement is correct?
> <br />
> • The model predicting species richness by NDVI is significant. <br />
> • The model predicting species richness by elevation is significant. <br />
> • Both models are significant. <br />
> • Neither model is significant. <br />

<br />

> 📝 **Question 7**. What is the R-square value of the regression that regresses species richness with elevation? (round to 0.01)

<br />

> 📝 **Question 8**. The paper by Robinson et al. 2018 (in the canvas module 5) deals with Tree Diversity in the same study area. Which gradients/spatial patterns do they mention in their description of the study area which might influence (plant) biodiversity?

<br />

> 📝 **Question 9**. Robinson et al 2018 mention both the linear decrease of diversity with elevation, but also mention the possibility for a peak at mid-elevation. Throughout the paper, they give one actual reason and one reason stemming from poor sampling design: what are both these explanations for a linear decrease of diversity with elevation?
What is their explanation for a mid-elevation peak of diversity?

<br />

> 📝 **Question 10**. Finally Robinson et al mention that slope angle might play a role in controlling diversity. Why do the authors argue are steeper slopes typically less diverse?

<br />
<br />

<nav>
  <ul>
    <li><a href="intro.html">Step 1: Problem Description</a></li>
    <li><a href="QGIS.html">Step 2: Extracting Data</a></li>
    <li><strong>Step 3: Data Analysis</strong></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>

