Vehicle Detection and Tracking

The goals project is to detect vehicles on a video frame using computer vision and machine learning methodology. Techniques used for classification was LinearSVC model and for feature extraction a combination of HOG technique, color histogram, spatial bindings.

Using sliding window technique, I extracted imaged patches from the frames. These windows were then passed for feature extraction. The generated features were used as the input for the LinearSVC model. The output of the SVM model indicates whether the given window is a vehicle or not. The result was further processed by removing duplicate detections and false positives, bounding boxes were drawn to indicate vehicles.

### Main Code:

### The code for this project is in the &quot;vehicle\_detection\_training.ipynb&quot;

##### Each section is marked with the functionality they perform and those code snippets on jupyter notebook are listed under the same name as here.

### Data set exploratory Images

### For this project, I was provided with a labeled dataset of vehicle and non-vehicle examples to train my classifier. These example images come from a combination of the GTI vehicle image database, the KITTI vision benchmark suite, and examples extracted from the project video itself. There set was almost evenly balanced between cars and non-cars. The images were provided as .png files.

### Total number of images labeled as Cars: 8792

### Total number of images labeled as Non-Cars: 8968

### Here are some sample images:

###

![]IAMGE

I used the _glob_ module to import images into my workspace. Code for this can be found under the &quot;Exploratory data analysis and Loading the data&quot; section

### **Sliding Windows for Identifying Vehicles**

Vehicle detection pipeline starts with the sliding window stage. In this stage, we convolved a small window to extract image patches. The width and hight of the window and the overlap between two windows are parameters of our sliding window algorithm.

Normally, people use different window sizes and overlap in order to increase the detection accuracy. In the initial stage of the project, We also tried several window sizes, overlaps, and search areas. But, finally, we picked the following window size, overlap, and search areas which perform better on images and videos.

Sliding window was focused only to y\_axis at with min,max at [400,600]

xy\_window=(96, 85)#xy\_window=(96, 96), xy\_overlap=(0.5, 0.5)

xy\_overlap=(0.75, 0.75)

1. xy\_window = (96, 96)
2. xy\_overlap = (0.75, 0.75)
3. y\_start\_stop = [400, 600]
4. x\_start\_stop = [None, None]

Following image shows the locations of the search windows used by our detection system.

&lt;IMAGE OF GRIDED HERE&gt;

### Code location

### **Feature Extraction**

### For the HOG feature extraction I started trying out RGB, Gray, HLS color spaces to get better gradients and finally got better results with YCrCb and LUV was also better. I also played by visualizing images with different HOG params settings namely the orienatations, pixels per cell and the cell per blocks. Code block &quot;Training and Tweaks&quot; has the parameter values for HOG and implementation can be found &quot; Feature Extractions and Sliding window&quot;

### orient = 9  # HOG orientations

### pix\_per\_cell = 8 # HOG pixels per cell

### cell\_per\_block = 2 # HOG cells per block

### hog\_channel = &#39;ALL&#39; # Can be 0, 1, 2, or &quot;ALL&quot;

###

![](data:image/*;base6

Training the Machine Learning Model
Training the Machine Learning Model

For faster classification and more accurate prediction LinearSVC was selected for this project. (Good inputs from the class as well on this model).

Code for the training can be found under &quot;Training and Tweaks&quot;

Data was divided into Training and test set. 20% of the data was used for testing. Data was split and shuffled using the sklearn.model\_selection packages module train\_test\_split. Training data was mixed with Car and non-car images and their respective labels (features). Test accuracy was high:

Using: 9 orientations 8 pixels per cell and 2 cells per block

Feature vector length: 8460

7.79 Seconds to train SVC...

Test Accuracy of SVC =  0.9901

For the ML and proper detection (tested against a cut sample video), following hyper parameters were tweaked to get best results: Code under &quot;Training and Tweaks&quot;

color\_space was set to &#39;YCrCb&#39; # others RGB, HSV, LUV, HLS, YUV

Hog orientations at 9  # HOG orientations

HOG pixels per cell was set at 8

HOG cells per block

hog\_channel was used as  &#39;ALL&#39;

spatial\_size was set at (32, 32) # Spatial binning dimensions

Number of histogram bins was 32

### Handling False Positives and Multiple Detectors

Once we have above pipeline stages were ready, we created a vehicle detector using those pipeline stages. However, it gave us a lot of duplicate and false positive detections.

Images from Hot Maps and overlays

Next,  **heat-maps**  are used to remove both false positives and multiple detectors. Heat-map work as follows.

1. Initialize heat-map image with dimension equals to the dimension of the input images.
2. Add &quot;heat&quot; (+=1) for all pixels within windows where a positive detection is reported by your classifier.
3. The hot parts of the map are where the cars are, and by imposing a threshold, I rejected areas affected by false positives.

The **add\_heat(heatmap, bbox\_list)** and **apply\_threshold(heatmap, threshold)** methods in the  **helper**  file encapsulate the functionalities described in above three items. Based on the output of the **apply\_threshold(heatmap, threshold)** methods, we draw bounding boxes around each detected cars.

In addition to **add\_heat(heatmap, bbox\_list)** and **apply\_threshold(heatmap, threshold)** we created a separate class call  **FrameQueue**  in the  **vehicle**  file in order to improve the smoothness of the predicted bounding boxes.  **FrameQueue**  stores last N (configurable) number of heat-maps. When it comes to predicting the thresholded heat-map, we calculated the sum of last N heat-maps and that calculated heat-map passed to the **apply\_threshold(heatmap, threshold)** method.

Talk about the DEQUEUE thing

### Code location

Final output from the pipe line image:

IMAGE here, FEW IMAGES



## Conclusions and Future Improvements

In this project, we studied on of the key components of autonomous vehicles called  **Detection and Tracking**. One of the main objects of this project is to familiarize ourself with traditional computer vision and machine learning techniques. Therefore, we used HOG and special binnings for feature extraction. In addition to that Linear SVM classifier was used for object detection.

Though vehicle detection works well, the key drawback of this system is its performance. The number of frames processed per second was very low. Hence, it is very difficult it uses it for real-time applications. Therefore, I would like to propose following enhancements.

1. In order to identify performance bottlenecks of the system, I ran a debug session. According to its result, **hog()** (of skimage.feature package) method consumes the bulk of the CPU power. Hence, in the future, I&#39;m planning to use an alternative HOG calculator.
2. Recently, new deep learning based method such ash  [YOLO](https://pjreddie.com/darknet/yolo/) and  [SSD](https://arxiv.org/abs/1512.02325) have been recorded very good performance on  **Detection and Tracking**  benchmark datasets. Hence, in the future, I&#39;m planning to evaluate those new deep learning based system for vehicle tracking.
3. Additionally, in the future, I would like to improve this system for detecting other objects such as road signs and pedestrians.
