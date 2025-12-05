# A fun example
```python
import torch
from torchvision import transforms, models
from PIL import Image

img = Image.open('A_small_cup_of_coffee.JPG')

transform = transforms.Compose([
  transforms.Resize(256),
  transforms.CenterCrop(224),
  transforms.ToTensor(),
  transforms.Normalize(mean=[0.485,0.465,0.406],std=[0.229,0.224,0.225])
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
  print(classes[idx],prob[idx].item())
```

# Tensor
```python
import torch

# cpu version
x = torch.tensor([[1,2,3],[4,5,6]])
y = torch.tensor([[7,8,9],[10,11,12]])
z = x + y
print(z)
print(z.size())

# gpu version
device = "cuda" if torch.cuda.is_available() else "cpu"
x = torch.tensor([[1,2,3],[4,5,6]], device=device)
y = torch.tensor([[7,8,9],[10,11,12]], device=device)
z = x + y
print(z)
print(z.size())
print(z.device)
```
## Creating Tensors
```python
w = torch.tensor([1,2,3])
w = torch.tensor((1,2,3))
w = torch.tensor(numpy.array([1,2,3]))
w = torch.empty(100,200) # uninitialized, element values are not predictable
w = torch.zeros(100,200)
w = torch.ones(100,200)

w = torch.rand(5,10) # 值在[0,1)上
w = torch.randn(5,10) # 正态分布随机数，平均值0，方差1 
w = torch.randint(1,9,(5,10)) # [1,9)上随机数
w = torch.empty((100,200),dtype=toch.float64,device="cuda")
x = torch.empty_like(w)
```
## Tensor attributes
```python
x.dtype
x.device
x.shape
x.ndim
x.requires_grad
x.grad
x.grad_fn
x.s_cuda,x.is_sparse,x.is_leaf
x.layout
```
## Tensor operations
```python
x = torch.tensor([[1,2],[3,4],[5,6],[7,8]])
print(x)
print(x[1,1])
print(x[1,1].item())
print(x[:2,1])
print(x[x<5])
print(x.t())
print(x.view((2,4))) # usually view() is preferred over reshape()

y = torch.stack((x,x))
print(y)
a,b = x.unbind(dim=1)
print(a,b)
```

