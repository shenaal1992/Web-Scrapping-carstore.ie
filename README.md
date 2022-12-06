# Creating a Data Preprocessing pipeline
The main Task of this project is to create a develop a Data Acquisition and preprocessing Pipeline ,including data acquisition.

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



