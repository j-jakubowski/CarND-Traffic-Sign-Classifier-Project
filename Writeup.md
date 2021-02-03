# **Traffic Sign Recognition** 

## Writeup

---

**Build a Traffic Sign Recognition Project**

The goals / steps of this project are the following:
* Load the data set (see below for links to the project data set)
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images
* Summarize the results with a written report


[//]: # (Image References)

[Histogram_before]: ./Photos/histogram_before.png "Histogram_before"
[Histogram_after]: ./Photos/histogram_after.png "Histogram_after"
[photo1]: ./Photos/20210110_122908.jpg "Traffic Sign 1"
[photo2]: ./Photos/20210110_123748.jpg "Traffic Sign 2"
[photo3]: ./Photos/20210110_125839.jpg "Traffic Sign 3"
[photo4]: ./Photos/20210110_125910.jpg "Traffic Sign 4"
[photo5]: ./Photos/20210110_130157.jpg "Traffic Sign 5"

## Rubric Points
### Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/481/view) individually and describe how I addressed each point in my implementation.  
---
### Submission files

All needed files are available at https://github.com/j-jakubowski/CarND-Traffic-Sign-Classifier-Project

### Data Set Exploration

As a data set for this project I have used a set supplied by Udacity team available [here]( https://s3-us-west-1.amazonaws.com/udacity-selfdrivingcar/traffic-signs-data.zip).
This data set consists of total 51 839 RGB images (32x32px) of 43 German Traffic Signs images, of which 34799 are prepared as training set, 4410 as validation set and  12 630 as test set.

Basic data set exploration was done using inbuilt functions or using numpy.


Following chart shows distribution of the training data without any preprocessing. It's clear that a lot of classes are undersampled which might influence severly training results.

![Histogram of the raw data set][Histogram_before]

### Design and Test a Model Architecture

My starting point was a LeNet model prepared in previous class, which gave me accuracy of roughly 90% as expected. From there I've started to explore possible improvments. 

I've decided to artificially increase the training set by copying images from undersampled classes and performing randomly one of predefined functions (averaging, gaussian blur, sharpening). I've hoped that modyfing the pictures even a bit would be more beneficial than just having a direct copy as it could help generalise the data.

Here's the distribution of the training data after feeding with additional photos.

![Histogram of the modified data set][Histogram_after]

I've tried converting RGB photos to grayscale, yuv or hsv. I've tried feeding the ML model with just one channel of the following color models or choosing channels from more than one color model. None of the following possibilites have given significant improvement so I've decided to drop this trail and focus on other solutions.

Finally all photo sets are simply preprocessed to achieve zero mean and equal variance.

After all the changes in the preprocessing I've still noticed that validation accuracy was significantly lower than training one which, as I understand correctly, is a sign of overfitting. To counter it I've decided to introduce some changes to the initial model. I've substituted one Max pooling layer with additional Convolution layer and increased filters depth. Both actions increased noticably training accuracy.


### Final ML Model 

My final model consisted of the following layers:

| Layer         		|     Description	        					| 
|:---------------------:|:---------------------------------------------:| 
| Input         		| 32x32x3 RGB image   							| 
| Convolution 5x5     	| 1x1 stride, valid padding, outputs 28x28x12 	|
| RELU					|												|
| Max pooling	      	| 2x2 stride,  outputs 14x14x12   				|
| Convolution 5x5	    | 1x1 stride, valid padding, outputs 10x10x48   |
| RELU					|												|
| Convolution 5x5	    | 1x1 stride, valid padding, outputs 6x6x60     |
| Fully connected		| outputs 120  									|
| RELU					|												|
| Fully connected		| outputs 84  									|
| RELU					|												|
| Fully connected		| outputs 43  									|

 
To train the mode I used the same rate, and batch size as used used in LeNet. I've tried to tweak them a bit but it seemed they already have been in the sweet spot. The only parameter I changed was the number of epochs (increased to 30). Addtionally training is interrupted if validation reaches level 0.96 before 30 epochs.

My final model results were:
* training set accuracy of 0.999
* validation set accuracy of 0.964
* test set accuracy of 0.938
 

### Testing how the Model works on New Images

As it happens I am currently in Germany I used that chance to take pictures of some traffic signs on the streets and see how the model works on the REAL real world samples. Every sign was photographed from two angles to change geometry and lighting a bit. 

![Sign1][photo1] ![Sign2][photo2] ![Sign3][photo3] 
![Sign4][photo4] ![Sign5][photo5]

Some of these signs are not new, they have dents and some stickers on them. I've been really curious how the model would perform on them. 

#### 2. Discuss the model's predictions on these new traffic signs and compare the results to predicting on the test set. At a minimum, discuss what the predictions were, the accuracy on these new predictions, and compare the accuracy to the accuracy on the test set (OPTIONAL: Discuss the results in more detail as described in the "Stand Out Suggestions" part of the rubric).

Here are the results of the prediction:

| Image			        |     Prediction	        					|  Probability | 
|:---------------------:|:---------------------------------------------:|:------------:|
| 30km/h1       		| 30km/h   								     	|      99% 	   |
| 30km/h2       		| 80km/h    									|    87,2%     |
| Turn right1  			| Turn right									|     100% 	   |
| Turn right2  			| Turn right									|     100% 	   |
| Priority road1		| Priority road									|     100% 	   |
| Priority road2  		| Priority road									|     100% 	   |
| Yield1      		    | Yield							 				|     100% 	   |
| Yield2      		    | Yield							 				|     100% 	   |
| Road works1			| Road works        							|     100% 	   |
| Road works2			| Bicycles crossing      						|      99% 	   |


The model was able to correctly guess 8 of the 10 traffic signs, which gives an accuracy of 80%. Generally the model was certain of the classification, even when it misclassified signs. 

Even though Road Works sign is quite distinguishable, the photo taken from acute angle wasn't classified correctly. It seems that training model could contain more images with perspective transformation. Signs 'Speed limit 30km/h' and 'Speed limit 80km/h' are quite similar to each other and I expected here some errors. I believe that the same improvment on trainign set as previously (more perspective transformed photos) could improve the situation.



