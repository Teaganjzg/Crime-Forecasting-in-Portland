# Crime-Forecasting-in-Portland(!!!should be completed by the end of 9/21/2017)
A project of Data Science course in 2016Fall. The idea was originally from the [Real-Time Crime Forecasting Challenge](https://nij.gov/funding/Pages/fy16-crime-forecasting-challenge.aspx).
## Table of Contents
* [Introduction](#intro)
* [Implementation](#implem)
* [Results](#result)
* [Credits](#credits)
* [Contact](#contact)
## <a name="intro">Introduction</a>
Some of the criminal justice community's most vexing problems happen every day in
America. Although there are many methods to help police respond to crime and conduct
investigations more effectively, predicting where and when a crime is likely to occur—and
which type of crimes is likely responsible for prior crimes—has recently gained considerable
currency.
We have all of the data downloaded from The National Institute of Justice's (NIJ) website for
this project. Based on those data, we developed our model to predict the hotspots for street
crime in November 2016.
## <a name="implem">Implementation</a>
The algorithms we applied are Linear Regression, self-defined Proportion Algorithm. Both of
them can give a good overall accuracy. Python and QGIS are the tools we use to clean, prepare, visualize, predict, and analyze our data as well as the result. The data we downloaded from the [NIJ website](https://nij.gov/funding/Pages/fy16-crime-forecasting-challenge-document.aspx#data) includes eight zipfiles of crime records which start from March 2012 to October 2016 and one zipfile of Portland district information.

* Data Cleaning
  QGIS is a Free and Open Source Geographic Information System and is widely used to present geographic data. That's exactly what we need.  The version of QGIS we used is [QGIS 2.16.3](https://mega.nz/#!93ZH0JLC!yRSVekUVllTlCnNP8F2yB4RibjHLRxKW2LfBpUaR0GI), [their website](http://www.qgis.org/en/site/) has the lastest version. There are filters in QGIS that we can use to only keep the columns we are interested in. After loading the map in QGIS, the records which are presented as dots can be removed in QGIS manually. 
  
  ![image](https://user-images.githubusercontent.com/31550461/30728104-e2518cea-9f12-11e7-9113-01262b0bc550.png)
  
* Oracle VirtualBox with Ubuntu64bit
  All the data processing, analyzing is done in Oracle VirtualBox 5.1.14 with Ubuntu64 bit using ipython notebook on a window10 64bit laptop.
  In order to use ipython notebook, run the following command in terminal
  
  ` ipython notebook `

## <a name="result">Results</a>

## <a name="credits">Credits</a>
## <a name="contact">Contact</a>
