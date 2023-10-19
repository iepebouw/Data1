---
layout: page
title: Assignment 4
permalink: /assignment-4/
nav_order: 5
has_children: true
---

## Amsterdam Transport

#### Preparation

To prepare the data for this assignment we used the folowing steps:

``` python
import pandas as pd 
from pandas import DataFrame 
import matplotlib.pyplot as plt 
import osmnx as ox 
from geopy.geocoders import Nominatim 
import networkx as nx 

#Get Amsterdam as a graph, we only want to see the canals and rivers (waterways)
city = (ox.graph_from_place('Amsterdam, Netherlands',custom_filter=["waterway"])) 
print(city) 
```
```python
#Starting point of the race
start = ox.geocode("Haarlemmerplein 50, 1013 HS Amsterdam") 
print(start) 
```
```python
#Endpoint of the race
eind = ox.geocode("Olympisch Stadion 2, 1076 DE Amsterdam") 
print(eind) 
```

```python
#Closest point in the graph for the start- and endpoint 
start_node = ox.distance.nearest_nodes(city, start[1], start[0], return_dist=False) 
eind_node = ox.distance.nearest_nodes(city, eind[1], eind[0], return_dist=False) 
print(start_node,eind_node)
```

```python
#To calculate the (shortest) race route between these two nodes:
race = ox.shortest_path(city, start_node, eind_node) 
print(race)
```

```python
#To give a visualisation of this race route
pt = ox.graph_to_gdfs(city, edges=False).unary_union.centroid 
bbox = ox.utils_geo.bbox_from_point(start, dist=5000) 
fig, ax = ox.plot_graph_route(city,race,bbox=bbox)
```

#### Find the centre of the nodes of the swimming route.

```python
#Create two emptie lists, one for the latitude and one for the longtitude:
lat = [] 
long = [] 
#Calculate the latitude and longtitude for each point and put them in the corresponding list:
for node in race: 
    y = city.nodes[node]["y"]#request the latitude
    lat.append(y)#Add to the list
    x = city.nodes[node]["x"]#request the longtitude
    long.append(x)#Add to the list
  
#To calculate the centre you sum the latitudes and divide them over the amount of points (calculate the average). 
print(sum(lat)/len(lat)) 
# DO the same for the longtitudes
print(sum(long)/len(long))
```

#### Calculate the centrality of the start, finish, and centre node of the route. Which centrality calculation makes the most sense. 
For this excercise we want to know the centrality of the three nodes. How accessible start is to finish, and how accessible the centre is from/to the start and finish. Therefore the closeness centrality calculation is the best suitable option for this calculation.

```python
#Calculate the nearest location to these coordinates with reverse geocoding:
from geopy.geocoders import Nominatim
geolocator = Nominatim(user_agent="AMS")
location = geolocator.reverse("52.3675863, 4.8659963")
print(location.address)
```
```python
#For this excercise we need a map including the roads. As we had a map with only the waterways we need to create a new map:
city1 = (ox.graph_from_place('Amsterdam, Netherlands')) 
print(city1) 
#Use the previously calculated address:
centraal = ox.geocode("Wenslauerstraat 1C, 1053 AV Amsterdam") 
#Calculate the nearest nodes of the coordinates in this new graph:
start_node = ox.distance.nearest_nodes(city1, start[1], start[0], return_dist=False) 
eind_node = ox.distance.nearest_nodes(city1, eind[1], eind[0], return_dist=False) 
centr_node = ox.distance.nearest_nodes(city1, centraal[1], centraal[0], return_dist=False) 
print(start_node,eind_node,centr_node) 
punten = [start_node,eind_node,centr_node]
```
```python
#Now we calculate the centrality
print("The degree centrality for the three nodes is:",nx.group_closeness_centrality(city1,punten)) # je kiest hiervoor omdat je iets wil zeggen over hoe verbonden de drie punten met elkaar zijn
```



Go to the first assignment: [Paralympics]({{site.baseurl}}/assignment-1)
