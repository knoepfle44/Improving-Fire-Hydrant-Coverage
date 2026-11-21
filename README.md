[README.md](https://github.com/user-attachments/files/35213553/README.md)
Water Hydrants and Candidate Swimming Pools – GIS Workflow

A reproducible GIS workflow that detects candidate swimming pools from orthophotos using a pre-trained YOLO model, combines them with OpenStreetMap fire hydrant data, and estimates the potential improvement in firefighting water-access coverage for selected villages in Carinthia, Austria (Velden/Lind ob Velden, Hermagor, Treffen).

Project Objective

Fire departments rely on hydrants as the primary water source for firefighting. This project explores whether private swimming pools, detected automatically from aerial imagery, could serve as supplementary water sources — and quantifies the potential coverage gain (150 m service radius) this would provide per village.

Workflow Overview


Configuration – CRS, study-area bounding boxes, and orthophoto tile URLs are defined centrally.
Data Acquisition – fire hydrants are pulled from OpenStreetMap; orthophoto tiles are downloaded from the Carinthian state GIS server.
Orthophoto Preparation – tiles are merged into one raster per village and clipped to the study area.
Swimming Pool Detection – a pre-trained YOLO model detects candidate pools via a sliding-window chipping approach; detections are exported as GeoPackages.
Coverage Analysis – 150 m buffers around hydrants and detected pools are compared to compute the coverage gain (%, ha) per village.
Visualization – interactive Folium web maps are generated per village.


Requirements


Python ≥ 3.10
Internet access (OpenStreetMap + Land Kärnten GIS server)
~2–3 GB free disk space for orthophoto tiles per village


Install dependencies

bashpip install rasterio pyproj requests tqdm geopandas duckdb osmnx
pip install ultralytics huggingface_hub

These are commented out as #!pip install ... at the top of the notebook — uncomment or run them manually before executing.

Other libraries used (installed as dependencies of the above)

numpy, pandas, shapely, folium, Pillow (PIL)

Project Structure

.
├── water_hydrants_pool_final.ipynb
├── data/
│   ├── raw_tiles/              # downloaded orthophoto tiles (.tif)
│   ├── fire_hydrants_*.gpkg    # OSM hydrants per village
│   ├── *_merged.tif            # merged orthophoto mosaics
│   └── *_clipped.tif           # clipped orthophotos (analysis input)
└── outputs/
    ├── *_pools.gpkg            # detected candidate pools per village
    ├── coverage_analysis.csv   # before/after coverage results
    └── *_map.html              # interactive web maps per village

data/ and outputs/ are created automatically by the notebook on first run if they don't exist.

How to Run

Run all cells top to bottom in a fresh kernel. The notebook is not safe to re-run cells out of order,  file names are referenced directly between steps.


Install dependencies (see above).
Run the import cell and the directory-setup cell.
Check the CONFIG section — adjust bounding boxes, tile URLs, or buffer radius if needed (see Adding a New Village below).
Run the hydrant download cells — downloads OSM data per village to data/fire_hydrants_<village>.gpkg.
Run the orthophoto download cells — fetches tiles listed in CONFIG["tiles_per_bbox"] to data/raw_tiles/. This step can take a while depending on connection speed and tile count.
Run the merge and clip cells — produces one clipped raster per village.
Run the YOLO detection cells — downloads the pre-trained model (mozilla-ai/swimming-pool-detector) from Hugging Face Hub on first run, then chips each clipped orthophoto and runs inference (confidence threshold: 0.3).
Run the coverage analysis cells — computes buffers, intersections, and the gain table; writes outputs/coverage_analysis.csv.
Run the visualization cells — generates one HTML map per village in outputs/.

Adding a New Village

## To extend the workflow to a new village, only the CONFIG section needs to change:


Add a new bounding box entry under CONFIG["bboxes"] in the target CRS (EPSG:31255), as [minx, miny, maxx, maxy].
Add the corresponding orthophoto tile URLs under CONFIG["tiles_per_bbox"], using the same key name as the bbox. Tile URLs and IDs must be looked up manually on the Land Kärnten GIS portal.
Add a new place_name / output_file pair for the hydrant download step, using an OSM-resolvable place name (e.g. "Villach, Austria").
Add the new village to the villages dictionary used in the merge/clip/detection/coverage loop, mirroring the existing entries.


No changes to the processing functions themselves should be required.

Output Data

FileDescriptionfire_hydrants_<village>.gpkgFire hydrant point locations from OSM<village>_clipped.tifClipped RGBI orthophoto used for detection<village>_pools.gpkgDetected candidate swimming pool polygons/centroidscoverage_analysis.csvPer-village coverage before/after, gain %, gain (ha), priority<village>_map.htmlInteractive map with hydrants, pools, buffers, coverage layers

Data Sources


Fire hydrants: OpenStreetMap (via osmnx)
Orthophotos: Land Kärnten GIS / Open Government Data
Pool detection model: mozilla-ai/swimming-pool-detector (Hugging Face Hub)
