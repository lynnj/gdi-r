UFO Sightings Analysis with R
========================================================
author: Lynna Jirpongopas
date: Wed Mar 04 15:39:05 2015



***
![UFO 1 goes here](UFO1.jpg)



What is R?
========================================================

    "R is a free software programming language and software environment for statistical computing and graphics." 
                
    -Wikipedia


- Dialect of S language
- Free software
- In 2000, R version 1.0.0 is released


Learning goals
========================================================
- Install RStudio
- Using common features of RStudio
- Load a csv table 
- Use zoo package for dates data type 
- Subset data 
- Draw insights from data!


Download and installing RStudio
======================================================== 
1. Getting RStudio: http://www.rstudio.com/products/rstudio/download/ 

2. Customizing your GUI

3. Alternative: RGui from http://www.r-project.org/




Dive into R basics (works for RGui or RStudio)
======================================================== 
<small>

  Case sensitive
  
  For comments use #
  
  ; is not necessary, but optional
  
 <-  instead of =
  
  " " are required for strings
  
  : is used to generate sequence of numbers
  
  ? before a function name to get help with a function

</small>


Dive into R basics (works for RGui or RStudio)
======================================================== 

Hotkeys:
- up and down for last or recent line of code
- Ctrl + r is for running a line of code
- Ctrl + l is for clearing the console


RStudio tools
========================================================

- install packages
- RStudio has help tab
- set working directory
- preview data
- executing R code - try 2+3
- save and open your R file
- save data


A very hotkey stroke:
- "tab" for predictive keyboard

Load a csv table
========================================================
There are several methods:

1) use RStudio GUI to set working directory

2) use setwd()

3) your csv file name in read.csv() contains the entire directory



Load a csv table, method 3
========================================================

```r
nasdaqData <- read.csv("C:/Users/lynnaj/Desktop/UFO/NASDAQOMX-NDX.csv")
```



read.csv() arguments
========================================================
There are bunch of arguments (ie. options)!
Here is my advice for best practice:

```r
opecData <- read.csv("OPEC-ORB.csv", header = T, sep = ",", stringsAsFactors = F) #loading csv file

opecData <- read.csv("OPEC-ORB.txt", header = T, sep = "\t", stringsAsFactors = F) #loading txt file
```



PS. Data sets were taken from quandl.com


Structure of your table
========================================================
2 ways to find out what kind of table you have 

(matrix, list, data frame, etc.)

Method 1:

```r
str(nasdaqData)
```


Method 2: Go to Environment tab


Subsetting data
========================================================
- Method 1: using subset()
- Method 2: using square brackets (ie. extraction brakets)


subset()
========================================================
let's say we only want to see part of the data with index value higher than 4000


```r
highIndexValue <- subset(nasdaqData, Index.Value > 4000)
```



using square brackets []
========================================================

```r
highIndexValueBrackets <- nasdaqData[nasdaqData$Index.Value > 4000 , ]
```

Comma is needed because brackets have 2 arguments: the row and the column

[row, col]

The criteria that we're using tells R which row to extract!


using square brackets [] to extract a column
========================================================
Your criteria goes after the comma!


```r
justACol <- nasdaqData[, "Low"]
```


You can use the column name with quotes or the number of the column.

Now you have a vector, justACol, that contains everything that is in Low column from nasdaqData


Basic statistics
========================================================
use summary() to get summary statistics


```r
summary(nasdaqData)
summary(highIndexValue)
```



UFO data
========================================================

http://blog.modeanalytics.com/five-public-dataset/

- Which month is most popular for UFO sighting?


Load our UFO data!
========================================================
![UFO 2 goes here](UFO2.png)


Zoo package
========================================================
install zoo package
so that we can use as.Date()

***
![funny zoo pic goes here](zoo.jpg)



Dates
========================================================




```r
library(zoo)
ufoData$month <- as.Date(ufoData$month, format = "%Y-%m-%d")
```



Basic statistics
========================================================
use summary() to get summary statistics


```r
summary(ufoData)
```




Plot the data using base package
========================================================


```r
plot(ufoData$month, ufoData$sightings)
```

