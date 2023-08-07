# Machine Learning Tutorials in Python

Machine Learning tutorials by Dataquest

## Using Python and Jupyter Notebooks

### Working with Virtual Environments
Before starting up Jupyter Notebooks we should work in a virtual environment. 
This is to alleviate conflicting module versions. 
See tutorial [here](https://docs.python.org/3/tutorial/venv.html)

To create a virtual environment in Python: 
```
python -m venv ml-env
```
To run the environment on Windows
```
ml-env\Scripts\activate.bat
```
To run the environment on Unix or MacOS
```
source ml-env/bin/activate
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
python -m venv ml-env
ml-env\Scripts\activate.bat
pip install -r requirements.txt
jupyter notebooks
```

## Project 1: Predict Stock Market with Machine Learning

[Link to tutorial](https://www.youtube.com/watch?v=1O_BenficgE)

Our goal is to predict whether the stock price will go up in the next day. 

### Simple Random Forest Classifier
We download historical SP500 data using `yfinance` package
Create a column of `Tomorrow` prices
```
sp500["Tomorrow"] = sp500["Close"].shift(-1)
```
We also add a `boolean` as `int` type to indicate if tomorrow's price increased
```
sp500["Target"] = (sp500["Tomorrow"] > sp500["Close"]).astype(int)
```

```
from sklearn.ensemble import RandomForestClassifier
model = RandomForestClassifier(n_estimators=100, min_samples_split=100, random_state=1)
```
We will use the last 100 rows as our test data
```
train = sp500.iloc[:-100]
test = sp500.iloc[-100:]
```
We train our model using all 5 data attributes
```
predictors = ["Close", "Volume", "Open", "High", "Low"]
model.fit(train[predictors], train["Target"])
```

**Back Test Results**
```
from sklearn.metrics import precision_score
preds = model.predict(test[predictors])
import pandas as pd
preds = pd.Series(preds, index=test.index)
precision_score(test["Target"], preds)
```
We can break up our data and test multiple segments of our data
```
def predict(train, test, predictors, model):
    model.fit(train[predictors], train["Target"])
    preds = model.predict(test[predictors])
    preds = pd.Series(preds, index=test.index, name="Predictions")
    combined = pd.concat([test["Target"], preds], axis=1)
    return combined

def backtest(data, model, predictors, start=2500, step=250):
    # each year has about 250 trading days. 
    # we train the data on 10-year data, and predict the 11th year's outcome
    all_predictions = []
    for i in range(start, data.shape[0], step):
        train = data.iloc[0:i].copy()
        test = data.iloc[i:(i+step)].copy()
        predictions = predict(train, test, predictors, model)
        all_predictions.append(predictions)
    return pd.concat(all_predictions)

predictions = backtest(sp500, model, predictors)
precision_score(predictions["Target"], predictions["Predictions"])
```

**Improved Model**
We improve our model by add 2 new predictors and apply ML over 5 different time spans: 
`Close_Ratio` = `Close` / Rolling average of `Close` over previous x days
`Trend` = sum of days in teh time horizon that the index price when up

```
horizons = [2, 5, 60, 250, 1000]
new_predictors = []

for horizon in horizons: 
    rolling_averages = sp500.rolling(horizon).mean()
    ratio_column = f"Close_Ratio_{horizon}"
    sp500[ratio_column] = sp500["Close"] / rolling_averages["Close"]

    trend_column = f"Trend_{horizon}"
    sp500[trend_column] = sp500.shift(1).rolling(horizon).sum()["Target"]
    # calculates the sum of the days in the horizon, that the index went up

    new_predictors += [ratio_column, trend_column]
```
We also modify our `Random Forest Classifier` to have better fit.
```
model = RandomForestClassifier(n_estimators=200, min_samples_split=50, random_state=1)
```
The resulting model makes less predictions and have a better accuracy (56.9%). 
```
def predict(train, test, predictors, model):
    model.fit(train[predictors], train["Target"])
    preds = model.predict_proba(test[predictors])[:,1]
    #instead of a 0 or 1 response, we return the probably if index will go up or not 
    preds[preds >= .6] = 1
    preds[preds <.6] = 0
    preds = pd.Series(preds, index=test.index, name="Predictions")
    combined = pd.concat([test["Target"], preds], axis=1)
    return combined

predictions = backtest(sp500, model, new_predictors)
predictions["Predictions"].value_counts()
precision_score(predictions["Target"], predictions["Predictions"])
```






## Thanks
Many thanks to Dataquest for sharing his knowledge.


