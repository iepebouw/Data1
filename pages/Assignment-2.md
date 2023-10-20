---
layout: page
title: Assignment 2
permalink: /assignment-2
nav_order: 3
---

## Amsterdam TourBoats
Amsterdam has set progressive goals in order to comply with the Clean Air Policy. The city must be completely emission-free by 2030, and the goal is to have exclusively emission-free pleasure boats within the canal belt by 2025. All boats have a vignette, and from 2025 on only boats with a green vignette (electric or hand-propelled boats) or a yellow vignette (hybrid-boats, only using electric motor when entering the emission-free zones) are allowed to enter the canals of the city center. The through shipping routes are an exception from this, and boats with white () or red () vignettes are still allowed to enter these waterways until the transitional periode of 4 years ends. The IJ ferries that connect Amsterdam North with the rest of Amsterdam also need to be emission-free by 2025. 

Even though the event will take place in 2024, it is still important to limit the pollution levels in the swimming water and to have safe water quality. The municipality also wants to create ideal conditions to ensure high performances. In addition, the event must be energy neutral. To make sure that the swimming route of the Paralympics meets this requirements, we need several data sets. Data on the motor of the boats, peak hours on the canals, carbon footprint, wind and solar energy is in the table below.


| Name | File type | Source | Comments|
| :-----------|:-----------|:--------|:-----|
| Grachtenmonitor 2022 | pdf | https://openresearch.amsterdam/nl/page/92981/grachtenmonitor-2022 | Information about ditribution of vignetten (q1), peak times (q2) |
|WOWSA Rules & Regulations |URL|https://www.openwaterswimming.com/docs/rules-regulations/wowsa-rules-regulations/ | Rules and regulations regarding open water swimming, e.g. information about position boats |


### How many of the canal boats currently in use are diesel/fossil fuel driven and how many boats are electrical driven?
Vignettes are valid for one year, and thus need to be renewed every year. From the tabel below, we can conclude that there are 7350 boats in total in 2022, excluding passage boats. Only 34% of these vignettes is green or yellow, so only 34% of these boats is allowed to enter emission-free zones. Almost 31% of the boats is fully electrical driven. In 2024, a higher amount of green or yellow vignettes can be expected, since it approaches the enddate for the emission-free zones in 2025. Moreover, the municipality is installing more charging points for electric boats, to make the transition go faster.
Add table from grachtenmonitor 2022 page 19

### Are there peak times for the canals?
The canals in the canalbelt are the most intensively used canals, but this also varies during the day. In the figure (add figure grachtenmonitor 2022 page 13) can be seen that from 8AM there is a sharp increase in boats on the canals. At 4 PM passenger shipping peaks, and between 6PM and 7PM there is a peak of leisure boats. From this we can conclude that an event in the afternoon has a bigger impact on shipping than am event held earlier that day. 

### Use canal boats more or less energy in relation to their carbon footprint compared to another activity?
To calculate whether the use of canal boats has less impact on the environment than another activity, we compared the carbon footprint of a canal boat to the carbon footprint of a hop on hop off bus. 

