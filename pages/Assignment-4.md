---
layout: page
title: Assignment 4
permalink: /assignment-4/
nav_order: 5
has_children: true
---

# Amsterdam Transport
{: .no_toc }

If the Paralympics become as big as we hope, we need a nice location for the Event Headquarters. Preferably, the Event Headquarters is close to the center of the swimming route, in a easily accessible location, with enough space for the stream of visitors and that could host a potential after party. 

If you run the code in Python, you get the interactive map with all the cafes and restaurants in the radius of a 10 minute walk as a result.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

### Preparation

To prepare the data for this assignment we used the following steps:

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

#Starting point of the race
start = ox.geocode("Haarlemmerplein 50, 1013 HS Amsterdam") 
print(start) 

#Endpoint of the race
eind = ox.geocode("Olympisch Stadion 2, 1076 DE Amsterdam") 
print(eind) 

#Closest point in the graph for the start- and endpoint 
start_node = ox.distance.nearest_nodes(city, start[1], start[0], return_dist=False) 
eind_node = ox.distance.nearest_nodes(city, eind[1], eind[0], return_dist=False) 
print(start_node,eind_node)

#To calculate the (shortest) race route between these two nodes:
race = ox.shortest_path(city, start_node, eind_node) 
print(race)

#To give a visualisation of this race route
pt = ox.graph_to_gdfs(city, edges=False).unary_union.centroid 
bbox = ox.utils_geo.bbox_from_point(start, dist=5000) 
fig, ax = ox.plot_graph_route(city,race,bbox=bbox)
```
<img width="615" alt="image" src="https://github.com/iepebouw/data1/assets/145610700/c179e566-a4ef-47e9-af69-7b6641f460d8">

### Find the centre of the nodes of the swimming route.

```python
import folium 
#Create empty list for latitude and longtitude 

lat = [] 
long = [] 

#For every node in rade calculate the following

for node in race: 
    y = city.nodes[node]["y"]#vraag de hoogtegrade op 
    lat.append(y)#voeg hem toe aan de lijst 
    x = city.nodes[node]["x"]#vraag de breedtegrade op 
    long.append(x)#voeg hem toe aan de lijst 

#to find the center of the nodes, sum all results and calculate mean 
print(sum(lat)/len(lat)) 
centr_node_lat = [sum(lat)/len(lat)] 
print(sum(long)/len(long)) 
centr_node_long = [sum(long)/len(long)] 

m = folium.Map([centr_node_lat[0], centr_node_long[0]], zoom_start=13,tiles="Cartodbdark_matter") #create a map  
location = [centr_node_lat[0], centr_node_long[0]] # add the locations  
cafe_name = "Centre Node" #add the name at the location points  
folium.CircleMarker(location=location, tooltip=cafe_name).add_to(m) #add a nice marker  
display(m) 
```
Centre node of the swimming route:
<img width="637" alt="image" src="https://github.com/iepebouw/data1/assets/145610700/3c1cacc4-395b-4ef9-afee-3803572ba4f6">

### Use the centre to find a suitable spot for the Event Headquarters.

The centre is close to the [Hallen](https://www.iamsterdam.com/uit/agenda/nachtleven/clubbing/de-hallen-amsterdam), which is a huge complex which can facilitate the EH for the time being. It has good accessibility and parking, and with collaboration of the other enterprises in de Hallen it could also mean a local boost for the local shops during the event. 

![image](https://github.com/iepebouw/data1/assets/144791642/3f5b4e56-3d4c-47a6-8bcb-d34e90ee1dc2)


### Find the closest bus and tram stops at the start and finish of the swimming route. How many people can be transported within an hour?

```python
import overpy 
walking_radius = 300 #We decided on a 300 meter radius 
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
stops =[] 
stop_lat = []  
stop_long = []  
# printing the results in a list 

