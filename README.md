# NoisyBit_RJHirst


#This is a code to create a continuous scale of luminance for images through randomly 
#assigning an opacity setting to each pixel in an image. 
#Python code runs in Psychopy version 1.85.2
#code initially expressed via the Psychopy discourse page: https://discourse.psychopy.org/t/dithering-to-measure-contrast-sensitivity/2337/2

#Based on method outlined by Allard, R., & Faubert, J. (2008). The noisy-bit method for 
#digital displays: Converting a 256 luminance resolution into a continuous resolution. 
#Behavior Research Methods, 40(3), 735-743.

#The code takes an image with four channels (RGB Alpha). (eg. “BlackSquare.png”). 
#Set the path to your image by setting the “File” variable.

#File - An image file with four channels (RGB Alpha). (eg. “BlackSquare.png”). 

#value - float between 0 and 255. - (In this example is set to 100.5)
