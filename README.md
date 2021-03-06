## Deep Learning Project ##
---

![following](https://github.com/mahajanrevant/Deep-Learning-Follow-me/blob/master/RoboND-DeepLearning-Project/docs/misc/following.png)

## Setup Instructions
**Clone the repository**
```
$ git clone https://github.com/mahajanrevant/Deep-Learning-Follow-me
```

**Download the data**

Save the following three files into the data folder of the cloned repository. 

[Training Data](https://s3-us-west-1.amazonaws.com/udacity-robotics/Deep+Learning+Data/Lab/train.zip) 

[Validation Data](https://s3-us-west-1.amazonaws.com/udacity-robotics/Deep+Learning+Data/Lab/validation.zip)

[Sample Evaluation Data](https://s3-us-west-1.amazonaws.com/udacity-robotics/Deep+Learning+Data/Project/sample_evaluation_data.zip)

**Download the QuadSim binary**

To interface your neural net with the QuadSim simulator, you must use a version QuadSim that has been custom tailored for this project. The previous version that you might have used for the Controls lab will not work.

The simulator binary can be downloaded [here](https://github.com/udacity/RoboND-DeepLearning/releases/latest)

**Install Dependencies**

You'll need Python 3 and Jupyter Notebooks installed to do this project.  The best way to get setup with these if you are not already is to use Anaconda following along with the [RoboND-Python-Starterkit](https://github.com/udacity/RoboND-Python-StarterKit).

If for some reason you choose not to use Anaconda, you must install the following frameworks and packages on your system:
* Python 3.x
* Tensorflow 1.2.1
* NumPy 1.11
* SciPy 0.17.0
* eventlet 
* Flask
* h5py
* PIL
* python-socketio
* scikit-image
* transforms3d
* PyQt4/Pyqt5

## Contents
* [Introduction](#introduction)
* [Network Architecture](#network-architecture)
* [Hyper Paramters](#hyper-paramters)
* [Tuning](#tuning)
* [Results](#results)
* [Improvements](#improvements)

## Introduction
The goal of this project is to create a FCN to guid the drone to follow the target of interest- a lady. The FCN is created using Keras and Tensorflow in jupyter Notebooks. To speed up training, it is performed over AWS using a custom AMI on a p2.xlarge instance. Aditional data can be collected from the Unity Simulator but is not required for the accuracy goal. The FCN should have a minimum score of *__0.45__*


## Network Architecture

As mentioned above, Convoltional Neural Networks(__CNN__) are suitable to the task of the image detection. Although they can only tell if a particular object in the image is the desired object or not. Modifying CNN a little bit, we can get a Fully COnvolutional Network(__FCN__). FCN performs exceptionally well at the task of _Semantic Segmentation_. FCN is used in this project to achieve the desired goal. The following Architecture is used and explained below:-

```
Input Layer (Unknown,160, 160, 3)-----
Encoder 1 (Unknown,80, 80, 32)------ |
Encoder 2 (Unknown,40, 40, 64) --- | |
Encoder 3 (Unknown,20, 20, 128)  | | |  Skip Connections
1x1 Conv (Unknown,20, 20, 128)   | | |
Decoder 1 (Unknown,40, 40, 128)--- | |
Decoder 2 (Unknown,80, 80, 64)------ |
Decoder 3 (Unknown,80, 80, 64)--------
Output Layer (Unknown,160, 160, 3)
```

The input layer is the size of the input images to be used as the training and usage data. The FCN comprises of three parts- Encoder block, 1X1 Convolution layer, Decoder block.
Each encoder block is made up of bunch of convolutional layers and its job is to identify features. 

The 1X1 convolution layer is used instead of a fully connected layer. A fully connected layer gives a 2D tensor as an output and destroys the spatial information of the encoder output. On the contrary, 1X1 convolutional layer preserves the spatial in formation and passes it on the the decoder blocks.This is quite suitable for the given task as the goal is to perform semenatic segmetnation.

The Decoder blocks upscale the output of the Encoder blocks to the orignal size as the input. This leads to the classification of each pixel and ultimatley segemntation. 

SKip connectins are way of retaining information about the input by using multiple no adjacent layers to make the decisions. As we go deep in the FCN, the focus is more on features rather than the image as a whole. Despite upsampling, not all information is obtained. To improve this, skip connections are implemented.

## Hyper Paramters

* learning_rate: The paramter dictates how much influence does a change have on the current weights of the networks while training. A higher learning rate will lead to big changes in weights while lower learning rate ensures steady changes.
* batch_size: A data set is usually divided in a equal collection called mini-batches in order to save storage memory while training. These batches are fed to the network to train it.  The size of batches is defined by this parameter..
* num_epochs: An epoch is a single forward and backward pass of the whole dataset. This is used to increase the accuracy of the model without requiring more data.
* steps_per_epoch: The number of batch iterations before a training epoch is considered finished.If not defined, trains on all the batches and makes training more compute intensive 
* validation_steps: This is similar to steps_per_epoch. It is the number of batch iterations before a validation epoch is considered finished.

## Tuning

The following values for the above mentioned hyper parameters were used:-

* learning_rate = 0.01
* batch_size = 40
* num_epochs = 110
* steps_per_epoch = int(4131/batch_size)-1
* validation_steps = int(1000/batch_size)-1

![epoch1](https://github.com/mahajanrevant/Deep-Learning-Follow-me/blob/master/RoboND-DeepLearning-Project/docs/misc/epoch1.PNG)

![epoch2](https://github.com/mahajanrevant/Deep-Learning-Follow-me/blob/master/RoboND-DeepLearning-Project/docs/misc/epoch2.PNG)

The recommended learning rate was used to train this model. The idea was to train with a small batch size but multiple times. This is why batch size was 30 while the number of epochs was 110. Previously tried values for the num_epochs were 70,90. The training error starts to diverge around epoch number 120. Although the validation error starts diverging from the training error at around epoch number 30, this was no the case for all tries. Also, validation error stays about the same, although oscillating.Further accuracy could have been gained by using a bigger batch_size. 

## Results

### With Object of Interest
![patrol](https://github.com/mahajanrevant/Deep-Learning-Follow-me/blob/master/RoboND-DeepLearning-Project/docs/misc/target.PNG)

### Without Object of Interest
![no patrol](https://github.com/mahajanrevant/Deep-Learning-Follow-me/blob/master/RoboND-DeepLearning-Project/docs/misc/no_target.PNG)

### Scores for while the quad is following behind the target.
number true positives: 539, number false positives: 0, number false negatives: 0

### Scores for images while the quad is on patrol and the target is not visable
number true positives: 0, number false positives: 42, number false negatives: 0

### This score measures how well the neural network can detect the target from far away
number true positives: 137, number false positives: 2, number false negatives: 164

### Final Score
The validation score obtained was __0.453975601959__ which is greater than the required score.

## Improvements 

* Real life implementation of this project can be done
* More data needs to be collected to improve the score
* Addig more layers to the FCN will also increase the accuracy.
* Inception module should be deployed to get a better score.
* Can be used to track other objects, but will have to be retrained. Also, this case could use transfer learning.

