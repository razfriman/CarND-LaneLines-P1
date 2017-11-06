# **Finding Lane Lines on the Road** 


[//]: # (Image References)
[image0]: ./writeup_images/0.jpg "Original image"
[image1]: ./writeup_images/1_hls.jpg "HLS color mode"
[image2]: ./writeup_images/2_white_yellow.jpg "Filtering for yellows and whites"
[image3]: ./writeup_images/3_gaussian_blur.jpg "Gaussian blur"
[image4]: ./writeup_images/4_canny.jpg "Canny edge detection"
[image5a]: ./writeup_images/5_left.jpg "Left region masking"
[image5b]: ./writeup_images/5_right.jpg "Right region masking"
[image6a]: ./writeup_images/6_left.jpg "Left hough transform"
[image6b]: ./writeup_images/6_right.jpg "Right hough trasnform"
[image7]: ./writeup_images/7_weighted.jpg "Final output"

### Reflection


### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

I will describe my pipline in detail below. To go alone with the process of the pipeline, I will beusing this image as an example to demonstrate each step.

![alt text][image0]

First, I converted the images to use the HLS (Hue, Lightness, Saturation) color scale. From testing, I found this color scale was able to filter out the yellows and whites more distinctly from the image.


![alt text][image1]


Next, I performed a color filter, looking for color ranges within a certain threshold associated with the whites and yellows found in car lanes.

![alt text][image2]

After that, I performed a guassian blur on the image to help smooth out the pixels for later analysis.

![alt text][image3]

Next, I performed canny edge detection on the image to look constrasting edges. This helps accentuate distinct features in the image.

![alt text][image4]

Next, I performed region masking. However, instead of selecting one region from the image, I found better results in selecting two separate regions: one for the left and right sides of the lane, respectively. This was useful because we can assume certain features about each of these sections. For example, for the left side of the lane, we know the lane will most likely be pointing to the right towards the center of the image. This helps us eliminate some outliers when searching for the lane lines later on.

![alt text][image5a]
![alt text][image5b]

After that, I perform the hough transform on each of the images to get a list of lines back. I use these lines to calculate the final line drawn over the image.

To draw the final lane lines (draw_lines()), we need to extrapolate from the lines detected using the hough transform. However, this data will return one line for each feature found by the algorithm. To help compensate for outlying data, I extrapolate more points from the lines found. The longer lines will extrapolate more points than shorter lines, which helps compensate for small outlying lines detected.

Once all the points are collected from the detected lines, I perform a linear regresssion to find a line of best fit for the data. After the line is found, we extrpolate that line to display in the correct regions.

![alt text][image6a]
![alt text][image6b]

Lastly, we apply weights to the image to overlay on top of the original image.

![alt text][image7]

### 2. Identify potential shortcomings with your current pipeline

One potential shortcoming would be what would happen when the road ahead is curved. Since I am using a linear regression, the output of the regression is always going to be a straight line.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to perform a higher order regression on the data to be able to display curved lanes. This would help match curves of the lanes, should there happen to be any.

Another improvement to be made is to add 'state' to the algorithm. Currently all of the calculations get performed from the ground up, for every single frame of the image. We could improve this by keeping a history of data from previous frames and know that the data from the next frame is most likely going to be very similar to what was previously seen as the video progresses.