# Deep Learning Development
## Data Preparation
One of the popular datasets is CIFAR-10, Canadian Institute for Advanced Research. It consists of 10 possible objects: airplanes, cars, birds, cats, deer, dogs, frogs, horses, ships and trucks.
```python
from torchvision.datasets import CIFAR10
train_data = CIFAR10(root="./train/", train=True, download=True)
print(train_data)
print(len(train_data))
print(train_data.data.shape)
# print(train_data.targets)
print(train_data.classes)
print(train_data.class_to_idx)

print(type(train_data[0]))
print(len(train_data[0]))
data,label = train_data[0]
print(type(data))
print(label)
print(train_data.classes[label])

test_data = CIFAR10(root="./test/",train=False,download=True)
print(test_data)
print(len(test_data))
print(test_data.data.shape)
```
## Data Transforms
```python
from torchvision.datasets import CIFAR10
from torchvision import transforms 

train_transforms = transforms.Compose([
    transforms.RandomCrop(32,padding=4),
    transforms.RandomHorizontalFlip(),
    transforms.ToTensor(),
    transforms.Normalize(mean=(0.4914,0.4822,0.4465),std=(0.2023,0.1994,0.2010))
])

train_data = CIFAR10(root="./train",train=True,download=True,transform=train_transforms)
print(train_data)
print(train_data.transforms)
data,label = train_data[0]
print(type(data))
print(data.size())
print(data)

test_transforms = transforms.Compse([
    transforms.ToTensor(),
    transforms.Normalize((0.4914,0.4822,0.4465),(0.2023,0.1994,0.2010))
])

test_data = CIFAR10(root="./test/",train=False,transform=test_transforms)
print(test_data)
```
## Data Batching
When you train your model, you will want to pass in small batches of data at each iteration. Sending data in batches not only allows more efficient training but also takes advantage of the parallel nature of GPUs to accelerate training.
```python
import torch 
trainloader = torch.utils.data.DataLoader(train_data,batch_size=16,shuffle=True) 

data_batch,labels_batch = next(iter(trainloader))
print(data_batch.size())
print(labels_batch.size())

testloader = torch.utils.data.DataLoader(test_data,batch_size=16,shuffle=False)
```
## Model Development
```python
from torchvision import models 

vgg16 = models.vgg16(pretrained=True)
# The VGG16 model consists of three parts: features, avgpool, classifier
print(vgg16.classifier)
```
There are many famous untrained and pretrained models available, including AlexNet, VGG, ResNet, Inception, and MobileNet.
```python
# load a model from pytorch hub
import torch 
waveglow = torch.hub.load('nvidia/DeepLearningExamples:torchhub','nvidia_waveglow')

torch.hub.list('nvidia/DeepLearningExamples:torchhub')
```
## torch.nn
```python
import torch 
from torch import nn 
import torch.nn.functional as F 

class SimpleNet(nn.Module):
    def __init__(self):
        super(SimpleNet,self).__init__()
        self.fc1 = nn.Linear(2048,256)
        self.fc2 = nn.Linear(256,64)
        self.fc3 = nn.Linear(64,2)

    def forward(self, x):
        x = x.view(-1, 2048)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = F.softmax(self.fc3(x),dim=1)
        return x

simplenet = SimpleNet()
print(simplenet)
# input = torch.rand(2048)
# output = simplenet(input)
```
```python
from torch.utils.data import random_split
train_set, val_set = random_split(train_data, [40000,10000])
trainloader = torch.utils.data.DataLoader(train_set,batch_size=16,shuffle=True)
valloader = torch.utils.data.DataLoader(val_set,batch_size=16,shuffle=True)
print(len(trainloader))
print(len(valloader))

model = LeNet5().to(device="cuda")
criterion = nn.CrossEntropyLoss()
optimizer = optim.SGD(model.parameters(), lr=0.001, momentum=0.9)

N_EPOCHS = 10
for epoch in range(N_EPOCHS):
    # Training
    train_loss = 0.0
    model.train()
    for inputs,labels in trainloader:
        inputs = inputs.to(device)
        labels = labels.to(device)

        optimizer.zero_grad()

        outputs = model(inputs)
        loss = criterion(outputs, labels)
        loss.backward()
        optimizer.step()

        train_loss += loss.item()

    # Validation
    val_loss = 0.0
    model.eval()
    for inputs,labels in valloader:
        inputs = inputs.to(device)
        labels = labels.to(device)

        outputs = model(inputs)
        loss = criterion(outputs, labels)

        val_loss += loss.item()

    print(f"Epoch: {epoch} Train Loss: {train_loss/len(trainloader)} Val Loss: {val_loss/len(valloader)}")

```
If the loss decreases for validation data, then the model is doing well.
```python
num_correct = 0.0
for x_test_batch, y_test_batch in testloader:
    model.eval()
    y_test_batch = y_test_batch.to(device)
    x_test_batch = x_test_batch.to(device)
    y_pred_batch = model(x_test_batch)
    _, predicted = torch.max(y_pred_batch, 1)
    num_correct += (predicted==y_test_batch).float().sum()

accuracy = num_correct/(len(testloader) * testloader.batch_size)
print(len(testloader), testloader.batch_size)
print(f"Test Accuracy: {accuracy}")
```
## Model Deployment
```python
torch.save(model.state_dict(), "./lenet5_model.pt")

model = LeNet5().to(device)
model.load_state_dict(torch.load("./lenet5_model.pt"))
```

# Neural Network Development Reference Designs
Three examples:
1. perform transfer learning to classify images of bees and ants with a small dataset and pretrained network.  
2. perform sentiment analysis using text data to train a NLP model that predicts the positive or negative sentiment of movie reviews.  
3. demostrate generative learning by training a generative adversarial network(GAN) to generate images of aricles of clothing.
## Transfer Learning
AlexNet and VGG models have been trained with the ImageNet dataset. Although ImageNet contains 1000 different image classes, it may not contain the classes that you need to solve your image classification problem. We will train a model to classify images of bees and ants-classes not contained in ImageNet.  
To train our new classifier, we will fine-tune another famous model - ResNet18, by loading the pretrained model and training it with 120 new training images of bees and ants.
