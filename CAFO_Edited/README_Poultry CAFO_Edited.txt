Poultry CAFO Detection Using Aerial Imagery and Deep Learning

This repository contains scripts and workflows for detecting poultry Concentrated Animal Feeding Operations (CAFOs) using aerial imagery and deep learning.

1. Setup and Dependencies

Before starting, ensure you have the necessary environment:

# Create environment
conda create --name cafo --no-default-packages
conda activate cafo

# Install required packages
conda install python=3.10 pip
conda install pytorch torchvision torchaudio pytorch-cuda=11.7 -c pytorch -c nvidia
conda install -c conda-forge rasterio fiona gdal geopandas shapely rtree
conda install -c conda-forge scikit-image scikit-learn matplotlib pandas
pip install segmentation-models-pytorch pystac-client planetary-computer


Download the Delmarva_PL_House_Final.zip file from:
ScienceBase Link
Extract and convert to GeoJSON (EPSG:4326) using:

ogr2ogr -of GeoJSON -t_srs epsg:4326 Delmarva_PL_House_Final2_epsg4326.geojson Delmarva_PL_House_Final2.shp


Place the GeoJSON file in the data/ directory.

2. Scripts and Their Functions
2.1 Download and Prepare Tiles

File: download_naip_tiles.ipynb / .py

Queries the Planetary Computer STAC API for NAIP imagery.

Filters tiles that overlap with CAFO polygons.

Downloads selected tiles to your working directory.

Outputs a naip_tile_index.geojson.

2.2 Rasterize CAFO Masks

File: rasterize_masks.ipynb / .py

Converts poultry barn polygons into raster mask images that match NAIP tiles.

Saves masks in results/masks/ directory.

2.3 Extract Patches from Tiles

File: extract_patches.ipynb / .py

Splits tiles and masks into smaller patches (e.g., 512×512).

Skips patches with no CAFOs (all-background).

Saves patches into patches/images and patches/masks.

2.4 Visualize Patch Samples

File: visualize_patches.ipynb / .py

Randomly selects patches and displays the image + mask side by side.

Saves sample visualizations to patch_visualizations/.

2.5 Train Deep Learning Model

File: train_model.ipynb / .py

Defines a PyTorch Dataset with optional augmentation.

Trains a U-Net segmentation model on image patches.

Saves trained model to weights/cafo_multi_patch.pt.

Logs training metrics to results/training_metrics.csv.

2.6 Inference on New Tiles

File: predict_tiles.ipynb / .py

Loads the trained model.

Runs inference on full NAIP tiles.

Produces binary masks indicating predicted CAFO locations.

Saves predictions to results/updated_predictions_YYYY/.

2.7 Create Prediction Overlays

File: create_overlay.ipynb / .py

Reads RGB tiles and predicted masks.

Overlays predictions in red on the original imagery.

Saves PNG overlays for visual inspection.

3. Recommended Workflow

Set up the environment and install dependencies.

Download the Delmarva poultry dataset and convert to GeoJSON.

Run download_naip_tiles to get the relevant NAIP tiles.

Run rasterize_masks to generate raster masks from polygons.

Run extract_patches to split images and masks into patches.

Optional: Run visualize_patches to inspect sample patches.

Train the model with train_model.

Run predict_tiles on new tiles to get CAFO predictions.

Run create_overlay to generate visual overlays of predictions.

4. Notes

Make sure all paths are correctly updated in each script according to your system.

Use ipynb files in Colab for interactive exploration; use .py files for scripted runs on your server.

Adjust patch size, stride, and thresholds in patch extraction and prediction steps as needed.