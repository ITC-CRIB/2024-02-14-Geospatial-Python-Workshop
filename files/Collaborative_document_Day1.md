![](/codimd/uploads/8f24674fa3405411f0ed7e800.png)

# Collaborative Document

2024-02-14 Introduction to Geospatial Raster and Vector Data with Python.

Welcome to The Workshop Collaborative Document.

This Document is synchronized as you type, so that everyone viewing this page sees the same text. This allows you to collaborate seamlessly on documents.

----------------------------------------------------------------------------

This is the Document for today: [link](https://crib.utwente.nl/codimd/2024-02-14-Geospatial-Python-Workshop-Day1)

Collaborative Document day 1: [link](https://crib.utwente.nl/codimd/2024-02-14-Geospatial-Python-Workshop-Day1)

Collaborative Document day 2: [link](https://crib.utwente.nl/codimd/2024-02-14-Geospatial-Python-Workshop-Day2)


## 👮Code of Conduct

Participants are expected to follow these guidelines:
* Use welcoming and inclusive language.
* Be respectful of different viewpoints and experiences.
* Gracefully accept constructive criticism.
* Focus on what is best for the community.
* Show courtesy and respect towards other community members.
 

## ⚖️ License

All content is publicly available under the Creative Commons Attribution License: [creativecommons.org/licenses/by/4.0/](https://creativecommons.org/licenses/by/4.0/).

## 🙋Getting help

Contact nearby Helper or put on RED sticker note on your laptop if you need assistance.


## 🖥 Workshop website

[link](https://itc-crib.github.io/2024-02-14-Geospatial-Python-Workshop/)

## 🛠 Setup

🐍 Python & Environment:

[link](https://carpentries-incubator.github.io/geospatial-python/#software-setup) 

📚 Download files:
- [brpgewaspercelen_definitief_2020_small.gpkg](https://figshare.com/ndownloader/files/37729413)
- [brogmwvolledigeset.zip](https://figshare.com/ndownloader/files/37729416)
- [status_vaarweg.zip](https://figshare.com/ndownloader/files/37729419)

## 👩‍🏫👩‍💻🎓 Instructors

Robert Ohuru, Rosa Aguilar, Jay Gohil

## 🧑‍🙋 Helpers

Indupriya Mydur, Adhitya Bhawiyuga  

## Attendance

---
## 🗓️ Agenda

**Day 1**
 
| Time  | Topic                                         |
| ----- | --------------------------------------------- |
| 09:30 | Welcome and icebreaker, setup check           |
| 09:45 | Introduction to raster, vector, and CRS       |
| 10:00 | Access satellite imagery using Python         |
| 11:00 | *Coffee break*                                |
| 11:15 | Read and visualize raster data         |
| 12:30 | *Lunch*                                |
| 13:30 | Vector data in Python                |
| 14:30 | *Coffee break*                        |
| 14:45 | Raster with rioxarray and geopandas|
| 15:45 | *Short break*                                   |
| 16:00 | Raster calculations in Python |
| 16:45 | Wrap-up |
| 17:00| END of Day 1|

**Day 2**

| Time  | Topic                                         |
| ----- | --------------------------------------------- |
| 09:15 | Welcome and icebreaker                       |
| 09:30 |  Calculating Zonal Statistics on Rasters    |
| 10:45 | *Coffee break*                                |
| 11:00 | Parallel raster computations using Dask       |
| 12:15 | Wrap-up                                |
| 12:30 | END                                         |

## 🔧 Exercises


Search for all the available Sentinel-2 scenes in the sentinel-2-l2a collection that satisfy the following criteria: 
- intersect a provided bounding box (use ±0.01 deg in lat/lon from the previously defined point); 
- have been recorded between 20 March 2020 and 30 March 2020; 
- have a cloud coverage smaller than 10% (hint: use the query input argument of client.search).

How many scenes are available? Save the search results in GeoJSON format.



## 🧠 Collaborative Notes

Is the environment setup working on your computer?


```sh
conda activate -n geospatial
jupyter lab
```

```python
import rioxarray
```

### Introduction on raster data and data formats

- Access STAC catalog data
- STAC: `pystac-client` and `pystac`
- Raster data: `rioxarray` which is `xarray` with `rasterio` (based on GDAL)
```python
api_url = "https://earth-search.aws.element84.com/v1"
```
```python
from pystac_client import Client
```
```python
client = Client.open(api_url)
```
```python
collection = "sentinel-2-l2a"
```
```python
from shapely.geometry import Point
point = Point(4.89, 52.37)
point
```
```python
search = client.search(
    collections=[collection],
    intersects=point,
    max_items=10
)
```
```python
print(search.matched())
```
```python
items = search.item_collection()
print(len(items))
```
```python
for item in items:
    print (item)
```
```python
item = items[0]
print(item.properties)
```
```python
print(item.geometry)
```
```python
print(item.datetime)
```
```python
print(item.extrafields)
```
```python
bbox = point.buffer(0.01).bounds    #only .buffer gives a circle
```
```python
search = client.search(
    collection=[collection],
    bbox=bbox,
    datetime=2020-03-20/2020-03-30",
    query=["eo:cloud_cover<15"]
)
print(search.matched())
```
```python
items = search.item_collection()
items.save_object("search.json")
```
```python
assets = items[0].assets
assets
```
```python
for key, asset in assets.items():
    print(key, asset.title)
```
```python
print (assets["thumbnail"].href)
```

### Read and visualize raster data

```python
import pystac
items = pystac.ItemCollection.from_file("search.json")
items
```
```python
import rioxarray
```
search.json file can be downloaded from this link:
https://github.com/ITC-CRIB/2024-02-14-Geospatial-Python-Workshop/blob/gh-pages/files/search.json
```python
item = items[0]
assets = item.assets
assets.keys()
```
```python
nir09 = assets['nir09']
nir09.href
```
```python
raster_ams_b9 = rioxarray.open_rasterio(nir09.href)
raster_ams_b9    #one band
```
```python
visual = assets['visual']
visual.href
```
```python
visual_ams_visual = rioxarray.open_rasterio(visual.href)
visual_ams_visual    # 3 bands
```
```python
print (raster_ams_b9.rio.crs)
```
```python
print (raster_ams_b9.rio.bounds())
```
```python
print (raster_ams_b9.rio.height, raster_ams_b9.rio.width)    #pixels
```
```python
print (raster_ams_b9.rio.nodata)    #value that is given to nodata, not number of nodata values
```
```python
raster_ams_b9.values
```
```python
raster_ams_b9.plot()
```
```python
raster_ams_b9.plot(robust=True)    #improves contrast, stretch bw 2-98 percentile
```
```python
raster_ams_b9.plot(vmin=100, vmax=7000)    #visualise between pixel values 100 - 7000
```
```python
#Lets mask out nodata values in order to visualise better, and useful for calculations
raster_ams_b9 = rioxarray.open_rasterio(nir09.href, masked=True)    
raster_ams_b9.plot()
```
```python
raster_ams_b9.values    # 0 replaced with nan, dtype change
```
```python
print("Max=", raster_ams_b9.max(), "Min=", raster_ams_b9.min())
```
```python
visual = assets['visual']
raster_ams_visual = rioxarray.open_rasterio(visual.href)
raster_ams_visual 
```
```python
raster_ams_visual.shape
```
```python
# raster_ams_visual.plot()
raster_ams_visual.plot.imshow()    #multiple bands
```
```python
# using overview parameter to load a lower resolution image
visual = assets['visual']
raster_ams_visual = rioxarray.open_rasterio(visual.href, overview_level=3)
raster_ams_visual.plot.imshow() 
```
```python
from pyproj import CRS
```
```python
epsg = raster_ams_b9.rio.crs.to_epsg()
crs = CRS(epsg)
crs
```
```python
crs.name
```
```python
crs.area_of_use
```
```python
crs.axis_info
```
```python
raster_ams_b9.rio.width
```
```python
# Subset the raster to save a smaller file
raster_ams_b9[0,500:1000,500:1000].rio.to_raster("nir09_subset.tif")
```
### Vector data in Python
- Tabular data: `geopandas`: all of `pandas` with geospatial querying

```python
import geopandas as gpd
```
```python
fields = gpd.read_file('./data/brpgewaspercelen_definitief_2020_small.gpkg')
fields
```
```python
fields.plot()
```
```python
fields.columns
```
```python
fields['category']
```
```python
fields['geometry']
```
```python
# filter by attribute
fields[fields.category == 'Grasland']
```
```python
fields[fields.gewascode == 265]
```
```python
# describes only numerical data
fields.describe()
```
```python
# for data types of each column
fields.dtypes
```
```python
fields_1row = gpd.read_file('./data/brpgewaspercelen_definitief_2020_small.gpkg', rows=1)
fields_1row
```
```python
# (no. of rows, no. of columns)
fields_1row.shape, fields.shape
```
```python
# CRS information
fields.crs
```
```python
# coordinates of xmin,xmax,ymin,ymax
fields.total_bounds
```
```python
# Define bounding box
xmin, xmax = (120000, 135000)
ymin, ymax = (485000, 500000)
```
```python
fields_box = gpd.read_file('./data/brpgewaspercelen_definitief_2020_small.gpkg', bbox=(xmin, ymin, xmax, ymax))
```
```python
fields_box.plot()
```
```python
#check total bounds of bbox
fields_box.total_bounds
```
Use https://geojson.io/#map=2/0/20 to get coordinates in lat long for bounding box
```python
# Using fiona for inspecting the dataframe
import fiona
```
```python
with fiona.open('./data/brpgewaspercelen_definitief_2020_small.gpkg') as f:
    bounds = f.bounds
    crs = f.crs
```
```python
bounds
```
```python
crs
```
```python
print(crs)
```
```python
xmin, xmax = (120000, 135000)
ymin, ymax = (485000, 500000)
fields_cx = fields.cx[xmin:xmax, ymin:ymax]
```
```python
fields_cx.plot()
```
```python
# Write files with geopandas
fields_cx.to_file('./data/fields_cropped.shp')
```
```python
# Save as geopackage
fields_cx.to_file('./data/fields_cropped.gpkg')
```
```python
wells = gpd.read_file('./data/brogmwvolledigeset.zip')
```
```python
wells.plot(markersize=0.1)
```
```python
wells_clip = wells.clip(fields_cx)
```
```python
wells.crs, fields.crs
```
```python
wells = wells.to_crs(fields_cx.crs)
```
```python
wells.crs
```
```python
fields.crs
```
```python
wells_clip = wells.clip(fields_cx)
```
```python
wells_clip.plot()
```
```python
# plot the two datasets together using matplotlib
from matplotlib import pyplot as plt
fig, ax = plt.subplots()    #default one subplot
fields_cx.plot(ax=ax)    #using the same axis for both datasets
wells_clip.plot(ax=ax, color='r')    #wells_clip in red
```
```python
buffer = fields_cx.buffer(50)
```
```python
wells_clip_buffer = wells.clip(buffer)
```
```python
fig, ax = plt.subplots()
fields_cx.plot(ax=ax)
wells_clip_buffer.plot(ax=ax,color='r')
```
```python
fig, axes = plt.subplots(2,1)
for ax, geom in zip(axes, [fields_cx, wells_clip_buffer]):
    geom.plot(ax=ax)
```
```python
buffer
```
```python
fields_cx
```
```python
# retain buffer geometry in fields_cx
fields_buffer = fields_cx.copy()
fields_buffer['geometry'] = buffer
fields_buffer
```
```python
# different approach to dissolve many polygons into one and then clip
fields_buffer_dissolve = fields_buffer.dissolve()
fields_buffer_dissolve
```
```python
wells_clip_buffer_dissole = wells.clip(fields_buffer_dissolve)
wells_clip_buffer_dissole.plot()
```
### Raster data processing with rioxarray and geopandas
```python
import pystac
import rioxarray
```
```python
items = pystac.ItemCollection.from_file('search.json')
items
```
```python
raster = rioxarray.open_rasterio(items[1].assets['visual'].href)
print(raster.shape)
```
```python
raster.plot.imshow(figsize=(8,8))
```
```python)
# using overview parameter to load a lower resolution image
raster_overview = rioxarray.open_rasterio(items[1].assets['visual'].href, overview_level=3)
print(raster_overview.shape)
```
```python
raster_overview.plot.imshow(figsize=(8,8))
```
### Crop raster data with rioxarray and geopandas
```python
import geopandas as gpd
```
```python
fields = gpd.read_file('./data/fields_cropped.shp')
```
```python
raster.rio.crs
```
```python
# convert fields crs to raster crs
fields = fields.to_crs(raster.rio.crs)
```
```python
fields.crs
```
```python
#use '?' for help
raster.rio.clip_box?
```
```python
raster_clip_box = raster.rio.clip_box(*fields.total_bounds)
```
```python
raster_clip_box.shape, raster.shape
```
```python
raster_clip_box
```
```python
raster_clip_box.plot.imshow(figsize=(8,8))
```
```python
#check nodata values
raster_clip_box.rio.nodata
```
```python
#Save clipped raster
raster_clip_box.rio.to_raster('raster_clip.tif')
```
```python
# clip raster to fields, when there is no fields it will be nodata
raster_clip_fields = raster.rio.clip(fields['geometry'])
```
```python
#filter for the area of our interest
raster_clip_fields.plot.imshow(figsize=(8,8))
```
### Raster calculations in Python
```python
import pystac
items = pystac.ItemCollection.from_file('search.json')
```
```python
items
```
```python
red_uri = items[1].assets["red"].href
nir_uri = items[1].assets["nir"].href
```
```python
import rioxarray

red = rioxarray.open_rasterio(red_uri, masked=True)
nir = rioxarray.open_rasterio(nir_uri, masked=True)
```
```python
print(red)
```
```python
red.rio.crs
```
```python
#clip to smaller extent
bbox = (629_000, 5_804_000, 639_000, 5_814_000)

red_clip = red.rio.clip_box(*bbox)
nir_clip = nir.rio.clip_box(*bbox)
```
```python
# plot with robust option
red_clip.plot(robust=True)
```
```python
nir_clip.plot(robust=True)
```
```python
red_clip.shape, nir_clip.shape
```
```python
red_clip.rio.crs, nir_clip.rio.crs
```
```python
#Calculate NDVI
ndvi = (nir_clip - red_clip)/(nir_clip + red_clip)
```
```python
ndvi.rio.crs
```
```python
ndvi.plot()
```
```python
ndvi.plot.hist()
```
```python
ndvi.plot(robust=True, cmap='winter')
```
```python
ndvi.min()
```
```python
ndvi.min().values.item()
```
```python
ndvi.isnull()
# marks false or true when there is no value
```
```python
ndvi.isnull().sum()
```
```python
#Lets plot histogram with more bins so that data is spread
ndvi.plot.hist(bins=50)
```
```python
#Using this we discreatise ndvi values
class_bins = (-1, 0., 0.2, 0.7, 1)
ndvi.plot(levels=class_bins)
#Only for visualization
```
```python
#interpolate nodata values
ndvi_nonan = ndvi.interpolate_na(dim='x')
```
```python
# Save file
ndvi_nonan.rio.to_raster('ndvi.tif')
```
```python
#Using numpy.digitise to reclassify values in the raster
import numpy as np
import xarray
```
```python
class_bins = (-1, 0., 0.2, 0.7, 1)
```
```python
ndvi_classified = xarray.apply_ufunc(
    np.digitize,
    ndvi_nonan,
    class_bins
)
```
```python
ndvi_classified
```
```python
from matplotlib.colors import ListedColormap
ndvi_colors = ['blue','gray','green','darkgreen']
ndvi_cmap = ListedColormap(ndvi_colors)
ndvi_classified.plot(cmap=ndvi_cmap)
```
```python
#Save map
ndvi_classified.rio.to_raster('ndvi_classified.tif')
```