# Stock Master

The "Stock Master" database collates fundamental and price data for US stocks.  

Data is collected from:
* the Securities and Exchange Commission ("SEC"), via the Financial Statement Data Sets made available by the [Economic and Risk Analysis Office](https://www.sec.gov/dera/data/financial-statement-data-sets.html), and
* the [Alpha Vantage](https://www.alphavantage.co/) API

This repo contains Python, R and SQL scripts for interacting with the PostgreSQL database housing this data.

## Update procedures

### Monthly

1. Import price data with ```alphavantage_import.py```
2. Import fundamental data with ```edgar_import.py```
3. Transform the raw SEC data with the ```edgar.edgar_fndmntl_all_vw``` view, inserting the results into ```edgar.edgar_fndmntl_all_tb``` table
4. Derive technical indicators from the price data and insert into  ```access_layer.return_attibutes``` table with the R script ```return_attibutes.R```
5. Derive financial ratios and valuation metrics from the fundamental data and insert into the  ```access_layer.fndmntl_attibutes``` table with the R script ```fndmntl_attibutes.R```

### New calendar year

In addition to monthly data collection, the "universe" of stocks for which analysis is performed requires updating.  The universe of stocks is determined with reference to fundamental data as of Q3 of the preceding year.  The following steps are required prior to updating data in a new calendar year:
* Update the ```reference.fundamental_universe``` table with data derived from the function ```edgar.edgar_fndmntl_fltr_fn``` function, parameterised as appropriate.  

## Date reporting conventions

The date at which data is available for modeling often differs to the release date, and in the case of financial statement information, the fiscal period to which it relates.  This necessitates careful tracking of a number of different dates.  Date reporting conventions are as follows.  

##### Report date
The ending date of the fiscal period to which a piece of financial information relates.  Applies only to financial statement information. Labelled ```report_date```.
    
##### Publish date  
The date on which financial statement information is made available to the public.  Labelled ```publish_date```. 
  
##### Capture date
The date data was downloaded.  Labelled ```capture_date```.

##### Date stamp
The date at which information is available for analysis. Labelled ```date_stamp```. 

An example of date reporting for financial statement information taken from the SEC website.   IBM filed it's 30 June 2020 quarterly results on 28 July 2020 (note that it reported EPS on 20 July per the AlphaVantage earnings data).  This information was made available by the SEC in its 2020 Q3 data set on 1 October 2020 and was downloaded on 5 October 2020.  In this case the reporting dates will look like this.

| Date reference        | Date       |
| --------------------- |:----------:|
| ```report_date```     | 2020-06-30 |
| ```publish_date```    | 2020-07-28 |
| ```capture_date```    | 2020-10-05 |
| ```date_stamp```      | 2020-10-31 |

The months subsequent to October 2020, and preceding the capture date of the next quarters financial statements, will have identical reporting, publish and capture dates.  

## Development / random ideas
1. Price return modeling
    1. Survival analysis assessing risk of drawdown - [link](https://lib.bsu.edu/beneficencepress/mathexchange/06-01/jiayi.pdf)
2. Dimensionality reduction of returns at portfolio level
    1. PCA
    2. Autoencoders?
3. Historical earnings dates for PEAD calculation - [link](https://www.alphavantage.co/documentation/#earnings)
4. Default risk as return predictor
    1. Merton for Dummies: A Flexible Way of Modelling Default Risk - [link](https://econpapers.repec.org/paper/utsrpaper/112.htm)
    2. In Search of Distress Risk - [link](https://scholar.harvard.edu/files/campbell/files/campbellhilscherszilagyi_jf2008.pdf)
5. [LPPLS](https://github.com/Boulder-Investment-Technologies/lppls), see also - [link](https://youtu.be/6x4-GcIFDlM)
