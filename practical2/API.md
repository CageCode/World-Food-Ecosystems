# Step 2: Data Exploration  

The first thing to do is to understand the data you are working with. 

In the folder you downloaded on the previous page there are 3 types of files: 
- "Lakes_Reservoirs_database.csv": this is the original lakes database: each line represents a measurement of EC at a given time and location. Each location has an X and Y and a unique location name. You'll see that for a lot of points we have multiple measurements, spanning over multiple years. In our case, we want to compare data from 1980-1990 to those measured in the timespan of 2005-2015. We only want to consider those points who have data for at least 5y in both epochs. If we filter only these points, and then average all the measurements for these points we end up with the pre-processed csv file: 
- "lakesaverage.csv": this file thus contains only those points which have sufficient measurements in both epochs. First, we calculated for each station the average EC in each year: those stations which have at least 5 years of measurements in both epochs are considered: for those stations, the average over the two epochs was calculated: do you see in which columns these values now appear? What is the DIFF column? 
- "lakes.cpg,dbf,prj,shp,shx": this is the shapefile that basically contains the information of lakesaverage.csv but now in a spatial data object. 

<br />
<br />

### Importing the data and building a script

Let's explore this data in Rstudio first. Find Rstudio on your computer and open the program. 
It might be some time ago since you last used Rstudio, so if all goes well, it should look something like this: 

![Rstudio_Home](https://user-images.githubusercontent.com/89069805/131488428-fe3591d5-2cd0-4107-8dd1-84b4aafe883b.png)

<br />

With on the left the console (where you execute single commands), on the top right a panel where you can see the variables in the 'environment'. In the bottom right you can see the plots you make, but also e.g. the packages that are installed, or the help function. 

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

Now let's code ourselves: let's first import the data. Therefore, we need to put the data on a place R can work with it.

```r
# this is the file where we will list the commands needed for the analysis
# using a hashtag = comments 

# first let's see in which directory R is working now, using the command getwd()
# This stands for 'get working directory': 
getwd()
```

<br />

This is probably not the directory you want (the directory where you stored your csv files). 
So we need to change this using the command setwd() - setting the working directory:

```r
# Set your working directory to your WFE folder were you also
# put the downloaded data. In my case this is: 
setwd("C:/Users/dkooij/WFE/PRACT2")
# note that R requires forward slashes in your path name

# check if it worked: 
getwd()

# let's now import the data, starting with the ndvi dataset: 
lakesaverage <- read.csv("lakesaverage.csv")

#check out the dataset: which two columns are of interest to us? 
View(lakesaverage)
```

<br />

The original database might take a while to load. The lakesaverage.csv should load quickly. 

> 🔍 **Review 1**. Why does it take a while to load the original database? 

<br />
<details>
<summary>Answer Review 1.</summary>
The original database contains all the measurements for every timestep for every lake, quite some data!
</details>
<br />

***

<br />

Now, let's do a first check: how well are the EC values in the second epoch (2005-2015) associated with those in the first epoch (1980-1990)?  

```r
plot(lakesaverage$EC19801990, lakesaverage$EC20052015) # give the axis correct names

#theoretically, if no change occurs, they should more or less lie on the 1:1 line (the diagonal): let's plot this: 
abline(a=0, b=1,col="red")
legend(x = 2, y=1000, legend = c("diagonal line"), col=c("red"), lty=1)
```

Because of the two outliers and the legend covering the data, it's a bit difficult to see what goes on with the majority of the points... 

> 📝 **Question 1**. Build the same plot, but now limit the x and y axis to '2500', give the axis appropriate names (what is the unit?) and adjust the legend so that it still falls within the plot. Personalize this graph by adding a title with your name and load this graph to the canvas quiz.

<br />


If you don't know how to do this by heart, google is your best friend. I found e.g. [this resource](https://statisticsglobe.com/set-axis-limits-in-r)


> 🔍 **Review 2**. Visually, do you conclude that most points have increased or decreased in their EC content?

<br />
<details>
<summary>Answer Review 2.</summary>
As you can see in the plot, most lakes increased in EC values between ~1980 and ~2015
</details>
<br />



OK, now on to the GEE processing: let's extract data on water deficit and the biomes for each point [in the next exercise](Mapping.html)


<nav>
  <ul>
    <li><a href="intro.html">Step 1: Problem description</a></li>
    <li><strong>Step 2: Data Exploration</strong></li>
    <li><a href="Mapping.html">Step 3: </a></li>
    <li><a href="Analysis.html">Step 4: </a></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>
