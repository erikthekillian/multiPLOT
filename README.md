# MultiPLOT

## Introduction
This workflow relies soley on QGIS tools. We created "Models" within the QGIS toolbox to automate some processing steps, but these can also be run manually the default toolbox. 

There are two goals in this workflow: isolating plant pixels and delineating plots in the image. Excess Greenness Index threshold is used to classify pixels as plant or background. To create an accurate plot map we overlay a grid on our image. This can be either the original RAW image or the plant-classified image as the grid is independent of the underlying image file. The grid is created automatically based on plot sizes and extent but can then be edited on a field and plot-specific level. Finally we can extract the plant pixel band information using the Zonal Statistics Tool. This will use the plant-classified image and the plot grid to export the average or any other statistic summary of each spectral band per plot.

![flowchart](https://github.com/user-attachments/assets/c397dbea-6655-4b18-96c0-578228d73759)

## Setup

### I. Import Models

In the Processing Toolbox tab click on the triple gear icon in the top left. Select “Add Model to Toolbox” and select model files. These models are packages of tools run in a batch process to streamline certain steps. They are run from the Toolbox just like any other QGIS tool. Because they are made up of default tools, they are easily edited or reproduced manually through the toolbox if desired.   

### II.	Import image.tif into QGIS

Drag and drop the stitched image into the center of the screen

### III. Clip Raster by Extent

Using the **Clip Raster by Extent** tool, we will crop out any extra imagery so only the field of interest is included. Below is an example of the dialog options. 

![image](https://user-images.githubusercontent.com/13274399/205352656-54bc59a0-cf61-4ceb-b096-5e779d8bbc0b.png)

### IV. Georeference

Images can be georeferenced in QGIS using the **Georeferencer** tool. This is recommended if the dataset includes multiple flights of the same area. With georeferenced images the plot grid we make later can be applied across all images. 

## 1. Plant Isolation

### I. Excess Greenness Index Histogram

Run the EGI model to calculate the excess greenness index of each pixel. The output image shows the EGI values of pixels across the whole image.
To view the histogram of greenness values, double-click on the EGI image layer to open Layer Properties. Find Histogram, then click Compute Histogram.

![Alt text](https://github.com/erikthekillian/multiPLI/blob/main/hist.jpg)

Select a threshold value based on this histogram curve. Values above the threshold will be labeled plants while below will be considered shadows and soil. The strictness of this threshold will define what are plants. Plants are represented by the higher value distribution. The distribution around 0 represents soil and some shadow pixels. It is best to select a threshold on the left-tail of the plant distribution to remove soil and shadows while maintaining plant pixels.   
 
1. Low threshold -> some shadows or soil will be called “plants.”
2. High threshold -> some plants will be removed as “soil or shadow.”

### II. PLantsOnly_variable
        a.	Enter your input image and the threshold value you decided on in the previous step. This tool will remove all the pixels considered “Soil or Shadow.”
             i. Since QGIS 3.30 the threshold value must be input manually to the model. Right-click the model and "Edit." Click the "Create Mask" and replace the number under "Expression." The default is 45. 
        b.	To make this layer look “normal,” go to Symbology under Layer Properties and change Contrast Enhancement to “No Enhancement.”
        c.	The PlantsOnly file will load on top of the original file, overlapping perfectly. De-select your input image in Layers Panel to hide the background image. 
        d.	PlantsOnly_MaskBase
             i. Use this only for consecutive flights of the same field. 
             ii. After georeferencing the fields so they are aligned, run this model so that we measure the same locations on both dates.

## 2. Plot Segmentation
The goal of plot segmentation is to accurately delineate the bounds of each plot in the image. 


### I. Create Grid

     a. Line
     b. Create a line grid, then move the lines to accurately cover each plot
         i. To edit the line grid, select the pencil "edit" icon when the line grid is selected. Then click on the edit vertices tool to move edges and lines. 

### II. Polygonize

     a. Turns grid from lines into polygons. Lines and vertices can now be edited on a plot-specific level. 
     b. Editing is done similarly to line grid. Entire polygons can be moved or removed as well by selecting the center of the polygon.  


***

### _Optional: Create in-set polygons to subsample plots_

#### _a. Centroids Tool_
        Using the polygon grid as a reference layer, we create centroid points in each polygon. These will be used as reference points to create in-set polygons. 

#### _b. Rectangles, Ovals, Diamonds Tool_
        Using the centroids as a reference layer, we create a polygon around each point. The size and shape can be defined using the dialog options. These are the subset plot polygons we will use to extract pixel data.  

***

## 3. Data Export
To extract pixel values, we require a PlantsOnly file and an accurate plot grid. Data extraction will summarize the pixel values in each plot and export to an excel file type. This is why it is important to have both an accurately classified image that only includes plants as well as an accurate plot grid that will measure only plants in the correct plot.  

### I. Zonal Statistics
     a. The Zonal Statistics tools will individually measure each polygon in the grid, sampling every pixel in each polygon.
     b. If you created in-set polygons in the previous optional step, use these polygons as your reference layer. 
     c. Zonal statistics can be run as a batch process to extract each band seperately. This data can then be combined in excel to create index calculations. 


## 4. Alternative Aproach
If you want to view or extract specific index values we can run the raster calculator to calculate a spectral index per-pixel. This will create a new raster layer which can then be viewed and extracted per-plot using the same method as above.

### I. Raster Calculator
***This is an optional step.***
     a. Input index.
          i. Excess Greenness Index = (2 * Green) – (Red + Blue)
          ii. Normalized Excess Greenness Index (NExG) = ((2 * Green) – (Red + Blue)) / (Red + Green + Blue)
          iii. Others
     b. Perform this on the PlantsOnly file. Otherwise this will include the background environment as well. 
     
