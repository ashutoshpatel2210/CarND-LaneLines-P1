# **Finding Lane Lines on the Road** 

---

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images/solidWhiteCurve.jpg "Original Image"
[image2]: ./processed_grayscale_solidWhiteCurve.png "Grayscale"
[image3]: ./processed_canny_solidWhiteCurve.jpg "Canny Image"
[image4]: ./processed_cropped_solidWhiteCurve.jpg "Cropped Image"
[image5]: ./processed_solidWhiteCurve.jpg "Final Image"

---

### Reflection

### Requirements:
Project expects 
 * To have left and right lane lines are accurately annoted for images as well as videos. 
 * Output images and videos are annonated versions of input images and videos.

### 1. Describe your pipeline. 

### Design
My pipeline consisted of following steps. 

### Reading the image from test_images

First, images are read from test_images folder using mpimg.imread(). Local copy of images are created using np.copy() function and copy image (lane_image) is passed to pipline function lane_finding_pipeline() to generate left and right lane annonated version of image. Before starting processing with all images, images shapes (height and width) are checked and made sure that they are of same size and processing of all images is done with same pipeline design code.     

![alt text][image1]

### Convert Image into Gray scale Image

Each image (RGB - 3channels) is converted into Gray scale image(1 channel). Coverting image from RGB to Gray scale saves lots of computation power and makes image processing faster. Image is coverted into gray scale image using OpenCV library function cvtColor(<image>, cv2.COLOR_RGB2GRAY). 

![alt text][image2]

### Remove noise from grary scale image
Gray scale image is processed with image blurring next. This step helps in reducing noise and supressing spurious gradiants. Image blurring is done by convolving the image with a low-pass kernel filter.Open CV library provides function to GaussianBlur(). This phase use GaussianBlur() function with kernel size = 5 to remove noise from gray scale image. 

### Canny edge detection

The filtered image is processed with canny edge detection API canny(). It uses open CV Canny function to find edges in filtered image. The algorithm will first detect strong edge (strong gradient) pixels above the high_threshold (150), and reject pixels below the low_threshold(50). Next, pixels with values between the low_threshold(50) and high_threshold(150) will be included as long as they are connected to strong edges. The output edges is a binary image with white pixels tracing out the detected edges and black else everywhere. 

![alt text][image3]

### Cropped with region of interest
Canny image detects all edges in input image but we are interrested only in finding lane from image. This step masks all other area of image except lane using region_of_interest() API. region_of_interest() API uses openCV fillPoly() function to idenity region of interest using vertices argument. This vertices argument represents quadilateral area of lane ((0, height),(450,320),(500,320), (width,height)) and it is filled with white color(255). The below code is filling the pixels in quadilateral area.   

    #filling pixels inside the polygon defined by "vertices" with the fill color    
    cv2.fillPoly(mask, vertices, ignore_mask_color)
 
 The below code uses openCV bitwise_and() function to retun image where lane (quadilateral area) is available. 
 
    #returning the image only where mask pixels are nonzero
    masked_image = cv2.bitwise_and(img, mask)
  
 The below image is generated after this step.
 
 ![alt text][image4] 

### Hough Transformation
Next, Output image of region_of_interest() passes to Hough transformation function. Hough’s transform method transforms a line from its traditional y = mx + b form to rho = x *cos (theta) + y * sin (theta) where rho(=2) is the perpendicular distance from origin to the line, and theta(np.pi/180) is the angle formed by this perpendicular line and horizontal axis. 

The hough transformation function is called with following parameters. 
rho = 2 # distance resolution in pixels of the Hough grid
theta = np.pi/180 # angular resolution in radians of the Hough grid
threshold = 15     # minimum number of votes (intersections in Hough grid cell)
min_line_length = 40 #minimum number of pixels making up a line
max_line_gap = 30    # maximum gap in pixels between connectable line segments

This API gives output "lines " in array having endpoints of detected line segments. 

### Modifying draw lines function
Hough trnasformation API gives output in lines array. These lines could be left or right side of lane. This step requires processing of each line. To get slope and intercept , np.polyfit() function is called. left lane and right lane is classified as per below. 

    left lane: slope < 0
    right lane: slope > 0
    
The left_line[] and right_line[] lists find all left and right sides of lane and averages their slopes and intercepts. After finding average slope and intercept of left and right side of lane, code finds x1,y1,x2,y2 points from slope and intercept and passes it to openCV line() API to draw line of desired thickness.  

### Weighted Average
API weighted_img() draw lines on original image after adding 0.8 times weights to lines. 

Final image after applying pipeline. 

![alt text][image5]

### Applying to video
Function process_image() extracts each frame and apply pipeline lane_finding_pipeline() to each frame. lane_finding_pipeline() function returns left and right lanes for each frame.  


### 2. Identify potential shortcomings with your current pipeline

One of shortcoming of the pipeline is that it can not be applied well for curvatures roads. 


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to to reduce region of interest and iterative increase of y values in region of interest to tune region of interest into stright lines(small window) instead of curvature lines(large window). 