To answer this question we first need to make a definition for the carbon footprint. This definition is used: 
1. For comparison: a traditional car’s footprint is calculated by multiplying the quantity of fuel used in a year by an “emissions factor,” the pounds of CO2 emitted by combustion of one gallon of that fuel. ([Terrapass](https://terrapass.com/blog/driving-calculator-20/#:~:text=First%2C%20for%20comparison%3A%20a%20traditional,one%20gallon%20of%20that%20fuel.))
2. To make it easier we convert the fuel usage towards kWh and the CO2 emission towards this value as well. We assume that for the olympic variant electric canal boats are used as this whole excercise is about the impact on the environment and we are in the middle of a transition to emission-free Amsterdam.
The estimate for carbon emissions of electricity 295 gCO2/kWh ([European Environment Agency](https://www.eea.europa.eu/data-and-maps/daviz/co2-emission-intensity-5#tab-googlechartid_chart_11_filters=%7B%22rowFilters%22%3A%7B%7D%3B%22columnFilters%22%3A%7B%22pre_config_ugeo%22%3A%5B%22European%20Union%20(current%20composition)%22%5D%7D%7D))
3. The estimate of carbon emission for diesel is 2700g C02/liter ([Autosmart](https://natural-resources.canada.ca/sites/www.nrcan.gc.ca/files/oee/pdf/transportation/fuel-efficient-technologies/autosmart_factsheet_9_e.pdf)):
- 1 liter of diesel is 0,84kg ([CBS](https://www.cbs.nl/en-gb/our-services/methods/definitions/weight-units-energy))
- 1 kg of diesel is 12.7 kWh ([Quora](https://www.quora.com/How-can-I-convert-diesel-consumption-to-kWh#:~:text=If%20you%20look%20at%20the,12.7%20kWh%2Fkg%20for%20diesel.))
4. So the carbon emission of 1kWh of Diesel is:
```python
((1/0.84)*2700)/12.7
```
_This gives 253.0933633295838gCO2/kWh_

5. The average usage of a canal boat is 15.6 kWh ([Waternet](https://www.waternet.nl/siteassets/innovatie/electric-shipping-in-the-city-of-amsterdam-tno2.pdf))
6. The average usage of a similar English variant of the hop on hop of bus is 1:5,5 ([Pverbeek](https://www.pverbeek.nl/verkoop/#:~:text=Onze%20Engelse%20dubbeldekker%20bussen%20bijvoorbeeld,een%20moderne%20vrachtwagen%20en%20autobus!))
```python
print("carbon footprint canal boat in CO2/h:",15.6*295)

print("carbon footprint hop on hop of bus in CO2/l:",((5.5/0.84)*2700)/12.7)

```
_carbon footprint canal boat in CO2/h: 4602.0_

carbon footprint hop on hop of bus in CO2/h: 1392.013498312711
7. These are the CO2 emissions of the canal boat per hour AND the CO2 emissions of the bus PER LITER. To calculate how much this is an hour:
- The map of [Spotzi](https://www.researchgate.net/figure/Map-of-average-traffic-speeds-in-central-Amsterdam-Source-Spotzi_fig5_332660949) shows an average speed on the [routes](https://www.citysightseeingamsterdam.nl/nl/route-stops/) of the bus of around 15-30 Km/H. Assuming the bus stops quite a lot of times probably the average speed is 15km/h.
8. This means that the average usage of the bus is
```python
print("carbon footprint hop on hop of bus in CO2/h:", (15.5/5.5)*((5.5/0.84)*2700)/12.7)
```
_carbon footprint hop on hop of bus in CO2/h: 3922.947131608549_

9. From the above made calculations we can conclude that canal boats for tourists use more energy than the touristic hop on hop off buses, since 4602 > 3922. 

### Would you consider it economically feasible?
The result from the calculations above shows how electric boats use more energy than diesel driven buses. Despite this conclusion, in the long run, electric canal boats are more fututre proof when it comes to the economical perspective. From 2025 on all vehicles need to be [emission-free](https://www.amsterdam.nl/en/policy/sustainability/clean-air/), or in transition to becoming emission-free. Diesel driven vehicles are forbidden in Amsterdam from 2030 on, so whether this shift to emission-free verhicles is made now, or in a few years, does not matter.

### How many support boats and vehicles are needed for the Paralympics event only?
Support boats are not compulsory for open water swimming events, but strongly recommended. The amount of support boats and paddlers depends on the waterbody where the event is being helt. An open water body requires and has more room for safety boats and paddlers than a swimming route through the narrow canals. Is it difficult to find guidelines about how many support boats and paddlers are recommended for a big event like the Paralympics. A somewhat similar event might be [Amsterdam City Swim](https://www.amsterdamcityswim.nl/informatie/waterveiligheid), where lifeguards in the water and supervisors on the quay were present. In addition to this, every 500 meters there is a first aid post and rest point. To guard safety on the swimming route of the Paralympics, there are 10 of these first aid posts and resting points needed. The amount of lifeguards can only be determined when the number of participants is known. 









Go to the next assignment: [Housing]({{site.baseurl}}/assignment-3)
