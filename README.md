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
  QGIS is a Free and Open Source Geographic Information System and is widely used to present geographic data. That's exactly what we need.  The version of QGIS we used is [QGIS 2.16.3](https://mega.nz/#!93ZH0JLC!yRSVekUVllTlCnNP8F2yB4RibjHLRxKW2LfBpUaR0GI), [their website](http://www.qgis.org/en/site/) has the lastest version. There are filters in QGIS that we can use to only keep the columns we are interested in. After loading the map in QGIS, the records which are presented as dots can be removed in QGIS manually. A layer of grid is applied on to the map(11992 in total).
  
  <image src="https://user-images.githubusercontent.com/31550461/30728104-e2518cea-9f12-11e7-9113-01262b0bc550.png" width=400><image src="https://user-images.githubusercontent.com/31550461/30729360-2d213276-9f1c-11e7-92dc-e55eb82c473d.png"  width=400>
  
* Oracle VirtualBox with Ubuntu64bit
  All the data processing, analyzing is done in **Oracle VirtualBox** 5.1.14 with **Ubuntu64bit** using **ipython notebook** on a window10 64bit laptop.
  In order to use ipython notebook, run the following command in terminal
  
  ` ipython notebook `
  
  Then the Python code can be run in the cells.
  
* Data importion
  Data can be imported into ipython notebook in excel format which is the original format by import the python package **xlrd** and **panda**
  `import xlrd` `import panda as pd` In order to import all the excel files, the datastructure is **dataframe**. a loop and some data import function of panda are needed
```Python
excel_name = ['2015_JAN01_DEC31','2016_JAN01_JUL31','2016_AUG01_AUG31_USE','2016_SEP01_SEP30','2016_OCT01_OCT31']
data = {} #the library that contines all the dataframes which extract from excel files one by one
for i, val in enumerate(excel_name):
      xlsx_file = pd.ExcelFile('/home/datascience/Project/street crime excel/NIJ'+val+'.xlsx')
      df = xlsx_file.parse(xlsx_file.sheet_names[0])  
      del df['CALL_GROUP']
      del df['CASE_DESC']
      del df['census_tra']
      del df['final_case']
      del df['wkt_geom']  
      del df['CATEGORY']
      df = df.reset_index(drop=True) #drop the index of each excel file to keep the consistancy of the whole data collection
      data[val] = df
```
  Notice that the excel files have beed filtered by QGIS with only CATEGORY= 'Street Crime' left. After this step, we only have coordination information and occurance time for each street crime record.
  
* Data Overview  
  1. Concatenate multiple dataframe by time into one.
  2. Because our prediction is by month, the 'occ_date' need to be grouped by month. 
  3. the coordination information which is 2 discrete number need to be transfered into which square spot of the grid they belong.
  4. Convert the old dataframe which is made of 'occ_data','Coordinate_X','Coordinate_Y' tuples into the new dataframe which only has 56 different colunms each represents one month individually and each row represents each square spot of the grid individually.
  5. Set the grid number as index.
```Python
df = pd.DataFrame()
month = pd.DataFrame()
monthly = pd.DataFrame()
for i, val in enumerate(excel_name):  
  df = (data[val].copy())
  month = df.set_index('occ_date')
  month = month.groupby(pd.TimeGrouper(freq='M')).size() 
  month.columns = ['counts']
  monthly = pd.concat([monthly,month])     
```
   The packages matplotlib is required to plot the bar chart to get an intuitive overview. It is also needed to flip the pivot and deal with the date.
```Python
import matplotlib.pyplot as plt
import matplotlib.dates as mdates
```
```Python
monthly.index=monthly.index.map(lambda t: t.strftime('%Y-%m'))
ax = monthly.plot(kind='bar', title ="STREET CRIMES counts in Portland",figsize=(10,8),legend=True, fontsize=12,)
ax.set_xlabel("month",fontsize=8)
ax.set_ylabel("counts",fontsize=12)
plt.show()
```
<image src="https://user-images.githubusercontent.com/31550461/30730199-841fc6d6-9f22-11e7-8f85-f1c5302e4f0f.png" width=500 align="center">

* Data Processing </br>
This is the one of the most important part because that's how we relate the crime records to all the square spots which are the candidate hotspots. 
First of all, import the grid layer excel file which contains the coordinary information of all the square spots.

