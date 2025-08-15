# CAFOS_Edited
Poultry CAFO Detection with Aerial Imagery and Deep Learning
Overview

This project detects poultry Concentrated Animal Feeding Operations (CAFOs) using aerial imagery (NAIP) and deep learning. The workflow covers data preparation, patch extraction, mask generation, model training, inference, and visualization.

1. Project Setup

Clone the repository:

git clone https://github.com/microsoft/poultry-cafos.git
cd poultry-cafos


Create and activate conda environment:

conda create --name cafo --no-default-packages
conda activate cafo


Install dependencies:

conda install python=3.10 pip
conda install pytorch torchvision torchaudio pytorch-cuda=11.7 -c pytorch -c nvidia
conda install -c conda-forge rasterio fiona gdal geopandas shapely rtree
conda install -c conda-forge scikit-image scikit-learn matplotlib pandas
pip install segmentation-models-pytorch pystac-client planetary-computer


Download Delmarva_PL_House_Final.zip from ScienceBase and convert to GeoJSON:

ogr2ogr -of GeoJSON -t_srs epsg:4326 Delmarva_PL_House_Final2_epsg4326.geojson Delmarva_PL_House_Final2.shp


Copy resulting files to data/ folder.

2. Workflow Steps
2.1 Download NAIP Tiles

Queries Planetary Computer STAC API for tiles.

Filters tiles overlapping CAFO polygons.

Saves tile index as naip_tile_index_YYYY.geojson.

Downloads selected tiles.

2.2 Rasterize CAFO Masks

Converts polygon shapefiles to raster masks matching tile resolution.

Saves masks in results/updated_chesapeake_predictions_YYYY/masks/.

2.3 Extract Image Patches

Divides tiles and masks into smaller patches (512x512).

Skips patches with only background.

Saves patches in patches/images/ and patches/masks/.

2.4 Visualize Sample Patches

Displays random image-mask pairs.

Saves PNGs to patch_visualizations/.

2.5 Model Training

Uses U-Net with ResNet18 encoder.

Trains on image patches with data augmentation.

Logs metrics: accuracy, precision, recall, F1, AUC-ROC.

Saves model to weights/cafo_multi_patch.pt and metrics CSV.

2.6 Inference

Loads trained model.

Runs prediction on new tiles.

Saves binary prediction masks in results/updated_predictions_YYYY/.

2.7 Overlay Visualization

Overlays prediction masks on RGB tiles.

Saves PNGs with red mask overlay.
