# **Finding Lane Lines on the Road** 

While driving we ensure that vehicles are within lane constraints, so as to make sure traffic is smooth and minimize chances of collisions with other cars. The lines on the road that show us where the lanes are and act as our constant reference for where to steer the vehicle. When we drive, we use our eyes to see where the lane lines are. Similarly, it is a critical task for an autonomous vehicle to perform. We can make cars see the lane lines by using computer vision techniques. The goal of this project is to create a pipeline that finds lane lines on the road using Python and OpenCV.


## 1. **Lane Detection Pipeline**
My pipeline consisted of following steps.
1. Convert original image to grayscale.
2. Apply slight Gaussian Blur.
3. Apply canny Edge Detector to get edges.
4. Define Region of Interest. This helps in weeding out unwanted edges detected by canny edge detector.
5. Retrieve Hough lines.
6. Consolidate and extrapolate the Hough lines and draw them on original image.
7. Bit-wise AND mask with original image .

Original images:
![solidWhiteCurve](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Original_solidWhiteCurve.jpg)
![solidWhiteRight](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Original_solidWhiteRight.jpg)
![solidYellowCurve](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Original_solidYellowCurve.jpg)
![solidYellowCurve2](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Original_solidYellowCurve2.jpg)
![solidYellowLeft](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Original_solidYellowLeft.jpg)
![whiteCarLaneSwitch](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Original_whiteCarLaneSwitch.jpg)

### Convert to grayscale

Converting original image to grayscale has its benefit. Grayscale image has only one channel compare to color image which got 3 channels(R,G,B).So processing 1 channel image is faster than 3 channel image and less computational.

![solidWhiteCurve](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Gray_solidWhiteCurve.jpg)
![solidWhiteRight](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Gray_solidWhiteRight.jpg)
![solidYellowCurve](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Gray_solidYellowCurve.jpg)
![solidYellowCurve2](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Gray_solidYellowCurve2.jpg)
![solidYellowLeft](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Gray_solidYellowLeft.jpg)
![whiteCarLaneSwitch](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Gray_whiteCarLaneSwitch.jpg)

### Gaussian Blur
Gaussian blur is pre-processing step used to reduce the noise from image.This pre-processing step to remove many detected edges and only keeps the most prominent edges from the image. GaussianBlur from OpenCV expect kernel size(odd value) for blurring. After trying some values, We used 7.
![solidWhiteCurve](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Gaussian_blur_solidWhiteCurve.jpg)
![solidWhiteRight](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Gaussian_blur_solidWhiteRight.jpg)
![solidYellowCurve](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Gaussian_blur_solidYellowCurve.jpg)
![solidYellowCurve2](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Gaussian_blur_solidYellowCurve2.jpg)
![solidYellowLeft](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Gaussian_blur_solidYellowLeft.jpg)
![whiteCarLaneSwitch](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Gaussian_blur_whiteCarLaneSwitch.jpg)

### Canny Edge Detection
Now I applied Canny edge detection to these Gaussian blurred images. It helps to find out the edges of the lanes. Canny Edge Detector uses the pixel gradient values and filters the image according to the Lower and Higher threshold provided. The pixel below lower threshold are ignored, while pixels above higher threshold are accepted. While pixels lying between lower and higher threhold are accepted if there are in connection with the pixels above the higher threshold.The preffered ratio of threshold are 2:1, 3:1.Here I used the ratio 3:1
![solidWhiteCurve](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Canny_solidWhiteCurve.jpg)
![solidWhiteRight](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Canny_solidWhiteRight.jpg)
![solidYellowCurve](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Canny_solidYellowCurve.jpg)
![solidYellowCurve2](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Canny_solidYellowCurve2.jpg)
![solidYellowLeft](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Canny_solidYellowLeft.jpg)
![whiteCarLaneSwitch](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Canny_whiteCarLaneSwitch.jpg)

### Region of interest
I then selected the Region of Interest with a polygon of trapeziodal shape focusing on the lower part where the probability of finding lane is more. The region outside the ROI is excluded by masking 0. Assumption here is camera remains in constant place and lanes are flat.
![solidWhiteCurve](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/ROI_solidWhiteCurve.jpg)
![solidWhiteRight](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/ROI_solidWhiteRight.jpg)
![solidYellowCurve](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/ROI_solidYellowCurve.jpg)
![solidYellowCurve2](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/ROI_solidYellowCurve2.jpg)
![solidYellowLeft](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/ROI_solidYellowLeft.jpg)
![whiteCarLaneSwitch](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/ROI_whiteCarLaneSwitch.jpg)

### Hough lines
I then used Hough Line Transform to find out the lines in ROI. It returns lines endpoint (x1,y1,x2,y2) detected in the images.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by using the weighted average method. First I seperated the right and left lane by using slope ( left lane has positive slope and right lane has negative slope). I then weighted average the slope and the intercept by using lenght of the line. After getting the average slope and intercept of Left and Right lane, we just need to extrapolate the lines.

![solidWhiteCurve](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/hough_solidWhiteCurve.jpg)
![solidWhiteRight](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/hough_solidWhiteRight.jpg)
![solidYellowCurve](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/hough_solidYellowCurve.jpg)
![solidYellowCurve2](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/hough_solidYellowCurve2.jpg)
![solidYellowLeft](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/hough_solidYellowLeft.jpg)
![whiteCarLaneSwitch](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/hough_whiteCarLaneSwitch.jpg)

### Getting final output
To draw the lanes on the image, I used the weigthed image of line and the original Image as shown in the Output Directory

![solidWhiteCurve](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Output_solidWhiteCurve.jpg)
![solidWhiteRight](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Output_solidWhiteRight.jpg)
![solidYellowCurve](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Output_solidYellowCurve.jpg)
![solidYellowCurve2](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Output_solidYellowCurve2.jpg)
![solidYellowLeft](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Output_solidYellowLeft.jpg)
![whiteCarLaneSwitch](https://view54dc5dc0.udacity-student-workspaces.com/view/CarND-LaneLines-P1/test_image_output/Output_whiteCarLaneSwitch.jpg)

### Applying Pipeline to videos

Now let’s apply the same above pipeline for videos.

Pipeline works pretty good for straight lanes.
videos are saved in test_video_output

## Shortcomings with current pipeline
1. Hough Lines based on straight lines do not work for curved road/lane.
2. The Canny edge threshold needs to be dynamic to get proper edges even when the light intensity varies
3. Also Region of Interest assumes that camera stays at same location and lanes are flat. 

## Possible improvements to pipeline
1. Instead of line , it would be beneficial to use higher degree curve that will be useful on curved road.
2. Even when we used information from previous frames, just averaging lanes might not be very good strategy. may be weightd average might work.
