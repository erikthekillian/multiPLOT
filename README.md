# MultiPLI

## Introduction

This protocol involves three main aspects.
1. Plant Isolation
2. Plot Segmentation
3. Index Application & Data Extraction


## Setup

### 1. Import Models

In the Processing Toolbox tab click on the triple gear icon in the top left. Select “Add Model to Toolbox” and select model files.

### 2.	Import image.tif into QGIS

Drag and drop the stitched image into the center of the screen


### 3. Clip Raster by Extent

Using this tool, we will crop out any extra imagery so only the field of interest is included. Below is an example of the dialog options. 

![image](https://user-images.githubusercontent.com/13274399/205352656-54bc59a0-cf61-4ceb-b096-5e779d8bbc0b.png)


## Plant Isolation

### 4. EGI Histogram

Run the EGI model on the image from step 3 to calculate the excess greenness index of each pixel. 
The output shows the greenness values of pixels across the whole image.
To view the histogram of greenness values, double-click on the EGI layer to open Layer Properties. Find Histogram, then click Compute Histogram.

![Alt text](https://github.com/erikthekillian/multiPLI/blob/main/hist.jpg)

Select a threshold value based on this histogram curve. Values above the threshold will be labeled plants while below will be considered shadows and soil. The strictness of this threshold will define what are plants. Plants are represented by the higher value distribution. The distribution around 0 represents soil and some shadow pixels. It is best to select a threshold on the left-tail of the plant distribution to remove soil and shadows while maintaining plant pixels.   
 
1. Low threshold -> some shadows or soil will be called “plants.”
2. High threshold -> some plants will be removed as “soil or shadow.”

### 5. PLantsOnly_variable
        a.	Enter your input image and the threshold value you decided on in the previous step. This tool will remove all the pixels considered “Soil or Shadow.”
             i. Since QGIS 3.30 the threshold value must be input manually to the model. Right-click the model and "Edit." Click the "Create Mask" and replace the number under "Expression." The default is 45. 
        b.	To make this layer look “normal,” go to Symbology under Layer Properties and change Contrast Enhancement to “No Enhancement.”
        c.	De-select your input image to get rid of the background to view only the plants. 
        d.	PlantsOnly_MaskBase
             i. Use this only for consecutive flights of the same field. 
             ii. After georeferencing the fields so they are aligned, run this model so that we measure the same locations on both dates.

## Plot Segmentation

### 6. Create Grid

     a. Line
     b. Create a line grid, then move the lines to accurately cover each plot
         i. To edit the line grid, select the pencil "edit" icon when the line grid is selected. Then click on the edit vertices tool to move edges and lines. 

### 7. Polygonize
     a. Turns grid from lines into polygons. From here move vertices as required.
     b. Editing is done similarly to line grid. If any polygons need to be removed, select and delete these. 


***

### _Optional: Create in-set polygons to subsample plots_

#### _a. Centroids Tool_
        Using the polygon grid as a reference layer, we create centroid points in each polygon. These will be used as reference points to create in-set polygons. 

#### _b. Rectangles, Ovals, Diamonds Tool_
        Using the centroids as a reference layer, we create a polygon around each point. The size and shape can be defined using the dialog options. 

***

## Index Application and Data Export

### 8. Raster Calculator
***This is an optional step. Alternatively index calculations can be done after exporting raw data to an excel spreadsheet.***
     a. Input index.
          i. Excess Greenness Index = (2 * Green) – (Red + Blue)
          ii. Normalized Excess Greenness Index (NExG) = ((2 * Green) – (Red + Blue)) / (Red + Green + Blue)
          iii. Others
     b. Perform this on the PlantsOnly file. Otherwise this will include the background environment as well. 
     

### 9. Zonal Statistics
     a. The zonal statistics tools will individually measure each polygon in the grid, sampling every pixel in each polygon.
     b. If you created in-set polygons in the previous optional step, use these polygons as your reference layer. 
     c. Zonal statistics can be run as a batch process to extract each band seperately. This data can then be combined in excel to create index calculations. 
