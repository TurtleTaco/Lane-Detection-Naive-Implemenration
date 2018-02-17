# **Finding Lane Lines on the Road** 

## Writeup Report

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./intermediate_results/gray.png "gray"
[image2]: ./intermediate_results/blur.png "blur"
[image3]: ./intermediate_results/canny.png "canny"
[image4]: ./intermediate_results/masked.png "masked"
[image5]: ./intermediate_results/original.png "original"
[image6]: ./intermediate_results/overlay.png "overlay"


---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

The pipeline has 5 major steps for image lane identification.
1. Image is loaded with mpimg.imread()
<img align="left" src=[image5]>
2. Converting the image into grayscale using grayscale(image)
![alt text][image1]
3. Applying Gaussian Blur to the grayscaled image
![alt text][image2]
4. Applying Canny edge detection
![alt text][image3]
5. Hard code a mask that applies to the specific dataset provided in this assignment
![alt text][image4]
6. Creating overlay, highlighting the lane with red mark and create an overlay effect with cv2.addWeighted() function.
![alt text][image6]

In order to connect all the line segments on left and right, I modified draw_lines() function by getting the two "extreme points" from all line segments generated in a single run. These two "extreme points" corresponds to the left/right lane buttom and left/right lane top points.

As the second step, I calculate the slope of the two lines connecting left/right buttom and top points. Depending on the absolute value of the slopes, I filter out any pair of points that generates slope less then 0.5 to compensate disturbance data from solidYellowLeft video. Since all videos have lines start from the very buttom of every frame, I extend left and right lanes until both reaches the buttom of frame (y = 590) following the same slope.

After finishing the above steps, I found the lanes highlighted is not quite stable. To solve this, I created another function insert_pop_average(). This function takes the new generated "extreme points" and insert it into a list with length N (N = 30 in this case) and at the same time pop out the first item (oldest data) exists in the list. The function then returns the average "extreme points" coordinates and draw them on the frame. With this function, I managed to stabalize lane detection by considering the previous 30 frames and the result is in test_video_outputs folder.


### 2. Identify potential shortcomings with your current pipeline


The biggest shortcoming with my stabalization algorithm is when the car takes a sharp turn, it will require 30+ frames to completely adapt to the changes because the algorithm basically assumes lane directon will not change much in any 30 frame interval.

Another shortcoming is the hard coded mask which only works for a specific situation where the cars have their cameras installed at a fix point and the cameras capture exactly the same field of view.


### 3. Suggest possible improvements to your pipeline

To improve on the first potential shortcoming, we can either decrease the number of N to accelerate the adaption speed. However this produces less stable output as tradeoff.

To address the second shortcoming, new algorithm needs to be developed that can identify lanes no matter what the relative position the lanes have in each frame.
