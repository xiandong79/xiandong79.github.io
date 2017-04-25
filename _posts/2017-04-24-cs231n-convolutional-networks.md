**ConvNet** architectures make the explicit assumption that the inputs are images, which allows us to encode certain properties into the architecture. 

These then make the forward function more efficient to implement and vastly reduce the amount of parameters in the network(32* 32* 3 = 3072 weights a layer for a regular Neural Network).

The cs231n class is published on [github](http://cs231n.github.io/convolutional-networks/).

## Architecture Overview
For example, the input images in CIFAR-10 are an input volume of activations, and the volume has dimensions 32x32x3 (width, height, depth respectively). As we will soon see, the neurons in a layer will only be connected to a small region of the layer before it, instead of all of the neurons in a fully-connected manner. 

![neural_net2](/downloads/neural_net2.jpeg)

*Left: A regular 3-layer Neural Network. Right: A ConvNet arranges its neurons in three dimensions (width, height, depth), as visualized in one of the layers. Every layer of a ConvNet transforms the 3D input volume to a 3D output volume of neuron activations. In this example, the red input layer holds the image, so its width and height would be the dimensions of the image, and the depth would be 3 (Red, Green, Blue channels).*

>>>A ConvNet is made up of Layers. Every Layer has a simple API: It transforms an input 3D volume to an output 3D volume with some differentiable function that may or may not have parameters.

### ConvNet Layers
Example Architecture: Overview. We will go into more details below, but a simple ConvNet for CIFAR-10 classification could have the architecture [INPUT - CONV - RELU - POOL - FC]. In more detail:

* INPUT [32x32x3] will hold the raw pixel values of the image, in this case an image of width 32, height 32, and with three color channels R,G,B.
* CONV layer will compute the output of neurons that are connected to local regions in the input, each computing a dot product between their weights and a small region they are connected to in the input volume. This may result in volume such as [32x32x12] if we decided to use **12 filters**.
* RELU layer will apply an elementwise activation function, such as the max(0,x) thresholding at zero. This leaves the size of the volume unchanged ([32x32x12]).
* POOL layer will perform a **downsampling operatio**n along the **spatial dimensions (width, height)**, resulting in volume such as [16x16x12].
* FC (i.e. fully-connected) layer will compute the **class scores**, resulting in volume of size [1x1x10], where each of the 10 numbers correspond to a class score, such as among the 10 categories of CIFAR-10. As with ordinary Neural Networks and as the name implies, each neuron in this layer will be connected to all the numbers in the previous volume.


In this way, ConvNets transform the original image layer by layer from the original pixel values to the final class scores. Note that some layers contain parameters and other don’t. In particular, the CONV/FC layers perform transformations that are a function of not only the activations in the input volume, but also of the parameters (the weights and biases of the neurons). On the other hand, the RELU/POOL layers will implement a fixed function. The parameters in the CONV/FC layers will be trained with **gradient descent** so that the class scores that the ConvNet computes are consistent with the labels in the training set for each image.

![convnet](/downloads/convnet.jpeg)

We now describe the individual layers and the details of their hyperparameters and their connectivities.

#### Convolutional Layer


Pooling Layer
Normalization Layer
Fully-Connected Layer
Converting Fully-Connected Layers to Convolutional Layers
ConvNet Architectures
Layer Patterns
Layer Sizing Patterns
Case Studies (LeNet / AlexNet / ZFNet / GoogLeNet / VGGNet)
Computational Considerations
Additional References
