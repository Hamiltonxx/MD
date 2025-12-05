# code
https://github.com/PacktPublishing/PyTorch-Computer-Vision-Cookbook

# Get Started
``` shell
import torch
torch.__version__  #'1.13.0.dev20220630+cu113'
import torchvision
torchvision.__version__  #'0.14.0.dev20220630+cu113'
torch.cuda.is_available()  #True
torch.cuda.device_count()  #1
torch.cuda.current_device()  #0
torch.cuda.get_device_name(0)  #'NVIDIA GeForce RTX 3090'
```
## PyTorch tensor
A PyTorch tensor is an n-dimensional array, similar to NumPy arrays.
```python
import torch
# x = torch.ones(2,2)
x = torch.ones(2,2,dtype=torch.int8)
print(x)
print(x.dtype)
x = x.type(torch.float)
print(x.dtype)

x = torch.rand(2,2)
print(x)
print(x.dtype)
y = x.numpy()
print(y)
print(y.dtype)
```
By default, PyTorch tensors are stored on the CPU. To speed up computing, we need to move the tensors to the CUDA device.
```python
x = torch.tensor([1.5,2])
print(x)
print(x.device)
if torch.cuda.is_available():
    device = torch.device("cuda:0")
    x = x.to(device)
    print(x)
    print(x.device)
device = torch.device("cuda:0")
x = torch.ones(2,2,device=device)
print(x)
```
## Dataset
Training Dataset: train the model.  
Validation Dataset: track the model's performance during training.  
Test Dataset: final evaluation of the model.
```python
from torchvision import datasets, utils
import matplotlib.pyplot as plt
import numpy as np
%matplotlib inline

path2data="./data"
# loading the training data
train_data=datasets.MNIST(path2data, train=True, download=True)

x_train, y_train = train_data.data, train_data.targets
print(x_train.shape)
print(y_train.shape)

# loading validation data
val_data=datasets.MNIST(path2data, train=False, download=True)
x_val,y_val=val_data.data, val_data.targets
print(x_val.shape)
print(y_val.shape)

if len(x_train.shape)==3:
    x_train=x_train.unsqueeze(1)
print(x_train.shape)
if len(x_val.shape)==3:
    x_val=x_val.unsqueeze(1)
print(x_val.shape)

def show(img):
    '''Display tensors as images'''
    npimg = img.numpy()
    npimg_tr=np.transpose(npimg, (1,2,0)) # convert to H*W*C shape
    plt.imshow(npimg_tr, interpolation='nearest')
    
# make a grid of 40 images, 8 images per row
x_grid=utils.make_grid(x_train[:40], nrow=8, padding=2)
print(x_grid.shape)

show(x_grid)
```
## Data transformation
Image transformation(augmentation) is an effective technique that's used to improve a model's performance.
```python
from torchvision import transforms
train_data = datasets.MNIST(path2data, train=True, download=True)
data_transform = transforms.Compose([
    transforms.RandomHorizontalFlip(p=1),
    transforms.RandomVerticalFlip(p=1),
    transforms.ToTensor(),
])
# train_data = datasets.MNIST(path2data, train=True, download=True, transform=data_transform)
img = train_data[0][0]
img_tr = data_transform(img)
img_tr_np = img_tr.numpy()
plt.subplot(1,2,1)
plt.imshow(img,cmap="gray")
plt.title("original")
plt.subplot(1,2,2)
plt.imshow(img_tr_np[0],cmap="gray")
plt.title("transformed")
```

## Building models
A model is a collection of connected layers that process the inputs to generate the outputs. The nn package is a collection of modules that provide common deep learning layers.
```python
import torch
input_tensor = torch.randn(64,1000)
linear_layer = torch.nn.Linear(1000,100) # 1000 inputs, 100 outputs
output = linear_layer(input_tensor)
print(output.shape)
```
```python
import torch.nn.functional as F
class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(1,20,5,1)
        self.conv2 = nn.Conv2d(20,50,5,1)
        self.fc1 = nn.Linear(4*4*50, 500)
        self.fc2 = nn.Linear(500, 10)
        
    def forward(self, x):
        x = F.relu(self.conv1(x))
        x = F.max_pool2d(x, 2, 2)
        x = F.relu(self.conv2(x))
        x = F.max_pool2d(x, 2, 2)
        x = x.view(-1, 4*4*50)
        x = F.relu(self.fc1(x))
        x = self.fc2(x)
        return F.log_softmax(x, dim=1)
    
model = Net()
print(model)
```
## Defining the loss function and optimizer
```python
def loss_batch(loss_func, xb, yb, yb_h, opt=None):
    loss = loss_func(yb_h, yb)
    metric_b = metrics_batch(yb,yb_h)
    if opt is not None:
        loss.backward()
        opt.step()
        opt.zero_grad()
        
    return loss.item(), metric_b

def metrics_batch(target, output):
    pred = output.argmax(dim=1, keepdim=True)
    corrects=pred.eq(target.view_as(pred)).sum().item()
    return corrects

def loss_epoch(model,loss_func,dataset_dl,opt=None):
    loss=0.0
    metric=0.0
    len_data=len(dataset_dl.dataset)
    for xb,yb in dataset_dl:
        xb=xb.type(torch.float).to(device)
        yb=yb.to(device)
        yb_h=model(xb)
        
        loss_b,metric_b=loss_batch(loss_func,xb,yb,yb_h,opt)
        loss+=loss_b
        if metric_b is not None:
            metric+=metric_b
    loss/=len_data
    metric/=len_data
    return loss,metric

def train_val(epochs, model, loss_func, opt, train_dl, val_dl):
    for epoch in range(epochs):
        model.train()
        train_loss,train_metric=loss_epoch(model,loss_func,train_dl,opt)
        model.eval()
        with torch.no_grad():
            val_loss,val_metric=loss_epoch(model,loss_func,val_dl)
        accuracy=100*val_metric
        print("epoch: %d, train loss: %.6f, val loss: %.6f, accuracy: %.2f" %(epoch, train_loss,val_loss,accuracy))
        
num_epochs=5
train_val(num_epochs, model, loss_func, opt, train_dl, val_dl)
```
Once training is complete,we'll want to store the trained parameters in a file for deployment and future use.
```python
path2model="./models/model.pt"
torch.save(model,path2model)

_model = Net()
_model=torch.load(path2model)
```
```python
x = x.unsqueeze(0)
x = x.type(torch.float)
x = x.to(device)

output = _model(x)
pred = output.argmax(dim=1, keepdim=True)
print(pred.item(), y.item())
```

# Binary Image Classifiction
Image classification/Image recognition, is an important task in computer vision.