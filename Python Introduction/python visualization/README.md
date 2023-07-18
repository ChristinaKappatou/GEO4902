# Hands-on Weather forecast and Climate data
Example notebook for GEO4902

This tutorial is significantly based on [Getting your hands-on Climate data](https://nordicesmhub.github.io/climate-data-tutorial/).


## What is numerical weather prediction?
According to the [Glossary of Meteorology](https://glossary.ametsoc.org/wiki/Numerical_forecasting), _numerical forecasting_ is defined as
~~~
(Also called mathematical forecasting, dynamical forecasting, physical forecasting, numerical weather prediction.) The integration of the governing equations of hydrodynamics by numerical methods subject to specified initial conditions.

Numerical approximations are fundamental to almost all dynamical weather prediction schemes since the complexity and nonlinearity of the hydrodynamic equations do not allow exact solutions of the continuous equations.
See numerical integration, numerical simulation.
~~~

## What is reanalysis?
According to the [Glossary of Meteorology](https://glossary.ametsoc.org/wiki/Climate_prediction), _reanalysis_ is defined as 

~~~  
Same as analysis, except for two important practical differences.

First, it is not done in real time, and second, the background field is made by an NWP model that does not change over the entire period of the reanalysis. A serious problem with climate change studies made from standard analyses results from frequent changes in the model used to generate the background field. These changes (including changes in resolution and orography) lead to discontinuities in a time series of real-time analyses. A reanalysis yields complete, global gridded data that are as temporally homogeneous as possible. Reanalysis data include many derived fields (heating, soil moisture over land, etc.) for which direct observations are nearly absent.
~~~ 

## What is climate prediction?
According to the [Glossary of Meteorology](https://glossary.ametsoc.org/wiki/Climate_prediction), _climate prediction_ is defined as 

~~~  
The prediction of various aspects of the climate of a region during some future period of time.

Climate predictions are generally in the form of probabilities of anomalies of climate variables (e.g., temperature, precipitation), with lead times up to several seasons (
see climate anomaly). The term "climate projection" rather than "climate prediction" is now commonly used for longer- range predictions that have a higher degree of uncertainty and a lesser degree of specificity. For example, this term is often used for "predictions" of climate change that depend on uncertain consequences of anthropogenic influences such as land use and the burning of fossil fuels.
~~~

 

## 1. What is the weather like in Oslo?
The script guides you to access and plot a single point-forecast of the 2-meter temperature, humidity, and wind forecast.

### 1.1 Open the latest weather forecast from the Norwegian forecast system (MEPS).
- We recomend to use `xarray`, to keep track of your coordinates and the metadata in your data! (E.g. numpy doesn't do this)
- This is why the NetCDF format is so popular -- it keeps track of these things and is extremely easy to load

### 1.2 Change the 2-m temperature to $^{o}$ C. And assign the new attributes to your dataset. This is nice to do when you are plotting. `xarray` is looking for `units`, `standard_name`, and `long_name` when plotting.


### 1.3 Let us now select a grid point close to the meteorological station at Blindern and check if you selected the closest grid point.

**Oslo - Blindern**
- Station number (id): SN18700
- Height above mean sea level: 94 m
- Latitude: 59.9423º N
- Longitude: 10.72º E
- Operating period: 01.01.1931 - now
- WMO number: 1492
- Information taken from <https://seklima.met.no/stations/> a service by Met.no. 

> **_NOTE:_**  AROME Arctic is on an irregular grid, so the `xarray.sel` functionality does not work. Create a `function` to select the closest grid point.

### 1.4 Plot the point forecast of the 2-meter temperature, humidity, and wind forecast close to the Blindern station.


## 2. What is the climate in Oslo between 1985 - 2014?
To get some information about the (past 30 years) climate in Oslo, we will first look at monthly averages. For this we use Reanalysis data from [ERA5](./visualization_python/data/2t_Amon_ERA5_1985_2014_Norway.nc).

### 2.1 Open the dataset and get familiar with it. What are the differences between MEPS and ERA5? 

### 2.2 Change the 2-m temperature to $^{o}$ C. And assign the new attributes to your dataset. This is nice to do when you are plotting. `xarray` is looking for `units`, `standard_name`, and `long_name` when plotting.

### 2.3 Create a 30-year mean for each month by using [`xarray.DataArray.groupby`](http://xarray.pydata.org/en/stable/generated/xarray.DataArray.groupby.html) and [`xarray.DataArray.mean`](http://xarray.pydata.org/en/stable/generated/xarray.DataArray.mean.html).

### 2.4 Plot 2-m temperature, averaged over the 30 years per month from ERA5 on spatial map. Select the same month as your weather forecast, MEPS.

### 2.5 Create some statistics for the 30 years and all month, such as [mean](http://xarray.pydata.org/en/stable/generated/xarray.DataArray.mean.html), [standard deviation](http://xarray.pydata.org/en/stable/generated/xarray.DataArray.std.html), and 0.05, 0.95 [percentiles](http://xarray.pydata.org/en/stable/generated/xarray.DataArray.quantile.html). Plot the statistics on a spatial map.

### 2.6 Select the closest grid point to Oslo Blindern and plot the 2-m temperature. From ERA5 plot the 30-yr yearly-mean temperature. Make another plot which shows temperature forecast of MEPS latest and the 30-yr climate average of the month of MEPS latest.

To select the closest grid point in a regular grid you can use [Nearest neighbor lookups](http://xarray.pydata.org/en/stable/user-guide/indexing.html#nearest-neighbor-lookups). The label based selection methods `sel()` support method and tolerance keyword argument.


# 3. CMIP& monthly data on single levels
Retrieve near surface temperature using Pangeo cataloge. 

### 3.1 Open CMIP6 online catalog with `intake.open_esm_datastore()`

### 3.2 Search for corresponding data
- `source_id`: e.g. list_models, 
- `experiment_id`: e.g. historical, 
- `variable_id`: e.g. 'tas' (near surface temperature), 
- `member_id`: e.g. r1i1p1f1

### 3.3 Create dictionary from the list of datasets we found
This step may take several minutes so be patient!
Use `.to_dataset_dict`

### 3.4 Select one dataset and have a look at it. How does it differ to ERA5?

### 3.5 Shift the longitude from 0-->360 to -180-->180 and sort by longitude and time

### 3.6 Plot 30-yr seasonal mean for 2-m temperature in degC from CMIP 6 - NorESM. 
### 3.7 Select area of Norway from the CMIP6 dataset.
You can use `.latitude.min()`, `.latitude.max()`, `.longitude.min()`, `.longitude.max()` from the ERA5 dataset.


### 3.8 Regrid the ERA5 data to the same resolutio as the NorESM grid. 
- ERA5 has a 0.25deg resolution
- NorESM-MM has a resolution of ~100km

We use the [universal regridder for geospatial](https://xesmf.readthedocs.io/en/latest/) data `xesmf`. We regrid between rectilinear grids.

The input grid will be the ERA5 grid, while NorESM-MM will be used as output grid.

### 3.9 Plot the difference between the 2-m temperatura of ERA5 and CMIP6 as seasonal averages.
