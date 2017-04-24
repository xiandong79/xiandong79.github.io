Module - MXNet’s high-level interface for neural network training.

In this tutorial, we will use a simple multilayer perception for 10 classes and a synthetic dataset.

## One example

### 1. model
```
import mxnet as mx
from data_iter import SyntheticData

net = mx.sym.Variable('data')
net = mx.sym.FullyConnected(net, name='fc1', num_hidden=64)
net = mx.sym.Activation(net, name='relu1', act_type="relu")
net = mx.sym.FullyConnected(net, name='fc2', num_hidden=10)
net = mx.sym.SoftmaxOutput(net, name='softmax')
# synthetic 10 classes dataset with 128 dimension 
data = SyntheticData(10, 128)
```

I learned the `data_iter` and `SyntheticData` from [mxnet-notebooks/python/basic/data_iter.py](https://github.com/dmlc/mxnet-notebooks/blob/master/python/basic/data_iter.py)

### 2. Module
The most widely used module class is Module, which wraps a Symbol and one or more Executors.

We construct a module by specify:

* symbol : the network Symbol
* context : the device (or a list of devices) for **execution**
* data_names : the list of data variable names
* label_names : the list of label variable names

```
mod = mx.mod.Module(symbol=net, 
                    context=mx.cpu(),
                    data_names=['data'], 
                    label_names=['softmax_label'])
```

### 3. Train, Predict, and Evaluate

```
import logging
logging.basicConfig(level=logging.INFO)

batch_size=32
mod.fit(data.get_iter(batch_size), 
        eval_data=data.get_iter(batch_size),
        optimizer='sgd',
        optimizer_params={'learning_rate':0.1},
        eval_metric='acc',
       num_epoch=5)       
```

To predict with a module, simply call `predict()` with a `DataIter`. It will collect and return all the prediction results.

```
y = mod.predict(data.get_iter(batch_size))
```

If we do not need the prediction outputs, but just need to evaluate on a test set, we can call the `score()` function with a `DataIter` and a `EvalMetric`:

```
mod.score(data.get_iter(batch_size), ['mse', 'acc'])
```

## Module as a computation “machine”

We already seen how to module for basic training and inference. Now we are going to show a more flexiable usage of module.

A module represents a **computation component**. The design purpose of a module is that it abstract a computation “machine”, that accpets Symbol programs and data, and then we can run forward, backward, **update parameters**, etc.

We aim to make the APIs easy and flexible to use, especially in the case when we need to use imperative API to work with multiple modules (e.g. stochastic depth network).

A module has several states:

* Initial state. Memory is not allocated yet, not ready for computation yet.
* Binded. Shapes for inputs, outputs, and parameters are all known, memory allocated, ready for computation.
* Parameter initialized. For modules with parameters, doing computation before initializing the parameters might result in undefined outputs.
* Optimizer installed. An optimizer can be installed to a module. After this, the parameters of the module can be updated according to the optimizer after gradients are computed (forward-backward).