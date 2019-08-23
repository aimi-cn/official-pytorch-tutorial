教程学习目标：

- 理解Pytorch 的 Tensor库，以及神经网络。
- 训练一个简单的图像分类网络。
  
假设已经了解numpy的基本用法，并确保已经安装好torch和torchvision。

# 什么是PyTorch？

> 搜索微信公众号:'AI-ming3526'或者'计算机视觉这件小事' 获取更多算法、机器学习干货  
> csdn：https://blog.csdn.net/baidu_31657889/  
> github：https://github.com/aimi-cn/AILearners

> 译者注：本教程在pytorch官方教程的基础上翻译修改得到,代码输出是在本人在自己笔记本上运行之后放上去的，可能会和官方的输出结果有所不同，一切输出结果按照官方教程为准,原教程请点击[pytorch-official-tutorial](https://pytorch.org/tutorials/index.html)

PyTorch是一个基于python的科学计算包，主要针对两类人群：

- 作为NumPy的替代品，可以利用GPU的性能进行计算
- 作为一个高灵活性、速度快的深度学习平台

## 入门指南：

### 张量(Tensor)

Tensor（张量）类似于NumPy的ndarray，但还可以在GPU上使用来加速计算。

```python
from __future__ import print_function
import torch
```

构建一个没有初始化的5*3矩阵：

```python
x = torch.empty(5,3)
print(x)
```

输出：

```python
tensor([[ 1.1267e-32,  0.0000e+00, -2.4310e+03],
        [ 2.8671e-42,  0.0000e+00,  0.0000e+00],
        [ 2.1019e-44,  0.0000e+00,  1.1303e-32],
        [ 0.0000e+00,  3.0951e+32,  3.2603e-12],
        [ 0.0000e+00,  0.0000e+00,  2.1019e-44]])
```

构造一个随机初始化矩阵:

```python
x = torch.rand(5,3)
print(x)
```

输出：

```python
tensor([[0.1727, 0.9027, 0.2841],
        [0.7782, 0.7239, 0.2810],
        [0.3806, 0.2683, 0.3111],
        [0.1063, 0.9016, 0.5227],
        [0.3175, 0.3370, 0.2928]])
```

构造一个填满0且数据类型为long的矩阵:

```python
x = torch.zeros(5, 3, dtype=torch.long)
print(x)
```

输出：

```python
tensor([[0, 0, 0],
        [0, 0, 0],
        [0, 0, 0],
        [0, 0, 0],
        [0, 0, 0]])
```

直接从数据构造张量：

```python
x = torch.tensor([5.5,3])
print(x)
```

输出：
```python
tensor([5.5000, 3.0000])
```

或者根据现有的张量创建张量。 除非用户提供新属性，这些方法将重用输入张量的属性，例如， dtype。

```python
x = x.new_ones(5,3,dtype=torch.double)  # new_* 这个方法是张量的大小
print(x)

x = torch.randn_like(x, dtype=torch.float)  # 重载 dtype!
print(x)    # 结果有相同的size
```

输出：
```python
tensor([[1., 1., 1.],
        [1., 1., 1.],
        [1., 1., 1.],
        [1., 1., 1.],
        [1., 1., 1.]], dtype=torch.float64)

tensor([[-0.3437, -0.3024, -0.3263],
        [ 0.9715,  0.6363,  0.0973],
        [ 0.7263,  0.8896, -0.8012],
        [ 0.7325,  2.1229,  1.0394],
        [-2.0560,  1.2830, -0.2176]])
```

获取它的形状：
```python
print(x.size())
```
输出：
```python
torch.Size([5, 3])
```

- 注意：  
  torch.Size实际上是一个元组(tuple)，所以它支持所有的元组操作。

### 运算（Operations）
操作有多种语法。在下面的示例中，我们将研究加法操作。

加法：语法1

```python
y = torch.rand(5,3)
print(x+y)
```

输出：
```python
tensor([[ 0.8051,  3.2802,  0.8486],
        [-0.6485, -0.3066, -0.8842],
        [ 1.6792, -0.5011,  0.7273],
        [ 2.6411,  0.8881,  1.5917],
        [-0.1139,  1.0991,  1.0866]])
```

加法：语法2

```python
print(torch.add(x,y))
```

输出：
```python
tensor([[ 0.8051,  3.2802,  0.8486],
        [-0.6485, -0.3066, -0.8842],
        [ 1.6792, -0.5011,  0.7273],
        [ 2.6411,  0.8881,  1.5917],
        [-0.1139,  1.0991,  1.0866]])
```

加法：给定一个输出张量作为参数
```python
result = torch.empty(5,3)
torch.add(x,y,out=result)
print(result)
```
输出：
```python
tensor([[ 0.8051,  3.2802,  0.8486],
        [-0.6485, -0.3066, -0.8842],
        [ 1.6792, -0.5011,  0.7273],
        [ 2.6411,  0.8881,  1.5917],
        [-0.1139,  1.0991,  1.0866]])
```

加法：原地算法
```python
y.add_(x)
print(y)
```
输出：
```python
tensor([[ 0.8051,  3.2802,  0.8486],
        [-0.6485, -0.3066, -0.8842],
        [ 1.6792, -0.5011,  0.7273],
        [ 2.6411,  0.8881,  1.5917],
        [-0.1139,  1.0991,  1.0866]])
```

- 注意  
    任何一个in-place原地算法改变张量的操作后面都固定一个_。例如x.copy_(y)、x.t_()将更改x

也可以使用像标准的NumPy一样的各种索引操作：

```python
print(x)
print(x[:,-1])
```
输出：
```python
tensor([[ 2.1515,  5.2749,  1.4879],
        [ 0.5255,  0.5955, -0.6843],
        [ 3.3294,  0.6444,  1.1733],
        [ 2.8809,  2.7091,  3.0874],
        [-0.0774,  2.1830,  2.7367]])
tensor([ 1.4879, -0.6843,  1.1733,  3.0874,  2.7367])
```

改变形状：如果想改变形状，可以使用torch.view
```python
#ps:torch.rand和torch.randn有什么区别？一个均匀分布，一个是标准正态分布。
x = torch.randn(4,4)
y = x.view(16)
z = x.view(-1,8)    #大小-1是从其他维度推断出来的,也就是根据后面的8来推测出-1的真实值应该是2
print(x.size(),y.size(),z.size())
```
输出：
```python
torch.Size([4, 4]) torch.Size([16]) torch.Size([2, 8])
```

如果是仅包含一个元素的tensor，可以使用.item()来得到对应的python数值

```python
x = torch.randn(1)
print(x)
print(x.item())
```
输出：
```python
tensor([0.8693])
0.8693485856056213
```

后续阅读：

超过100中tensor的运算操作，包括转置，索引，切片，数学运算， 线性代数，随机数等，具体访问[这里](https://pytorch.org/docs/stable/torch.html)

这里我给出pytorch中文文档 有需要的可以参照阅读学习，中文文档地址：https://github.com/apachecn/pytorch-doc-zh

## 对接Numpy

将一个Torch张量转换为一个NumPy数组是轻而易举的事情，反之亦然。

Torch张量和NumPy数组将共享它们的底层内存位置，更改一个将更改另一个。

### 将torch的Tensor转化为NumPy数组

```python
a = torch.ones(5)
print(a)
```
输出：
```python
tensor([1., 1., 1., 1., 1.])
```

```python
b = a.numpy()
print(b)
```
输出：
```python
[1. 1. 1. 1. 1.]
```
看numpy数组的值是如何变化的。
```python
a.add_(1)
print(a)
print(b)
```
输出：
```python
tensor([2., 2., 2., 2., 2.])
[2. 2. 2. 2. 2.]
```

### 将Numpys数组转化为Torch的张量

看改变NumPy数组是如何自动改变Torch张量的：
```python
import numpy as np
a = np.ones(5)
b = torch.from_numpy(a)
np.add(a,1,out=a)
print(a)
print(b)
```

输出：
```python
[2. 2. 2. 2. 2.]
tensor([2., 2., 2., 2., 2.], dtype=torch.float64)
```

CPU上的所有张量(CharTensor除外)都支持转换为NumPy以及由NumPy转换回来。

## CUDA上的张量

张量可以使用.to方法移动到任何设备（device）上：

```python
# 只有我们电脑cuda是可用状态才可以运行下面的cell
# 我们将使用`torch.device`来将tensor移入和移出GPU
if torch.cuda.is_available():
    device = torch.device("cuda")          # a CUDA device object
    y = torch.ones_like(x, device=device)  # 直接在GPU上创建tensor
    x = x.to(device)                       # 或者使用`.to("cuda")`方法
    z = x + y
    print(z)
    print(z.to("cpu", torch.double))       # `.to`也能在移动时改变dtype
```

输出：

```python
tensor([1.8693], device='cuda:0')
tensor([1.8693], dtype=torch.float64)
```

[笔记github地址~](https://github.com/aimi-cn/official-pytorch-tutorial)