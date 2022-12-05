# Tartarus

## Introduction

This protocol involves three main aspects.
1. Plant Isolation
2. Plot Segmentation
3. Index Application & Data Extraction


## Setup

### 1. Import Models

In the Processing Toolbox tab click on the triple gear icon in the top left. Select “Add Model to Toolbox” and select model files.

### 2.	Import Heading_6_23.tif into QGIS

Drag and drop the stitched image into the center of the screen


### 3. Clip Raster by Extent

Using this tool, we will crop out any extra imagery so only the field of interest is included. Below is an example of the dialog options. 

![image](https://user-images.githubusercontent.com/13274399/205352656-54bc59a0-cf61-4ceb-b096-5e779d8bbc0b.png)


## Plant Isolation

### 4. NExG Histogram

The output shows the greenness values of pixels across the whole image. We only want the greenest pixels part of the second group. Using the cropped image from step 3. 

Picture of histogram

Select a threshold value. Values above the threshold will be labeled plants while below will be considered shadows and soil. The strictness of this threshold will define what are plants. The default threshold is 40.
 
1. Low threshold -> some shadows or soil will be called “plants.”
2. High threshold -> some plants will be removed as “soil or shadow.”

### 5. PLantsOnly_variable
a.	Enter your input image and the threshold value you decided on in the previous step. This tool will remove all the pixels considered “Soil or Shadow.”
b.	To make this layer look “normal,” go to Symbology under Layer Properties and change Contrast Enhancement to “No Enhancement.”
c.	De-select your input image to get rid of the background to view only the plants. 
d.	PlantsOnly_MaskBase
        i. Use this for each consecutive flight of the same field. 
        ii. After georeferencing the fields so they are aligned, run this model so that we measure the same locations on both dates.

## Plot Segmentation

### 6. Create Grid

     a. Line
     b. Create a line grid, then move the lines around to accurately cover each plot

### 7. Polygonize
     a. Don't include field from the grid
     b. Turns grid from lines into polygons. From here move vertices as required.


***

### _Optional: Create in-set polygons to subsample plots_

#### _a. Centroids Tool_
        Using the polygon grid as a reference layer, we create centroid points in each polygon. These will be used as reference points to create in-set polygons. 

#### _b. Rectangles, Ovals, Diamonds Tool_
        Using the centroids as a reference layer, we create a polygon around each point. The size and shape can be defined using the dialog options. 

***

## Index Application and Data Export

### 8. Raster Calculator
     a. Input index.
          i. Excess Greenness Index = (2 * Green) – (Red + Blue)
          ii. Normalized Excess Greenness Index (NExG) = ((2 * Green) – (Red + Blue)) / (Red + Green + Blue)
          iii. Others
     b. Perform this on the PlantsOnly file. Otherwise this will include the background environment as well.  

### 9. Zonal Statistics
     a. The zonal statistics tools will individually measure each polygon in the grid, sampling every pixel in each polygon.
     b. If you created in-set polygons in the previous optional step, use these polygons as your reference layer. 
