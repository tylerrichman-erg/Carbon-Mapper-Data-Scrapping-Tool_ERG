# Carbon Mapper Data Scrapping Tool
<img src="misc/README_img.PNG" alt="img" style="width:100vw;height:70vh;"/>

## Overview 
This repository contains two R Markdowns that retrieves plume data from <a href="https://data.carbonmapper.org/" target="_blank">Carbon Mapper</a> and <a href="https://earth.jpl.nasa.gov/emit-mmgis-lb/?mission=EMIT&site=ert&mapLon=-103.84810013696554&mapLat=32.54933309954321&mapZoom=9&globeLon=0&globeLat=3.508354649267438e-15&globeZoom=2&globeCamera=9.000268457972838,-10000000,10.000298286636488,0,1,0&panePercents=0,100,0&on=3d9e9b7f-9c7c-4c92-94d8-dec04c300168$1.00,8fed617c-0c4e-4841-87d1-f4ffd1a56d4e$1.00,37414e25-e3d3-4b78-ade5-75edfe4e5da0$1.00,ba365157-1ba0-4c7e-9a3a-4bce7ad3ed13$0.70" target="_blank">NASA EMIT</a> based on user defined geographic locations of interest. These tools supports ERG’s effort of obtaining information on Methane “Super Emitters” which are sources that emit methane gas in very large quantities, often associated with the energy production industry, or landfills. Both resources have an application programming interface (API) through which the data is obtained from. Plume data that is geotagged to a location within a user defined distance of each geographic location of interest is exported to an excel file.

## R Markdown Structure
The tool contains three seperate chunks to import libraries, initialize parameters, and retrieve plume data. Users will only need to edit the second chunk which initialzes the following parameters:
<ul>
 <li><b>excel_file_path:</b> Path of the input excel file.</li>
 <li><b>output_folder:</b> Folder that the output file will be saved to.</li>
 <li><b>x_field:</b> Field name of X-Coordinates.</li>
 <li><b>y_field:</b> Field name of Y-Coordinates.</li>
 <li><b>bufferDist_mi:</b> Distance of the buffer in miles.</li>
 <li><b>limit (Carbon Mapper only):</b> Number of records obtained per API call (should not have to alter).</li>
</ul>

### General Workflow
The excel file from the input file path is converted into a data frame. Records that do not have any coordinates are removed from the dataset. The data frame is then converted into a spatial data frame with the <a href="https://epsg.io/4269">NAD83</a> coordinate reference system (CRS). This spatial data frame is then projected to the <a href="https://epsg.io/102005">NAD 1983 Equidistant Conic contiguous USA</a> CRS. This is a distance perserving projection that allows for accurate distance calculations. A buffer with a user specified radius is generated for each record in the spatial data frame.

Plume data from the API endpoint is converted into a spatial data frame with the NAD 1983 Equidistant Conic contiguous USA CRS. An inner spatial join is performed between the buffer spatial data frame and plume spatial data frame. For Carbon Mapper, the API endpoint contains informations on plume clusters and further processing is required to obtain data on specific plumes. The resulting spatial data frame is converted back to a regular data frame. The distance between the location of interest and associated plume is calculated within a new column. The data frame is the exported to an excel file within a folder specified by the user in the input parameters.
