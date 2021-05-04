# Automated Extraction of Labels from Large-Scale Historical Maps

[![DOI](https://github.com/IngaSchl/Label-Extraction/blob/main/zenodo.4722899.svg)](https://doi.org/10.5281/zenodo.4721174)

**Author:** Inga Schlegel

**Date**: 04/26/2021

Research data and software used and implemented for the AGILE 2021 full paper *Automated Extraction of Labels from Large-Scale Historical Maps*. 

This repository's structure follows the one of section 3 within the paper. Each subfolder *data* contains exemplary input and output data for each process step 02-06.

All \*.ipynb files can be executed directly within Jupyter Notebook. To install and set up Jupyter Notebook via `conda` or `pip` see [Jupyter's installation guide](https://jupyter.org/install).


## Library requirements

The Python libraries needed for the \*.ipynb files are listed below. They can be installed with `pip install -r requirements.txt`.

```
contextlib2==0.6.0.post1
fuzzywuzzy==0.18.0
gdal==3.0.4
geopandas==0.7.0
matplotlib==3.1.3
more-itertools==8.4.0
msys2-conda-epoch==20160418
numpy==1.18.1
pandas==1.0.3
python-levenshtein==0.12.0
shapely==1.6.4
```


## 01 Data

Data source: Hamburg, Germany, 1853 (Raster Image). [GeoTIFF]. Harvard Map Collection, Harvard College Library. Retrieved from https://maps.princeton.edu/catalog/harvard-g6299-h3-1853-l5


## 02 Text detection

### Run Strabo for text detection
see also https://github.com/spatial-computing/strabo-text-recognition-deep-learning

As Strabo (Version 0.67) only works with input images in JPEG or PNG format, the linked dataset in *01 Data* was converted into lossless PNG with smallest possible compression. This step may be performed with the help of common image editing software (e.g. GIMP).

In Linux command line:
1) `cd path/to/strabo-text-recognition-deep-learning`
2) `python3 run_command_line.py --checkpoint-path /path/to/east_icdar2015_resnet_v1_50_rbox --image data/input/G6299_H3_1853_L5_subset-5.png --config configuration.ini`

The output files are stored in `path/to/strabo-text-recognition-deep-learning/static/results/test2/`.


## 03 Additional adjustments

### 03-01 Unify overlapping bounding boxes
Generalizes Strabo's output in order to facilitate subsequent processing steps.

### 03-02 Rotate text image areas
Each text image area was rotated manually through its averaged rotation angle which was derived from Strabo's outputted geojson file.

After rotating the text image areas, each one was cropped to its actual extent.
At the same time, the black background (if present) was removed and replaced by a transparent one.
See folder [data/output](https://github.com/IngaSchl/Label-Extraction/tree/main/03%20Additional%20adjustments/02%20rotate%20text%20image%20areas/data/output) for the rotated and cropped data which was stored in tiff format without compression.


## 04 Text recognition

### Run Tesseract OCR for text recognition

see also https://github.com/tesseract-ocr/tesseract/blob/master/doc/tesseract.1.asc and https://tesseract-ocr.github.io

In Windows command line:
1) `cd path/to/Tesseract-OCR`
2) `tesseract data/input/ID_24.tif data/output/ID_24 -l deu --psm 8`

Here, the German language package was chosen (`-l deu`). The input images were treated as single words (`--psm 8`).


## 05 String similarity

The (exemplary) input and (all) output data from the implemented Levenshtein Distance algorithm ([String_Similarity_by_Levenshtein_Distance.ipynb](https://gitlab.com/g2lab/label-extraction-from-historical-maps/-/blob/master/05%20String%20similarity/String_Similarity_by_Levenshtein_Distance.ipynb)) are stored in [05 String similarity/data/OCR_results.xlsx](https://gitlab.com/g2lab/label-extraction-from-historical-maps/-/blob/master/05%20String%20similarity/data/OCR_results.xlsx).


## 06 Approximate georeferencing

Finds the reference points of a current street dataset by different types of centroids per street name.

As a following step, an approximate georeferencing can be performed with QGIS Georeferencer. With the help of the best overall matches (throughout the whole map, see green marking in [05 String similarity/data/OCR_results.xlsx](https://github.com/IngaSchl/Label-Extraction/blob/main/05%20String%20similarity/data/OCR_results.xlsx)), the historical maps' centroids of the bounding boxes - which were already extracted in the text detection step with Strabo - can be referenced to the generated "centroid" of the respective current street or place. As a result, an initial, approximate georeferencing of the historical map may be performed via affine transformation.

**Caution**: The script [Centroids_current_streets.ipynb](https://github.com/IngaSchl/Label-Extraction/blob/main/06%20Approximate%20georeferencing/Centroids_current_streets.ipynb) is still in beta phase. Therefore, computing time may be extensive (on our machine with 2 cores this took .............).


## Figures in paper

The output datasets shown in the paper's figures are linked as follows:

* Figure 1: [02 Text detection/data/input/G6299_H3_1853_L5_subset.png](https://github.com/IngaSchl/Label-Extraction/blob/main/02%20Text%20detection/data/input/G6299_H3_1853_L5_subset.png)
* Figure 3: [02 Text detection/data/output/geoJson1.json](https://github.com/IngaSchl/Label-Extraction/blob/main/02%20Text%20detection/data/output/geoJson1.json)
* Figure 4: [03 Additional adjustments/01 unify overlapping bounding boxes/data/output/improved_boundingboxes_strabo_utm.shp](https://github.com/IngaSchl/Label-Extraction/blob/main/03%20Additional%20adjustments/01%20unify%20overlapping%20bounding%20boxes/data/output/improved_boundingboxes_strabo_utm.shp)
* Figure 5: [03 Additional adjustments/01 unify overlapping bounding boxes/data/output/improved_boundingboxes_strabo_utm.shp](https://github.com/IngaSchl/Label-Extraction/blob/main/03%20Additional%20adjustments/01%20unify%20overlapping%20bounding%20boxes/data/output/improved_boundingboxes_strabo_utm.shp)
* Figure 6: [06 Approximate georeferencing/data/output/roads_centroids_utm.shp](https://github.com/IngaSchl/Label-Extraction/blob/main/06%20Approximate%20georeferencing/data/output/roads_centroids_utm.shp)
