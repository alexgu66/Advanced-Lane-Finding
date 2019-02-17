
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

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the code cell 18 and 20 of the IPython notebook located in "adv_lane_line.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![Undist](/Output/Undist.png)



### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![Undist_test1](/Output/Undist_test1.png)

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image at cell 22.  Here's an example of my output for this step.   I tried the different combinations mentioned in the lesson and the S/gradient combination shows the best result, which is normal since the saturation of color keeps at same level even in shadow area.

![threshold_image](/Output/threshold_image.png)                 



#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform is at cell 23.  Following is a sample. I verified that my perspective transform was working as expected by verifying that the lines appear parallel in the warped image.

![warp1](/Output/warp1.png)

I chose to hardcode the source and destination points in the following manner:

```python
src = np.float32([[577, 463], [250, 692], [705, 463], [1059, 692]])
dst = np.float32([[250, 0], [250, 692], [900, 0], [900, 692]])
```

This resulted in the following source and destination points:

|  Source   | Destination |
| :-------: | :---------: |
| 577, 463  |   250, 0    |
| 250, 692  |  250, 692   |
| 705, 463  |   900, 0    |
| 1059, 692 |  900, 692   |



#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![fit](/Output/fit.png)

The code is in cell 24 and 25. The FindNextPolynomial() in cell 25 searches the fit by polynomial identified in previous frame.



#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in cell 26 with MeasureCurvature(), which also converts the result to meters. The car offset 

In code cell 24 and 25, the car offset (car_offset) is also calculated and converted to meters.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The function called WarpBack() in cell 27 also has this functionality, plus filling the identified lane area with green. Following is a sample.

![warp2](/Output/warp2.png)



---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./projecproject_video_all.mp4), the project_video_all.mp4 has the lane line identified as green. The annotation of curvature and car offset are also added to the video.

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I met an issue that the polynomial identified changes dramatically as following,

![wrong_fit](/Output/wrong_fit.png)



It's fixed by reduce search_window_margin from 100 to 50 to screen out the noises. I think the further enhancement can be adjust the color and gradient threshold to filter it out, which shall be a more generic approach.

I used a csv file to record the curvature and polynomial of each frame, which is a good start to tune the parameters. However, it's not that general since the data only come from one video. Collecting more data from real driving will be needed to make it more robust.

Some light condition, such as rain, night, sunset, and so on, may impact the camera's performance. Some image processing can be employed to make it more robust, like increasing color saturation, image denoise, higher exposure, etc.

