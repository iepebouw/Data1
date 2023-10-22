---
layout: default
title: Assignment 3
permalink: /assignment-3
nav_order: 4
has_children: true
---

# Amsterdam Housing
{: .no_toc }

There are 30.000 visitors expected during the Paralympics. Only a part of these visitors are tourists, but to make this assignment more clear, we consider all the visitors as people who might need a roof above their heads during the event. For this exercise we used the data from the [Dataset Basis Bestand Gebieden
Amsterdam (BBGA)](https://onderzoek.amsterdam.nl/dataset/basisbestand-gebieden-amsterdam-bbga) and the data set about [Airbnb's](http://insideairbnb.com/get-the-data/) in Amsterdam. 


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

### What Amsterdam will receive from tourist tax if the event lasts a week and you will have 30.000 visitors?
The municiplity of Amsterdam will receive 30.000 (visitors) * 3 (euro) * 6 (nights) = 540.000 euro

### Plot the amount of AirBnB locations per neighbourhood.

```python
import pandas as pd 
from pandas import DataFrame 
import matplotlib.pyplot as plt 
import osmnx as ox 
from geopy.geocoders import Nominatim 
import plotly.express as px 
from plotly.express import histogram  
airbnb = pd.read_csv(""PASTE THE PATH TO THE listings.csv FILE"") # read Airbnb file
airbnb.head() # print first five colums
fig = histogram(airbnb, x="neighbourhood", text_auto=True) 
fig.update_layout(height=500,title_text='Airbnb count per neighbourhood') 
fig.show() 
```
<img width="1073" alt="image" src="https://github.com/iepebouw/data1/assets/145610700/5b57a39a-855b-483e-9ace-dab4759272cf">


### Which street in Amsterdam has the most AirBnB apartments?
```python
#warning it costs 71 minutes to run
import itertools 
geolocator = Nominatim(user_agent="AMS") 
latitude = airbnb['latitude'].tolist() #add the airbnbs lat to a list 
longitude = airbnb['longitude'].tolist() #add the airbnbs lon to a list 

streets = [] #make a new street list 
for lat, lon in zip(latitude, longitude): #zip makes it possible to use two lists at the same time 

    coor = str(lat),str(lon) #coordinates of the airbnbs 
    address = geolocator.reverse(coor, zoom=16) #reverse geocode the coordinates, zoom=16 makes sure you only get the streets 
    streets.append(address.address) #add the addresses to the streetlist 
def most_frequent(List): #new function to get the most frequent occuring street 
    return max(set(List), key = List.count) 
print("The street with the most Airbnbs is:",most_frequent(streets),"and it has",streets.count(most_frequent(streets)),"Airbnbs") 
```
The street with the most Airbnbs is: Govert Flinckstraat, De Pijp, Zuid, Amsterdam, Noord-Holland, Nederland, 1072 LC, Nederland and it has 35 Airbnbs 

### Try to cross reference the data from the AirBnB dataset with the BBGA. Can you figure out if all apartments of AirBnB are designated as housing? Which number of apartments are not rented out all the time but are also used as normal housing?

```python
bbga = pd.read_excel(""PASTE THE PATH TO THE BBGA FILE"") # Read the BBGA file 
bbga_2018 = bbga[bbga.jaar == 2018] #filter on the first year there is a full dataframe
fig = histogram(bbga_2018, x="gebiednaam", y = "BHVESTAIRBNB", text_auto=True) #create a similar histogram but with BBGA
fig.update_layout(height=500,title_text='Airbnb count per neighbourhood BBGA') 
fig.show()# You can compare on the webiste (by saying the amounts are higher than the airbnb listings and by saying that there are more neighbourhoods than the airbnb listings)
total_amsterdam = bbga_2018._get_value(3926, 'BHVESTAIRBNB') #get the total amount 
percentage = 100 - (len(airbnb) / total_amsterdam * 100) #divide total availability of the listings list by the total availiability of BBGA*100%
print(percentage,"% is not always rented out but also used as normal housing") # This is the amount not on the airbnb site but registered as airbnb, this means people also live there 
```
71.01579511284692 % is not always rented out but also used as normal housing

<img width="1074" alt="image" src="https://github.com/iepebouw/data1/assets/145610700/64ba9107-b903-4cb1-a46f-fdcbcd391213">

### How many hotel rooms should be built if Amsterdam wants to accommodate the same number of tourists?

According to [Airbtics](https://airbtics.com/airbnb-occupancy-rates-by-city/) is on average 37% of all airbnbs occupied, which means we can use 63% of the listings.csv file.

According to [CBS](https://www.cbs.nl/en-gb/figures/detail/82058ENG) 46% of hotel rooms are occupied during the year in the netherlands, so 54% of all hotel rooms are available for visitors.
  
```python

#If we assume every Airbnb has two persons renting:
airbnb_spots = round(2 * len(airbnb) * 0.63) # times two because each room houses 2 people and 63 percent of the airbnbs are available 
hotel_spots = round(2 * bbga._get_value(2367, 'BHHOTKAM') * 0.54) # 54 percent of the hotel rooms are available 
visitors = 30000 # number of visitors 
if (airbnb_spots + hotel_spots) >= visitors: # if the total amount of rooms is more than the visitors, then you don't need to build anymore rooms 
    print("You don't need to build any hotel rooms") 
else: 
    number_rooms = (visitors - (airbnb_spots + hotel_spots)) / 2 #else calculate how many rooms you need to build, divided by two because each room houses 2 people 
    print("You have to build",number_rooms,"more rooms") 
```
You don't need to build any hotel rooms

### How many different licenses are issued?

```python
print("There are",len(airbnb["license"].unique()),"different licences") 
```
There are 7289 different licences 

Go to the next assignment: [Transport]({{site.baseurl}}/assignment-4)
