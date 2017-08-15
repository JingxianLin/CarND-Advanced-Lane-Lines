---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc. to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/undistort.png "Undistorted"
[image2]: ./output_images/test.png "Road"
[image3]: ./output_images/binary.png "Binary"
[image4]: ./output_images/warped.jpg "Warp"
[image5]: ./output_images/example.jpg "Output"
[video1]: ./out_project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first two code cells of the IPython notebook.

I start by preparing "object points", like (0,0,0), (1,0,0), (2,0,0), ..., (8,5,0), and `objpoints` will be appended with such a copy every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result:

![alt text][image1]

I save the result from all images to "./output_images/calibration" and "./output_images/undistorted".  For some images without corners found, smaller values for nx = 6, 7 or ny = 5 will help.

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.
To demonstrate this step, I show what I get from applying the distortion correction to one of the test images like this one:
![alt text][image2]
Compared to the original image, the undistorted image only includes part of the white car.
####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I used a combination of color and gradient thresholds to generate a binary image (thresholding steps in `Color/gradient threshold` and `Helper functions': gradient threshold on x direction for red values or r channel from RGB threshold, then returning an image only where mask pixels are nonzero).Here's an example of my output for this step.

![alt text][image3]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in the 6th code cell of the IPython notebook.  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose to hardcode the source and destination points in the following manner:

```
src = np.float32(
    [[(img_size[0] / 2 - 70), img_size[1] * 0.65],
    [(img_size[0] / 2 - 370), img_size[1] - 50],
    [(img_size[0] / 2 + 370), img_size[1] - 50],
    [(img_size[0] / 2 + 70), img_size[1] * 0.65]])
dst = np.float32(
    [[(img_size[0] / 2 - img_size[1] / 2), 0],
    [(img_size[0] / 2 - img_size[1] / 2), img_size[1]],
    [(img_size[0] / 2 + img_size[1] / 2), img_size[1]],
    [(img_size[0] / 2 + img_size[1] / 2), 0]])

```

This resulted in the following warped image and its binary counterpart.

![alt text][image4]

Warped images for all the test images are supplied in "./output_images/transformed"

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I first took a histogram along all the columns in the bottom half of the image to identify peaks, then used sliding windows to find the lines, and fit a 2nd order polynomial to each (the 8th code cell of the Ipython notebook).

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in the 9th code cell of the Ipython notebook.

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in `Detect lane lines` and `Test images` in the function `processing()`.  Here is an example of my result on a test image:

![alt text][image5]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./out_project_video.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I learn a lot from the implementation of this project and suggestions provided after the first submission.  Especially, when creating the binary image, it may prove helpful to create the birds-eye perspective first.  The reason is that error pixels in the binary get stretched out when the birds-eye transformation is performed second.  In shadows or changing road conditions, the R color channel is strongly represented in yellows and whites.  Thes suggestions lead to improvement in this submission, and I might implement sanity checks on every frame to obtain a more robust result for challenge videos.
