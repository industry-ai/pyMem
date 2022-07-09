# Memoire for pyTorch

- [What does "with torch no_grad" do in PyTorch?](https://www.tutorialspoint.com/what-does-with-torch-no-grad-do-in-pytorch)
- [NO_GRAD](https://pytorch.org/docs/stable/generated/torch.no_grad.html)
- 下面两个内容一致，前者英文，后者中文翻译抄袭(有可能机器翻译)
    - [Python torch.no_grad() Examples](https://www.programcreek.com/python/example/116214/torch.no_grad)
    - [Python torch.no_grad方法代码示例](https://vimsky.com/examples/detail/python-method-torch.no_grad.html)
- [Can torch.no_grad() used in training?](https://discuss.pytorch.org/t/can-torch-no-grad-used-in-training/71661) \
  这个方法似乎可用于我们进行fix-relax时的操作，**将来有空时再做测试**
- [Use of ‘model.eval()’ and ‘with torch.no_grad()’ in PyTorch model evaluate](https://androidkt.com/use-of-model-eval-and-with-torch-no_grad-in-pytorch-model-evaluate/)
- [Pytorch中requires_grad_(), detach(), torch.no_grad()的区别](https://www.jianshu.com/p/ff74ccae25f3)
- [PyTorch 中“detach()”和“with torch.nograd()”的区别？](https://qa.1r1g.com/sf/ask/3977136901/)

**********************************


## Index

1. [What does "with torch no_grad" do in PyTorch?](#with_torch_no_grad) \
   [1.1 Example 1](#example-1) \
   [1.2 Example 2](#example-2)
1. 



## 1 What does "with torch no_grad" do in PyTorch? <span id="with_torch_no_grad"></span>

The use of "**with torch.no_grad()**" is like a loop where every tensor inside the 
loop will have **requires_grad** set to **False**. It means any tensor with 
gradient currently attached with the current computational graph is now detached 
from the current graph. We no longer be able to compute the gradients with respect 
to this tensor.

A tensor is detached from the current graph until it is within the loop. As soon as 
it is out of the loop, it is again attached to the current graph if the tensor was 
defined with gradient.

Let's take a couple of examples for a better understanding of how it works.



[Back to index](#index)


### 1.1 Example 1

In this example, we created a tensor x with **requires_grad = true**. Next, we 
define a function y of this tensor x and put the function within the with 
torch.no_grad() loop. Now x is within the loop, so its requires_grad is set to False.

Within the loop the gradients of y could not be computed with respect to x. So, 
y.requires_grad returns False.

```python
# import torch library
import torch

# define a torch tensor
x = torch.tensor(2., requires_grad = True)
print("x:", x)

# define a function y
with torch.no_grad():
   y = x ** 2
print("y:", y)

# check gradient for Y
print("y.requires_grad:", y.requires_grad)
```

#### Output

```shell
x: tensor(2., requires_grad=True)
y: tensor(4.)
y.requires_grad: False
```


### 1.2 Example 2

In this example, we have defined the function z out of the loop. So, 
**z.requires_grad** returns True.
```python
# import torch library
import torch

# define three tensors
x = torch.tensor(2., requires_grad = False)
w = torch.tensor(3., requires_grad = True)
b = torch.tensor(1., requires_grad = True)

print("x:", x)
print("w:", w)
print("b:", b)

# define a function y
y = w * x + b
print("y:", y)

# define a function z
with torch.no_grad():
   z = w * x + b

print("z:", z)

# check if requires grad is true or not
print("y.requires_grad:", y.requires_grad)
print("z.requires_grad:", z.requires_grad)
```

#### Output

```shell
x: tensor(2.)
w: tensor(3., requires_grad=True)
b: tensor(1., requires_grad=True)
y: tensor(7., grad_fn=<AddBackward0>)
z: tensor(7.)
y.requires_grad: True
z.requires_grad: False
```

[Back to index](#index)






[Back to index](#index)