```Python
xlsx_file = pd.ExcelFile('/home/datascience/Project/excel/grid.xlsx')
df_g = xlsx_file.parse(xlsx_file.sheet_names[0])
```
```Python
import numpy as np
def isNaN(num): # Define a function which can decide the num is null or not
    return num != num
df_nbm = pd.DataFrame()
for index, row in df_g.iterrows():        
        one_g = {}
        xmin = row['xmin']
        xmax = row['xmax']
        ymin = row['ymin']
        ymax = row['ymax']
        month1 = pd.DataFrame()
        df1 = pd.DataFrame()
        df2 = pd.DataFrame()
        monthly1 = pd.DataFrame()
        for i, val in enumerate(excel_name):
            df1 = (data[val].copy())
            df1 = df1[(df1.x_coordina >= xmin) & (df1.x_coordina < xmax) & (df1.y_coordina >= ymin) & (df1.y_coordina < ymax)]
            df2 = df2.append(df1,ignore_index=True)
        month1 = df2.set_index('occ_date')
        month1 = month1.groupby(pd.TimeGrouper(freq='M')).size()
        month1.index = month1.index.map(lambda t: t.strftime('%Y-%m'))
        for i, v in month1.iteritems():     
                      one_g[i] = v              
        df_nbm = df_nbm.append(one_g,ignore_index=True)
        if month1.empty:
            df_nbm.ix[(len(df_nbm)-1)] = 0
df_nbm=df_nbm.fillna(0) 
df_nbm=df_nbm.reindex_axis(sorted(df_nbm.columns), axis=1) #rearrange by colunm names
```  
The df_nbm is the dataframe of the number of records of each square spot by month. Since df_nbm is too large, we can select part of it to see what it looks like.
``` Python
df_nbm[4545:4555]
```
The output should be like this: </br>
<image src="https://user-images.githubusercontent.com/31550461/30778550-df4fa890-a095-11e7-8581-015d6c147c45.png" width=500></br>
* Linear Regression</br>
  The idea of our Linear Regression model is to apply the Linear Regression to individual sqaure spots based on the number of street crime happened in every past month with the data before January 2016 as trainnig data and the rest as testing data and improve it by adjusting the degree **(order of polynomial)** of linear regression functions. The degrees we tried is in the range of (0,5). We obtained the linear regression model for each individual square spot which show the overall trends. Then we compare the predicted hotspots and real hotspots by our threshold of the number of crime happened or protenially will happen in specific month to measure the accuracy of our model.
  Below is part of the code with degree = 1. The original code file can be found in this repository. Here is the [link]().
  ```Python
  threshold=20
     while threshold > 3: #range to test threshold to decide which is the one we need  
        num16hots=[]
        for i,val in enumerate(num16notzero):
            if val[1] > threshold:
                num16hots.append(val)
  x=[] # x for linear regression
        lg=[] # contains the polyfit for each grid
        for i in range(0,44+ii):
           x.append(i)
        for index, row in df_nbm.iterrows():
           p = np.polyfit(x,row[0:(44+ii)],1) # degree can be set by the lase attribute in ()
           lg.append(p)
        phots=[] # contains the predict result for each grid
        for i,val in enumerate(lg):
           f = np.poly1d(val) 
           phots.append([f(44+ii),Rsquare(x,df_nbm.as_matrix()[i][0:(44+ii)],f)])   
        phots_notzero = [] #contains the grids(index, pedict result ) which predict result is not zero
        evaR2all = 0
        for i,val in enumerate(phots):
            evaR2all=evaR2all+val[1]
            if val[0] != 0:
              phots_notzero.append([i,val[0],val[1]])
        phots_f=[]
        evaR2=0
        for i,val in enumerate(phots_notzero):
            if val[1] > threshold:
                evaR2=evaR2+val[2]
                phots_f.append(val)              
        threshold=threshold-1
        if ((len(phots_f) > 20) & (len(phots_f) <= 58) & (len(num16hots) <= 58) & (len(num16hots) > 20)) :
            break
  ```
    Notice that the threshold is not a fixed number. There is a outter loop (threshold reduce by 1 from 20 to 3) for testing which threshold can make the number of actual hotspots and the predicted hotspots(the square spots whose value is beyond threshold can be considered as hot spots) both in the rang of (20,58). For my consideration, it is more resonable, because if we just sign a fixed number to threshold, there is a possibility that only the number of actual hotspots is in (20,58), but the number of predicted value which can satisfy this threshold is less than 20. In that case the accuracy which is (number of matched hotspots)/(number of predicted hotspots) is not realistic.
## <a name="result">Results</a>
   Here is the acccuracy of the linear Regression model.
   ![image](https://user-images.githubusercontent.com/31550461/30786620-f9c88bd8-a135-11e7-8862-3ae8cd2b551d.png)
   The hotspots on map will be like this.
   https://user-images.githubusercontent.com/31550461/30786629-2017063e-a136-11e7-93f0-64ba17def1bf.png
## <a name="credits">Credits</a>
## <a name="contact">Contact</a>
