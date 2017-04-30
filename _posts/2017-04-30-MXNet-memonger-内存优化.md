别人训练好的模型我们常常不知道有哪些层，这时候需要列出所有的层，以便于我们找到特征层：
```
internals=model.symbol.get_internals()   #list all symbol
internals.list_outputs()
1
2
1
2
```
列出网络中所有的层，像这样：
```
['data',
 'conv1_weight',
 'conv1_bias',
 'conv1_output',
 'slice1_output0',
 'slice1_output1',
 '_maximum0_output',
 ……
 ……
 'slice_fc1_output0',
 'slice_fc1_output1',
 '_maximum9_output',
 'drop1_output',
 'fc2_weight',
 'fc2_bias',
 'fc2_output',
 'softmax_label',
 'softmax_output']
 ``` 
 
 
 在下面，我们将推断所有的需要作为输入数据的模型的参数
 
``` 
>>> net = mx.symbol.Variable('data')
>>> net = mx.symbol.FullyConnected(data=net, name='fc1', num_hidden=10)
>>> arg_shape, out_shape, aux_shape = net.infer_shape(data=(100, 100))
>>> dict(zip(net.list_arguments(), arg_shape))
{'data': (100, 100), 'fc1_weight': (10, 100), 'fc1_bias': (10,)}
>>> out_shape
[(100, 10)]
```

我们可以看一下net.infer_shape函数的功能

**infer_shape**(self, *args, **kwargs) method of mxnet.symbol.Symbol instance
    Infer the shape of outputs and arguments of given known shapes of arguments.
    
    User can either pass in the known shapes in positional way or keyword argument way.
    Tuple of Nones is returned if there is not enough information passed in.
    An error will be raised if there is inconsistency found in the known shapes passed in.
    
    Parameters
    ----------
    *args :
        Provide shape of arguments in a positional way.
        Unknown shape can be marked as None
    
    **kwargs :
        Provide keyword arguments of known shapes.
    
    Returns
    -------
    arg_shapes : list of tuple or None
    
这个模型推断将被用来作为一个调试机制来检测模型的不一致。



#### **enumerate()** function

* enumerate()是python的内置函数
* enumerate在字典上是**枚举、列举**的意思
* 对于一个可迭代的（iterable）/可遍历的对象（如列表、字符串），enumerate将其组成一个索引序列，利用它可以同时获得索引和值
* enumerate多用于在for循环中得到计数

如果对一个列表，既要遍历索引又要遍历元素时，首先可以这样写：

```
list1 = ["这", "是", "一个", "测试"]
for i in range (len(list1)):
    print i ,list1[i]
1
2
3
1
2
3
```
上述方法有些累赘，利用enumerate()会更加直接和优美：

```
list1 = ["这", "是", "一个", "测试"]
for index, item in enumerate(list1):
    print index, item
>>>
0 这
1 是
2 一个
3 测试
```

#### **simple_bind** function
**simple_bind**(ctx, grad_req='write', type_dict=None, group2ctx=None, **kwargs)¶

Bind current symbol to get an executor, allocate all the ndarrays needed. Allows specifying data types.

This function will ask user to pass in an NDArray of position they like to bind to, and it will automatically allocate the ndarray for arguments and auxiliary states that user did not specify explicitly.

Parameters:	

* ctx (Context) – The device context the generated executor to run on.
* grad_req (string) – {‘write’, ‘add’, ‘null’}, or list of str or dict of str to str, optional Specifies how we should update the gradient to the args_grad. - ‘write’ means everytime gradient is write to specified args_grad NDArray. - ‘add’ means everytime gradient is add to the specified NDArray. - ‘null’ means no action is taken, the gradient may not be calculated.
* type_dict (dict of str->numpy.dtype) – Input type dictionary, name->dtype
* group2ctx (dict of string to mx.Context) – The dict mapping the ctx_group attribute to the context assignment.
* kwargs (dict of str->shape) – Input shape dictionary, name->shape

Returns:	
executor – The generated Executor

Return type:	
mxnet.Executor

```
# allocate memory by Simplebind in net
exe = net.simple_bind(ctx=mx.cpu(), **input_info)

#combile network arguments and simple_bind allocate array
exe_info = dict(zip(net.list_arguments(), exe.arg_arrays))

```

#### **debug_str()** function
Gets a debug string.

Returns:	debug_str – Debug string of the symbol.
Return type:	string

## [Resnet](https://github.com/KaimingHe/deep-residual-networks) example & analysis

### Core code: 

```
layers = [3, 24, 36, 3]
# Get a 4-stage residual net, with configurations specified as layers.

batch_size = 32
net = get_symbol(layers)
dshape = (batch_size, 3, 224, 224)
net_mem_planned = memonger.search_plan(net, data=dshape)
old_cost = memonger.get_cost(net, data=dshape)
new_cost = memonger.get_cost(net_mem_planned, data=dshape)

print('Old feature map cost=%d MB' % old_cost)
print('New feature map cost=%d MB' % new_cost)
# You can savely feed the net to the subsequent mxnet training script.
```


### `Resnet` Architrcture
![resnet-architrcture](/downloads/resnet-architrcture.png)

### Reuslt：

![mem-result](/downloads/mem-result.png)
