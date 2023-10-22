---
layout: page
title: Python Code
permalink: /code4/
parent: Assignment 4
nav_order: 1
has_children: yes
---

## Python Code Excercise 4

Below the complete code for excercise 4. Just copy/paste in the top right corner to use it yourself:

_make sure the following libraries are installed:_
- Pandas
- Matplotlib
- Osmnx
- Networkx
- overpy

Runtime code: Around 1m 30s

```python
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

eind = ox.geocode("Olympisch Stadion 2, 1076 DE Amsterdam") 
print(eind)

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



#-------------------------------------------------------------------------------------------------

#Create two empty lists, one for the latitude and one for the longtitude:
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

#-------------------------------------------------------------------------------------------------

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

#-------------------------------------------------------------------------------------------------

#Calculate the nearest location to these coordinates with reverse geocoding:
from geopy.geocoders import Nominatim
geolocator = Nominatim(user_agent="AMS")
location = geolocator.reverse("52.3675863, 4.8659963")
print(location)

#We calculate the nearest node for location
centraal = ox.geocode("Wenslauerstraat 1C, 1053AV, Amsterdam") 
start_node = ox.distance.nearest_nodes(city1, start[1], start[0], return_dist=False) 
eind_node = ox.distance.nearest_nodes(city1, eind[1], eind[0], return_dist=False) 
centr_node = ox.distance.nearest_nodes(city1, centraal[1], centraal[0], return_dist=False) 
print(start_node,eind_node,centr_node)

#Now we calculate the centrality
punten = [start_node,eind_node,centr_node] 
print("The degree centrality for the three nodes is:",nx.group_closeness_centrality(city1,punten))

#-------------------------------------------------------------------------------------------------

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
