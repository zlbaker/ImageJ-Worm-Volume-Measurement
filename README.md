# Zach Baker's ImageJ Worm Volume Measurement Macro
## Macro Introduction
This macro script was originally designed to estimate the full volume of a *Lumbriculus variegatus* worm using an image of a single organism. It was developed for use in the Fiji image processing package for ImageJ (Schneider et al., 2012; Schindelin et al., 2012). By collecting the estimated worm length and worms widths at various points along the worm using the ridge detection package for ImageJ (Wagner et al., 2017), this macro models the full worm as a series of small cylinders. The volumes of these cylinder are then added up to estimate the full volume of the worm. After analyzing a full folder of images, with one worm in each image, the resulting data is saved as a CSV files that can be used for later data analysis.

This macro was produced as a Kenyon College COMP401 undergraduate project in the Spring of 2024.

Any questions can be directed to zachlenbaker@gmail.com

## Macro Warnings
**This macro, while fairly successful in being able to measure relative volumes, is quite unrefined due to time constraints in completing this project.**
*As such, please take all results with a grain of salt. It is quite possible that there are bugs scattered throughout this code. With this, I suggest viewing this macro as a tool to build on for your own uses rather than one that can be downloaded and used immediately.*

This macro script is designed for use with ImageJ Version 1.54i.

This macro is designed to produce volume measurements that can be compared to each other rather than ones that can be prepared between different analyses. To put it more directly, *the volume measurements are likely innacurate to the actual volume of the worm*. All comparisons that I conducted throughout the development of this macro were between measurements made within the same macro run period.

## Macro Use
To use this macro simply download the .ijm (ImageJ Macro) file, open it using Fiji in ImageJ, and select the "Run" button. It is expected that you have images of full worms to analyze in addition to a picture of a scale bar to ensure that the later measurements are accurate.

![Run Button Demo](https://github.com/zlbaker/ImageJ-Worm-Volume-Measurement/assets/156845020/d7f25b1b-dfd7-4a73-8fc7-b50c3ad2c9ec)


### Selecting Images to Analyze, an Output Folder, and an Appropriate Scale Bar
After starting the program, you will first be prompted by a popup window to select a folder containing the images that you are interested in analyzing. Use the menu to select such a folder. This folder should NOT include the image of the scale bar.

After selecting this folder, you will be prompted to select a folder to store the output CSV file in. Select any folder.

You will then be prompted to select the image that contains the appropriate scale bar, after selecting "OK" on a popup prompt. It is incredibly important that this image is take at the same magnification as the worm images that will be analyzed.

###

## References
Schneider, C. A., Rasband, W. S., & Eliceiri, K. W. (2012). NIH Image to ImageJ: 25 years of image analysis. *Nature Methods*, 9(7), 671–675.

Schindelin, J., Arganda-Carreras, I., Frise, E., Kaynig, V., Longair, M., Pietzsch, T., … Cardona, A. (2012). Fiji: an open-source platform for biological-image analysis. *Nature Methods*, 9(7), 676–682. doi:10.1038/nmeth.2019

Wagner, T., Hiner, M., & Xraynaud. (2017). thorstenwagner/ij-ridgedetection: Ridge Detection 1.4.0 (Version v1.4.0). *Zenodo*. doi:10.5281/ZENODO.845874
