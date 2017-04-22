This is my ongoing MXNet learning record.

## [MXNet Logistic Regression Example](https://github.com/xiandong79/aws-summit-2017-seoul/blob/master/mxnet-logistic_regression_diabetes.ipynb)

We use 8 features to predict whether the patient has diabetes or not.

### load data
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

### build model


