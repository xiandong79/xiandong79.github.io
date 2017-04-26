Visualizing what ConvNets learn.

Several approaches for understanding and visualizing Convolutional Networks have been developed in the literature, partly as a response the **common criticism** that the learned features in a Neural Network are not interpretable. In this section we briefly survey some of these approaches and related work.

## Visualizing the activations and first-layer weights
**Layer Activations**. The most straight-forward visualization technique is to show the activations of the network during the forward pass. For ReLU networks, the activations usually start out looking relatively blobby and dense, but as the training progresses the activations usually become more sparse and localized. One dangerous pitfall that can be easily noticed with this visualization is that *some activation maps may be all zero* for many different inputs, which can indicate dead filters, and can be a symptom of high learning rates.

![act1](/downloads/act1.jpeg)![act2](/downloads/act2.jpeg)

**Conv/FC Filters**. The second common strategy is to visualize **the weights**. These are usually most interpretable on the first CONV layer which is looking directly at the raw pixel data, but it is possible to also show the filter weights deeper in the network. The weights are useful to visualize because well-trained networks usually *display nice and smooth filters* without any noisy patterns. Noisy patterns can be an indicator of a network that hasn’t been trained for long enough, or possibly a very low regularization strength that may have led to overfitting.