# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images/solidWhiteCurve.jpg "Original Image"
[image2]: ./processed_grayscale_solidWhiteCurve.png "Grayscale"

---

### Reflection

### 1. Describe your pipeline. 
As starting point of project, Project requirements and design should be in place. 

### Requirements:
Project expects 
 * To have left and right lane lines are accurately annoted for images as well as videos. 
 * Output images and videos are annonated versions of input images and videos.

### Design
My pipeline consisted of following steps. 

### Reading the image from test_images

First, images are read from test_images folder using mpimg.imread(). Local copy of images are created using np.copy() function and copy image (lane_image) is passed to pipline function lane_finding_pipeline() to generate left and right lane annonated version of image.     
![alt text][image1]

### Convert Image into Gray scale Image

Each image (RGB - 3channels) is converted into Gray scale image(1 channel). Coverting image from RGB to Gray scale saves lots of computation power and makes image processing faster. Image is coverted into gray scale image using OpenCV library function cvtColor(<image>, cv2.COLOR_RGB2GRAY). 

![alt text][image2]

### Remove noise from grary scale image

4. Canny edge detection
5. Cropped with region of interest
6. Hough Transformation
7. Average lines
8. Weighted Average


Applying to video
    
In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...

If you'd like to include images to show how the pipeline works, here is how to include an image: 




### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
