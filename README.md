Water Access Analysis Using Fire Hydrants and
Candidate Swimming Pools
Project Overview
This project presents a reproducible Geographic Information System (GIS) workflow that identifies
candidate swimming pools from aerial orthophotos and evaluates their potential as supplementary
firefighting water sources.
A pre-trained YOLO object detection model detects swimming pools from high-resolution orthophotos.
The detected pools are combined with existing OpenStreetMap (OSM) fire hydrants to estimate
improvements in village-level water-access coverage.
The complete workflow is controlled through a central CONFIG section, making it easy to reproduce the
analysis for different study areas.
Objectives
The project aims to:
Download fire hydrants from OpenStreetMap
Download and prepare orthophotos
Merge and clip raster tiles
Detect candidate swimming pools using AI
Combine hydrants and pools in a GIS analysis
Estimate the increase in firefighting water coverage
Produce maps and interactive web visualizations
Study Areas
The workflow is demonstrated for three villages in Carinthia, Austria:
Lind ob Velden
Hermagor
Treffen
Each study area is defined by a manually created bounding box stored in the configuration.
•
•
•
•
•
•
•
•
•
•
1
Workflow
1. Configuration
The CONFIG section contains all user-defined parameters:
Coordinate Reference System (CRS)
Study area bounding boxes
Orthophoto URLs
Buffer radius
Output directories
Only this section must be modified to analyse a different study area.
2. Fire Hydrant Data
Download hydrants from OpenStreetMap
Reproject to EPSG:31255
Clip hydrants to the study area
Save as GeoPackage (.gpkg)
3. Orthophoto Preparation
Download orthophoto tiles
Merge tiles into a single raster
Clip merged raster to each study area
Save clipped orthophotos
4. Swimming Pool Detection
A pre-trained YOLO model from Mozilla AI is used to detect candidate swimming pools.
The workflow:
Split orthophotos into image chips
Run AI detection
Convert detections to polygons
Save detections as GeoPackage files
•
•
•
•
•
•
•
•
•
•
•
•
•
•
•
•
•
2
5. Coverage Analysis
For every village:
Create a 150 m buffer around each fire hydrant
Create a 150 m buffer around each detected pool
Calculate
Existing hydrant coverage
Total coverage (hydrants + pools)
Additional coverage provided by candidate pools
The analysis is clipped to the predefined study area.
6. Visualization
The workflow produces:
Interactive Web Maps
Each village receives an interactive Folium map containing:
Study area
Fire hydrants
Candidate pools
Hydrant buffers
Pool buffers
Total coverage
Coverage gain
Presentation Maps
Publication-quality PNG maps are generated for each village:
Current coverage (hydrants only)
Potential coverage (hydrants + candidate pools)
These maps are intended for reports and presentations.
Project Structure
project/
│
├── data/
│ ├── fire_hydrants_*.gpkg
│ ├── merged_orthophotos/
│ └── clipped_orthophotos/
│
•
•
•
•
•
•
•
•
•
•
•
•
•
•
•
3
├── raw_tiles/
│
├── outputs/
│ ├── pools_*.gpkg
│ ├── coverage_comparison.csv
│ ├── map_*.html
│ ├── *_before.png
│ └── *_after.png
│
├── water_hydrants_pool.ipynb
├── README.md
Required Python Libraries
GeoPandas
Rasterio
Shapely
OSMnx
Ultralytics
Hugging Face Hub
Folium
Matplotlib
NumPy
Pandas
Pillow
Rasterio
Matplotlib ScaleBar
Outputs
The workflow generates:
Fire hydrant datasets (.gpkg)
Merged orthophotos (.tif)
Clipped orthophotos (.tif)
Candidate swimming pool datasets (.gpkg)
Coverage comparison table (.csv)
Interactive web maps (.html)
Before/after coverage maps (.png)
•
•
•
•
•
•
•
•
•
•
•
•
•
•
•
•
•
•
•
•
4
