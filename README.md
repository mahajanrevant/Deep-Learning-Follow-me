## Deep Learning Project ##
---

In this project, you will train a deep neural network to identify and track a target in simulation. So-called “follow me” applications like this are key to many fields of robotics and the very same techniques you apply here could be extended to scenarios like advanced cruise control in autonomous vehicles or human-robot collaboration in industry.

[image_0]: ./docs/misc/sim_screenshot.png
![alt text][image_0] 

## Setup Instructions
**Clone the repository**
```
$ git clone https://github.com/udacity/RoboND-DeepLearning.git
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

## Implementation of the Project

1. Implemented the solution in model_training.ipynb using Keras
2. Trained the network on AWS
3. Continued to experiment with the training data and network until attained a score of *__0.4__*.

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

## Hyper Paramteres

* batch_size: number of training samples/images that get propagated through the network in a single pass.
* num_epochs: number of times the entire training dataset gets propagated through the network.
* steps_per_epoch: number of batches of training images that go through the network in 1 epoch.Recommended value to try would be based on the total number of images in training dataset divided by the batch_size.
* validation_steps: number of batches of validation images that go through the network in 1 epoch. This is similar to steps_per_epoch, except validation_steps is for the validation dataset. We have provided you with a default value for this as well.
* workers: maximum number of processes to spin up. This can affect your training speed and is dependent on your hardware. We have provided a recommended value to work with.

## Tuning

The following values for the above mentioned hyper parameters were used:-

* learning_rate = 0.01
* batch_size = 40
* num_epochs = 110
* steps_per_epoch = int(4131/batch_size)-1
* validation_steps = int(1000/batch_size)-1



The recommended learning rate was used to train this model. The idea was to train with a small batch size but multiple times. This is why batch size was 30 while the number of epochs was 110. Previously tried values for the num_epochs were 70,90. The training error starts to diverge around epoch number 120. Although the validation error starts diverging from the training error at around epoch number 30, this was no the case for all tries. Also, validation error stays about the same, although oscillating.Further accuracy could have been gained by using a bigger batch_size. 

## Results



The validation score obtained was __0.453975601959__ which is above the required goal.

## Improvements 

