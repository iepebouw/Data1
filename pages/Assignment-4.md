---
layout: page
title: Assignment 4
permalink: /assignment-4/
nav_order: 5
has_children: true
---

## Amsterdam Transport
{: .no_toc }
For this exercise you have chosen a route in Amsterdam for the canal swimming event.
Preferably you have this route calculated using Python. You can set a start point and an end
point and then try to find a route that has a certain distance (min. 5 km.)
The Municipality wants you to find a location for the Event Headquarters. They decided it
would be best if this E.H. is as close to the centre of the swimming route. There is a bit of a
concern for the after party and the stream of visitors. They want you to quantify the number of
visitors that can reach the event and the capacity for festivities after the event.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

### Preparation

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

### Find the centre of the nodes of the swimming route.

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

### Use the centre to find a suitable spot for the Event Headquarters.

The centre is close to the Hallen, which is a huge complex which can facilitate the HQ for the time being. It has good accessibility and parking, and with collaboration of the other enterprises in de Hallen it could also mean a local boost for the local shops during the event. 

### Find the closest bus and tram stops at the start and finish of the swimming route. How many people can be transported within an hour?

```python
import overpy 
walking_radius = 500 #We decided on a 500 meter radius 
 
# getting information about public transport stops 
query = f""" 
    [out:json]; 
    ( 
        node["public_transport"="stop_position"] 
            (around:{walking_radius},{start[0]},{start[1]}); 
        node["public_transport"="stop_position"] 
            (around:{walking_radius},{eind[0]},{eind[1]}); 
    ); 
    out center; 
""" 
api = overpy.Overpass() 
result = api.query(query) 
 
# printing the results in a list 
for node in result.nodes: 
    print(f'Node: {node.tags.get("name", "Unknown")}, Location: ({node.lat}, {node.lon})') 
print(len(result.nodes)) 
```
Bushaltes zijn handmatig verkregen door te kijken naar de website van de GVB en Google Maps: 
Start - Nassauplein: Bus 22 (to Sloterdijk and Muiderpoort) [52.38538340407277, 4.881430461066379] 
Haarlemmerplein: Bus 22 (to Sloterdijk and Muiderpoort), Bus 18 (to Slotervaart and Centraal), Bus 21 (to Geuzenveld and Centraal), Tram 5 (to Amstelveen Stadshart and Zoutkeetsgracht) [52.38496779311146, 4.883595326000165] 
Finish – Olympisch Stadion: Bus 62 (to Station Lelylaan and Rivierenbuurt), Tram 24 (to VUmc and Centraal)[52.34396636629443, 4.856894263000401] 
Olympiaweg: Bus 15 (to Sloterdijk and Station Zuid), Tram 24 (to VUmc and Centraal)[52.345251047340675, 4.858472173464992] 
Amstelveenseweg: metro 50 (to Gein and Isolatorweg), metro 51 (to Centraal and Isolatorweg)[52.33858597150655, 4.857614965924259] 
[GVB](https://reisinfo.gvb.nl/nl/haltes/07121)
 
A bus in amsterdam has a maximum capacity of 150 passengers [GVB](https://over.gvb.nl/ov-in-amsterdam/voer-en-vaartuigen/bus-in-cijfers/)
A tram in amsterdam has a maximum capacity of 151 passengers [GVB](https://over.gvb.nl/ov-in-amsterdam/voer-en-vaartuigen/tram-in-cijfers/)
A metro in amsterdam has a maximum capacity of 480 passengers [GVB](https://over.gvb.nl/content/uploads/2018/11/Factsheet-CAF-GVB-M7-metro-voor-Amsterdam.pdf)
 
Looking at the GVB website schedule we could detact that the metro leaves every 10 minutes (6 times in an hour), the busses and trams at the finish run every 15 minutes (4 times in an hour) and the busses and trams at the start every 10 minutes (6 times in an hour) 
 
So a little math gives us the following answer: 
For Nassauplein: 2 (to Sloterdijk AND to Muiderpoort) * 150 (capacity) * 6 (frequency) = 1800 passengers / hour 
For Haarlemmerplein: (6 * 150 * 6) + (2 * 151 *6) = 7212 passengers / hour 
For Olympisch Stadion: (2 *150 * 4) + (2 * 151 * 4) = 2408 passengers / hour 
For Olympiaweg: (2 *150 * 4) + (2 * 151 * 4) = 2408 passengers / hour 
For Amstelveenseweg: 4 *480 * 6 = 11520 passengers / hour 
Off course all calculated with python.

#### In summary: 
The Start can transport 9012 passengers / hour 
The Finish can transport 16336 passengers / hour 

### Can you find which bus and tram lines these are, and can you find their routes?
See answer on previous question


### Calculate the centrality of the start, finish, and centre node of the route. Which centrality calculation makes the most sense. [See this link.](https://networkx.org/documentation/stable/reference/algorithms/centrality.html)
For this excercise we want to know the centrality of the three nodes. How accessible start is to finish, and how accessible the centre is from/to the start and finish. Therefore the closeness centrality calculation is the best suitable option for this calculation.

```python
#Calculate the nearest location to these coordinates with reverse geocoding:
from geopy.geocoders import Nominatim
geolocator = Nominatim(user_agent="AMS")
location = geolocator.reverse("52.3675863, 4.8659963")
print(location)
```
```python
#We calculate the nearest node for location
centraal = ox.geocode("Wenslauerstraat 1C, 1053AV, Amsterdam") 
start_node = ox.distance.nearest_nodes(city1, start[1], start[0], return_dist=False) 
eind_node = ox.distance.nearest_nodes(city1, eind[1], eind[0], return_dist=False) 
centr_node = ox.distance.nearest_nodes(city1, centraal[1], centraal[0], return_dist=False) 
print(start_node,eind_node,centr_node) 
```
```python
#Now we calculate the centrality
punten = [start_node,eind_node,centr_node] 
print("The degree centrality for the three nodes is:",nx.group_closeness_centrality(city1,punten))
```

### Find all cafes, restaurants near the finish line. Walking time smaller than 10 minutes
```python
import folium 
walking_radius = 800 #We decided on a 500 meter radius 
 
# getting information about the cafés and restaurants 
query = f""" 
    [out:json]; 
    ( 
        node["amenity"="cafe"] 
            (around:{walking_radius},{eind[0]},{eind[1]}); 
        node["amenity"="restaurant"] 
            (around:{walking_radius},{eind[0]},{eind[1]});       
    ); 
    out center; 
""" 
api = overpy.Overpass() 
result = api.query(query) 
 
#Now we start a loop for every cafe and restaurant found in the area 
cafe_name = [] 
cafe_lat = [] 
cafe_long = [] 
for node in result.nodes: 
    cafe_lat.append(node.lat) #add the lat to the list 
    cafe_long.append(node.lon) #add the long to the list 
    coor = str(node.lat),str(node.lon) #We make a string of the lat and the long 
    address = geolocator.reverse(coor) #and reverse geocode the address to get the name of the café as well 
    cafe_name.append(address.address) #add the name to the list 
     
    print(address) #print all addresses 
 
cafe_coords = pd.DataFrame({"name":cafe_name,"lat":cafe_lat,"lon":cafe_long}) #make a new dataframe with the café information 
 
m = folium.Map([cafe_coords.lat.mean(), cafe_coords.lon.mean()], zoom_start=15,tiles="Cartodbdark_matter") #create a map 
for i in range(cafe_coords.shape[0]): 
  location = [cafe_coords.lat.iloc[i], cafe_coords.lon.iloc[i],] # add the locations 
  cafe_name = cafe_coords.name.iloc[i] #add the name at the location points 
  folium.CircleMarker(location=location, tooltip=cafe_name).add_to(m) #add a nice marker 
display(m)
```

![image](https://github.com/iepebouw/data1/assets/145610700/b8d74cec-c4a2-48e9-a3dd-ba2d91e0746d)


Go to the first assignment: [Paralympics]({{site.baseurl}}/assignment-1)
