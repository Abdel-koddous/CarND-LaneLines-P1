# **Finding Lane Lines on the Road** 

## Project Writeup by Abdelkoddous Khamsi

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report 


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[image2Test]: ./test_images/solidYellowCurve.jpg "image title ??"
[image3Test]: ./test_images/solidWhiteRight.jpg "some title"
---
![Kitten](./examples/grayscale.jpg#thumbnail)

### Reflection
img[src*="#thumbnail"] {
   width:150px;
   height:100px;
}
### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I .... 

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]

#### Pipeline layout:
My straightforward pipeline given an initial image consists in 6 main steps:
1. Converted initial image to grayscale.
2. Applied gaussian filter to smooth image.
3. Used Canny edge detection.
4. Filtered out uninteresting parts of the image using region of interest mask.
5. Used Hough transform to detect line segments in image space.
6. Drew detected line segments on initial image.

![alt text][image2Test]
![alt text][image3Test]

Later on, I have done more processing on the line segments detected by the houg transform:
* I classifed them as into 2 classes according to their slope: left and right lane line segments.
* I computed the average for each class.
* I extrapolated the generated average to cover all the lane lines.
* I drew the extrapolated lines on the initial image.

<img src="test_images/solidYellowCurve2.jpg" width="300" alt="Combined Image" />

Later on, I noticed that the hough transform still detect some segments in the Region of interest that are clearly not part of the lane line. These undesired line segments tend to share a common feature: they all have a small slope, worse case being horizontal lines with 0 slope. these segments do naturally and negatively the accuracy of the average for left and right lines that I compute in an earlier stage. Therefore I decided define a slope threshold to decide whether a give line segments should be considered for the average or not.

---

### 2. Identify potential shortcomings with your current pipeline


Later on, I wanted to try my pipeline on the challenge video, it struggled to keep up with:
* Diffrent colorations of the road.
* variant lighting and shadows.

I tried to darken the grayscale image to try make the white and yellow lines pop up as much as possible. avoid the ground mini wall lighting difference:
result:
* it could solve the issue of ground/wall difference
* still noticing the proplem in ground segment that is very pale.

---

### 3. Suggest possible improvements to your pipeline

Explore other color spaces: HSV, HSL
Take the information from previous frames