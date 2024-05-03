# Zach Baker's ImageJ Worm Volume Measurement Macro
## Macro Introduction
This macro script was originally designed to estimate the full volume of a *Lumbriculus variegatus* worm using an image of a single organism. It was developed for use in the Fiji image processing package for ImageJ (Schneider et al., 2012; Schindelin et al., 2012). By collecting the estimated worm length and worms widths at various points along the worm using the ridge detection package for ImageJ (Wagner et al., 2017), this macro models the full worm as a series of small cylinders. The volumes of these cylinder are then added up to estimate the full volume of the worm. After analyzing a full folder of images, with one worm in each image, the resulting data is saved as a CSV files that can be used for later data analysis.

## Macro Warnings
*This macro, while fairly successful in being able to measure relative volumes, is quite unrefined*
*As such, please take all results with a grain of salt*

This macro script is designed for use with ImageJ Version 1.54i.



## References
Schneider, C. A., Rasband, W. S., & Eliceiri, K. W. (2012). NIH Image to ImageJ: 25 years of image analysis. *Nature Methods*, 9(7), 671–675.

Schindelin, J., Arganda-Carreras, I., Frise, E., Kaynig, V., Longair, M., Pietzsch, T., … Cardona, A. (2012). Fiji: an open-source platform for biological-image analysis. *Nature Methods*, 9(7), 676–682. doi:10.1038/nmeth.2019

Wagner, T., Hiner, M., & Xraynaud. (2017). thorstenwagner/ij-ridgedetection: Ridge Detection 1.4.0 (Version v1.4.0). *Zenodo*. doi:10.5281/ZENODO.845874
