![](/codimd/uploads/8f24674fa3405411f0ed7e800.png)

# Collaborative Document

2024-02-14 Introduction to Geospatial Raster and Vector Data with Python.

Welcome to The Workshop Collaborative Document.

This Document is synchronized as you type, so that everyone viewing this page sees the same text. This allows you to collaborate seamlessly on documents.

----------------------------------------------------------------------------

This is the Document for today: [link](https://crib.utwente.nl/codimd/2024-02-14-Geospatial-Python-Workshop-Day2)

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
| 14:30 | *Coffee break*                        |              | 14:45 | Raster data preprocessing with rioxarray and geopandas|
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

## 🧠 Collaborative Notes

### Zonal Statistics
```python
import geopandas as gpd
import rioxarray
```
```python
fields = gpd.read_file('fields_cropped.zip')
# use './data/fields_cropped.zip'
```
```python
ndvi = rioxarray.open_rasterio('ndvi.tif').squeeze()
# use './data/ndvi.tif'
```
```python
ndvi.shape
```
```python
fields.crs, ndvi.rio.crs
```
```python
fields_utm = fields.to_crs(ndvi.rio.crs)
```
```python
# create a list of geom and corresponding code
geom = fields_utm[['geometry','gewascode']].values.tolist()
# geom
```
```python
from rasterio import features

# to rasterise fields polygons 
fields_rasterized = features.rasterize?
```
```python
fields_rasterized = features.rasterize(
    geom, 
    out_shape=ndvi.shape, 
    transform=ndvi.rio.transform()
)
```
```python
import numpy as np

print(fields_rasterized.shape)
print(np.unique(fields_rasterized))
```
```python
from matplotlib import pyplot as plt

plt.imshow(fields_rasterized)
plt.colorbar()
```
```python
import xarray as xr

fields_rasterized_xarr = ndvi.copy()
fields_rasterized_xarr.data = fields_rasterized

fields_rasterized_xarr.plot(robust=True)
```
```python
from xrspatial import zonal_stats

zonal_stats?
```
```python
zonal_stats(fields_rasterized_xarr, ndvi)
```

### Zonal stats on ndvi_classified

Let’s calculate NDVI zonal statistics for the different zones as classified by ndvi_classified in the previous episode.

Load both raster datasets: NDVI.tif and NDVI_classified.tif. Then, calculate zonal statistics for each class_bins. Inspect the output of the zonal_stats function.

```python
ndvi = rioxarray.open_rasterio("ndvi.tif").squeeze()
ndvi_classified = rioxarray.open_rasterio("ndvi_classified.tif").squeeze()
```
```python
zonal_stats(ndvi_classified, ndvi)
```
### Parallel raster computations using Dask

```python
import pystac

items = pystac.ItemCollection.from_file("search.json")
# use './data/search.json'
items
```
```python
assets = item[-1].assets

visual_href = assets["visual"].href    #true color composite

# cog file - Cloud Optimised GeoTiff
visual_href
```
```python
import rioxarray
visual = rioxarray.open_rasterio(visual_href, overview_level=2)
print(visual.rio.resolution())    #resolution in m at overview lvl 2
```
```python
visual = visual.load()    #untill now the file was only a href to cloud
```
```python
visual.plot.imshow(figsize=(8,8))
```
```python
%%time

median = visual.rolling(x=7, y=7, center=True).median()
```
```python
median.plot.imshow(robust=True, figsize=(8,8))
```
```python
# Save median raster
median.rio.to_raster('visual_median_filter.tif')
```
### Chunked arrays
```python
blue_band_href = assets["blue"].href
blue_band = rioxarray.open_rasterio(blue_band_href, chunks=(1, 4000, 4000))
```
```python
blue_band
```
### Exercise -  CHUNK SIZES MATTER

We have already seen how COGs are regular GeoTIFF files with a special internal structure. other feature of COGs is that data is organized in “blocks” that can be accessed remotely via independent HTTP requests, abling partial file readings. This is useful if you want to access only a portion of your raster file, but it also allows for efficient parallel reading. You can check the blocksize employed in a COG file with the following code ippet:
```python
import rasterio
with rasterio.open(visual_href) as r:
    if r.is_tiled:
        print(f"Chunk size: {r.block_shapes}")
```
In order to optimally access COGs it is best to align the blocksize of the file with the chunks ployed when loading the file. Open the blue-band asset (the “blue” asset) of a Sentinel-2 scene as a chunked DataArray object using a suitable chunk size. Which elements do you think should be considered when choosing the chunk size?

```python
import rasterio
with rasterio.open(blue_band_href) as r:
    if r.is_tiled:
        print(f"Chunk size: {r.block_shapes}")
```
```python
band = rioxarray.open_rasterio(blue_band_href, chunks=(1, 6144, 6144))    #testing arbitary chunk size

band
```
```python
band = rioxarray.open_rasterio(blue_band_href, chunks='auto'))    #auto

band
#not so optimal, large size
```
```python
visual_dask = rioxarray.open_rasterio(visual_href, overview_level=2, lock=False, chunks=(3, 500, 500))
#lock=False loads data parallelly

visual_dask
```
```python
visual_dask = visual_dask.persist(scheduler='threads', num_workers=4)
#using 4 threads in parallel
#everything upto this point is in memory now

visual_dask
```
```python
%%time

median_dask = visual_dask.rolling(x=7, y=7, center=True).median()

#here, the median() is still not calculated
```
```python
median_dask = median_dask.persist(scheduler='threads', num_workers=4)
#everything upto this point is in memory now
#therefore, now, median() is calculated
```
```python
import dask
dask.visualize(median_dask)

#visualise task graph
```
```python
# write median_dask to disk

from threading import lock

median_dask.rio.to_raster("visual_median_filter_dask.tif",
                         tiled=True,
                         lock=Lock())
```
```python
median_dask.plot.imshow(robust=True, figsize=(8,8))
```
