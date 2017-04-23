We use 8 features to predict whether the patient has diabetes or not. The source code is [here](https://github.com/xiandong79/aws-summit-2017-seoul/blob/master/mxnet-logistic_regression_diabetes.ipynb).

### 1. Load data
when I first find that the data is stored in a .csv file. I searched "python load csv file" and "numpy load csv file". After reading the first few links...

Method 1 - **pandas.read_csv**

Robust IO tools for loading data from flat files (CSV and delimited), Excel files, databases, and saving / loading data from the ultrafast HDF5 format.

```
import pandas as pd
df=pd.read_csv('myfile.csv', sep=',',header=None)
df.values
array([[ 1. ,  2. ,  3. ],
       [ 4. ,  5.5,  6. ]])
```



Method 2 - **numpy.loadtxt**

```
>>> c = StringIO("1,0,2\n3,0,4")
>>> x, y = np.loadtxt(c, delimiter=',', usecols=(0, 2), unpack=True)
>>> x
array([ 1.,  3.])
>>> y
array([ 2.,  4.])
```

Method 3 - **numpy.genfromtxt**

```
>>> from StringIO import StringIO
>>> test = "a,1,2\nb,3,4"
>>> a = np.genfromtxt(StringIO(test), delimiter=",", dtype=None)
>>> print a
array([('a',1,2),('b',3,4)], dtype=[('f0', '|S1'),('f1', '<i8'),('f2', '<i8')])
```
It seems that the original author selected the second method. Next, we need to slice the original data into two sets:  training data, testing data.

```
xy = np.loadtxt(os.path.join('data-03-diabetes.csv'), delimiter=',', dtype=np.float32)
```

### 2. Build model

Our purpose is to do linear regression on the date sample so I searched "mxnet linear regression". I found something relevant on the [official website of MXNet](http://mxnet.io/api/python/symbol.html).

- **FullyConnected**. 

Apply a linear transformation: Y=XWT+b

- **LinearRegressionOutput**. 

Use linear regression for final output, this is used on final output of a net.

Here is the code:

```
data = mx.sym.Variable("data")
target = mx.sym.Variable("target")
fc = mx.sym.FullyConnected(data=data, num_hidden=1, name='fc')
pred = mx.sym.LogisticRegressionOutput(data=fc, label=target)
```

### 3. Construct the Module
Next, I was confused by the **Construct the Module** block. And.. We will construct the Module object based on the symbol. Module will be used **for training and testing**. I went to the [Module API of MXNet](http://mxnet.io/api/python/module.html).

#### Module API - Overview

The module API, defined in the module (or simply mod) package, provides **an intermediate and high-level interface** for performing computation with a Symbol. One can roughly think a module is a machine which can execute a program defined by a Symbol.

The class `module.Module` is a commonly used module, which accepts a `Symbol` as the input. For instance,

```
mod = mx.mod.Module(out)  # create a module by given a Symbol.
mod.bind() # Bind the symbols to construct executors.
mod.init_params()	# Initialize the parameters and auxiliary states.
mod.init_optimizer()	# Install and initialize optimizers.
```

Until now, we construct a execution flow, init its parametes and needed data. Next, we begin to fit the data and train the model.

### 4. Training 
```
# First constructing the training iterator and then fit the model
train_iter = mx.io.NDArrayIter(train_x_data, train_y_data, batch_size, shuffle=True, label_name='target')
metric = mx.metric.CustomMetric(feval=lambda labels, pred: ((pred > 0.5) == labels).mean(),
                                name="acc")
net.fit(train_data=train_iter, eval_metric=metric, num_epoch=15)
```
This is the first time that I know we can construct a `train_iter = mx.io.NDArrayIter()`. Instead, I might choose the `mod.fit()` and define every parameter if needed.

### 5. Testing
From the Module API, we can use the following function to test the final module and get a accuracy of our prediction.

```
mod.predict(new_data)  # predict on new data.
mod.score(val_dataiter, metric)
```
 
### My final code:

```

```



