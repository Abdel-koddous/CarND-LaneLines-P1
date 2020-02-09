# **Finding Lane Lines on the Road** 

## Project Writeup by Abdelkoddous Khamsi

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on work in a written report 


[//]: # (Image References)


[image1]: ./writeup_images/Gray.jpg "Grayscale image"
[image2]: ./writeup_images/Gaussian.jpg "Gaussian image"
[image3]: ./writeup_images/GaussianDarken.jpg "GaussianDarken image"
[image4]: ./writeup_images/Canny.jpg "Canny image"
[image5]: ./writeup_images/RoI.jpg "RoI image"
[image6]: ./writeup_images/HoughNoDark.jpg "HoughNoDark image"
[image7]: ./writeup_images/HoughWithDark.jpg "HoughWithDark image"
[image8]: ./writeup_images/HoughExtrapolate.jpg "HoughExtrapolate image"
[image9]: ./writeup_images/HoughNoDarkFinal.jpg "HoughNoDarkFinal image"
[image10]: ./writeup_images/HoughWithDarkFinal.jpg "HoughWithDarkFinal image"
[image11]: ./writeup_images/ExtraplatedOnFinal.jpg "ExtraplatedOnFinal image"
[image12]: ./writeup_images/ZchallengesNoDark.png "ZchallengesNoDark image"
[image13]: ./writeup_images/ZChallengeDarken.png "ZChallengeDarken image"
[gif1]: ./writeup_images/inAction.gif "ZChallengeDarken image"
[image14]: ./writeup_images/RoIDraw.jpg "RoI image"
[gif2]: ./writeup_images/inAction2.gif "ZChallengeDarken image"
[gif3]: ./writeup_images/inAction3.gif "ZChallengeDarken image"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.



#### Pipeline layout:
My first pipeline consists in 6 main steps given an initial image where I:
1. Converted initial image to grayscale:
2. Applied gaussian filter:

A kernel size of 5 was used to smooth the grayscale image. 

3. Used Canny edge detection with the following parameters:

`low_threshold = 100;
high_threshold = 150`

4. Filtered out uninteresting parts of the image using region of interest mask:
I defined a function _generateMaskedRegion_ that given an input image returns a set of 4 vertices defining the Region of Interest. the two first points are by default the bottom corners of the image and two upper points define a Trapezoid. 

![alt text][image14]

5. Used Hough transform to detect line segments in image space.
As covered in the course material I used the hough_lines helper function to detect the lines from the canny filter output.
In the pipeline I used the following parameters parameters:


`min_line_len = 35;
max_line_gap = 20`


6. Drew detected line segments on initial image.
I finally used the cv2.addWeighted function to combine the detected lines with the initial image.

The follwing table shows the resulting image of each step of this first pipeline
Solarized dark             |  Solarized Ocean
:-------------------------:|:-------------------------:
1. Grayscale image   |  ![alt text][image1]
2. Gaussian filtering |  ![alt text][image2]
3. Canny edge detection    |  ![alt text][image4]
4. Region of Interest mask.    |  ![alt text][image5]
5. Hough Transform  |  ![alt text][image6]
6. Lane Lines segments on initial image  |  ![alt text][image9]



Later on, I have done more processing on the line segments detected by the hough transform step:
* I classifed the segments into 2 classes according to their slope: left and right lane line segments.
* I computed the average for each class.
* I extrapolated the generated average to cover all the lane line.
* I drew the extrapolated lines on the initial image.

Solarized dark             |  Solarized Ocean
:-------------------------:|:-------------------------:
1. Average and extrapolate line segments for each side   |  ![alt text][image8]
2. draw resulting lines on initial image|  ![alt text][image11]
   

### *Let's show some action:*
![alt text][gif1]


Later on, I noticed that the hough transform still detect some segments in the Region of interest that are clearly not part of the lane line. These undesired line segments tend to share a common feature: they all have a small slope, worse case being horizontal lines with 0 slope. these segments do naturally and negatively impact the accuracy of the average for left and right lines that I computed in an earlier stage. Therefore I decided to define a slope threshold to decide whether a give line segments should be considered for the average or not.

This helps getting a more accurate lane lines detection on the "SolidYellowLeft" video, since the average is not imapcted by the small horizontal lines detected on the main lane line.

### *Let's show some action again:*
![alt text][gif2]

---

### 2. Identify potential shortcomings with your current pipeline


Finally, I wanted to try my pipeline on the challenge video which has a different and harsher environment:
* Diffrent colorations of the road.
* Variant lighting and shadows on the road and the sidewall.
* Car Hood visible in the frame.

The slope treshold allowed to filter out the lines on the edge of the hood since they are almost horizontal. another (simpler) way around it is to adjust the Region of interest of the specific video.

The lines detected due to the change of colorations/portions on the road are filterd out as well since they tend to have a small slope.

However, my line detection suffered the most, on the bright portion of the road as it particularily could not detect the yellow line.

I tried to darken the grayscale image to try make the white and yellow lines pop up as much as possible and avoid the ground mini wall lighting difference:
result:
* it helped Hough transform detect better line segments on the previous videos.

Solarized dark             |  Solarized Ocean
:-------------------------:|:-------------------------:
1. Used Hough transform to detect line segments in image space |  ![alt text][image7]
2. Drew detected line segments on initial image.  |  ![alt text][image10]


* it also could solve some issues in ground/side wall difference.


Solarized dark             |  Solarized Ocean
:-------------------------:|:-------------------------:
1. Before Darkening |  ![alt text][image12]
2. After Darkening and Adjusting RoI|  ![alt text][image13]

* Yet we are still noticing the problem in ground segment that is bright.
  
Let's check the result in action:
![alt text][gif3]



---

### 3. Suggest possible improvements to your pipeline

I can see for now two main improvements to be checked next:

* First, by exploring the potential of other color spaces: such as HSV, HSL ...

* Second, as we notice that our pipeline is working separatly on each frame, taking the information from previous frames could help give smoother and more accurate result