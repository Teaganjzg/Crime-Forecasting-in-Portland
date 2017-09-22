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
The algorithms we applied are **Linear Regression**, self-defined Proportion Algorithm. Both of
them can give a good overall accuracy. **Python** and **QGIS** are the tools we use to clean, prepare, visualize, predict, and analyze our data as well as the result. The data we downloaded from the [NIJ website](https://nij.gov/funding/Pages/fy16-crime-forecasting-challenge-document.aspx#data) includes eight zipfiles of crime records which start from March 2012 to October 2016 and one zipfile of Portland district information.

* Data Cleaning
  QGIS is a Free and Open Source Geographic Information System and is widely used to present geographic data. That's exactly what we need.  The version of QGIS we used is [QGIS 2.16.3](https://mega.nz/#!93ZH0JLC!yRSVekUVllTlCnNP8F2yB4RibjHLRxKW2LfBpUaR0GI), [their website](http://www.qgis.org/en/site/) has the lastest version. There are filters in QGIS that we can use to only keep the columns we are interested in. After loading the map in QGIS, the records which are presented as dots can be removed in QGIS manually. 
  
  ![image](https://user-images.githubusercontent.com/31550461/30728104-e2518cea-9f12-11e7-9113-01262b0bc550.png)
  
* Oracle VirtualBox with Ubuntu64bit
  All the data processing, analyzing is done in **Oracle VirtualBox** 5.1.14 with **Ubuntu64bit** using **ipython notebook** on a window10 64bit laptop.
  In order to use ipython notebook, run the following command in terminal
  
  ` ipython notebook `
  
  Then the Python code can be run in the cells.
  
* Data importion
  Data can be imported into ipython notebook in excel format which is the original format by import the python package **xlrd** and **panda**
  ` import xlrd` `import panda as pd` In order to import all the excel files , a loop and some data import function of panda are needed
```
excel_name=['2015_JAN01_DEC31','2016_JAN01_JUL31','2016_AUG01_AUG31_USE','2016_SEP01_SEP30','2016_OCT01_OCT31']
data={}
for i, val in enumerate(excel_name):
      xlsx_file = pd.ExcelFile('/home/datascience/Project/street crime excel/NIJ'+val+'.xlsx')
      df = xlsx_file.parse(xlsx_file.sheet_names[0])  
      del df['CALL_GROUP']
      del df['CASE_DESC']
      del df['census_tra']
      del df['final_case']
      del df['wkt_geom']  
      del df['CATEGORY']
      df=df.reset_index(drop=True)
      data[val]=df
```
  Notice that the excel files have beed filtered by QGIS with only CATEGORY= 'Street Crime' left.
  

## <a name="result">Results</a>

## <a name="credits">Credits</a>
## <a name="contact">Contact</a>