![plot of chunk unnamed-chunk-11](UFO-figure/unnamed-chunk-11.png) 



Plot only 1900 to 2014
========================================================

```r
ufoData1900to2014 <- subset(ufoData, month < "2014-12-31" & month > "1900-01-01")

plot(ufoData1900to2014$month, 
     ufoData1900to2014$sightings)
```

![plot of chunk unnamed-chunk-12](UFO-figure/unnamed-chunk-12.png) 



Plot using zoo package
========================================================
Instead of scatterplot, you want a line graph

```r
z <- zoo(ufoData$sightings, ufoData$month)
plot(z)
```

![plot of chunk unnamed-chunk-13](UFO-figure/unnamed-chunk-13.png) 



Which month is most popular for UFO sighting?
========================================================
There are many ways to solve this problem.

Plan:
- Aggregate sightings by month
- Combind all the months together to form a data frame (ie. table)
- Use summary() to find the mean and median sightings for each month

OR
- We can use summary() for each month, then combind the summaries


Which month is most popular for UFO sighting?
========================================================
Problem with the first plan is that different months, we have diffent amount of sightings.

Can't use cbind with vectors that have different lenths.

There are ways around this by inputting "NA", but let's just use the second method...



Subset data by month
========================================================
Use what we know about:
- summary()
- subset()
- square brackets

Put them all together!

Hint: format.Date(month, "%m") == "01" is the criteria that we have to use to tell subset function to select rows with month = 01.



Subset data by month
========================================================


```r
Jan <- summary(subset(ufoData, format.Date(month, "%m") == "01" )[,"sightings"])
Feb <- summary(subset(ufoData, format.Date(month, "%m") == "02" )[,"sightings"])
Mar <- summary(subset(ufoData, format.Date(month, "%m") == "03" )[,"sightings"])
Apr <- summary(subset(ufoData, format.Date(month, "%m") == "04" )[,"sightings"])
```

... replicate each line for each month of the year


Or use a self-defined function to replace the long line of R nested functions
========================================================
We can cover how to write functions in a different R course

```r
summMonth <- function(mm){
  summary(subset(ufoData, format.Date(month, "%m") == mm )[,"sightings"])
}
```



Only replicate the self-defined function
========================================================
We have to input month name and corresponding month number...

```r
Jan <- summMonth("01")
Feb <- summMonth("02")
Mar <- summMonth("03")
Apr <- summMonth("04")
May <- summMonth("05")
Jun <- summMonth("06")
Jul <- summMonth("07")
Aug <- summMonth("08")
Sep <- summMonth("09")
Oct <- summMonth("10")
Nov <- summMonth("11")
Dec <- summMonth("12")
```



Which month is most popular for UFO sighting?
========================================================

```r
ufoDataByMonth <- cbind(Jan, Feb, Mar, Apr, May, Jun, Jul, Aug, Sep, Oct, Nov, Dec)
```


Check out ufoDataByMonth table!


Know the answer?! 
========================================================
![UFO 2 goes here](UFO2.jpg)

We're only interested in the row called, Mean.

```r
maxMean <- max(ufoDataByMonth["Mean",])
```



Which month!!!?
========================================================

```r
colnames(ufoDataByMonth)[ufoDataByMonth["Mean",] == maxMean]
```

```
[1] "Jul"
```

```r

maxMedian <- max(ufoDataByMonth["Median",])
colnames(ufoDataByMonth)[ufoDataByMonth["Median",] == maxMedian]
```

```
[1] "Jun"
```



How was it?!
========================================================

![UFO 3 goes here](UFO3.jpg)

more light shed on the mystery of R ?


Want to learn more?
========================================================
1) A foundation course 

<small>Understanding data types for data cleansing </small>

2) Best of R

<small>Intro to statistics and machine learning with R </small>

3) Dataviz with R

<small>Learn how to visualize your data with the following R packages: ggplot2 & shiny </small>


Data visualisation and interactivity with Shiny
========================================================
We will go through this tutorial together:
http://shiny.rstudio.com/tutorial/lesson5/

or we can do something else!

I'm open to suggestions for the type of R class you're interested!
Also, I'm open to using any types of data set.
