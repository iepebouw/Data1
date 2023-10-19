---
layout: page
title: Python Code
permalink: /code3/
parent: Assignment 3
nav_order: 1
has_children: yes
---

## Python Code Excercise 3

Below the complete code for excercise 4. Just copy/paste in the top right corner to use it yourself:

_make sure the following libraries are installed:_
- Pandas
- Matplotlib
- Osmnx
- plotly

_Download the following file and past the path before #read Airbnbn file_

[listings.csv](http://data.insideairbnb.com){:target="_blank" rel="noopener"}


Runtime code: Around 1m 30s

```python
# Plot the amount of AirBnB locations per neighbourhood.

#opdracht 3.1 
import pandas as pd 
from pandas import DataFrame 
import matplotlib.pyplot as plt 
import osmnx as ox 
from geopy.geocoders import Nominatim 
import plotly.express as px 
from plotly.express import histogram 

airbnb = pd.read_csv("C:/Users/GBomm/OneDrive/Documents/MADE/Q1/Data/WS4/listings.csv") # lees de airbnb file 
airbnb.head() # print de eerste 5 kolommen 
 
fig = histogram(airbnb, x="neighbourhood", text_auto=True) 
fig.update_layout(height=500,title_text='Airbnb count per neighbourhood') 
 
fig.show() 
#-------------------------------------------------------------------------------------------------
# How many different licenses are issued?
print(len(airbnb["license"].unique())) 
#-------------------------------------------------------------------------------------------------

```
