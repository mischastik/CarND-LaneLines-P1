# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[roi]: ./roi.jpg "Depiction of chosen ROI"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consists of 5 steps. 
First the image is converted to grayscale as color information is not particularly useful in this case.
Then the image is blurred to get rid of image noise and also potentially misleading image details like debris on the street surface.
On the result Canny edge detection is perfomed to get only pixels that are potential line candidates.
All pixels of the edge image outside a region of interest (ROI) are removed in the next step. For the ROI, a quad is chosen 
which has the full image width at the base and much shorter horizontal extent slightly below the half height. Upper regions are neglegted 
be cause the lines might be significantly bent close to the horizon.

![Depiction of chosen ROI][roi]

The actual lines are detected with the standard probablilistic Hough approach.

The method draw_lines accumulates and filters the line segements from Hough using the following rules:
* based on their x-coordinate, the lines are either considered as part of the left or right line
* extremely steep line segments (abs(x2 - x1) < 3) are discarded to avoid numeric issues
* extremely flat line segements (slope < 0.5) are discarded as they have to be misdetections
* the median slope and offset of the accumulated line segments is then computed and used as the respective left and right lane marker estimates. The median is used to get rid of potential outliers.


### 2. Identify potential shortcomings with your current pipeline

The algorithm cannot handle driving over the lanes (for instance when overtaking or turning).
The line detection will probably fail in curves with small radius as the lane markers are too much bent.
It might have issues with low contrast between markings and street surface (for instance on wet streets at night or if the markings are worn).
If another car blocks a significant part of the lane markings, the line detection might also fail due to lack of data.

### 3. Suggest possible improvements to your pipeline

A more flexible model for the lane markings might be very usefull in practice which can handle bent lines and which can make use of geometric properties, such that parallel lines at around 4-5 meters distance on a flat surface need to be detected.
Also temporal information can be very useful, i. e. tracking of the lanes from frame to frame. This could even be improved with using odometry data from the car.
