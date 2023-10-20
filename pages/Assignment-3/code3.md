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
- Openpyxl

_Go to [Airbnbn](http://data.insideairbnb.com/the-netherlands/north-holland/amsterdam/2023-09-03/visualisations/listings.csv) and download the listings.csv file and paste the path on the mentioned place (in CAPITALS) in the code_

_go to [BBGA](https://onderzoek.amsterdam.nl/dataset/basisbestand-gebieden-amsterdam-bbga) and download the 'Het BBGA inclusief metadata in xlsx formaat' file. Paste the path on the mentioned place (in CAPITALS) in the code_

```python
import pandas as pd 
from pandas import DataFrame 
import matplotlib.pyplot as plt 
import osmnx as ox 
from geopy.geocoders import Nominatim 
import plotly.express as px 
from plotly.express import histogram  
airbnb = pd.read_csv("PASTE THE PATH TO THE LISTINGS.CSV FILE") # read Airbnb file
airbnb.head() # print first five colums
fig = histogram(airbnb, x="neighbourhood", text_auto=True) 
fig.update_layout(height=500,title_text='Airbnb count per neighbourhood') 
fig.show() 

#-------------------------------------------------------------------------------------------------

import itertools 
airbnb_oudwest = airbnb[airbnb.neighbourhood == "De Baarsjes - Oud-West"] #filter on the neighbourhood with the most airbnbs 
geolocator = Nominatim(user_agent="AMS") 
latitude = airbnb_oudwest['latitude'].tolist() #add the airbnbs lat to a list 
longitude = airbnb_oudwest['longitude'].tolist() #add the airbnbs lon to a list 
streets = [] #make a new street list 
for lat, lon in zip(latitude, longitude): #zip makes it possible to use two lists at the same time 
    coor = str(lat),str(lon) #coordinates of the airbnbs 
    address = geolocator.reverse(coor, zoom=16) #reverse geocode the coordinates, zoom=16 makes sure you only get the streets 
    streets.append(address.address) #add the addresses to the streetlist 
def most_frequent(List): #new function to get the most frequent occuring street 
    return max(set(List), key = List.count) 
print("The street with the most Airbnbs is:",most_frequent(streets),"and it has",streets.count(most_frequent(streets)),"Airbnbs") 

#-------------------------------------------------------------------------------------------------

bbga = pd.read_excel("PASTE THE PATH TO THE BBGA FILE") # Read the BBGA file 
bbga_2018 = bbga[bbga.jaar == 2018] #filter on the first year there is a full dataframe
fig = histogram(bbga_2018, x="gebiednaam", y = "BHVESTAIRBNB", text_auto=True) #create a similar histogram but with BBGA
fig.update_layout(height=500,title_text='Airbnb count per neighbourhood BBGA') 
fig.show()# You can compare on the webiste (by saying the amounts are higher than the airbnb listings and by saying that there are more neighbourhoods than the airbnb listings)
total_amsterdam = bbga_2018._get_value(3926, 'BHVESTAIRBNB') #get the total amount 
percentage = 100 - (len(airbnb) / total_amsterdam * 100) #divide total availability of the listings list by the total availiability of BBGA*100%
print(percentage,"% is not always rented out but also used as normal housing") # This is the amount not on the airbnb site but registered as airbnb, this means people also live there 

#-------------------------------------------------------------------------------------------------

#If we assume every Airbnb has two persons renting:
airbnb_spots = round(2 * len(airbnb) * 0.63) # times two because each room houses 2 people and 63 percent of the airbnbs are available 
hotel_spots = round(2 * bbga._get_value(2367, 'BHHOTKAM') * 0.54) # 54 percent of the hotel rooms are available 
visitors = 30000 # number of visitors 
if (airbnb_spots + hotel_spots) >= visitors: # if the total amount of rooms is more than the visitors, then you don't need to build anymore rooms 
    print("You don't need to build any hotel rooms") 
else: 
    number_rooms = (visitors - (airbnb_spots + hotel_spots)) / 2 #else calculate how many rooms you need to build, divided by two because each room houses 2 people 
    print("You have to build",number_rooms,"more rooms") 

#-------------------------------------------------------------------------------------------------

print(len(airbnb["license"].unique()))
```
