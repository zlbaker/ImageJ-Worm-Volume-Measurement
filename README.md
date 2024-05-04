# Zach Baker's ImageJ Worm Volume Measurement Macro
## Macro Introduction
This macro script was originally designed to estimate the full volume of a *Lumbriculus variegatus* worm using an image of a single organism. It was developed for use in the Fiji image processing package for ImageJ (Schneider et al., 2012; Schindelin et al., 2012). By collecting the estimated worm length and worms widths at various points along the worm using the ridge detection package for ImageJ (Wagner et al., 2017), this macro models the full worm as a series of small cylinders. The volumes of these cylinder are then added up to estimate the full volume of the worm. After analyzing a full folder of images, with one worm in each image, the resulting data is saved as a CSV files that can be used for later data analysis.

This macro was produced as a Kenyon College COMP401 undergraduate project in the Spring of 2024.

The following explanation assumes a basic understanding of ImageJ. While there are countless sources out there to help you learn about this, I found the following link from the Harvard Neurobiology Imaging website to be useful: https://nif.hms.harvard.edu/sites/nif.hms.harvard.edu/files/education-files/Fiji%20for%20Beginners.pdf

Any questions can be directed to zachlenbaker@gmail.com

## Macro Warnings
**This macro, while fairly successful in being able to measure relative volumes, is quite unrefined due to time constraints in completing this project.**
*As such, please take all results with a grain of salt. It is quite possible that there are bugs scattered throughout this code. With this, I suggest viewing this macro as a tool to build on for your own uses rather than one that can be downloaded and used immediately.*

This macro script is designed for use with ImageJ Version 1.54i.

This macro is designed to produce volume measurements that can be compared to each other rather than ones that can be prepared between different analyses. To put it more directly, *the volume measurements are likely innacurate to the actual volume of the worm*. All comparisons that I conducted throughout the development of this macro were between measurements made within the same macro run period.

## General Macro Use
To use this macro simply download the .ijm (ImageJ Macro) file, open it using Fiji in ImageJ, and select the "Run" button. It is expected that you have images of full worms to analyze in addition to a picture of a scale bar to ensure that the later measurements are accurate.

![Run Button Demo](https://github.com/zlbaker/ImageJ-Worm-Volume-Measurement/assets/156845020/d7f25b1b-dfd7-4a73-8fc7-b50c3ad2c9ec)


### Selecting Images to Analyze, an Output Folder, and an Appropriate Scale Bar
After starting the program, you will first be prompted by a popup window to select a folder containing the images that you are interested in analyzing. Use the menu to select such a folder. This folder should NOT include the image of the scale bar.

After selecting this folder, you will be prompted to select a folder to store the output CSV file in. Select any folder.

You will then be prompted to select the image that contains the appropriate scale bar, after selecting "OK" on a popup prompt. It is incredibly important that this image is take at the same magnification as the worm images that will be analyzed.

### Setting Scale
The scale bar image that you selected earlier will now open and you will be prompted to enter a DPI (dot per inch, refers to the resolution of the image) value to reduce the resolution of the image to. The following function assumes that the original image is 600 DPI, so the entered value should be between 1 and 600. The default value is set to 100, which will likely need to be adjusted according to the resolution of your images.

The image will then be converted to 8-bit and its resolution will be decreased according to the number that you just entered. Both of these steps are necessary for anaylsis with the ridge detection module, which was designed to analyze much smaller features than our worms. A pop-up window will ask if the scaling is satisfactory, continuing the program if you enter "y" and allowing you the change the DPI adjustment if you enter "n". While it may be difficult to tell if the image is scaled appropriately at this stage, you will be able to adjust this scaling later if the resolution of the worm images is reduced too much or not enough (the worms should ultimated be around 30 pixels wide).

The line tool will then be automatically selected and you will be prompted to set the scale using the scale bar image. *Make sure that the "global" box is checked*, then hit the "OK" button on the popup prompt. These steps are represented by the numbers on the image below.

![Scale Demo](https://github.com/zlbaker/ImageJ-Worm-Volume-Measurement/assets/156845020/7f5fd87a-5cb2-4a6e-b33b-1d901b78bae4)

### Modifying Worm Images, Gathering 2D Information, and Estimating Volume
All of the images contained within the input folder will now open. The first one opened will be automatically selected and modified, after which the user will be asked if the resulting modifications look correct. It is at this point that you may notice that the worm is too large for the detection software or that it is much smaller than 30 pixels in width. Inputting "y" will continue the program and inputting "n" will allow you to rescale this image, after which the scale bar is rescaled based on the new DPI input and the scale will have to be set again. This is done to ensure that all of the measurements collected stay consistent with the scale bar reference image.

Continuing the program selects the "Polygon Selection" tool, inverts the image, and prompts you to select the area containing the worm. The image is invert so that the removed area is white later in the analysis instead of black, which may be registered as a line by the ridge detection program. To move forward, simply create a selection around the worm, making sure to exclude any objects in the image aside from the worm, and hit the "OK" button on the popup prompt. This is shown in the image below.

![Selection Demo](https://github.com/zlbaker/ImageJ-Worm-Volume-Measurement/assets/156845020/4e27a702-22f6-410a-8b6b-50ec422af2ea)

A new popup window will then prompt you to enter in a value for the expected width of the line in pixels. This value should be between 1 and 30 pixels, given the limitations of the ridge detection tool, and can be estimated by eye.

Entering a value will then allow the ridge detection software to run. All objects within the image that the software has detected as a line will show the estimated line length in red. The estimated width of the line will be represented by a green line on either side of the red line. *The red line should be in the center of the worm and the green lines should highlight the edges. The red line should also be continuous across the length of the worm.* A popup window will prompt you to input "y" or "n" to assess the quality of the ridge detection. Inputting "y" continues the program while inputting "n" opens a menu of possible adjusts that one can make to improve the results. See the later section titled "Image Modifications to Fix Ridge Detection Issues" for more information and guidance in regard to these results. A picture of what a correct selection might look like is shown below.

![image](https://github.com/zlbaker/ImageJ-Worm-Volume-Measurement/assets/156845020/089410b8-903f-4cbe-a06c-9d489f3c7733)

## Image Modifications to Fix Ridge Detection Issues


## References
Schneider, C. A., Rasband, W. S., & Eliceiri, K. W. (2012). NIH Image to ImageJ: 25 years of image analysis. *Nature Methods*, 9(7), 671–675.

Schindelin, J., Arganda-Carreras, I., Frise, E., Kaynig, V., Longair, M., Pietzsch, T., … Cardona, A. (2012). Fiji: an open-source platform for biological-image analysis. *Nature Methods*, 9(7), 676–682. doi:10.1038/nmeth.2019

Wagner, T., Hiner, M., & Xraynaud. (2017). thorstenwagner/ij-ridgedetection: Ridge Detection 1.4.0 (Version v1.4.0). *Zenodo*. doi:10.5281/ZENODO.845874
