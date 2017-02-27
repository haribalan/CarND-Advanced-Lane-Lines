##Detecting Lane Lines by Advanced Method
### Goal of this project is to detect lane lines using advanced techquie such as camera calibration, distortion correction, thresholding using color and gradients.

**Advanced Lane Finding Project**

The steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

###Project Code
The entire project has been coded on the Jupyter notebook, [advanced_lane_finding.ipynb](./advanced_lane_finding.ipynb)
#####Each section is marked with the functionality they perform and those code snippets on jupyter notebook are listed under the same name as here.

###Camera Calibration and Distortion correction
Cameras might create distorted images, say objects near the edges can streched or skewed. This can be due to various reasons such as lens issues. It is important to correct these image distortion, as it can change the apparent size, shape or appearance of an object in an image. Undistorting these images are key for lane finding or any other computer vision based work. Technique used on this project for undistrotion involved learning the distrotion matrix and co-effcients using 15+ chessboard images and open source CV2 library methods. Code is listed in the jupyter nodebook under camera calibration section.

Here is an example distorted image and the undistorted image eqiualent created by  the Camerca Calibration program.

![image1] (./output_images/1dist_undist.png "Undistorted")

![image1b] (./output_images/2dist_undist.png "Undistorted")

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

###Thresholding (Color Transforms and Gradients)
In this step I converted undistorted image to different color spaces and create binary thresholded images which highlight only the lane lines and ignore everything else. I found that S-Channel combined with Sobel X provided a good lane lines identification. This technique worked well with Yellow and white colored lanes. For S-Channel Min and Max thresholded used where 170 and 255 respectively and for Sobel X it was 20, 100 respectively.

![image2] (./output_images/test5_thresh.png "Road Transformed")

###The Birds-eye view
The birds-eye view or the Perspective transform to rectify binary image was the next step.
Two key methos from CV2 library was used to achieve this. 
First method _getPerspectiveTransform_ that takes the source points and distination points. For source points trapezoid like shape on the binary image taken and rectangle shaped co-ordinates for the distination points. This methos outputs the transform matrix that is passed into the second method _warpPerspective_ which takes the original image and performs the actual transform. I have used the INTER_LINEAR interpolation for the warp. I also computed the Inverse perspective transform which will later be used the orignal image.

![image3] (./output_images/test5_warp.png "Binary Example")

###Detect lane pixels and fit lane boundary
Transformed binary image now has the lane pixels highlighted, however, there are still other pixels and noise on the image and also we need a find the lane automatically. To achieve this a sliding histogram was computed to detect clusters of marked pixels. The highest peak of each histogram represent the lanes and space inbetween is the width/inside the lane.   This method holds the discussed implementation _find_lanes_by_histogram_slide_window_ 

With pixels assigned to each lane, a second order polynomials can be fitted. f(y)=Ay^2+By+C . Once the lines has been fitted the next  frames of video just search in a margin around the previous line position, using the method _extract_lane_next_ .
These polynomials were also used to calculate the curvature of the lane. The distance from center was converted from pixels to meters by multiplying the number of pixels by 3.7/700.

![image4] (./output_images/test5_lanefit1.jpg "Warp Example")
![image5] (./output_images/test5_lanefit2.jpg "Fit Visual")

###Pipeline - putting it all together
Final step includes warpping the detected lane boundaries back onto the original image. The 'Inverse perspective transform' computed earlier is used to warp back to orignial image.  I have used the moviepy's VideoFileClip library to call the pipeline on every frame to detect lanes and overlay the detected lanes on top of the original image/video.
![image6] (./output_images/test4_final.png "Output")

Here's a [link to my video result](./project_video.mp4)

The above steps and the videos shows the final result for this submission, however I plan to do further work and tuning on this code. Since during debugging and testing this code against the advanced/harder challenge videos, I observed that with intense color variance on the video due to road color, shadow, near by vehicle, lane paint and deeper curvers; the lane detection and fitting struggles. This result can further be improved with fine tuning thresolding, wrapping and lane fitting.
