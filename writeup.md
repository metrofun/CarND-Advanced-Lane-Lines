## Writeup Template

---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[calibration1]: ./output_images/undistorted_calibration1.jpg "Calibration 1"
[undistorted_calibration1]: ./output_images/undistorted_calibration1.jpg "Undistorted calibration 1"
[test6]: ./output_images/test6.jpg "Test 6"
[undistorted_test6]: ./output_images/undistorted_test6.jpg "Undistorted test 6"
[warped_test6]: ./output_images/warped_test6.jpg "Warped test 6"
[binary_test6]: ./output_images/binary_test6.jpg "Binary test 6"
[lines_test6]: ./output_images/lines_test6.jpg "Lines test 6"
[roads_test6]: ./output_images/roads_test6.jpg "Roads test 6"
[orig_with_lanes_test6]: ./output_images/orig_with_lanes_test6.jpg "Projected back lanes"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in "P2.ipynb", section 1.3 "Distortion Calibration".

I use all available calibration images. First detect the chessboard corners (i.e., "image points") and map them to 3D "object points" grid which lies on 2D manifold. And use them to compute distortion using "cv2.calibrateCamera".

![calibration1.jpg][calibration1]
![undistorted calibration1.jpg][undistorted_calibration1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![test6.jpg][test6]
![undistorted test6.jpg][undistorted_test6]

#### 2. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

Perspective transform is implemented in section 1.5 in "P2.ipynb". I used "straight_lines1.jpg" for that purpose.
This resulted in the following source and destination points:

| Source        | Destination   |
|:-------------:|:-------------:|
| 595, 450      | 300, 0        |
| 230, 700      | 300, 720      |
| 1075, 700     | 980, 720      |
| 685, 450      | 980, 0        |

I verified that my souce points make sense by drawing a polyline on top of the "straight_lines1.jpg".
![warped test6.jpg][warped_test6]

#### 3. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

One of the most fragile pieces of the pipeline. Just a bunch of intuitively resonable numbers.
The code is in section 1.4 in "P2.ipynb". The main method "threshold" is in section 1.2 "Helper Functions".
I tool Saturation and Light channels from HSL images, thresholded them.In also used Sobel fitler to futher prune away some noise from the images.
I took the convolution of Sobel kernels with the original image, but without Hue channel. And filtered pixels by their horizontal and overall magnitude.

![binary test6.jpg][binary_test6]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The code is in section 1.6 in "P2.ipynb". I used a convolution (sliding-window) of the rectangular template to find dense regions of thresholded values, that are likely to correspond to lanes. It's another far-fetched assumption.

![lines test6.jpg][lines_test6]

I have took points that fall into these dense regions and fitted 2nd degre polinomials to them. And filled the gap between the fitted polynomials with red color.

![road test6.jpg][roads_test6]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I took the same pixel/world rations from the lectures. The result is visible on the previous picture.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.
The logic is in section "1.6" in P2.ipynb. I have inverted the sign of distortion to disort lanes back. Not 100% positive on it's correctness. But visually it looks alright.

![orig with lanes]['orig_with_lanes_test6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output_images/project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

That's a pipeline of fragile hardcoded pieces. Which do not consider the whole picture and all the hardcoded values overfit to the test images.
Probably OK for learning classic computer vision techniques. Looking forward to the next chapters, to play with sementic segmentation.
