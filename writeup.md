
[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[image10]: ./output_images/banner.png "Banner"
[image10]: ./output_images/Straight_Lines1_Output.jpg "Straight_Lines1_Output"
[image11]: ./output_images/Straight_Lines2_Output.jpg "Straight_Lines2_Output"
[image12]: ./output_images/Test1_Output.jpg "Test1_Output"
[image13]: ./output_images/Test2_Output.jpg "Test2_Output"
[image14]: ./output_images/Test3_Output.jpg "Test3_Output"
[image15]: ./output_images/Test4_Output.jpg "Test4_Output"
[image16]: ./output_images/Test5_Output.jpg "Test5_Output"
[image17]: ./output_images/Test6_Output.jpg "Test6_Output"
[image18]: ./output_images/camera_calibration.png "Camera Calibration"
[image19]: ./output_images/distortion_removal_1.png "Distortion Removal 1"
[image20]: ./output_images/distortion_removal_2.png "Distortion Removal 2"
[image21]: ./output_images/image_channels.png "Image Channels"
[image22]: ./output_images/thresholds.png "Thresholds"
[image23]: ./output_images/pipeline_threshold.png "Pipeline Threshold"
[image24]: ./output_images/perspective.png "Perspecive Transform"
[image25]: ./output_images/advanced_lane_detection.gif "Output"
[image26]: ./output_images/histogram.png "Histogram"
[image27]: ./output_images/sliding_window.png "Sliding Window"
[image28]: ./output_images/individual_outputs.png "Individual Outputs"
[video1]: ./project_video.mp4 "Video"


## Advanced Lane Finding
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

![banner][image10]

### Goal - To identify the road lane boundaries in the video of a camera attached to the car.
---

 **OUTPUT**             
 :-------------------------:|
 ![Advanced Lane Detection][image25] | 
 
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

### CODE 
https://github.com/Mohit-Ak/CarND-Advanced-Lane-Lines/blob/master/advanced_lane_detection.ipynb

### Camera Calibration
#### Code - Section 2 ``` advanced_lane_detection.ipynb```

#### 1. Steps and Inference
- Chessboard doesn't have a depth/height and is fixed on the (x, y) plane at z=0 ### Steps
- Calculate object points and image points.
- "object points", which will be the (x, y, z) coordinates of the chessboard corners in a perfect scenario.
- img_points will be appended with the (x, y) pixel position of each of the corners in the image.
- Note - Chessboard size is 9x6
- Method ``` cv2.findChessboardCorners ``` used to find corners
- Method ```cv2.drawChessboardCorners``` used to draw the corner
- Method ``` cv2.calibrateCamera``` used to calibrate camera
- Around three images fail in calibration.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![Camera Calibration][image18]

### Pipeline (single images)

#### 1. Distortion Removal
#### Code - Section 3 ``` advanced_lane_detection.ipynb```
To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:

 **CHESS BOARD**                     |  **CAR CAM** 
 :-------------------------:|:-------------------------:
 ![Distortion Removal][image19] |  ![Distortion Removal][image20]

#### 2. Image Channels
#### Code - Section 5 ``` advanced_lane_detection.ipynb```
- Tried a number of image channels to see which ones are best suited for lane detection-
![image_channels][image21]

#### 3. Gradient and Color threholds
#### Code - Section 6 ``` advanced_lane_detection.ipynb```
- Experimented different gradient and color threshlolds to detect different color lane lines.
![Thresholding][image22]

- Realized that the following approaches work well.
* Sobel x operator.
* Saturation of HLS channel.
* Hue of HLS channel.

I used a combination of the above mentioned color and gradient thresholds to generate a binary image.  Here's an example of my output for this step.

![pipeline_threshold][image23]


#### 4. Perspective Transform
#### Code - Section 8 ``` advanced_lane_detection.ipynb```
The code for my perspective transform includes a function called `perspective_transform()`,  The `perspective_transform()` function takes as inputs an image (`img`), and also the hardcoded source (`src`) and destination (`dst`) points to calculate the transform.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 170, 720      | 100, 720        | 
| 2550, 460      | 100, 0      |
| 745, 460     | 1100, 0     |
| 1200, 720      | 1100, 720      |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![Perspective Transform][image24]

#### 4. Identified lane-line pixels
#### Code - Section 9 & 10``` advanced_lane_detection.ipynb```

*Steps*
- Take the histogram of the binary thresholded perspetive transformed image. It would give us two spikes for the two lanes present as the total vertical "white" concentration at those regions is higher thatn the rest of the image.
- Use the bottom white points as the starting points and use the sliding window technique to detect the first set of lines.
- Once the first set is detected, we can remember them to concentrate our ROI on the next image which is input instead of doing the histogram again.
- We also remember and average over the past values to remove any camera errors.
- Method ```sliding_window_polynomial_fit``` ultimately indentifies the lane with a 2D polynomial(A Parabola).
- ```polynomialfit_from_previous``` also indentifies the lane with a 2D polynomial(A Parabola).

 **Histogram**                     |  **Sliding Window** 
 :-------------------------:|:-------------------------:
 ![Histogram][image26] |  ![Sliding Window][image27]

#### 5.Radius of curvature of the lane and the position of the vehicle with respect to center.
#### Code - Section 10 ``` advanced_lane_detection.ipynb```
- Method ``` get_curvature ```
-  Define conversions in x and y from pixels space to meters
- 720 px is length of the lane 
- 900 px is the average width of the lane
- *Note* - The image under consideration is perspectively projected.
- *Assumption* : Camera is mounted at the center of the car and therefore ``` vehicle_offset = deviation of midpoint of the lane is = the deviation from center of image ```
- *Reference* : Jeremy shannon's Udacity notes
- The radius of curvature is based upon [this website](http://www.intmath.com/applications-differentiation/8-radius-curvature.php) and calculated-
```
curve_radius = ((1 + (2*fit[0]*y_0*y_meters_per_pixel + fit[1])**2)**1.5) / np.absolute(2*fit[0])
```
In this example, `fit[0]` is the first coefficient (the y-squared coefficient) of the second order polynomial fit, and `fit[1]` is the second (y) coefficient. 
- `y_0` is the y position within the image upon which the curvature calculation is based (the bottom-most y - the position of the car in the image - was chosen). 
- `y_meters_per_pixel` is the factor used for converting from pixels to meters. 


The position of the vehicle with respect to the center of the lane is calculated with the following lines of code:
```
 car_position = width / 2
 lane_center = (left_fitx[719] + right_fitx[719]) / 2
 vehicle_offset = (lane_center-car_position)*xm_per_pix
```

![Color_Fit Lines][image5]

#### 6. RESULTS
#### Code - Section 11 ``` advanced_lane_detection.ipynb```
Here is an example of my result on a test image:

![individual_outputs][image28]

---

### Pipeline (video)

Here's a [link to my video result](./output.mp4)
 
 **OUTPUT**             
 :-------------------------:|
 ![Advanced Lane Detection][image25] | 
---

### Discussion
- There was no direct solution to extract the lanes. I had to try different combinations and thresholds.
- Perspective transform values for SRC and DST had to be hadrcoded and experimented. It would be nice to have that part automated too.
- Considered only for 2 lines. There are cases where roads have markings inbetween which are similar to a line. It will be interesting to tackle those scenarios.
- Challenge video fails as it has 3 lines from histogram peak.
- **SNOW** I am from Buffalo, NY and this is out of experience. When the roads are shoveled after a snow, there are thin lines of snow left inbetween which look very similar to the lanes. The algorithm must identify and distinguish them from the actual lanes.
## Other techniques to experiment
- Smoothing/averaging
- Different Color channels
- Explore different way of removing pixel noise.
- Low pass filtering.
- Laplacian filters
- Convolutions instead of histgram
