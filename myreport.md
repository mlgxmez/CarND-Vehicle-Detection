**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/raw_images.png
[image5]: ./output_images/bbox1.png
[image6]: ./output_images/bbox2.png
[image7]: ./output_images/bbox3.png
[image2]: ./output_images/heatmap1.png
[image3]: ./output_images/heatmap2.png
[image4]: ./output_images/heatmap3.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points

---

1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

Before diving deeply into the report, just to mention that there exists two Python notebooks. One addresses the problem of car detection, the code where an SVM classifier is trained with image patches, whether they correspond to a car or anything different than a car. The other notebook loads the classifier and makes the prediction. The pipeline is implemented in this last notebook too.

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

For the HOG features we defined 9 different orientations, 8 pixels per cell and 2 cells per block. 

The code for this step is contained in the 6th code cell of the IPython notebook `Build_classifier.ipynb`.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then selected the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`. 

#### 2. Explain how you settled on your final choice of HOG parameters.

The choice of these parameters followed the guidelines of the lectures.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

For vehicle detection, we built an SVM classifier (it is found the notebook named Build_classifier.ipynb). Since the code runs in a laptop, we decided to train the SVM with a smaller set of patches (around 7000 of them).  The 80% of those patches are used for training and the remaining 20% is used for validation. These batches change since cross-validations has also been applied. A linear kernel has been selected and we test the model for different values of the C parameter. The model with highest score is the one with C=1.0. 

The accuracy of the model is close to 99.3% whereas the accuracy for the patches not used for training is close to 99.0%
### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

In the following section we bounded the search region to the lower part of the image and applied a sliding window with a scale of 1.5. As the window moves across the image, features are extracted from the pixels covered by the window and the classifier determines if that subimage corresponds to a car.

Taking into account the distance between the camera and the surrounding cars gives you the intuition and the overlap between windows. The overlap of pixels must be subtle in order to detect smoothly the car in all the frames of the video.

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images (with the heatmap, explained in the next section):

![alt text][image2]
![alt text][image3]
![alt text][image4]

---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)

Here's a [link to my video result](./project_video_output.mp4)

#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result of the resulting bounding boxes from test images, the result of `scipy.ndimage.measurements.label()`. A similar result can be seen in the output video.

![alt text][image5]
![alt text][image6]
![alt text][image7]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?
* I started using the `rbf` kernel for a long time and I could only reach a 50% accuracy of the model.
* A very important factor is to use as many samples and features as you can for the SVM.
* Recommended not to use dimensionality reduction techniques.
* Copy the image processing steps for both training the model and for prediction.

In terms of speed, this pipeline is quite low. So it will fail in a real situation, the car will not be able to react on time.
 

