# Product Documentation

## Examples

### 1. Read Infrastructure and Demographic Data inside a custom Polygon

Infrastructure variables include examples such as Restaurants, gyms, Grocery
stores, etc and demographics include variables such as Population and Area of
the given custom polygon. There are a lot of open-sourced tools from where you
can draw a polygon and get its coordinates\(such as
[kepler.gl](https://kepler.gl/) and [geojson.io](https://geojson.io/)\). These
variables help to analyze different prospects of areas of interest.

```python
# Import Libraries
import requests

# Define a closed polygon
data = {
    "coordinates": [
        {"lat": 13.388617618153711, "lng": 77.57514029874933},
        {"lat": 12.81883149952699, "lng": 77.03310705621149},
        {"lat": 12.752539132573569, "lng": 77.59368896484375},
    ],
    "status": "False",
}
Base_URL = ""
response = requests.post(Base_URL + "/post_json", json=data)
data = response.json()
print(data)
```

![Response from above endpoint ](.gitbook/assets/image.png)

### 2. Find out Reachability from a specific point of location and properties inside the returned contours of polygons

The Isochrone support from our API is intuitive in a way that given a routing
profile you will be able to get the information regarding the "within-reach"
areas. It helps you analyze whether there is a sufficient count of variables of
your interest around you. For eg. You are on a vacation and you want to find out
how many tourist spots or delight restaurants are around your location within
the walking limit.

```python
# Define Imports
import geopandas as gd
import pandas as pd
import requests
from keplergl import KeplerGl
from shapely.geometry import Point, Polygon

# Define the request parameters
"""
coordinates:Coordinate pair around which to center the isochrone lines.
contour_minutes:The time in minutes to use for each isochrone contour
profile:routing profile(driving,walking,cycling)
"""
payload = {
    "coordinates": "77.5946,12.9716",
    "profile": "cycling",
    "contours_minutes": "15",
}
Base_URL = ""
response = requests.post(Base_URL + "/isochrone_api", json=payload)
data = response.json()
coords = [float(i) for i in payload["coordinates"].split(",")]
columns = ["point_lat", "point_lng", "icon"]
icon = pd.DataFrame([coords.append("route-dot")], columns=columns)
isochrone = gd.GeoDataFrame(index=[0], geometry=[Polygon(data["coordinates"])])
map = KeplerGl(height=500, data={"icon": icon, "isochrone": isochrone})
map
print(data["data"])
```

![Ischrone for given Latitude and Longitude](.gitbook/assets/image%20%282%29%20%281%29.png)

![Data Response for Above Isochrone](.gitbook/assets/image%20%283%29.png)

### 3. Visualize a Heatmap for your Choice of Variable

Heatmap is a data visualization technique that helps analyze the magnitude of variables of interest. Suppose you are planning to open a restaurant in an area it is good to analyze your competition in different parts of the city.

```python
import requests
from keplergl import KeplerGl

args = {"var": "rest_count"}
Base_URL = ""
response = requests.get(Base_URL + "/sheatmap", params=args)
data = response.json()
heatmap = pd.DataFrame(data)
map = KeplerGl(height=500, data={"data": heatmap})
map
```

![Heatmap for Restaurants in Bengaluru](.gitbook/assets/image%20%284%29.png)
