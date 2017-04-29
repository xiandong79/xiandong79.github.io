这篇文章的截图均来自“[一天学会深度学习](https://www.slideshare.net/tw_dsconf/ss-62245351)” -- 一个301页的幻灯片，深入浅出的讲解了深度学习的主要内容。**我个人非常推荐初学者进行学习**。


**首先**，并不是所有的造成“不满意的模型”的原因 都是 `overfitting`。接下来这些也是可能造成“不满意的模型”的原因。

### Loss function

`Square Error` 和 `Cross Entropy` 对于不同问题，会造成不同的准确率。选好这个`判断标准`也是非常重要。

### Mini-batch

 <img src="./downloads/Mini-batch.png" width = "300" height = "200" alt="Mini-batch" align=center />

使用`Mini-batch`的原因是，可以更快的更新参数和训练模型。举个例子，以前是拿到了50000个图片 对于当前模型的 Loss 然后进行 back-propagation，更新参数，现在我们可以在 学习了100个图片后就更新参数，从而更快的收敛函数。背后的原因是，这100个图片所带来的`信息量`已经足够去更新一次参数，优化一次模型。

另外，需要注意的是`Mini-batch`可以使`Loss function`更快的收敛，但并不能帮助我们进一步减小`Loss`.

### Activation function

* Rectified Linear Unit (ReLU)

![relu.png](/downloads/relu.png)

`ReLu` activation fuction 是当前最火的激活函数，并且逐渐衍生出各式各样的新的激活函数，如，`𝐿𝑒𝑎𝑘𝑦 𝑅𝑒𝐿𝑈`，`𝑃𝑎𝑟𝑎𝑚𝑒𝑡𝑟𝑖𝑐 𝑅𝑒𝐿𝑈`。


###  Adaptive Learning Rate

`Learning Rate` 过大会造成`Loss`并没有减小的可能。`Learning Rate` 过小会造成函数收敛太慢的可能。直觉告诉我们，这个`Learning Rate`在学习的后期应该逐渐变小，最好呢，有一个自适应的调节。当前已经有很多解决方案，如：`Adagrad`,`Adam`.

### Momentum

![momentum.png](/downloads/momentum.png)


## Overfitting
`Overfitting`发生的根本原因是 用 `training set` 学习来的模型里面的 参数，并不能体现出在 `test set` 里面的**新特性**。比如，图像识别里面，数字`2`的 形状是扭曲的，或者图片背景是新的。有下面几种解决方案：

### Early stopping
![earlystopping.png](/downloads/earlystopping.png)


### Weight decay
![weightdecay.png](/downloads/weightdecay.png)

### Dropout
![dropout.png](/downloads/dropout.png)![dropout-2.png](/downloads/dropout-2.png)

`Dropout` 去掉了一些参数，使得剩下的参数能够学习足够的`信息量`。同时，在不同的`train set` dropout 掉不同的参数，相当于有效训练了多个模型，最后再将多个模型进行整合。


最后，如果依旧`overfitting`/`不理想的结果`，那可能就要更换新的模型。

## CNN


```
既然有了深度学习，那是不是以后很多类别的工作都会被取代？

会的，但是...

深度学习工程师的职位不会，因为为某一特定问题，”训练“特定的模型需要十分专业的知识，和长久的经验积累。
```