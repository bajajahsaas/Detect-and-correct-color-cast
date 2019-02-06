# Detection and correction of color cast in captured images

Use of openCV to detect cast (unwanted tints) in captured images. Further, using computer vision and image processing to correct the image and make it more appealing and natural 

# Overview
Color Cast is an unwanted tint in the captured image due to external and internal factors in camera. Internal factors include white-balance, color correction algorithm etc. External factors include lighting condition prevalent at the time of capture, object properties etc. The objective of the project was to detect the color cast in an image and correct it using a suitable algorithm. Later the working algorithm was to be ported on an android device.

Color Cast can be of 2 types: Real Cast and intrinsic cast (due to predominant color). The method only deals with detection and correction of real cast and the cases of intrinsic cast can separated by using an appropriate classifier as pre-processing step.

# Working
The algorithm will work in 2 steps. First step will be color cast detector which will give an output in form of color cast coefficient (ccc). If value of ccc is greater than some pre-defined threshold (0, in our case), then cast has been detected and the image is passed to color cast removal step. If value of ccc is less than the threshold, no significant cast has been found and the process can be terminated. 

# Color Cast Detection
The idea of this algorithm is derived from the paper based on ‘An Improved Color Cast Detection Method Based on an AB Chromaticity Histogram’.
Image is converted into LAB color space and split into different channels. 1-d histograms for A and B channels are computed. Compute mean and variance of A and B from the computed histograms. 
We can associate with each histogram an Equivalent Circle with its center in C = (µa, µb) and a radius of σ=sqrt(σa2+ σb2). Defining in this way a distance D: D=µ- σ, where µ=sqrt(µa2+µb2), a measure of how far the whole histogram lies from the neutral axis (a=0, b=0). To characterize the EC quantitatively, we introduce the ratio Dσ (color cast coefficient) =D/σ, as a useful parameter in measuring the intensity of the cast. The greater the Dσ, the stronger the cast.
If the histogram is concentrated (small value of variance) and far from the neutral axis (high value of µ and Dσ), the colors of the image are confined to a small region. This means we could be in presence of a cast.
If the value of color cast coefficient Dσ  is greater than 0, we will apply the color cast correction algorithm to rectify the image, otherwise we will terminate the process.

# Color Cast Correction
The idea for this algorithm is based on the technique of chromatic adaptation, wherein we will approximate the existing illumination of the image and perform illumination transformation to D-65 illuminant using Von Kries, Bradford and XYZ transformations. By changing the apparent illumination of a photo, we excite the same LMS cone responses in the eye as the desired illuminant. Typically, we assume that this is possible with a diagonal scaling of the axes after a transformation from XYZ to a space that more resembles the LMS cone space. In other words, once we're in the right space, we simply need to divide out our estimated illuminant and apply our desired illuminant separately for each channel.
In matrix form this is represented as: 

![alt text](https://raw.githubusercontent.com/bajajahsaas/Detect-and-correct-color-cast/master/CastCorrection/cast-correction.png)

The choice of the transformation matrix, MA is according to the transformation of Von Kries, Bradford and XYZ Scaling. 
Our desired lighting is the D65 CIE standard illuminant, which represents spectrum of the sun at mid-day. This is the white point for the sRGB color space that most monitors are calibrated for. Hence values of XWD, YWD and ZWD will be constant for the desired lighting required.
Only task remaining is to calculate the values of XWS, YWS and ZWS which will result from the approximation of the existing illuminant of the image, i.e. the white point estimation algorithm.
The output of this correction algorithm is an image just like the original one but with no color cast, and hence a better looking image.

# Results
Color Cast detection algorithm was successful to detect a cast in majority of the cases with accuracy of over 95%. The failure of the algorithm in presence of intrinsic cast was noted and can be resolved by a pre-processing classification process.
Color Cast correction results were also as per the requirement. The removal of cast could be guaranteed by passing the corrected image to the detector algorithm and checking the value of color cast coefficient. 
Another point to be noted is that the quality of the image was not affected in terms of SNR, resolution and color accuracy after the correction algorithm was applied to it. This means the output image contains all the richness in quality as the original image but with the removal of unwanted color cast.
