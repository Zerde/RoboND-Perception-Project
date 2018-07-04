## Project: Perception Pick & Place


## [Rubric](https://review.udacity.com/#!/rubrics/1067/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  
[//]: # "Image References"

[image1]: ./img/with_noise.png
[image2]: ./img/noise.png
[image3]: ./img/cluster.png
[image4]: ./img/confusion_matrix.png
[image5]: ./img/objects.png
[image6]: ./img/world1.png
[image7]: ./img/world2.png
[image8]: ./img/world3.png

---

### Exercise 1, 2 and 3 pipeline implemented
#### 1. Complete Exercise 1 steps. Pipeline for filtering and RANSAC plane fitting implemented.
First applied Statistical Outlier Filter to cancel out noises. By tweaking k means and threshold I was able to clean out majority of the noises
![image1]
![image2]

Then I applied Voxel Grid Downsampling setting `LEAF_SIZE = 0.00519`, after that I applied two PathThrough Filters , one by z axis, another one by y axis to cut off the table/box edges. Then I did RANSAC Plane Segmentation and extracted inliers to separate objects and table

#### 2. Complete Exercise 2 steps: Pipeline including clustering for segmentation implemented.  
For the object segmentation Euclidean Clustering was used. I set `cluster tolerance = 0.01, minClustersize = 100, maxClustersize = 3500`. After extracting indices for each cluster and assigning different colors to each cluster new cloud list containing all clusters was created
![image3]
#### 3. Complete Exercise 3 Steps.  Features extracted and SVM trained.  Object recognition implemented.
Features was extracted using `capture_features.py` , used 100 iteration for each object and used hsv when computing color histograms. 
In `train_svm.py` I used linear kernel which gave good(96%-99%) recognition results
![image4]
### Pick and Place Setup

#### 1. For all three tabletop setups (`test*.world`), perform object recognition, then read in respective pick list (`pick_list_*.yaml`). Next construct the messages that would comprise a valid `PickPlace` request output them to `.yaml` format.
For the object recognition looped through each detected cluster and  extracted histogram features and labeled each detected object.

test1.world
![image6]

test2.world
![image7]

test3.world
![image8]

To construct the message in the ` pr2_mover()` function I looped through the pick_list obtained from `pick_list_*.yaml`, and for each object compared  object name to the detected object labels to obtain object centroid coordinates, and object group to the dropbox group to define place pose and used arm, then using all the variables created a list of dictionaries. Finally 
send all output to the output .yaml file using `send_to_yaml()` function.

I spent some time adjusting filter parameters to the project world, canceling noises definitely took longer time since in previous lesson exercises world didn't have any noise. I was able to clear out most of the noise by setting k mean to lower value. Setting leaf size to a lower value also helped with the object recognition, with the higher leaf size all the objects had the same label. To train the SVM I used list of object from third yaml file, as it contained all the objects that are in other test worlds. Initially I used rbf kernel as it gave higher percentage on the confusion matrix when I did exercise 3, but in the project rbf kernel wasn't able to differentiate book, biscuits and snacks. Increasing the iteration amount for each object didn't help much, and I tried to add rgb color histogram to the feature and setting leaf size even lower, but result didn't change. Then I switched rbf kernel to linear kernel, and was able to recognize objects for all three setup.
If I were to pursue this project further I would increase the iteration amount for each object and play with some svm kernel parameters. And will try to implement robot motion for picking up objects and placing to the right box, and maybe try to place objects in different places, adding more boxes.  



