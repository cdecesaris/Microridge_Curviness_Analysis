# Microridge Curviness Analysis

This program takes pixel coordinate data from images of microridges and analyzes microridge 
curviness. Microridges are small ridges formed by actin protrusions on the surface of skin cells 
which are thought to function in mucus retention. These microridges are imaged on the surface 
of zebrafish skin cells by using LifeAct-GFP transgenic lines, making microridges 
fluorescent. Another member of the lab took these images and has a program that deconvolutes 
this data and turns it into a series of coordinates for each ridge. This program uses that 
existing data and returns an excel file containing a measure of curviness for each microridge.

### Introduction
Microridges are actin protrusions on the surface of skin cells. Our lab is interested in the development and function of these ridges in larval zebrafish. Images of ridges can be taken by labeling actin within the ridges with LifeAct-GFP, and imaging skin cells uing confocal microscopy. Some manipulations during development seem to increase the curviness of these ridges, so there was a need for an objective way to measure the "curviness" of the ridges of a cell. Therefore, I wrote the included scripts which output a meaure of curvature of each ridge, both as a simple function of the total ridge length divided by the distance between the start and end points of each ridge, and as the sum of the mathematical curvature at each point divided by the total length of the ridge.

Additional information and images can be found at the [lab website](https://www.mcdb.ucla.edu/Research/Sagasti/Sagasti_lab_home.html), under Projects/Skin cell morphogenesis.

###  Program Workflow

There are 3 main scripts included in this repository, along with a master script which can run the program in Hoffman2.

#### Concatenate.sh
This is a bash script which concatenates the input csv files, and outputs a single csv with the concatenated information. It also adds a new column at the beginning of the csv storing each file name.

#### Point_Simplifier.rmd
This is an R script which uses the package deldir to perform a Voronoi tesselation with the input points (orange). It then uses rgeos to find the insersections of the polygons within the pixel boundaries (blue)

The packages TSP and PairViz are used to perform an open Traveling Salesman Problem algorithm to order these points along the ridge (green), rather than by x value.

It loops this process over every ridge and cell, and saves the information as a csv which is the input for Curvature_Math.rmd

<img src="https://github.com/hamarkovic/Microridge_Curviness_Analysis/blob/master/Vignette/Voronoi%20after%20Viz%20green.gif">

#### Curvature_Math.rmd

This script calculates slope and acceleration at each point of the ridge, and then uses the following formula to calculate curvature at each point. It adds these curvature values together, and then divides by the length of the ridge.

<img src="https://github.com/hamarkovic/Microridge_Curviness_Analysis/blob/master/W6_curvature_fomula.png" width="50%">

It also outputs a more simple measue of curvature obtained by dividing the total ridge length by the distance between the endpoints of the ridge.

### Program Usage

#### Requirements
You need to install these packages, using the install.packages(*package*) command:  
 * deldir
 * rgoes
 * sp
 * tidyverse
 * TSP
 * PairViz
 * graph

I wrote the program using the 3.3.2 version of R.  

The data must:
 * be in the form of x and y coordinates of the pixels of the skeletonized ridge, and with a column for ridge number.
 * be inputted in csv format.
 * There cannot be data files with identical names.
 
#### Usage Instructions
These scripts can be individually run on your computer, or you can use the master script to run all of them.

All input files must be in the "Files_to_analyze" folder on hoffman; or, change the source of the files in the code, and run it on your computer with these files in a folder of your choosing.

#### Outputs
The program output a single csv file. The two columns of this file contain the cell ID and number of the ridge within that cell. The third column contains the ridge length. The fourth column contains a simple measure of curvature obtained by dividing the total length of the ridge by the distance between the endpoints of the ridge. The fifth column contains a curvature measure derived by calculating the curvature at each point using the first and second derivates, adding these values for each point of the ridge, and didviding by the length of the ridge.

#### Vignette
You should be able to run this on Hoffman; however, you would need to install many libraries, and it'll take too much time to do during discussion.
1. To run this test file, you first need to follow the directions for how to run the program, and install the required packages.
2. Change the input in the file Point_Simplifier.rmd (read.csv command) to be the file "Ridge5_test.csv" in the appropriate location on your computer.
3. Change the ouput of the file (write.csv command) to be in the apporpiate location on your computer.
4. Run the file Point_Simplifier in R.
5. The ouput will be the file "Points5.csv".
6. If you graph the points of the input and ouput, the input should look identical to the orange points in the file "Voronoi after Viz green.gif", and the output should look identical to the green points.

### Author
* Hannah Markovic
    * incoming Biology PhD student at the University of Oregon
    * B.S. in Molecular, Cell and Developmental Biology with minor in Biomedical Research, 
        expected June 2019, UCLA
    * contact: hmarkovi@uoregon.edu
* This program was created as the final project for the class EEB 177, Spring 2019, UCLA

### Acknowledgements
* Aaron van Loon - miroridge deconvolution program
* Aaron van Loon and Kaiser Atai - data
* Dr. Emily Curd and Daniel Chavez - guidance

### References
* https://stackoverflow.com/questions/9595117/identify-a-linear-feature-on-a-raster-map-and-return-a-linear-shape-object-using
* https://cran.r-project.org/web/packages/deldir/
* https://cran.r-project.org/web/packages/rgeos/
* https://cran.r-project.org/web/packages/sp/
* https://cran.r-project.org/web/packages/PairViz/
* https://cran.r-project.org/web/packages/TSP/
* https://rstudio-pubs-static.s3.amazonaws.com/202536_7a122ff56e9f4062b6b012d9921afd80.html
* http://tutorial.math.lamar.edu/Classes/CalcIII/Curvature.aspx
