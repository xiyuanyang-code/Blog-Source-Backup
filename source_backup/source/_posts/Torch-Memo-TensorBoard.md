---
title: Torch Memo TensorBoard
date: 2025-07-01 12:12:49
index_img: /img/cover/Tensorboard.png
excerpt: Tutorials for Tensor Board in torch.utils.tensorboard
math: true
categories:
  - Artificial Intelligence
  - Torch
tags:
  - Tutorial
  - Finished
  - TensorBoard
  - Pytorch
  - Python
---

# Torch Memo: Tensor Board

使用Tensor Board可以更加方便的查看训练时的可视化图像，作为TensorFlow库的原生支持，不用再重复造轮子。

![image](https://s1.imagehub.cc/images/2025/07/01/957b4cea13e3d7f057e64a0321247751.png)

GitHub Repo: [Tensor Board](https://github.com/tensorflow/tensorboard)

官方文档：[Tensor Board Official](https://www.tensorflow.org/tensorboard/get_started)

Relevant Website: [Tensor Board in torch](https://docs.pytorch.org/docs/stable//tensorboard.html)

{% note primary %}

**本文主要聚焦于Tensor Board**在Pytorch库中的使用。

All Source Code can be found in [This Repo](https://github.com/xiyuanyang-code/Torch_Memo/tree/master/src/Tensor_Board)

{% endnote %}

## Installation

```bash
pip install torch
```

`TensorBoard` is included in `torch.utils.tensorboard`, you don't need to install `tensorboard` or `tensorflow` again in case the conflicted version of **CUDnn**.

## Basic Usage

For Python, it supports `tensorboard` in `torch.utils.tensorboard`.

```python
import torch
import torchvision
from torch.utils.tensorboard import SummaryWriter
from torchvision import datasets, transforms

# Writer will output to ./runs/ directory by default
writer = SummaryWriter()

transform = transforms.Compose([transforms.ToTensor(), transforms.Normalize((0.5,), (0.5,))])
trainset = datasets.MNIST('mnist_train', train=True, download=True, transform=transform)
trainloader = torch.utils.data.DataLoader(trainset, batch_size=64, shuffle=True)
model = torchvision.models.resnet50(False)
# Have ResNet model take in grayscale rather than RGB
model.conv1 = torch.nn.Conv2d(1, 64, kernel_size=7, stride=2, padding=3, bias=False)
images, labels = next(iter(trainloader))

grid = torchvision.utils.make_grid(images)
writer.add_image('images', grid, 0)
writer.add_graph(model, images)
writer.close()
```

{% note primary %}

Tensor Board是如何可视化的？

- **数据收集**：TensorBoard本身不会产生新的数据，所可视化的数据本身依赖于使用torch，TensorFlow等深度学习库等生成的**日志文件**。

	- Summary Writer：**摘要写入器**
	- 支持的数据类型包括：
		- 标量（Scalars）：损失、准确率等数值。
		- 图像（Images）：输入/输出图像。
		- 直方图（Histograms）：权重分布。
		- 计算图（Graphs）：模型结构。
		- 嵌入向量（Embeddings）：高维数据降维可视化。

- 数据存储：数据以**二进制文件**存储在日志文件中，命名文件为`events.out.tfevents.1751354262.DB93.4101867.0.v2`.

- 数据读取：在数据读取阶段需要指定日志文件的目录，例如：

  - ```bash
  	tensorboard --logdir=./logs
  	```

  - ```bash
  	python -m tensorboard.main --logdir=./log
  	
  	# 注意lsof命令可以查看端口的占用情况
  	lsof -i :6006
  	```

- **最后的可视化展示阶段**。

因此，Tensorboard可以**最小化的减少画图等代码对训练代码的侵入程度**，只需要生成日志文件即可，并不需要插入大量的复杂的难调试的可视化的代码。

{% endnote %}

# Tensor Board Tutorial

# Tutorial for Tensor Board in Pytorch

## Introduction

Tensor Board is supported in `torch.utils.tensorboard`.

Docs: [Docs for tensor board](https://docs.pytorch.org/docs/stable//tensorboard.html)

## Installation

Actually you only need to install `torch`.

```python
%pip install torch tensorboard
```

## Quick Setup

To use Tensor Board quickly, run this command as follow:

```python
# tutorial for using tensor board
import torch
import torchvision
from torch.utils.tensorboard import SummaryWriter
from torchvision import datasets, transforms

print(torch.__version__)
print(f"Numbers of GPU available: {torch.cuda.device_count()}")

# Writer will output to ./runs/ directory by default
writer = SummaryWriter()

transform = transforms.Compose(
    [transforms.ToTensor(), transforms.Normalize((0.5,), (0.5,))]
)
trainset = datasets.MNIST(
    "data/mnist_train", train=True, download=True, transform=transform
)
trainloader = torch.utils.data.DataLoader(trainset, batch_size=64, shuffle=True)
model = torchvision.models.resnet50(weights=None)
# Have ResNet model take in grayscale rather than RGB
model.conv1 = torch.nn.Conv2d(1, 64, kernel_size=7, stride=2, padding=3, bias=False)
images, labels = next(iter(trainloader))

grid = torchvision.utils.make_grid(images)
writer.add_image("images", grid, 0)
writer.add_graph(model, images)
writer.close()

# then run tensor board in the command line!
```

Then you can open Tensor Board via this command:

```bash
tensorboard --logdir=./log
```

or

```bash
python -m tensorboard.main --logdir=./log
```

## SummaryWriter

The **SummaryWriter** class provides a high-level API to create an event file in a given directory and add summaries and events to it. The class updates the file contents asynchronously. This allows a training program to call methods to add data to the file directly from the training loop, without slowing down training.

### Scalars

You can write Scalars into Tensor Board.

This is one of the most common things to log. You can use add_scalar() to record a single numerical value, such as a loss or accuracy, at each training step. This is great for tracking your model's performance over time.

#### Adding scalars for single data

Parameters:

- `tag` (str) – Data identifier
- `scalar_value` (float or string/blobname) – Value to save
- `global_step` (int) – Global step value to record
- `walltime` (float) – Optional override default walltime (time.time()) with seconds after epoch of event
- `new_style` (boolean) – Whether to use new style (tensor field) or old style (simple_value field). New style could lead to faster data loading.

```python
from torch.utils.tensorboard import SummaryWriter
import numpy as np

writer = SummaryWriter(log_dir="test_dir")

for n_iter in range(50):
    writer.add_scalar("Loss/train", np.random.random(), n_iter)
    writer.add_scalar("Loss/test", np.random.random(), n_iter)
    writer.add_scalar("Accuracy/train", np.random.random(), n_iter)
    writer.add_scalar("Accuracy/test", np.random.random(), n_iter)

# then you can see the curve generated randomly in the Tensor Board!
writer_2 = SummaryWriter(log_dir="test_dir")


def test_func(x):
    return x


for n_iter in range(50):
    writer_2.add_scalar("test_1/test_2", test_func(n_iter), n_iter)

writer_2.close()
```

#### Adding Scalar for many data

Add many scalar data to summary.

Parameters

- `main_tag` (str) – The parent name for the tags
- `tag_scalar_dict` (dict) – **Key-value pair** storing the tag and corresponding values (It is a **dict**!)
- `global_step` (int) – Global step value to record
- `walltime` (float) – Optional override default walltime (time.time()) seconds after epoch of event

```python
from torch.utils.tensorboard import SummaryWriter
import numpy as np

r = 5
for i in range(100):
    writer_2.add_scalars(
        "run_14h",
        {
            "xsinx": i * np.sin(i / r),
            "xcosx": i * np.cos(i / r),
            "tanx": np.tan(i / r),
        },  # return multiple functions
        i,
    )

# !remember to add this line like plt.close()!
writer_2.close()

# This call adds three values to the same scalar plot with the tag
# 'run_14h' in TensorBoard's scalar section.
```

### Images and videos

#### Add single image

Parameters

- `tag` (str) – Data identifier
- `img_tensor` (**torch.Tensor, numpy.ndarray, or string/blobname**) – Image data
- `global_step` (int) – Global step value to record
- `walltime` (float) – Optional override default walltime (time.time()) seconds after epoch of event
- `dataformats` (str) – Image data format specification of the form CHW, HWC, HW, WH, etc.

img_tensor: Default is (3,H,W), (**CHW**: Channel, Height, Width).

More dataformats include:

- (1,H,W): for grey images which have only one channel. **CHW**
- (H,W): **HW**
- (H,W,3): **HWC**

```python
from torch.utils.tensorboard import SummaryWriter
import numpy as np

# demo 1: add a image
img = np.zeros((3, 100, 100))
img[0] = np.arange(0, 10000).reshape(100, 100) / 10000
img[1] = 1 - np.arange(0, 10000).reshape(100, 100) / 10000

img_HWC = np.zeros((100, 100, 3))
img_HWC[:, :, 0] = np.arange(0, 10000).reshape(100, 100) / 10000
img_HWC[:, :, 1] = 1 - np.arange(0, 10000).reshape(100, 100) / 10000

writer = SummaryWriter("test_dir")
writer.add_image("my_image", img, 0)

# demo 2: generate a random tensor for simulation
for step in range(10):
    img_random = np.zeros((3, 100, 100))
    for i in range(3):
        img_random[i] = np.random.random((100, 100))
    writer.add_image("random_image", img_random, step)

# If you have non-default dimension setting, set the dataformats argument.
writer.add_image("my_image_HWC", img_HWC, 0, dataformats="HWC")
writer.close()
```

#### Add multiple images

Add batched image data to summary.

Note that this requires the pillow package.

Parameters

- `tag` (str) – Data identifier
- `img_tensor` (torch.Tensor, numpy.ndarray, or string/blobname) – Image data
- `global_step` (int) – Global step value to record
- `walltime` (float) – Optional override default walltime (time.time()) seconds after epoch of event
- `dataformats` (str) – Image data format specification of the form NCHW, NHWC, CHW, HWC, HW, WH, etc.

Compared to `add_image`, it will add one dimension for **batches**, where the image_tensor is (N,3,H,W).

```python
from torch.utils.tensorboard import SummaryWriter
import numpy as np

img_batches = np.zeros((16, 3, 100, 100))

for step in range(16):
    for channel in range(3):
        img_batches[step, channel] = (
            np.arange(0, 10000).reshape(100, 100) / 10000 / 16 * step
        )

writer.add_images("test for batches", img_batches, 1, dataformats="NCHW")
```

`add_figure(tag, figure, global_step=None, close=True, walltime=None)`: Render **matplotlib** figure into an image and add it to summary.

Parameters

- tag (str) – Data identifier
- `figure` (Union[Figure, list['Figure']]) – Figure or a list of figures
- global_step (Optional[int]) – Global step value to record
- close (bool) – Flag to automatically close the figure
- walltime (Optional[float]) – Optional override default walltime (time.time()) seconds after epoch of event

```python
import matplotlib.pyplot as plt

writer = SummaryWriter("test_dir/figure")

# generate a matplotlib image
fig, ax = plt.subplots()
x = np.linspace(0, 2 * np.pi, 100)
y = np.cos(x)
ax.plot(x, y)
ax.set_title("Cos(x)")

writer.add_figure("cos_plot", fig, global_step=0)

plt.close(fig)
writer.close()
```

### Text

Parameters

- `tag` (str) – Data identifier
- `text_string` (str) – String to save
- `global_step` (int) – Global step value to record
- `walltime` (float) – Optional override default walltime (time.time()) seconds after epoch of event

```python
writer_3 = SummaryWriter("test_dir/test_for_text")
writer_3.add_text("tag-1", "Hello world")
writer_3.add_text("tag-2", "wow, this is fanastic!")
```

### Graph

add_graph(model, input_to_model=None, verbose=False, use_strict_trace=True)[source][source] Add graph data to summary.

Parameters

- `model` (torch.nn.Module) – Model to draw.
- `input_to_model` (torch.Tensor or list of torch.Tensor) – A variable or a tuple of variables to be fed.
- `verbose` (bool) – Whether to print graph structure in console.
- `use_strict_trace` (bool) – Whether to pass keyword argument strict to torch.jit.trace. Pass False when you want the tracer to record your mutable container types (list, dict)

```python
import torch
import torch.nn as nn
from torch.utils.tensorboard import SummaryWriter


# Define a simplified model
class SimpleModel(nn.Module):
    def __init__(self):
        super().__init__()
        self.fc1 = nn.Linear(10, 20)
        self.relu = nn.ReLU()
        self.fc2 = nn.Linear(20, 1)

    def forward(self, x):
        x = self.fc1(x)
        x = self.relu(x)
        x = self.fc2(x)
        return x


model = SimpleModel()
writer = SummaryWriter("test_dir/test_graph")

dummy_input = torch.randn(1, 10)  # (batch_size, input_dim)

writer.add_graph(model, dummy_input, verbose=True)
writer.close()
```

![image](https://s1.imagehub.cc/images/2025/07/01/f4bfc3177a9f6f53af4d0510df5a20b4.png)

### Embedding

```python
add_embedding(mat, metadata=None, label_img=None, global_step=None, tag='default', metadata_header=None)
```

Add embedding projector data to summary.

- `mat` (torch.Tensor or numpy.ndarray) – A matrix which each row is the feature vector of the data point, its size is (N,D), where N is the number of data and D is the feature dimension.
- `metadata` (list) – A list of labels, each element will be converted to string
- `label_img` (torch.Tensor) – Images correspond to each data point, (N,C,H,W).
- `global_step` (int) – Global step value to record
- `tag` (str) – Name for the embedding
- `metadata_header` (list) – A list of headers for multi-column metadata. If given, each metadata must be a list with values corresponding to headers.

![image](https://s1.imagehub.cc/images/2025/07/01/9c90c770dfda3461dd63f1f59fce0d49.png)

```python
import keyword
import torch

# keyword is for auto-generation

meta = []
while len(meta) < 100:
    meta = meta + keyword.kwlist  # get some strings
meta = meta[:100]

for i, v in enumerate(meta):
    meta[i] = v + str(i)

label_img = torch.rand(100, 3, 10, 32)
for i in range(100):
    label_img[i] *= i / 100.0

# print(meta)
print(len(meta))  #: 100
# print(label_img)


# define writer
writer = SummaryWriter("test_dir/test_for_embedding")
writer.add_embedding(torch.randn(100, 5), metadata=meta, label_img=label_img)
writer.add_embedding(torch.randn(100, 5), label_img=label_img)
writer.add_embedding(torch.randn(100, 5), metadata=meta)
add_pr_curve(tag, labels, predictions, global_step=None, num_thresholds=127, weights=None, walltime=None)
```

**Add precision recall curve**.

Plotting a precision-recall curve lets you understand your model’s performance under different threshold settings. With this function, you provide the ground truth labeling (T/F) and prediction confidence (usually the output of your model) for each target. The TensorBoard UI will let you choose the threshold interactively.

Precision=True PositiveTrue Positive+False Positive

Recall=True PositiveTrue Positive+False Negative

Parameters

- `tag` (str) – Data identifier
- `labels` (torch.Tensor, numpy.ndarray, or string/blobname) – Ground truth data. Binary label for each element.
- `predictions` (torch.Tensor, numpy.ndarray, or string/blobname) – The probability that an element be classified as true. Value should be in [0, 1]
- `global_step` (int) – Global step value to record
- `num_thresholds` (int) – Number of thresholds used to draw the curve.
- `walltime` (float) – Optional override default walltime (time.time()) seconds after epoch of event

```python
from torch.utils.tensorboard import SummaryWriter
import numpy as np

labels = np.random.randint(2, size=1000)  # binary label
predictions = np.random.rand(1000)
writer = SummaryWriter("test_dir/pr_curve_2")
writer.add_pr_curve("pr_curve", labels, predictions, 0)
writer.close()
```

### Mesh

Add meshes or 3D point clouds to TensorBoard.

The visualization is based on Three.js, so it allows users to interact with the rendered object. Besides the basic definitions such as vertices, faces, users can further provide camera parameter, lighting condition, etc. Please check https://threejs.org/docs/index.html#manual/en/introduction/Creating-a-scene for advanced usage.

Parameters

- `tag` (str) – Data identifier
- `vertices` (torch.Tensor) – List of the 3D coordinates of vertices. (**顶点坐标**)
- `colors` (torch.Tensor) – Colors for each vertex. (**顶点颜色**)
- `faces` (torch.Tensor) – Indices of vertices within each triangle. (Optional) (**面片索引**)
- `config_dict` – Dictionary with ThreeJS classes names and configuration.
- `global_step` (int) – Global step value to record
- `walltime` (float) – Optional override default walltime (time.time()) seconds after epoch of event

```python
import torch
from torch.utils.tensorboard import SummaryWriter

vertices_tensor = torch.as_tensor(
    [
        [1, 1, 1],
        [-1, -1, 1],
        [1, -1, -1],
        [-1, 1, -1],
    ],
    dtype=torch.float,
).unsqueeze(0)
# print(vertices_tensor.shape): (1,4,3), where 1 is the batch size and 4 is the number of vertices

colors_tensor = torch.as_tensor(
    [
        [255, 0, 0],
        [0, 255, 0],
        [0, 0, 255],
        [255, 0, 255],
    ],
    dtype=torch.int,
).unsqueeze(0)
# print(colors_tensor.shape): (1,4,3), the same as above

faces_tensor = torch.as_tensor(
    [
        [0, 2, 3],
        [0, 3, 1],
        [0, 1, 2],
        [1, 3, 2],
    ],
    dtype=torch.int,
).unsqueeze(0)

writer = SummaryWriter("test_dir/test_mesh")
writer.add_mesh(
    "my_mesh", vertices=vertices_tensor, colors=colors_tensor, faces=faces_tensor
)

writer.close()
```

### Hparams

Add a set of hyperparameters to be compared in TensorBoard.

- `hparam_dict` (dict) – Each **key-value pair** in the dictionary is the name of the hyper parameter and it’s corresponding value. The type of the value can be one of **bool**, **string**, **float**, **int**, or None.
- `metric_dict` (dict) – Each key-value pair in the dictionary is the name of the metric and it’s corresponding value. Note that **the key used here should be unique in the tensorboard record**. Otherwise the value you added by add_scalar will be displayed in hparam plugin. In most cases, this is unwanted.
- `hparam_domain_discrete` – (Optional[Dict[str, List[Any]]]) A dictionary that contains names of the hyperparameters and all discrete values they can hold
- `run_name` (str) – Name of the run, to be included as part of the logdir. If unspecified, will use current timestamp.
- `global_step` (int) – Global step value to record

```python
from torch.utils.tensorboard import SummaryWriter

with SummaryWriter("test_dir/hparams") as w:
    for i in range(5):
        w.add_hparams(
            {"lr": 0.1 * i, "bsize": i},
            {"hparam/accuracy": 10 * i, "hparam/loss": 10 * i},
        )
```

## Demo

In this section, we will train a complex model (CNN + LSTM + Transformer), and use `SummaryWriter` to log loss curve and other parameters.

![Demo for loss curve](https://s1.imagehub.cc/images/2025/07/02/cf43cf56c80afd47a0104b86594f2cd3.png)

```python
import torch
import torch.nn as nn
import torch.optim as optim
from torch.utils.tensorboard import SummaryWriter
from torch.utils.data import DataLoader, TensorDataset
import numpy as np

writer = SummaryWriter('runs/exp_demo')

class ComplexModel(nn.Module):
    def __init__(self):
        super().__init__()
        
        # CNN feature
        self.cnn = nn.Sequential(
            nn.Conv2d(3, 32, kernel_size=3, stride=1, padding=1),
            nn.BatchNorm2d(32),
            nn.ReLU(),
            nn.MaxPool2d(2),
            
            nn.Conv2d(32, 64, kernel_size=3, stride=1, padding=1),
            nn.BatchNorm2d(64),
            nn.ReLU(),
            nn.MaxPool2d(2),
            
            nn.AdaptiveAvgPool2d((4, 4))
        )
        
        # LSTM section
        self.lstm = nn.LSTM(
            input_size=64*4*4, 
            hidden_size=128, 
            num_layers=2,
            batch_first=True,
            bidirectional=True
        )
        
        # attention section
        self.attention = nn.Sequential(
            nn.Linear(256, 128),
            nn.Tanh(),
            nn.Linear(128, 1),
            nn.Softmax(dim=1)
        )
        
        # classifier
        self.classifier = nn.Sequential(
            nn.Linear(256, 64),
            nn.Dropout(0.5),
            nn.ReLU(),
            nn.Linear(64, 10)
        )

    def forward(self, x):
        # input shape: (batch_size, seq_len, C, H, W)
        batch_size, seq_len = x.shape[0], x.shape[1]
        
        cnn_features = []
        for t in range(seq_len):
            frame = x[:, t, :, :, :]
            features = self.cnn(frame)  # (bs, 64, 4, 4)
            features = features.view(batch_size, -1)  # (bs, 64*4*4)
            cnn_features.append(features)
        
        cnn_features = torch.stack(cnn_features, dim=1)  # (bs, seq_len, 64*4*4)
        lstm_out, _ = self.lstm(cnn_features)  # (bs, seq_len, 256)
        attention_weights = self.attention(lstm_out)  # (bs, seq_len, 1)
        context_vector = torch.sum(attention_weights * lstm_out, dim=1)  # (bs, 256)
        return self.classifier(context_vector)

# 3. 创建模拟数据 (视频分类任务)
def generate_fake_data(batch_size=16, seq_len=10):
    # 生成随机视频数据 (batch, seq_len, 3, 64, 64)
    videos = torch.randn(batch_size, seq_len, 3, 64, 64)
    labels = torch.randint(0, 10, (batch_size,))
    return videos, labels

train_data, train_labels = generate_fake_data(100)
val_data, val_labels = generate_fake_data(20)
train_dataset = TensorDataset(train_data, train_labels)
val_dataset = TensorDataset(val_data, val_labels)

model = ComplexModel()
criterion = nn.CrossEntropyLoss()
optimizer = optim.AdamW(model.parameters(), lr=3e-4)
scheduler = optim.lr_scheduler.CosineAnnealingLR(optimizer, T_max=100)

def train(model, dataloader, epoch):
    model.train()
    total_loss = 0
    
    for batch_idx, (inputs, targets) in enumerate(dataloader):
        optimizer.zero_grad()
        outputs = model(inputs)
        loss = criterion(outputs, targets)
        
        if batch_idx % 5 == 0:  
            for name, param in model.named_parameters():
                if param.grad is not None:
                    writer.add_histogram(
                        f"Gradients/{name}", 
                        param.grad, 
                        epoch * len(dataloader) + batch_idx
                    )
        
        loss.backward()
        
        nn.utils.clip_grad_norm_(model.parameters(), max_norm=1.0)
        
        optimizer.step()
        total_loss += loss.item()
        
        if batch_idx == 0:
            for name, param in model.named_parameters():
                writer.add_histogram(
                    f"Parameters/{name}", 
                    param, 
                    epoch
                )
    
    return total_loss / len(dataloader)

def validate(model, dataloader, epoch):
    model.eval()
    correct = 0
    with torch.no_grad():
        for inputs, targets in dataloader:
            outputs = model(inputs)
            preds = outputs.argmax(dim=1)
            correct += (preds == targets).sum().item()
    
    accuracy = correct / len(dataloader.dataset)
    writer.add_scalar('Accuracy/val', accuracy, epoch)
    return accuracy

train_loader = DataLoader(train_dataset, batch_size=16, shuffle=True)
val_loader = DataLoader(val_dataset, batch_size=8)

for epoch in range(100):
    train_loss = train(model, train_loader, epoch)
    val_acc = validate(model, val_loader, epoch)
    
    writer.add_scalar('Loss/train', train_loss, epoch)
    writer.add_scalar('Learning Rate', optimizer.param_groups[0]['lr'], epoch)
    
    scheduler.step()
    
    if epoch == 0:
        dummy_input = torch.randn(1, 10, 3, 64, 64)  
        writer.add_graph(model, dummy_input)

writer.flush()
writer.close()
```
