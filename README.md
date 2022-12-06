# Creating a Data Preprocessing pipeline
The main Task of this project is to create a develop a Data Acquisition and preprocessing Pipeline ,including data acquisition.

The colab notebook file is here.
'https://colab.research.google.com/drive/1e72ijLzfBjK0nF-Gw_OjAtaFNXQJb4dT#scrollTo=CWSQRVrUUJ7Z'

## Project Flow
Project is devided into sub-catogeries as,
1. Selecting a website.
2. Data Acquisition from selected website.
3. Feature Extraction.

### Selecting a website.
The website is selected as carstore.ie which included different kind of cars in the car market.

### Data Acquisition from selected website
Data aquisition method is selected as web-scrapping.
As the first step, consider one page of the website.
Selected the page as page 1 of the website.

Then, grabbed the main things from that website as follows.
1. URL
2. data (which include the whole data of the website)
3. headers (headers of the website)

```
url = 'https://www.carstore.ie/api/vehicle-search/?page=1'
data = 'data didn't shown here because of the lenth of the data is too large'
headers ={'accept': 'application/json, text/plain, */*',
'accept-encoding': 'gzip, deflate',
'accept-language': 'en-US,en;q=0.9',
'cookie': 'PHPSESSID=6821b6d390884dbd478774d3477fdf00; selected_currency=GBP; _gcl_au=1.1.614126171.1668503695; _ga=GA1.2.1010058837.1668503695; _gid=GA1.2.1006413804.1668503695; _tt_enable_cookie=1; _ttp=4bbef36b-4720-4369-9310-59b9f0ac5f0a; carstoreie_session=eyJpdiI6IldVWnRQWTg1aEtSS3FsQ2lNOXM3Y3c9PSIsInZhbHVlIjoiU1lwSHFLeWxYRDVSOFl0Nm16MEtreTBlSnJLVlFab1U5ckFwUzNwN1Y2bDEzQ3RyaTN4TnY5cUM1RFwvdnJoYzdtb2tERjA1cWlqVUR5c3lVNU9BSzI0cnJtVlJrYnE5ZUJhZ200c09jRkNlZ3NQSVBMbnAxSHRMS1lGeDhRQnNCIiwibWFjIjoiOTM3YTlkMGVlZWY4OTI1ZWNlMDUxMzRiYTI2Yzg5YzkwNGFmNjdjNGIxY2NlZTNlYmJiNzk4NzQzOTVjODgxMiJ9; __ggtruid=1668503699912.de297fc8-90c4-d7a9-7a54-9af296967a56; __ggtrses=1',
'referer': 'https://www.carstore.ie/find-a-vehicle/?page=3',
'sec-ch-ua': '"Google Chrome";v="107", "Chromium";v="107", "Not=A?Brand";v="24"',
'sec-ch-ua-mobile': '?0',
'sec-ch-ua-platform': '"Windows"',
'sec-fetch-dest': 'empty',
'sec-fetch-mode': 'cors',
'sec-fetch-site': 'same-origin',
'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/107.0.0.0 Safari/537.36'} # Before replacing headers, it converted into json format.

```

Then, imported the libraries which need to work with webscrapping.
1. json - To deal with json files
2. requests - To request data from a website.
3. pandas - To deal with dataframes

```
# Import Libraries
import json
import requests
import pandas as pd
from pandas import DataFrame
```

Then Initiated response by using requests library
```
resp = requests.get(url,data=data,headers = headers)
```

Then resp load as json file and converted that data into DataFrame
```
contents = json.loads(resp.content)
D1 = DataFrame(pd.json_normalize(contents,record_path = 'results'))
```
In here the D1 DataFrame is the DataFrame which corresponds to page 1 of the website.
Then created Empty DataFrame as **DF** then the columns of the **DF** is set to **D1** columns.

```
DF = pd.DataFrame(columns = D1.columns)
```

Now if we considered about **DF**, it is an empty DataFrame which included with same column names of **D1**.
Considering the webpage, it has 36 pages with car details.
By writing a **for** loop, it can be able to grab all data each an every details about cars.
So that every details appended to the empty DataFrame, **DF**.

```
for i in range(1,36):
  DF = DF.append(DataFrame(pd.json_normalize(json.loads(requests.get('https://www.carstore.ie/api/vehicle-search/?page='+str(i),data=data,headers = headers).content),record_path = 'results')))
```
Now we have a DataFrame **DF** with all the car details of carstore.ie
To take an overview of the details we can convert it into excel file because it is really easy to deal with excel file more than a DataFrame.

```
file_name = 'CarsData.xlsx'
DF.to_excel(file_name)
```

Now we have all the details of cars in excel file as figure below

