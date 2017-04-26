In this section we will introduce the Image Classification problem, which is the task of assigning an input image one label from a fixed set of categories. This is one of the core problems in **Computer Vision** that, despite its simplicity, has a large variety of practical applications. Moreover, as we will see later in the course, many other seemingly distinct Computer Vision tasks (such as object detection, segmentation) can be reduced to image classification.


## Intro to Image Classification, data-driven approach, pipeline
The image classification pipeline. We’ve seen that the task in Image Classification is to take **an array of pixels** that represents a single image and assign a label to it. Our complete pipeline can be formalized as follows:

* Input: Our input consists of a set of N images, each labeled with one of K different classes. We refer to this data as the training set.
* Learning: Our task is to use the training set to learn what every one of the classes looks like. We refer to this step as training a classifier, or **learning a model**.
* Evaluation: In the end, we evaluate the quality of the classifier by asking it to predict labels for a new set of images that it has never seen before. We will then compare the true labels of these images to the ones predicted by the classifier. Intuitively, we’re hoping that a lot of the predictions match up with the true answers (which we call the ground truth).

## Nearest Neighbor Classifier
You may have noticed that we left unspecified the details of exactly how we compare two images, which in this case are just two blocks of 32 x 32 x 3. One of the simplest possibilities is to compare the images pixel by pixel and add up all the differences. In other words, given two images and representing them as vectors I1,I2, a reasonable choice for comparing them might be the L1 distance:
 
<math xmlns="http://www.w3.org/1998/Math/MathML" display="block">
  <msub>
    <mi>d</mi>
    <mn>1</mn>
  </msub>
  <mo stretchy="false">(</mo>
  <msub>
    <mi>I</mi>
    <mn>1</mn>
  </msub>
  <mo>,</mo>
  <msub>
    <mi>I</mi>
    <mn>2</mn>
  </msub>
  <mo stretchy="false">)</mo>
  <mo>=</mo>
  <munder>
    <mo>&#x2211;<!-- ∑ --></mo>
    <mrow class="MJX-TeXAtom-ORD">
      <mi>p</mi>
    </mrow>
  </munder>
  <mrow>
    <mo>|</mo>
    <mrow>
      <msubsup>
        <mi>I</mi>
        <mn>1</mn>
        <mi>p</mi>
      </msubsup>
      <mo>&#x2212;<!-- − --></mo>
      <msubsup>
        <mi>I</mi>
        <mn>2</mn>
        <mi>p</mi>
      </msubsup>
    </mrow>
    <mo>|</mo>
  </mrow>
</math>

Here is an implementation of a simple Nearest Neighbor classifier with the L1 distance that satisfies this template:

## k-Nearest Neighbor
 The idea is very simple: instead of finding the single closest image in the training set, we will find **the top k closest images**, and have them vote on the label of the test image. In particular, when k = 1, we recover the Nearest Neighbor classifier. Intuitively, higher values of k have a smoothing effect that makes the classifier more resistant to outliers:
 

## Summary


* We introduced a simple classifier called the Nearest Neighbor classifier. We saw that there are multiple **hyper-parameters** (such as value of k, or the type of distance used to compare examples) that are associated with this classifier and that there was no obvious way of choosing them.
* We saw that the correct way to set these hyperparameters is to split your training data into two: a training set and a fake test set, which we call **validation set**. We **try different** hyperparameter values and keep the values that lead to the best performance on the validation set.
* If the lack of training data is a concern, we discussed a procedure called **cross-validation**, which can help reduce noise in estimating which hyperparameters work best.

* We saw that Nearest Neighbor can get us about 40% accuracy on CIFAR-10. It is simple to implement but requires us to **store** the **entire training set **and it is expensive to evaluate on a test image.
* Finally, we saw that the use of L1 or L2 distances on raw pixel values is not adequate since the **distances correlate** more strongly with backgrounds and color distributions of images than with their semantic content.

## Summary: Applying kNN in practice
## Further Reading