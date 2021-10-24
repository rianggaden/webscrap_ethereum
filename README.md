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
