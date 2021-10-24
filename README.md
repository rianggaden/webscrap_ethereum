# Ethereum Volume Trend Analysis

## Project Background

**Ethereum** is a blockchain platform with its own cryptocurrency, called **Ether (ETH)** or Ethereum, and its own programming language, called Solidity. 

As a blockchain network, Ethereum is a **decentralized** public ledger for verifying and recording transactions. The network's users can create, publish, monetize, and use applications on the platform, and use its Ether **cryptocurrency** as payment. Insiders call the decentralized applications on the network "dapps." As a cryptocurrency, Ethereum is second in market value only to Bitcoin, as of Oct 2021.

At this project, we try to scrap **Ethereum USD (Historical Data)** from **CoinGecko** website. CoinGecko is the world's largest independent cryptocurrency data aggregator with over 6,000 different cryptoassets tracked across more than 400 exchanges worldwide. We will try to scrap this sites for educational purpose only.

We will scrap 2 points from this sites. One is trading volume, which is the total amount of coins traded in the last 24 hours. The other one is dates in the period 1 January 2020 to 30 June 2021. After that we will try to get the trend of trading volume in the expected period to be analyzed and concluded.

## Library Preparation
```
import pandas as pd
import requests
from bs4 import BeautifulSoup
import matplotlib.pyplot as plt
```

## Requesting the Data and Creating a BeautifulSoup
Requesting the website with `request` get method to be able fetch the data and use Beautifulsoup to transforms a complex HTML document into a complex tree of Python objects.
```
url_get = requests.get('https://www.coingecko.com/en/coins/ethereum/historical_data/usd?start_date=2020-01-01&end_date=2021-06-30#panel')
soup = BeautifulSoup(url_get.content,"html.parser")
```
We can use `print(soup.prettify()[:500])` to see the result sample.

## Finding the Right Key to Scrap the Data
By using inspection from the website, we will find that line table tbody that contains Ethereum historical data, including date and volume data. So we will use `find` method at our beautifusoup object to get the line table.
```
table = soup.find('tbody')
```
From the line we just extract, we will find classes of each data we need as follows:
- date: `th, class="font-semibold text-center"`
- volume: `td, class="text-center"`

## Extracting Information
Run this code to find row lenght of date and volume, we will use that value for our looping process:
```
#date row lenght
row = table.find_all('th', attrs={'class':'font-semibold text-center'})
row_length = len(row)
row_length
```
```
#volume row lenght
row2 = table.find_all('td', attrs={'class':'text-center'})
row_length2 = len(row2)
row_length2
```
You will find that the results above seem odd because `data` and `volume` have different number of rows. This happens because `volume` has the same class as the other columns such as **Market Cap**, **Open**, and **Close**. So when we try to call that `class`, other data will also be called, causing the number of row increase. To get a better understanding of class `text-center` data, we will convert it to `list` type:
```
temp_vol = []

for i in range(0, 15):
    volume = table.find_all('td', attrs={'class':'text-center'})[i].text.strip()
    temp_vol.append(volume)

temp_vol
```
We found that the volume data lies in list number 1, 5, 9, 13, ... with a 4-digit gap.

From the analysis above, the scrapping process is then carried out by creating a special variable x which starts from number 1 and enters the looping process by adding 4 values for each iteration.

In the case of date, we can use a normal loop to get the list.
```
temp = []
x = 1

for i in range(0, row_length):
    date = table.find_all('th', attrs={'class':'font-semibold text-center'})[i].text
    volume = table.find_all('td', attrs={'class':'text-center'})[x].text.strip()
    x = x+4
    
    temp.append((date,volume))

temp = temp[::-1] #change row order. Descending frow oldest to newest
temp
```

## Creating data frame & Data wrangling
```
#put the array into dataframe
pd.set_option('display.float_format', lambda x: '%.2f' % x)
pd.options.display.float_format = '{:,}'.format

df = pd.DataFrame(temp, columns = ('Date','Volume'))
```

We will change the `volume` datatype to float datatype because normally trading volume presented in decimal form. But before we can do that, we need to drop the comma and dollar symbol by using `str.replace()`. Then lastly, we fix our `date` datatype to datetime datatype.
```
df['Volume'] = df['Volume'].astype('str')
df['Volume'] = df['Volume'].str.replace("$","", regex=True)
df['Volume'] = df['Volume'].str.replace(",","")
df['Volume'] = df['Volume'].astype('float64')
df['Date'] = df['Date'].astype('datetime64')
df = df.set_index('Date')
```

## Data Visualization & Analysis
First, visualization of the `df` dataframe in the form of a line chart is carried out. Line charts are considered the most relevant because the data represents trading volume growth during a given period of time.
```
df.plot(figsize=(16, 4), grid=True, title='Ethereum Volume Trend', xlabel='Date', ylabel='Volume (in 100B USD)')
```
At first glance, you will find out that the trading volume in 2021 is higher than in 2020. So I decide to do further analysis by analyzing the volatility and total of trading volume for each year in semester 1.

## Conclusion
1. Ethereum is highly volatile so one should be very cautious while investing, this instrument is considered more suitable for traders who are looking for large profits in a short period of time.
>Many of the reasons for price volatility in mainstream markets hold true for cryptocurrencies as well. News developments and speculation are responsible for fueling price swings in crypto and mainstream markets alike. But their effect is exaggerated in crypto markets as they have less liquidity than traditional financial markets — a result of crypto markets lacking a robust ecosystem of institutional investors and large trading firms. Heightened volatility and a lack of liquidity can create a dangerous combination because both feed off of each other. Other than bitcoin, most other cryptocurrencies also lack established and widely adopted derivatives markets. Under the sway of day traders and speculators, crypto prices sometimes exhibit healthy volatility of the type we see in mainstream markets.
\
\
source: https://www.gemini.com/cryptopedia/volatility-index-crypto-market-price

2. Even though it has high volatility, long-term investment through Etherium is still possible according to total YoY trading volume growth which still increasing up to 2 times. 
>Ethereum’s growth is attributed to an increasing number of developers building decentralized finance (DeFi) applications on the Ethereum blockchain platform. The rise of non-fungible tokens (NFT) also increases demand for Ethereum. Lastly, continued institutional interest in treating crypto like any other security also helps ETH. Coinbase is the new NYSE. ETH is No. 2 on Coinbase after Bitcoin. Seeing how the DeFi and NFT movements are directly connected to Ethereum, their increased activity has been the main driver for ETH prices in 2021, says Andrew Moss, GSR Capital’s managing director. “Users need ETH to interact with these technologies, so the more people who are involved with these communities and protocols, the higher the value of ETH goes,” he says.
\
\
source: https://www.forbes.com/sites/kenrapoza/2021/05/16/ethereum-faces-weekend-rout-but-some-see-eth-doubling-from-here/?sh=6845bee93817
