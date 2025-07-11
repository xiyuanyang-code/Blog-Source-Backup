---
title: Torch Memo Tensor Operations
date: 2025-05-03 11:48:08
index_img: /img/cover/tensor.jpg
excerpt: Frequently used torch Memo of operations of Tensor in Pytorch
categories:
  - Artificial Intelligence
  - Torch
tags:
  - Tutorial
  - Finished
  - Tensor
  - Pytorch
  - Python
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Several Operations of Tensor in Pytorch

## Introductions

You can download the source code from [this repo](https://github.com/xiyuanyang-code/Torch_Memo/blob/master/Tensor_operations.ipynb).

## Preliminaries

```python
import torch

print(torch.__version__)

device = "cuda" if torch.cuda.is_available() else "cpu"
print(device)

torch.manual_seed(2025)
```

## Creating Tensor

- `zeros` and `ones` and `zeros_like`
- `eyes` (Identity Matrix)
- `rand` and `randm` matrix

```python
# For creating random Tensors

# Create a randomized tensor between 0 and 1.
random_tensor_1 = torch.rand(2, 3, 4)
random_tensor_2 = torch.rand(2, 3, 4, dtype=torch.float16)
print(random_tensor_1)
print(random_tensor_2)

# Using rand_like to keep the same size
random_tensor_3 = torch.rand_like(random_tensor_2)
print(random_tensor_3)

# Random tensor with a uniform distribution of integers.
random_tensor_4 = torch.randint(1, 100, (3, 4, 4))
random_tensor_5 = torch.randint_like(random_tensor_4, high=10, low=1)
print(random_tensor_4)
print(random_tensor_5)

# Random tensor with a normal distribution.
random_tensor_6 = torch.randn(2, 3)
print(random_tensor_6)
random_tensor_7 = torch.randn(10000000)
print(random_tensor_7)
print(f"Average: {random_tensor_7.mean():.4f}")
print(f"Standard deviation: {random_tensor_7.std():.4f}")

# customizing mean and std, or using rand_like
mean, std = 5.0, 2.0
random_tensor_8 = mean + std * random_tensor_6
random_tensor_9 = torch.randn_like(random_tensor_3)
print(random_tensor_9)

# Returns a tensor of random numbers drawn from separate normal distributions, independently sample each element.
means = torch.tensor([[1.0, 2.0], [3.0, 4.0]])
std = torch.tensor([[1, 2], [4, 5]])
custom_normal = torch.normal(means, std)
print(custom_normal)
```

```python
# For data shuffle
perm = torch.randperm(5)
print(perm)
data = torch.tensor([10, 20, 30, 40, 50])
shuffled = data[torch.randperm(len(data))]
print(shuffled)
```

Well, you can also sample from different distributions...

- `torch.bernoulli()`
- `torch.poisson()`
- `torch.multinomial()`

In [ ]:

```python
from torch.distributions import Normal, Uniform, Gamma

# Normal Distribution
normal_dist = Normal(loc=0.0, scale=1.0)
samples = normal_dist.sample((2, 3))
print(samples)

# Uniform Distribution
uniform_dist = Uniform(low=-1.0, high=1.0)
samples = uniform_dist.sample((2, 3))
print(samples)

# Gamma Distribution
gamma_dist = Gamma(concentration=1.0, rate=1.0)
samples = gamma_dist.sample((2, 3))
print(samples)
```

You can also Padding tensor into random tensors.

In [ ]:

```python
# First create an uninitialized tensor, then fill it randomly.
uninitialized = torch.empty(2, 3)

# from the continuous uniform distribution
uninitialized.uniform_()
# rom the normal distribution parameterized
uninitialized.normal_()

# Or you can use the random methods directly
random_tensor = torch.Tensor(2, 3).uniform_(-1, 1)
random_tensor = torch.Tensor(2, 3).normal_(0, 1)
random_tensor = torch.Tensor(2, 3).exponential_(1)
```

## Check tensor information

You can use the following methods to check various information about the tensor!

### Query related dimension information.

In [ ]:

```python
random_tensor = torch.randn(
    4, 5, 6, 7, 8, dtype=torch.float16, device=device, requires_grad=True
)
# print(random_tensor)

# shape as a list
print(random_tensor.shape)
print(random_tensor.shape[0])

# dtype
print(random_tensor.dtype)

# device
print(random_tensor.device)

# dimensions
print(random_tensor.ndim)

# requires grad
print(random_tensor.requires_grad)
```

### Query the specific content.

Very similar with the `numpy` operations.

In [ ]:

```python
print(random_tensor.sum())
print(random_tensor.min())
print(random_tensor.max())
print(random_tensor.std())
print(random_tensor.mean(dim=2))
print(random_tensor.norm())
print(random_tensor.grad)
max_vals, max_indices = random_tensor.max(dim=1)
print(max_indices)

# Detach needs to be called if it is requires grad
print(random_tensor.detach().cpu().numpy())
```

## Operations

- Basic arithmetic operations and exponentiation. (Skip)
- Matrix multiplication.
- Linear Operations,see [this repo](https://github.com/xiyuanyang-code/numpy_tutorial) for more details. (Skip)
- Autograd operations.
- **Broadcasting mechanism.**

### Matrix Multiplications

In [ ]:

```python
# Matrix multiplications

A = torch.randn(4, 4)
B = torch.randn(4, 4)

# Element-wise multiplication of matrices.
print(A * B)

# Simple Matrix multiplications
print(A @ B)

vector_1 = torch.randn(100)
vector_2 = torch.randn(100)
# vector dots (for 1D only)
print(torch.dot(vector_1, vector_2))

# batch matrix multiplications
batch_1 = torch.randn(4, 50, 10)
batch_2 = torch.randn(4, 10, 20)
result_batch = torch.bmm(batch_1, batch_2)
print(result_batch.shape)
```

### Autograd Operations

In [ ]:

```python
x = torch.tensor(2.0, requires_grad=True)
y = x**2 + 3 * x + 1
y.backward()
print(y.shape)
print(x.grad)
```

### Broadcasting mechanism

The same as `numpy`.

Broadcasting follows the following rules:

1. Start comparing from the trailing dimensions: Begin comparing the dimensions of tensors from the rightmost dimension and move leftward.
2. Dimension compatibility conditions:
	- The two dimensions are equal.
	- One of the dimensions is 1.
	- One of the dimensions does not exist (i.e., the tensors have different numbers of dimensions).
	- If none of the conditions are met, an error is raised.

In [ ]:

```python
A = torch.randn(1, 3)
B = torch.randn(3, 1)
C = torch.randn(1, 2)

print(A + B)
print(B + C)
```

## Changing Tensors' shape

Matrix multiplication is one of the most common operations in PyTorch, so sometimes we need to **change the shape of the created tensors**.

- `unsqueeze(dim)`: Adds a new dimension of size 1 at the specified position.

In [ ]:

```python
x = torch.tensor([1, 2, 3])
y = x.unsqueeze(0)  # shape: (1, 3)
z = x.unsqueeze(1)  # shape: (3, 1)
print(y.shape)
print(z.shape)
```

- `squeeze(dim)`: Removes dimensions of size 1. If no dim is given, all singleton dimensions are removed.

In [ ]:

```python
a = torch.rand(1, 3, 1, 4)
b = a.squeeze(0)  # shape: (3, 1, 4)
d = a.squeeze(1)  # it will do nothing
c = a.squeeze()  # shape: (3, 4) (all size-1 dims removed)

print(a.shape)
print(b.shape)
print(c.shape)
print(d.shape)
```

- `reshape(new_shape)`: Returns a tensor with the same data but a new shape. May copy data if non-contiguous.

In [ ]:

```python
x = torch.arange(6)
y = x.reshape(2, 3)  # shape: (2, 3)
z = x.reshape(3, -1)
print(x.shape)
print(y.shape)
print(z.shape)
```

- `view(new_shape)`: Similar to `reshape`, but requires the tensor to be contiguous (throws an error otherwise).

- `repeat_interleave(repeats, dim)`: Repeats elements of a tensor along specified dimensions (similar to NumPy’s `repeat`).

In [ ]:

```python
x = torch.tensor([[1, 2], [3, 4]])
y = x.repeat_interleave(2, dim=0)  # shape: (4, 2)
# [[1, 2], [1, 2], [3, 4], [3, 4]]
z = x.repeat_interleave(3, dim=1)  # shape: (2, 6)
# [[1, 1, 1, 2, 2, 2], [3, 3, 3, 4, 4, 4]]

print(y.shape)
print(z.shape)
```

- `repeat`: Repeats the entire tensor along specified dimensions

In PyTorch, the function `repeat()` is used to **replicate the entire tensor along specified dimensions**, thereby expanding the shape of the tensor. It differs from `repeat_interleave()`, where `repeat()` performs overall replication, while `repeat_interleave()` does element-wise replication.

`repeat(*sizes)` accepts a tuple parameter that indicates the number of repetitions for each dimension and returns a new tensor.

In [ ]:

```python
x = torch.tensor([1, 2, 3])
print(x.shape)

y = x.repeat(2)  # shape: (6,) → [1, 2, 3, 1, 2, 3]
print(y)
print(y.shape)

z = x.repeat(3, 1)
print(z.shape)
print(z)

w = x.repeat(2, 2)  # shape: (2, 6)
# [[1, 2, 3, 1, 2, 3], [1, 2, 3, 1, 2, 3]]
print(w.shape)
print(w)

test = torch.randn(3, 4, 5, 6)
print(test.shape)
# print(test)
test = test.repeat(2, 2, 2, 2)
print(test.shape)
# print(test)
test = test.unsqueeze(-1)
test = test.repeat(1,1,1,1,4)
print(test.shape)
```

- `expand()`: Expands a tensor to a larger size by broadcasting (only works for singleton dimensions).

In [ ]:

```python
x = torch.tensor([[1], [2], [3]])  # shape: (3, 1)
y = x.expand(3, 4)  # shape: (3, 4)
# [[1, 1, 1, 1], [2, 2, 2, 2], [3, 3, 3, 3]]
print(y.shape)
```
