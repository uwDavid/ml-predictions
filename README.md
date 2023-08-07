# Algorithmic Trading in Python

Following tutorial by [Nick McCullum - Algorithmic Trading in Python](https://www.youtube.com/watch?v=xfzGZB4HhEE&t=17s)
[Source Github Repository](https://github.com/nickmccullum/algorithmic-trading-python)

## Using Python and Jupyter Notebooks

### Working with Virtual Environments
Before starting up Jupyter Notebooks we should work in a virtual environment. 
This is to alleviate conflicting module versions. 
See tutorial [here](https://docs.python.org/3/tutorial/venv.html)

To create a virtual environment in Python: 
```
python -m venv algo-env
```
To run the environment on Windows
```
algo-env\Scripts\activate.bat
```
To run the environment on Unix or MacOS
```
source algo-env/bin/activate
```
To deactivate the virtual environment
```
deactivate
```

### Installing dependencies
Dependencies in the virtual env is listed in the `requirements.txt` file. 
To create the `requirements.txt` file: 
```
pip freeze > requirements.txt
```
To install packages from a `requirements.txt` file: 
```
pip install -r requirements.txt
```
To upgrade packages: 
```
pip list --outdated
pip install -U fastapi
pip freeze > requirements.txt
```

### Tldr: To start up Jupyter Notebooks
Change directory to `algorithmic-trading-python` folder
```
python -m venv algo-env
algo-env\Scripts\activate.bat
pip install -r requirements.txt
jupyter notebooks
```

## Project 1: Building An Equal-Weight S&P 500 Index Fund
The user will enter a total investment amount, the program will output an .xlsx file listing the number of shares to buy for each stock.
For example: 
If we have a total investment of $1 million dollars, we will investment $2,000 into each stock. 

We achieve this by: 
1. Initiate a data request to IEX Cloud API in a batch request to obtain price information of all 500 stocks. 
2. Organize this data into a Pandas dataframe
3. Loop through the data to calculate the number of shares to buy for each stock
4. Output the dataframe using xlsxwriter

## Project 2: Building A Quantitative Momentum Investing Strategy
Momentum strategy means that we will invest in the stocks that have increased in price the most. 
We will invest an equal weight in the top 50 stocks that have increased in price the most. 

We achieve this buy: 
1. Initiate a data request to IEX Cloud API in a batch request to obtain price information of all 500 stocks. 
2. Organize this data into a Pandas dataframe
3. Sort the data based on 1-year-price increase 
4. Keep only the top 50 rows of the dataframe
5. Loop through the data to calculate the number of shares to buy for each stock
6. Output the dataframe using xlsxwriter

We also note that we need to indentify "high-quality" momentum stocks. 
For example, a small biotechnology company may cause large price increase given a positive one-time news event. 
To help identify "high-quality momentum stocks, we can look at price returns of different time intervals (ie. 1-month, 6-month, 1-year). 

To achieve this: 
1. Set up dataframe to include `price return` and `return percentile` for each time span that we want to add. This project uses 4 different time spans. 
2. Pull data for each stock on its `ChangePercentage` for each time span
3. Populate the `return percentile` for each time span using `dataframe.loc` and `stats.percentileofscore` functions
4. Calculate HQM score for each stock. The HQM score is the average of `return percentile` of all time spans
5. Sort dataframe to get the top 50 HQM scoring stocks
6. Loop through the data to calculate the number of shares to buy for each stock
7. Output the dataframe using xlsxwriter

## Project 3: Building A Quantitative Value Investing Strategy
Our value investment metric for this strategy is the `P/E ratio`

To achieve this: 
1. Pull data for each stock with its `PE ratio`
2. Sort the dataframe to identify the top 50 stocks with the highest PE ratios

### Improved Strategy - Composite Scoring
To improve upon this strategy, we can use a composite index of valuation metrics. 
We will use these metrics: `price-to-earnings ratio`, `price-to-book ratio`, `price-to-sales ratio`, `EV/EBITDA`, `EV/gross profit`.
We will have to calculate the last two metrics ourselves. 

There will be missing data in this dataframe.
There are 2 approaches to deal with this missing data: 
1. Drop the missing data from the data set using the `dropna` method 
2. Replace the missing data with a new value using `fillna` method

In this project, we will use `fillna` method to fill the missing value with the `avg` value of that data column

Our composite score `Robust Value` or `RV Score` will be the mean of the 5 the percentile score of the metrics above. 
**Note** - It is important to point out that we are using the percentile of each of those metrics, not the raw metrics themselves.

From there, it is a simple sort and output to Excel.

## Thanks and Future Work
Many thanks to Nic for sharing this tutorial. 

This tutorial only covers the very basics of how to create a portfolio of stocks based on various metrics. 
To dive deep into algorithmic trading, we will need to learn how to use technical indicators to implement real-time trading strategies. 


