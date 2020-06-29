# Survival Analysis Service

(WIP) A simple service to provide survival analysis results for INRG data.

## Design

The source data to fit Kaplan-Meier estimator is assumed to be available in the JSON format from an external API endpoint.

The server:

1. Listens to HTTP `GET` requests on `/`, with a factor variable to use as query string `?factor=xxx`
2. Fetches data from the source API endpoint
3. Fit Kaplan-Meier estimator to data based on query string `?factor=xxx`
4. Calculate p-value for log-rank test
5. Create a risk table containing number of subjects at risk per year
6. Serve results in JSON as response

## Project setup

1. Download and install Python(^3.6) and pip
2. Run `pip install -r requirements.txt` to install dependencies
3. run `export FLASK_APP=app.py`
4. Run `flask run`
5. Service is now running on port 5000

> NOTE: if `DATA_URL` in `./app.py` is equal to an empty string, the app will use fake data stored in `./data/fake.json`.

## Dependendcies

- `flask` for creating simple API server application
- `lifelines` for survival analysis
- `pandas` for fetching and parsing JSON data as data frame

## Simplified code

```python
# app.py
from flask import Flask, jsonify, abort, request
from lifelines import KaplanMeierFitter
from lifelines.statistics import multivariate_logrank_test
import pandas as pd

DATA_URL = "" # source data API endpoint

app = Flask(__name__) # default port 5000

def fetch_data(url):
    """Return a data frame of fetched data from an API endpoint """
    # ...
    return df

def get_survival_data(df, factor):
    """Return survival analysis data to serve."""
    # ...
    return {
      "pval": pval,
      "risktable": risktable,
      "survival": survival
    }

@app.route("/")
def get_survival():
    df = fetch_data(DATA_URL)
    factor = request.args.get("factor")
    return jsonify(get_survival_data(data, factor))
```
