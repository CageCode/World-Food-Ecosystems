# Step 2: Extracting the elevation data

This exercise is very similar to what we have done in the previous exercise (but easier): earlier today, you selected one image from an image collection, and then calculated the ndvi (this is also an image). After that you performed following lines: 

![Picture3](https://user-images.githubusercontent.com/89069805/179993101-c902ece6-2c3b-4efc-a3f7-23a1f486dfc1.png)

<br />

In earlier practicals, you have learned how to load a DEM (which will be an image). 
To now extract elevation values for the points, you can use what you have learned today and in the previous course. 

With your newly acquired skills, perform the following steps: 
1. import the DEM like you have done in the first practical
2. adjust the code above, but only the blue parts are necessary to adjust: 
    - line 1: here you should define the DEM image
    - line 2: here you clip the variable you just defined in line 1
    - line 3: here you make a variable that is used to visualize the dem later: replace the blue elements to whatever makes sense
    - line 4: Map your dem image with the visualization parameter you just made, also give it a new title
    - line 6: make a new variable where the elevation values are added to the table of points
    - line 7: export this new table, and also change its description
    
<br />


> - If you are stuck, make sure that you check again how you imported/visualized the DEM in the first practical, and that you understand everything you have done previously today. <br />
> - If you cannot find the answers there, feel free to ask your fellow students, search on Google or request some help from the teacher. <br />

Once you also have the elevation, export the two tables to your google drive: 


<video style="width:100%" controls>
  <source src="https://user-images.githubusercontent.com/89069805/179994188-ac967a3e-271f-4878-9f54-c2c5c17669ad.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

<br />

You can now find the .csv files in your google drive, and you can download them there. 

<br />

Let's try to import this data into Rstudio and build a simple  [statistical regression](Rstudio.html) to see if species richness is associated with elevation or NDVI

<nav>
  <ul>
    <li><a href="intro.html">Step 1: Problem Description</strong></li>
    <li><strong>Step 2: Extracting Data</strong></li>
    <li><a href="Rstudio.html">Step 3: Data Analysis</a></li>
    <li><a href="../"><b>Back to Overview Page</b></a></li>
  </ul>
</nav>



