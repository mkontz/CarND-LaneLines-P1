# **Finding Lane Lines on the Road** 

## Writeup by Matt Kontz


**Finding Lane Lines on the Road**

The goals / steps of this project are the following: 

* Make a pipeline that finds lane lines on the road 
* Demonstrate pipeline via output images and videos
* Reflect on your work in a written report

**Submission**

The project submission includes all required files:

* Ipython notebook with code
	* P1.ipynb
* Output images in folder: test_images_output
	* solidWhiteCurve_out.jpg
	* solidWhiteRight_out.jpg
	* solidYellowCurve_out.jpg
	* solidYellowCurve2_out.jpg
	* solidYellowLeft_out.jpg
	* whiteCarLaneSwitch_out.jpg
* Output videos in folder: test_videos_output
	* solidWhiteRight_out.mp4
	* solidYellowLeft_out.mp4
* Project write up
	* writeup_kontz.md

All file can be found at [https://github.com/mkontz/CarND-LaneLines-P1](https://github.com/mkontz/CarND-LaneLines-P1)

[//]: # (Image References)

[image1]: ./test_images_output/solidWhiteCurve_out.jpg "solidWhiteCurve"
[image2]: ./test_images_output/solidWhiteRight_out.jpg "solidWhiteRight"
[image3]: ./test_images_output/solidYellowCurve_out.jpg "solidYellowCurve"
[image4]: ./test_images_output/solidYellowCurve2_out.jpg "solidYellowCurve2"
[image5]: ./test_images_output/solidYellowLeft_out.jpg "solidYellowLeft"
[image6]: ./test_images_output/whiteCarLaneSwitch_out.jpg "whiteCarLaneSwitch"

[video1]: ./test_videos_output/solidWhiteRight.mp4 "solidWhiteRight"
[video2]: ./test_videos_output/solidYellowLeft.mp4 "solidYellowLeft"

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

The pipeline I create consisted on the following steps

* converted the input image tp grayscale using the provided helper function
* smoothed the image using the provide gaussian blur helper function
* applied the canny algorithm to the entire image using the provided helper function
* removed edge using a pyramid roughly the shape of the expected lines using the provided helper function
* removed the top of the pyramid near the vanishing point created by the lane lines using the provided helper function
	* Note: this could have been done using the same step, but it was a bit of an after thought and the pyramid was working quiet well
* Used Hough function to group lines and call draw function.  
* In order to group the Hough lines into lanes lines to the left and right of the car, I utilized sklearn's K-mean cluster algorithm based on the slope and intercept of lines.  With some generous thresholding, I was even able to remove most unwanted lines.  Once the lines where split left to right groups, the same K-mean help function was call to split the lines between to each side of both lane lines.  A this point, if everything went well there are four slope-intercept pair representing the edge of both the left and right lane lines.  The last step involved extending the line from the bottom of the image up toward the vanishing point.

This pipeline resulted in reasonable, but not perfect identification of the lanes lines for both the images and videos.  See images below.




![alt text][image1]

![alt text][image2]

![alt text][image3]

![alt text][image4]

![alt text][image5]

![alt text][image6]

### 2. Identify potential shortcomings with your current pipeline

This method has some short coming.  

* For starts it assume a straight flat road.  This work pretty well up close, but if the road is not flat or curves the straight flat assumption break down.  This is especially true as the image approaches the vanishing point.
* Using the K-mean square was simple and effective, but it didn't put any weight on the lines closer to the camera.  
* The in accuracies in the lines meant that they some times crossed and didn't always seem to point to the same vanishing point
* How I used the "region_of_interest" help function explicity assumes approximate vanishing point and location of car relative to the lanes.  Therefore during lane changes when the car is not pointed directly down the lane and/or not centered with the lane, my pipeline will likely break down.


### 3. Suggest possible improvements to your pipeline

Possible improvements:

* start by finding the lane lines near the bottom of the image (near the car) and then iterate through areas of interest in front of the lane lines found in order to piece together curves and then fit these lines segments to some type of curve or spline instead of a straight line.
* Add weighting to the K-mean clustering
* try to utilize the vanishing point to improve the accuracy of the generate line or curve
* Track the location of lines in the previous image to adjust the region of interest on the fly to improve robustness to lanes changes or not being centered to the lane.