
The code of this work is on [github](https://github.com/xiandong79/aws-summit-2017-seoul/blob/master/mxnet-mnist_deep_cnn.ipynb). About how we design the "hyper parameters" is from the [CS231 class](http://cs231n.github.io/convolutional-networks/)

In order to get the original data form Inetnet, I searched "sklearn mnist data" where **sklearn/scikit-learn** is a  very powerful machine learning library in Python and linked to [5.9. Downloading datasets from the mldata.org repository](http://scikit-learn.org/stable/datasets/). 

### 0. XXX.random.seed(77)

np.random.seed(0) makes the [random numbers predictable](http://stackoverflow.com/questions/21494489/what-does-numpy-random-seed0-do)

```
>> numpy.random.seed(0) ; numpy.random.rand(4)
array([ 0.55,  0.72,  0.6 ,  0.54])
>> numpy.random.seed(0) ; numpy.random.rand(4)
array([ 0.55,  0.72,  0.6 ,  0.54])
```
In mxnet, this seed will affect behavior of functions in this module. It also affects the results from executors that contain random numbers such as dropout operators.

```
mxnet.random.seed(seed_state)
Seed the random number generators in MXNet.

```


## 1. Load the data
Load the MNIST dataset. We use 55000 images for training, 5000 images for validation and 10000 images for testing.

For example, to download the MNIST digit recognition database:

```
>>> from sklearn.datasets import fetch_mldata
>>> mnist = fetch_mldata('MNIST original')
```

### hyper parameters
 - learning_rate = 0.001
 - training_epochs = 15
 - batch_size = 100
 - drop_out_prob = 0.3  # The keep probability is 0.7


```
z = np.array([[1, 2, 3, 4],
         [5, 6, 7, 8],
         [9, 10, 11, 12]])
z.reshape(-1)
array([ 1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12])
z.reshape(-1,1)
array([[ 1],
   [ 2],
   [ 3],
   [ 4],
   [ 5],
   [ 6],
   [ 7],
   [ 8],
   [ 9],
   [10],
   [11],
   [12]])
We have provided column as 1 but rows as unknown (indicated by -1).
```

## 2. build the model
Next we will build the symbol, which is used to determine the data flow.

#### [Convolution layer](http://mxnet.io/api/python/symbol.html#mxnet.symbol.Convolution)

```
mxnet.symbol.Convolution(*args, **kwargs)
Compute N-D convolution on (N+2)-D input.
```

#### [Activation layer](http://mxnet.io/api/python/symbol.html#mxnet.symbol.Activation)

```
mxnet.symbol.Activation(*args, **kwargs)
Elementwise activation function. The activation operations are applied elementwisely to each array elements. The following types are supported:

Parameters:
data = a symbol
act_type = 'relu', 'sigmoid', 'softrelu', 'tanh', required, is Activation function to be applied.
```

### l3\_flat = mx.sym.flatten(l3_pool)
Flattens the input array into a 2-D array by collapsing the higher dimensions.


### num_filter=32, 64, 128
```
l4_fc = mx.sym.FullyConnected(data=l3_drop, num_hidden=625, name='l4_fc')
```
According to the gudience of Zhihan and Xingjian, the num_filter represents the num of different features (feature map). Usually, it is a multiple of 16, e.g. 32, 64...

### num_hidden=10 

```
logits = mx.sym.FullyConnected(data=l4, num_hidden=10, name='logits')
```
because there are 10 different classes in the MNIST.


## 3. Construct the Module
We will construct the Module object based on the symbol. Module will be used for training and testing.

Also, the testing executor will try to **reuse the allocated memory** space of the training executor.

```
test_mod = mx.mod.Module(symbol=logits,
                         data_names=[data_desc.name],
                         label_names=None,
                         context=mx.cpu())
     
test_mod.bind(data_shapes=[data_desc],
              label_shapes=None,
              for_training=False,
              grad_req='null',
              shared_module=mod)                         
```
Setting the `shared_module` to ensure that the test network shares the same parameters and allocated memory of the training network

## 4. Training
Now,we can fit the training set now. The only thing I should mention is the use of `numpy.take()`.

```
numpy.take(a, indices, axis=None, out=None, mode='raise')
Take elements from an array along an axis.
>>> a = [4, 3, 5, 7, 6, 8]
>>> indices = [0, 1, 4]
>>> np.take(a, indices)
array([4, 3, 6])
```

## 5. Testing
Let's test the model on the test set.


## 6. Get one and predict
We can predict the label of a single sample

```
test_mod.reshape(data_shapes=[mx.io.DataDesc(name='data', shape=(1, 1, 28, 28), layout='NCHW')],
                 label_shapes=None)
r = np.random.randint(0, X_test.shape[0])
test_mod.forward(data_batch=mx.io.DataBatch(data=[nd.array(X_test[r:r+1])],
                                            label=None))
logits_nd = test_mod.get_outputs()[0]
print("Label: ", int(y_test[r]))
print("Prediction: ", int(nd.argmax(logits_nd, axis=1).asnumpy()[0]))
```
