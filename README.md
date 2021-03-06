# Get values of angles on images
With deep learning

### 写在前面

所以我真的是个菜鸟，才会想到这么无聊的一个项目。(开篇是一些感悟，感情不细腻的可以直接[点击这里跳转](#begin))

前前后后在网易云课堂看了200多个小时的深度学习课程，包括：
- [吴恩达的deeplearning.ai上的课程](https://study.163.com/provider/2001053000/index.htm)
- [莫烦的python和机器学习](https://study.163.com/provider/1111519/index.htm)

都是有情怀的人，因为课程都是免费的。在机器学习这个领域，天下没有免费的午餐这句话真的算是伪命题了。

于是我一直在想深度学习可以做什么。可能很多人都跟我一样，在没有思考清楚是什么，为什么的时候就一头扎进一个坑。

有好也有不好，起码在做事情的时候，人是在进步的。

所以，在这之前我更是个菜鸟。代码不是写出来的，而是查出来的；只能逐行写，不会定义函数，也不会面向对象；不知道异常处理，满屏都是bug。

于是真的想放弃编程了，直到看到了机器学习这个东西。很多人觉得机器学习和编程，就算不是一个东西也可能差不了多少。但在我这个菜鸟眼里，它们是对立的。这也是我能坚持学习python的根本原因。

用一句话说，编程是制定规则，而机器学习是寻找规则。我最喜欢的一句话是：这个世界太奇妙了。因此能制定的规则太少了，而需要探索的事物就太多了。

举一个简单的算法为例，决策树。当我们知道这棵决策树的全貌之后，我们可以直接用编程的语言将它实现，而且非常完美，准确性100%。例如临床医学中，几乎大多数的诊断其实都是一个决策树模型。

> 肾病综合征:

> 尿蛋白大于3.5g/d；

> 血浆白蛋白低于30g/L；

> 水肿；

> 高脂血症

想象一下，假如是一个机器学习繁荣而临床医学荒芜的时代，我们不知道这个诊断标准，但是我们有很多数据，很多患者被认为是有肾综，于是我们就可以通过机器学习的办法去找出这个诊断标准。可能会很幸运地找到了决策树模型，因为它具有良好的解释性。但最后我们学习的结果可能不尽满意，我们不一定会找到蛋白尿大于3.5这个值，也可是3.4或者3.6 。而我们的准确度也不会是100%，能有90%就不错了。

以上，是解决事物的两种思路。也是编程思想和机器学习思想的不同之处。

没听懂的话，就看看我这个无聊的项目吧。

### Begin

一开始我是想用计算机视觉来测量一个图片内圆的半径，做了一半才想到图片是以像素为单位的，长度其实并没有意义。于是我想到了角度——通过输入角的图片来得到角的度数。因为图片不管怎么放大，角度是不会变的。

抛开这个想法的无聊来思考问题。其实从角的度数得到角的图是比较容易的，编程思想可以实现。但是通过图片去得到角的大小，单纯使用编程思维，起码对于我是不可能完成的事。而机器学习在这里就可以大显神威。

首先利用matplotlib绘制角度的图片，这个过程属于编程过程，从角度到图片属于编程思维，很容易实现，不喜欢研究代码的可以直接忽略掉下面的内容。

```python
import numpy as np
import matplotlib.pyplot as plt
import random

#生成训练集，定义数据大小
data_size = 5000
random.seed(1)

# 定义坐标原点，边长范围和角度
X = [random.uniform(0.5,1) for _ in range(data_size)]
Y = [random.uniform(0.5,1) for _ in range(data_size)]
R = [random.uniform(0.5,1) for _ in range(data_size)]
T1 = [random.randint(-180,180) for _ in range(data_size)]
T2 = [random.randint(-180,180) for _ in range(data_size)]

# 利用plt画出角来
cir_no = 10000
for x,y,r,t1,t2 in zip(X,Y,R,T1,T2):
    cir_no += 1
    plt.figure(num=1, figsize=(2,2), dpi=90, frameon=False)#保存为180×180的图片
    plt.plot([x,x+np.sin(t1*np.pi/180)*r],[y,y+np.cos(t1*np.pi/180)*r],color='black',linewidth=1)
    plt.plot([x,x+np.sin(t2*np.pi/180)*r],[y,y+np.cos(t2*np.pi/180)*r],color='black',linewidth=1)
    plt.xlim((-0.5,2))
    plt.ylim((-0.5,2))
    plt.axis('off')
    plt.savefig('angles/angle_'+str(cir_no)+'.jpg')
    plt.close('all')
  ```

同样的办法生成测试集，关于数据集的大小我后面讲。然后我得到了5000张下图中的这些图片：

![angles](https://raw.githubusercontent.com/gscfwid/Get-the-value-of-angle-in-images/master/readme_pics/angles.png)

因为在产生图片的过程中已经计算了它的夹角，因此标签，也就是输出Y也已经有了。接下来我们就可以进行训练了。代码可以在[我github中的jupyter notebook中](https://github.com/gscfwid/Get-the-value-of-angle-in-images/blob/master/Angle%20measurement%20with%20deep%20learning.ipynb)查看，附带有比较详细的说明。

模型摸索了很久，一开始借鉴了U-net的前面部分，两层卷积+一层池化，重复一遍，然后展平与全连接层对接，输出层为一个无激活函数的神经元。效果不好，最多达到50%的准确性。后来借鉴了一下LeNet-5，又加了一层卷积和3×3的池化层，使最后展平的卷积层具有更小的长和宽，训练的结果才满意。

我用了5000个图片作为训练集，500个作为测试集，在误差不超过10°的情况下，准确率在测试集达到了95%左右；在误差不超过5°的情况下，准确率达到了75%。下面是随机取的三张图。

![angle_samples](https://raw.githubusercontent.com/gscfwid/Get-the-value-of-angle-in-images/master/readme_pics/angles_pred.png)

### 总结

虽然是非常脑残和无聊的一个项目，还是有一些值得深究的地方。

1. 准确性还能说得过去，这些图非常像钟表的指针，因此通过钟表的指针图片得到准确时间也是可以进行训练的，可能会比这个项目有用一点。。。汗~~~

2. 它的输出Y是连续型的变量，因此在输出神经元没有使用激活函数，这也是一个技巧，我试过用ReLU，效果很差。

3. 优化器非常重要，在训练的过程中，adam收敛在了局部最优解就真的没有办法下降了，而RMSprop能得到非常好的结果。

4. 在时间和机器允许的范围内，尽量不要设置earlystopper，有时多训练几轮会跳过局部最优解到达全局最优解。

5. 数据量也是非常重要的，从1000张图片增长到5000张图片作为训练集后，最终的表现增加了10%。

6. 深度学习真的很吃数据，这样一个比较简单的问题，还是需要上千的数据集才能做到不错的结果。
