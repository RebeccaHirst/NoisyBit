from __future__ import division  # so that 1/3=0.333 instead of 1/3=0
from psychopy import visual, core, data, event, logging, sound, gui
from psychopy.constants import *  # things like STARTED, FINISHED
import numpy as np  # whole numpy lib is available, prepend 'np.'
from numpy import sin, cos, tan, log, log10, pi, average, sqrt, std, deg2rad, rad2deg, linspace, asarray
from numpy.random import random, randint, normal, shuffle
import math
import numpy
from numpy import random
from PIL import Image
import time
import os  # handy system and path functions
##---------------INPUT--------------
File='/Users/rebeccahirst/Downloads/BlackSquare.png'#Add path to image. Image should have an alpha channel.
value=100.5 #what contrast level do you want your stimulus to be set at? (between 0 and 255)
##----------------------------------

#window
win = visual.Window(fullscr=False, screen=0, allowGUI=True, allowStencil=False,
    monitor='testMonitor', color=[0,0,0], colorSpace='rgb',
    blendMode='avg', useFBO=True,
    )
#------------------------functions for noisy bit------------
#Functions to set the opacity level of the images
##The function below takes "value" as an input. This value is any number between 0 and 255 and can be a float or integer allowing a continuous scale of luminance.
##the output is what percentage of the pixels is the lower val and what percentage is the upper val.
def getPixelPercent(value):
    #calculates the percentage of upp
    if (value%1)*100<50:
        Percent_Lower_Pixels=100-((value%1)*100)
        Percent_Upper_Pixels=100-Percent_Lower_Pixels
     #   print Percent_Lower_Pixels, Percent_Upper_Pixels
    elif (value%1)*100>50:
        Percent_Lower_Pixels=100-((value%1)*100)
        Percent_Upper_Pixels=100-Percent_Lower_Pixels
       # print Percent_Lower_Pixels, Percent_Upper_Pixels
    elif (value%1)*100==50:
        Percent_Lower_Pixels=100-((value%1)*100)
        Percent_Upper_Pixels=100-Percent_Lower_Pixels
       # print Percent_Lower_Pixels, Percent_Upper_Pixels
    return (Percent_Lower_Pixels, Percent_Upper_Pixels)
##The function below goes through each channel (red green blue and alpha) and identifies how many pixels are fully opaque).
##it then creates a list of pixel values (the same length as the number of pixels it identifies). this list contains both lower and upper values.
##meaning if you want to set a stimulus to 200.5 half the list will be 200 and half 201. This way half of the pixels will become the lower val
##and half the upper val - so the overall luminance/opacity is a middle step.
def getNumberofPixels(Pixels, im):
    arr = numpy.copy(numpy.asarray(im))#get the numpy array from the image
    arr_T = arr.T#transpose? check
    r, g, b, a = arr_T#extract each of the channels
    for i in range(im.size[0]):#for the size of the image
        for j in range(im.size[1]):
            if a[i,j]>0:#go through the alpha channel. if the current pixel is above 0 set it to the level we want 
                Pixels=Pixels+1
    Number_of_Upper_Pixels=(Percent_Upper_Pixels/100)*Pixels
    Number_of_Lower_Pixels=(Percent_Lower_Pixels/100)*Pixels
    Upper_Pixels=numpy.rint(Number_of_Upper_Pixels)
    Lower_Pixels=numpy.rint(Number_of_Lower_Pixels)
    if Pixels% 2 == 0:
        TotalPixels=2#even
    else:
        TotalPixels=1#odd
    if (Upper_Pixels+Lower_Pixels)==Pixels-1 and TotalPixels==1: #if the total number of pixels is odd and together they make one less than the total pixels 
        Lower_Pixels=Lower_Pixels+1
    if (Upper_Pixels+Lower_Pixels)==Pixels+1 and TotalPixels==1: #if the total number of pixels is odd and together they make one more than the total pixels 
        Upper_Pixels=Upper_Pixels-1
    Pixel_Vals=[]
    Pixel_Vals.extend([UpperVal]*Upper_Pixels)
    Pixel_Vals.extend([LowerVal]*Lower_Pixels)
    return Pixel_Vals
##The function below goes through each pixal in an image/stimulus. Isolates the red, green, blue, and alpha channels
##it identifies if that channel contains a fully opaque pixel (i.e. its alpha is above 200 - just a random criterion set by me as obviously
## fully opaque would be 255. if the pixel is opaque it then checks in the list of pixel vals and randomly selects either the upper or lower value from 
## the list of "Pixel_Vals". it then removes this item from the PixelVals list.
 
def setPixelVals(im, pix, Pixel_Vals):
#rather than going through each pixel in an image we can convert the image into a numpy array in order to speed up processing
    arr = numpy.copy(numpy.asarray(im))#get the numpy array from the image
    arr_T = arr.T#transpose? check
    r, g, b, a = arr_T#extract each of the channels
    for i in range(im.size[0]):#for the size of the image
        for j in range(im.size[1]):
            if a[i,j]>=200:#go through the alpha channel. if the current pixel is above 200 set it to the level we want 
                e=numpy.random.choice(Pixel_Vals)
                a[i,j] = e
                Pixel_Vals.remove(e)
                im= Image.fromarray(arr)#convert the array back into an image
    Pic = visual.ImageStim(win, image=im, flipHoriz=False, pos=(0,0))
    return Pic, im

##Implement code
Percent_Lower_Pixels, Percent_Upper_Pixels=getPixelPercent(value)
UpperVal=math.ceil(value)#then we get the two nearest integer values we can use to compose said image with a mean luminance of 'value'
LowerVal=math.floor(value)
im=Image.open(File)
pix = im.load()
Pixels=0
Pixel_Vals=getNumberofPixels(Pixels, im)
Pic, im1=setPixelVals(im, pix, Pixel_Vals)

Pic.draw()
win.flip()
core.wait(2)
core.quit()