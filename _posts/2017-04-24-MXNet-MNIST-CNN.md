In order to get the original data form Inetnet, I searched "sklearn mnist data" where **sklearn/scikit-learn** is a  very powerful machine learning library in Python and linked to [5.9. Downloading datasets from the mldata.org repository](http://scikit-learn.org/stable/datasets/).

For example, to download the MNIST digit recognition database:

```
>>> from sklearn.datasets import fetch_mldata
>>> mnist = fetch_mldata('MNIST original', data_home=custom_data_home)
```
