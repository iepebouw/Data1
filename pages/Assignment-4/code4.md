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
- Overpy
- Folium

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

#-------------------------------------------------------------------------------------------------

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

#-------------------------------------------------------------------------------------------------

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

#-------------------------------------------------------------------------------------------------

#Calculate the nearest location to these coordinates with reverse geocoding:
from geopy.geocoders import Nominatim
geolocator = Nominatim(user_agent="AMS")
location = geolocator.reverse("52.3675863, 4.8659963")
print(location)
city1 = (ox.graph_from_place('Amsterdam, Netherlands'))

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
