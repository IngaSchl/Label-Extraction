# Automated Extraction of Labels from Large-Scale Historical Maps

[![DOI](https://github.com/IngaSchl/Label-Extraction/blob/main/zenodo.4721175.svg)](https://zenodo.org/badge/latestdoi/361790617)

**Author:** Inga Schlegel

**Date**: 04/26/2021

Research data and software used and implemented for the AGILE 2021 full paper *Automated Extraction of Labels from Large-Scale Historical Maps*.

Each subfolder *data* contains exemplary input and output data for each process step 02-05.


## 01 Data

Data source: Hamburg, Germany, 1853 (Raster Image). [GeoTIFF]. Harvard Map Collection, Harvard College Library. Retrieved from https://maps.princeton.edu/catalog/harvard-g6299-h3-1853-l5


## 02 Text detection

### Run Strabo for text detection
see also https://github.com/spatial-computing/strabo-text-recognition-deep-learning

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
See folder ([data/output](https://github.com/IngaSchl/Label-Extraction/tree/main/03%20Additional%20adjustments/02%20rotate%20text%20image%20areas/data/output)) for the rotated and cropped data which was stored in tiff format without compression.


## 04 Text recognition

### Run Tesseract OCR for text recognition

see also https://github.com/tesseract-ocr/tesseract/blob/master/doc/tesseract.1.asc and https://tesseract-ocr.github.io

In Windows command line:
1) `cd path/to/Tesseract-OCR`
2) `tesseract data/input/ID_24.tif data/output/ID_24 -l deu --psm 8`

Here, the German language package was chosen (`-l deu`). The input images were treated as single words (`--psm 8`).


## 05 String similarity

The (exemplary) input and (all) output data from the implemented Levenshtein Distance algorithm ([String_Similarity_by_Levenshtein_Distance.ipynb](https://gitlab.com/g2lab/label-extraction-from-historical-maps/-/blob/master/05%20String%20similarity/String_Similarity_by_Levenshtein_Distance.ipynb)) are stored in [05 String similarity/data/OCR_results.xlsx](https://gitlab.com/g2lab/label-extraction-from-historical-maps/-/blob/master/05%20String%20similarity/data/OCR_results.xlsx).

As a following step, an approximate georeferencing (as described in the paper's section 3.6) can be performed with QGIS Georeferencer. With the help of the best overall matches (throughout the whole map, see green marking in [05 String similarity/data/OCR_results.xlsx](https://gitlab.com/g2lab/label-extraction-from-historical-maps/-/blob/master/05%20String%20similarity/data/OCR_results.xlsx)), the centroids of the bounding boxes extracted in the text detection step with Strabo can be referenced to the "centroid" of the respective street or place (column *geometry_centroid* in *strassennetz*) whose original dataset already has a coordinate system. As a result, an approximate and initial georeferencing of the historical map may be generated via affine transformation.