for node in result.nodes: 
    tag = node.tags.get("name", "Unknown") 
    if tag.startswith("Amsterdam"): #there are some stops with the same name but with Amsterdam in front of it, so this if-statement skips those stops 
            pass 
    else: 
            stop_lat.append(node.lat) 
            stop_long.append(node.lon) 
            stops.append(tag) 
print("The stops are:",set(stops)) #only print the unique stops 
print("There are",len(set(stops)),"stops") #print how many stops there are 

stops_coords = pd.DataFrame({"name":stops,"lat":stop_lat,"lon":stop_long}) #make a new dataframe with the stops information  

m = folium.Map([stops_coords.lat.mean(), stops_coords.lon.mean()], zoom_start=13,tiles="Cartodbdark_matter") #create a map  

for i in range(stops_coords.shape[0]):  
  location = [stops_coords.lat.iloc[i], stops_coords.lon.iloc[i],] # add the locations  
  cafe_name = stops_coords.name.iloc[i] #add the name at the location points  
  folium.CircleMarker(location=location, tooltip=cafe_name).add_to(m) #add a nice marker  
display(m) 
```

 <img width="640" alt="image" src="https://github.com/iepebouw/data1/assets/145610700/89ac4607-c3bc-40ba-8ff2-4c3f1393a087">

A bus in Amsterdam has a maximum capacity of 150 passengers [GVB](https://over.gvb.nl/ov-in-amsterdam/voer-en-vaartuigen/bus-in-cijfers/)
A tram in Amsterdam has a maximum capacity of 151 passengers [GVB](https://over.gvb.nl/ov-in-amsterdam/voer-en-vaartuigen/tram-in-cijfers/)
A metro in Amsterdam has a maximum capacity of 480 passengers [GVB](https://over.gvb.nl/content/uploads/2018/11/Factsheet-CAF-GVB-M7-metro-voor-Amsterdam.pdf)
 
Looking at the GVB website schedule we could detact that the metro leaves every 10 minutes (6 times in an hour), the busses and trams at the finish run every 15 minutes (4 times in an hour) and the busses and trams at the start every 10 minutes (6 times in an hour) 

```python
tram = 151 
bus = 150 

def passengers_an_hour(line,capacity,freq): 
    nmbr = capacity * freq 
    return nmbr,print("The amount of people that can be transported with line",line,"is",nmbr) 

#For Haarlemmerplein 
bus22 = passengers_an_hour(22,bus,6) 
bus18 = passengers_an_hour(18,bus,6) 
tram5 = passengers_an_hour(5,tram,6) 

#For Olympisch Stadion 
bus62 = passengers_an_hour(62,bus,4) 
tram24 = passengers_an_hour(24,tram,4) 

#For Olympiaweg 
bus15 = passengers_an_hour(15,bus,4) 
tram24 = passengers_an_hour(24,tram,4) 

#For Jan Wilsbrug isn't actually a stop so that's an error of the tagging 
print("The total amount of passengers that can be transported an hour is",(bus22[0]+bus18[0]+tram5[0]),"at the start") 
print("The total amount of passengers that can be transported an hour is",(bus62[0]+tram24[0]+bus15[0]+tram24[0]),"at the finish")

```
The amount of people that can be transported with line 22 is 900
The amount of people that can be transported with line 18 is 900
The amount of people that can be transported with line 5 is 906
The amount of people that can be transported with line 62 is 600
The amount of people that can be transported with line 24 is 604
The amount of people that can be transported with line 15 is 600
The amount of people that can be transported with line 24 is 604
The total amount of passengers that can be transported an hour is 2706 at the start
The total amount of passengers that can be transported an hour is 2408 at the finish


#### In summary: 
The total amount of passengers that can be transported an hour is 2706 at the start

The total amount of passengers that can be transported an hour is 2408 at the finish

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
1C, Wenslauerstraat, Kinkerbuurt, Oud-West, West, Amsterdam, Noord-Holland, Nederland, 1053 AV, Nederland
46447625 298097398 46367797
The degree centrality for the three nodes is: 0.02004401535297431

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