![ScreenshotCarDetails](https://user-images.githubusercontent.com/45353233/205383760-c08dfb9e-7bb6-435e-a4fb-0350f0cd6bc4.png)

### Feature Extraction
After grabbing the data from the website, we have to extract most important features from that data.

To take a broad overview of the data considered the excel file. There was nearly 120 columns. So more than dropping irrelevant data, It was easy to pick the most relevant data for data preperation.

Considering most relavant data, The data frame is initialized as DF1.
```
DF1 = DF[['registration_number','make','model_year','colour','fuel_type','transmission_type','body_type','engine_size','price','odometer_in_km','location.county']]
DF1
```
![1](https://user-images.githubusercontent.com/45353233/205882597-39740e4f-cc07-47d2-8384-85f5850b2696.png)

Considering registration_number column, there was some cars which has no registration number. So to identify the value of registration_number which has no registration number,

```
DF1['registration_number'].iloc[0]
```
![2](https://user-images.githubusercontent.com/45353233/205883244-fd8fed83-d463-44aa-824e-e1cafadf9cf3.png)

It has shown no value. 
So for the ease of data handling replaced all the values **''** to 0. And intialized the dataframe as DF2.

```
DF2 = DF1.replace('',0)
```

Then selected the cars which has not a 0 registration number and intialized it as DF3
```
DF3 = DF2[DF2['registration_number']!=0]
```

Now the registration numbers are in corrected format. Then considered about the 'make' column.
Considering 'make' column, took a look at what are the values in make column. Considering that data there was no repetetion because of lower and upper cases.

```
DF3['make'].unique()
```
![3](https://user-images.githubusercontent.com/45353233/205885058-b6195171-64b3-4913-a9b7-fd95b40e78a4.png)

Considering make column, made dummy columns which represent value 1 for each types. Then added that recreated dummy dataframe and dropped 'make' column.

```
DF4 = pd.get_dummies(DF3['make'])
DF5 = DF3.drop('make',axis=1)
DF6 = pd.concat([DF5, DF4], axis=1).reindex(DF5.index)
```

Considering color column, there was lower case and upper case issue.
```
DF3['colour'].unique()
```
![image](https://user-images.githubusercontent.com/45353233/205886525-7be7246d-ffb2-47e0-8820-f1d67f89a8bb.png)

Even after solving that issue there was colors as WHITE and WHIT. Then had to replace WHIT as WHITE.
Then created duumies as above and prepared as appropriate.

```
DF7 = DataFrame(DF6['colour'].str.upper()).replace('WHIT','WHITE')
DF7['colour'].unique()
DF8 = pd.get_dummies(DF7['colour']).drop('NONE',axis=1)
DF9 = pd.concat([DF6, DF8], axis=1).reindex(DF6.index).drop('colour',axis=1)
```

Same as considering fuel_fuel type, data is converted into the encoded format.

```
DF10 = DataFrame(pd.get_dummies(DF9['fuel_type']))
DF11 = pd.concat([DF9, DF10], axis=1).reindex(DF9.index).drop('fuel_type',axis=1)
```

There was two types of transmission type. So if considered about manual as 0, auotomatic will be 1. Considering that concept, created the data in to correct encoded format.

```
DF12 = DataFrame(pd.get_dummies(DF9['transmission_type']))
DF13 = DF12.drop('Manual',axis=1)
DF14 = pd.concat([DF11, DF13], axis=1).reindex(DF11.index).drop('transmission_type',axis=1)
```

Same way data is convered for 'body_type' and cosidering location if the car is in Dublin,12 considered as 1 and if the car is outside the Dublin, 12 it is considered as 0.

```
DF15 = DataFrame(pd.get_dummies(DF9['body_type']))
DF16 = pd.concat([DF14, DF15], axis=1).reindex(DF14.index).drop('body_type',axis=1)
DF17 = DataFrame(pd.get_dummies(DF16['location.county']))
DF18 = DF17.drop(0,axis=1)
DF19 = pd.concat([DF16, DF18], axis=1).reindex(DF16.index).drop('location.county',axis=1)
```

Considering the price coloumn there was some cars which has 0 price. Which means no price. So have to dop the cras which has no price as well. Then the final dataframe for the data preprocessing can be finalized and the data converted to excel format for overview.
```
DF19 = pd.concat([DF16, DF18], axis=1).reindex(DF16.index).drop('location.county',axis=1)
DF20 = (DF19[DF19['price']!=0])
DF20.to_excel('cardata.xlsx')
```
![4](https://user-images.githubusercontent.com/45353233/205891728-5b034cdd-d7b9-4d83-9678-a1fca1ac25b5.png)


