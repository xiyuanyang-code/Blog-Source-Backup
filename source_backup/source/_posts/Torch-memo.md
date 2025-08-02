---
title: Torch-memo
date: 2025-04-01 15:44:06
index_img: /img/cover/torch.jpg
excerpt: Frequently used torch Memo.
categories:
  - Artificial Intelligence
  - Torch
tags:
  - Tutorial
  - Updating
  - Python
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Torch Memo

## Preliminaries

- `.__dir__()`: Getting all the functions in the module (**Tool function**)

## TensorBoard

```python
from torch.utils.tensorboard import SummaryWriter
import numpy as np
from PIL import Image

writer = SummaryWriter("logs")

def compute(x):
    return (torch.sin(torch.tensor(x)) + torch.randn(1) * torch.sin(torch.tensor(x)))


# writer.add_scalar()
for i in range(100):
    writer.add_scalar("y=x", compute(i).item(), i)


# writer.add_image()
img = Image.open("003.jpg")
img_array = np.array(img)

# dataformats is HWC
writer.add_image("test", img_array, 1, dataformats='HWC')

```

## Transforms

Use Transforms to make **Data Augmentation** and design your own class of data!

## Use GPU in torch

```python
# Memo: Using GPU in Pytorch

import torch

import argparse

# Using parser
parser = argparse.ArgumentParser(description="Selecting device for Pytorch")
parser.add_argument(
    "--device",
    type=str,
    choices=["cuda", "cpu"],
    default="cuda" if torch.cuda.is_available() else "cpu",
    help= "Device to use, cuda or cpu"
)
args = parser.parse_args()

# Usage
device = args.device
print(f"Device selectd, using {device}")
```

## Tensor Operations

torch中最关键的数据结构就是张量，因此对张量做操作是数据预处理的关键操作之一。

