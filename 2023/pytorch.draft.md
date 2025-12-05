# 简介
PyTorch是最受欢迎的深度学习框架之一，主要由Facebook AI Research Lab(FAIR)开发的开源框架。  
加载数据、转换应用和构建模型仅仅需要几行代码。然后您可以灵活地编写自定义的训练、验证和测试循环，并轻松部署经过训练的模型。  

## 为什么使用PyTorch
1. PyTorch非常受欢迎
2. 所有主流云平台(AWS,GCP,Azure,AliCloud)都支持PyTorch
3. 无需预安装预配置即可在浏览器里运行
4. 成熟稳定
5. 支持CPU,GPU,TPU和并行处理
6. 支持分布式训练
7. 支持产品部署(TorchScript,TorchServe)
8. 支持移动设备部署
9. 有庞大的生态系统和开源库(Torchvision,fastai,PyTorch Lighting)
10. 有C++接口
11. 支持Open Neural Network Exchange(ONNX)
12. 有庞大的社区和开发人员

## A fun example
```shell
curl -o coffee.jpg https://upload.wikimedia.org/wikipedia/commons/4/45/A_small_cup_of_coffee.JPG
```
```python
import matplotlib.pyplot as plt
from PIL import Image

img = Image.open('coffee.jpg')
plt.imshow(img)
plt.show()
```
接下来我们把这张图片传到经预训练的图片分类神经网络(NN),在此之前，我们需要预处理图片。预处理在机器学习中非常常用，因为NN希望输入满足一定的要求。
```python
import torch
from torchvision import transforms, models

transform = transforms.Compose([ 
  transforms.Resize(256), 
  transforms.CenterCrop(224), 
  transforms.ToTensor(), 
  transforms.Normalize(
    mean=[0.485, 0.456, 0.406], 
    std=[0.229, 0.224, 0.225]
  )
])

img_tensor = transform(img)
batch = img_tensor.unsqueeze(0)
model = models.alexnet(pretrained=True)
device = "cuda" if torch.cuda.is_available() else "cpu" 
model.eval()
model.to(device)
y = model(batch.to(device))
prob = torch.nn.functional.softmax(y, dim=1)[0] * 100 
_, indices = torch.sort(y, descending=True)

for idx in indices[0][:5]:
  print(classes[idx], prob[idx].item())
```

# Tensor
tensor(张量)通常可以认为是NumPy的ndarray. 但它比numpy ndarray优秀。一、tensor用GPU加速(最主要)。二、tensor可以分布式计算。三、tensor会保留图运算记录。
## Create tensors
很像NumPy
## Tensor Operations
### Indexing,Slicing,Combining,Splitting Tensors
很像NumPy

# Deep Learning Developement
## The Overall Process
First: Data preparation stage. This data could be images, videos, speech recordings, audio files, text, general tabular data or any combination of these. Load the data into tensors.
Next: Model experimentation and development stage. Design an NN model, train the model, test its performance and optimize hyperparameters. torch.nn help you create and train your NNs.
Last: Model deployment stage. Researcher: save to a file, PyTorch Hub, local server. Software Developer: Deploy model to a product or service. on cloud server or mobile phone.
## Data Loading
One of these popular datasets is CIFAR-10.
```python
from torchvision.datasets import CIFAR10

train_data = CIFAR10(root="./train/", train=True, download=True)
print(train_data)
print(len(train_data))
print(train_data.data.shape)
print(train_data.targets)
print(train_data.classes)
print(train_data.class_to_idx)
```
## Data Transforms
Data might need to adjusted before it is passed into the NN model for training and testing.

