
## Writeup Template
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/car_image.png
[image11]: ./output_images/notcar_image.png
[image2]: ./output_images/HOG_Visualization.png
[image3]: ./output_images/window_search.jpg
[image4]: ./output_images/window_example1.jpg
[image5]: ./output_images/Heat_examples.png
[video1]: ./vehicle_detection.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the second code cell of the IPython notebook.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]![alt text][image11]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and evetually chose the following:

color_space = YCrCb, as it had the highest accuracy, and more consistency between different runs than HSV and LUV (the other two high accuracy performers)

orient = 9, insignificant differences from other values between 6 and 12

pix_per_cell = 8, although computationally more demanding that 16, the accuracy and final performance gains justified the gain in computational time

cell_per_block = 2, the best compromise between feature length, accuracy and computational time

hog_channel = 'ALL', signifcantlly better performance than single channels

spatial size = (32, 32), increased performance in final results

hist bins = 32, higher accuracy and feature vector length matching

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

In cell no.7, I trained a linear SVM using combined and normalized features(HOG, spatial and histogram), that were split into training and testing datasets, then shuffled. The training data was fed into sklearn's LinearSVC to train the classifier.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

In cell no.9, I implemented a discrete sliding window search. The implementation in cell no.11, defines 64x64, 96x96 and 128x128 search window sizes and their respective start stop coordinates. This produces an image with a search rea grid like this:

![alt text][image3]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on these three window sizes using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image4]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from the test images, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the image:

### Here are the test images and their corresponding heatmaps:

![alt text][image5]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Biggest issues faced were extremely slow computational times relative to the online virtual machine of udacity. This could be partly due to personal hardware being utilized. However, despite the dataset and feature vector length being significantly larger, the gain in computational time is not proportionate. A possible remedy would be to use HOG sub-sampling to extract features only once.

The second issue is the complex interactions betweeen the tuning parameters and the inconsistency of the classifier's accuracy without a change in variables. This could mean that the parameter values chosen are not ideal for performance speed. Some features, like colorspace, affected the speed significantly less. However, features like spatial binning and color histograms had a larger effect on speed, and relatively noticeable effect on accuracy. Despite that, ignoring the spatial binning and color histograms, although decreasing accuracy by less than 5%, the final performance of detection on the video stream is signficantly reduced.

Possible failure scenarios:
1. High vehicle density in the field of vision could result in encompassing several vehicles in the same bounding box, providing false information about the size of the vehicles surrounding.
2. Due to the slow processing speed (possibly due to hardware), it would be possibly unfeasible to implement.

Possible improvements:
1. More discrete definition of bounding boxes and sliding window search in order to distinguish between multiple vehicles.
2.Cross check performance of pipeline on representative hardware, and tune parameters accordingly for best speed/performance figures.



##### References:
1.Udacity lessons and code for functions

2.priya-dwivedi to ensure code functionality and output 
