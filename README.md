# Canada Wildfire 2023 - An Interactive Fire Impact Map
As a part of the final project in the SDS210 module at the University of Zurich this project focuses on wildfires in Canada during 2023. Therefore, it maps all recorded uncontrolled wildfires across the country and shows them on a interactive Folium map. Additionally it measures how close these fires were to high population cities of Canada, making it easiert to understand which areas were at risk or most affected. To make a connection we add present live satellite fire data from NASA FIRMS. It trys to respond the following research question: *Which Canadian Cities were exposed to the most risk during wildfire season in 2023?* 

## Data Sources

**Wildfires (NFDB Point Data):** A collection of large fires greater than 200ha distributed across entire Canada. Provided by Canadia dire management agiencies 

* **Link:**
https://cwfis.cfs.nrcan.gc.ca/geoserver/wfs?service=WFS&version=1.1.0&request=GetFeature&outputFormat=csv&typeNames=public:NFDB_point&sortBy=REP_DATE+D

**NASA FIRMS Fire Data:** VIIRS NOAA-20 Near Real-Time active fire detections

* **Link:** https://firms.modaps.eosdis.nasa.gov/usfs/api/area/

**World Cities:** In the basic database there are around 48 thousand prominent cities all over the world.  

* **Link:** https://simplemaps.com/data/world-cities

If you want to download them to your own folder make sure to load theme into the correct folder. Each raw data file is stored in the *../data/raw* folder.  

## Project Structure

```bash
sds210project/
|-- README.md
|-- environment.yml                  # Conda environment with all dependencies
|--  data/
|   |--  raw/                         # Original, unmodified source files (read only!)
|   |   |--  NFDB_point.csv
|   |   |__ worldcities.csv
|   |__ processed/                   # processed data
|       |--  fire_2023_clean.csv
|       |--  fire_2023_points.geojson
|       |--  selected_cities_canada.csv
|       |--  selected_cities_canada.geojson
|       |--  cities_country.csv
|       |__  fire_live.csv
|-- notebooks/
|   |--  fire2023_data_cleaning.ipynb
|   |--  cities_canada.ipynb
|   |__ visualization.ipynb
|__ outputs/
    |__ interactive_firemap.html     
```

## Setup Instructions:

**Step 1: Clone Repository**

```bash 
git clone https://github.com/ArohaRussi/sds210project.git
cd sds210project 
```

**Step 2: Create and activate the Conda environment**

In the `environment.yml` all required packages are listed. 

```bash
conda env create -f environment.yml
conda activate sds210project
```

**The `environment.yml` file:**
> The file includes all required packages for this project, such as pandas, geopandas, folium, leafmap & requests
> Since i worked with jupyterlab it is already installed in the `environment.yml`
> When creating the environment all required packages will be installed automatically.

***Step 3 is only required when raw data files are missing in Jupyter lab!***

**Step 3: Storing the raw data files**

After downloading the raw data files (see Data sources) make sure that all files are stored in the data/raw/ folder 

Make sure that your repository looks like in the project structure if not already. 

## Execution Order

When running the project for the first time it is important that you follow a strict order since they read some outputs of previous ran notbooks. Since we take information from an API it is essential to have a stable internet connection otherwise there will be an error for that layer. 

**Step 1:** Run first *notebooks/fire2023_data_cleaning.ipynb*
> It reads the raw data of the wildfires (NFDB_point.csv). Following the Code columns and rows get filtered and renamed. Additionally the wildfires of 2023 get classified by their size (in hectar) into categories.

**Step 2:** Secondly run *notebooks/cities_canada.ipynb*
> It reads the raw data of world cities and selects necessarly just the representative set of the 40 most populated Cities of Canada. Additionally due to visibility reasons it was used a 150km minimum spacing algorithm so that high population agglomerations will not be part of the 40 most populated Cities of Canada.

**Step 3:** Third run *notebooks/visualization.ipynb*
> Based on the data cleaning and the Canada City file it performs a spatial join to color the cities based on how severe the impacts of the surrounding wildfire on the cities. Additionally, a heatmap is being computed to visualize the distribution of the fire points. As a third layer live fire data from NASA FIRMS will be accessed with a API and a Map Key to then visualize it in the interactive map.
> **IMPORTANT:** Here an internet connection is required

>  Everything is being stored in the end as interactive_firemap.html in the outputs folder.

## Describtion folder data/processed/

**cities_country.csv :** All selected cities of Canada from the worldcities.csv file.

**selected_cities_canada.csv :** The final representative Canadian cities after applying the 150km minimum spacing algorithm stored as a csv 

**selected_cities_canada.geoson :** the same as above but converted to GeoJSON to use directly use in Folium. 

**fire_2023_clean.csv :** All uncontrolled Canadian wildfires of 2023 after data cleaning, classifying and weighting. 

**fire_2023_points.geoson :** The same as above but again converted to GeoJSON to use later on. 

**fire_live :** Live satellite detections from the NASA FIRMS API, which automatically gets updated when visualization.ipynb notebook is run; As a result it contains active fire points from the las five days from actualization day. 

## Output 

Once one have run all three notebooks the interactive_firemap.html can be opened in any browser. Then the map contains three toggleable layers: 

> * **Wildfires in 2023** - A weighted heatmap of all all NFDB fire records of Canada in the year 2023
> * **Impacted Cities in 2023** - CircleMarkers coloured by fire impact score
> * **Live wildfire** - VIIRS NOAA-20 satellite detections from the actualization date.
