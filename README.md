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



## Thanks
Many thanks to Dataquest for sharing his knowledge.


