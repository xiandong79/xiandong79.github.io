We use 8 features to predict whether the patient has diabetes or not by a 3 fully connected layer network.

#### mx.bind() in Module API
```
bind(data_shapes, label_shapes=None, for_training=True, inputs_need_grad=False, force_rebind=False, shared_module=None, grad_req='write')

Bind the symbols to construct executors. This is necessary before one can perform computation with the module.

shared_module (Module) 
- Useful for 'Testing'
– Default is None. This is used in bucketing. When not None, the shared module essentially corresponds to a different bucket 
– a module with different symbol but with the same sets of parameters (e.g. unrolled RNNs with different lengths).
```
### Testing

```
>>> np.equal([0, 1, 3], np.arange(3))
array([ True,  True, False], dtype=bool)
```

### Fianl Code

```
import mxnet as mx
import numpy as np
import mxnet.ndarray as nd

import logging
import sys
import os

logging.basicConfig(stream=sys.stdout, level=logging.DEBUG)
np.random.seed(777)

# 1. load the data
original_data = np.loadtxt('/Users/dong/Desktop/Code/MXNet-learn/data-03-diabetes.csv', delimiter=",", dtype=None)
# your own file path
input_data = original_data[:, 0:-1]
output_data = original_data[:, [-1]]

train_input_data, test_input_data = input_data[:700], input_data[700:]
train_output_data, test_output_data = output_data[:700], output_data[700:]

batch_size = 32

# 2. build the linear model by symbol
data = mx.sym.Variable('data')
target = mx.sym.Variable('target')
fc1 = mx.sym.FullyConnected(data = data, num_hidden =20, name ='fc1')
ac1 = mx.sym.Activation(fc1, act_type="relu", name ='ac1')
fc2 = mx.sym.FullyConnected(data = ac1, num_hidden =20, name ='fc2')
ac2 = mx.sym.Activation(fc2, act_type="relu", name ='ac2')
fc3 = mx.sym.FullyConnected(data=ac2, num_hidden =1, name ='fc3')
output = mx.sym.LogisticRegressionOutput(data=fc3, label=target)

# 3. construct the execution module by Module API
mod = mx.mod.Module(symbol=output, data_names=['data'], label_names=['target'], context=mx.cpu())
mod.bind(data_shapes = [mx.io.DataDesc(name='data', shape=(batch_size, 8))],
            label_shapes= [mx.io.DataDesc(name='target', shape=(batch_size, 1))])
mod.init_params()
mod.init_optimizer()

# 4. training
train_iter = mx.io.NDArrayIter(train_input_data, train_output_data, batch_size, label_name='target')
metric = mx.metric.CustomMetric(feval=lambda labels, output: ((output > 0.5) == labels).mean(),
                                name="acc")
mod.fit(train_data=train_iter, eval_metric=metric, num_epoch=15)

# 5. Testing
test_iter = mx.io.NDArrayIter(test_input_data, None, batch_size, shuffle=False, label_name=None)

pred_class = (fc3 > 0)
test_net = mx.mod.Module(symbol=pred_class,
                         data_names=['data'],
                         label_names=None,
                         context=mx.cpu())
test_net.bind(data_shapes=[mx.io.DataDesc(name='data', shape=(batch_size, 8), layout='NC')],
              label_shapes=None,
              for_training=False,
              shared_module=mod)
out = test_net.predict(eval_data=test_iter)
print(out.asnumpy())
acc = np.equal(test_output_data, out.asnumpy()).mean()
print("Accuracy: %.4g" %acc)
```