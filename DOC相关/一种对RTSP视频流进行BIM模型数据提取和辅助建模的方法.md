# 背景
人工智能(AI)应用于计算机辅助设计(CAD)的概念，自建筑师Nicolas Negroponte在1970年提出以来，已过了半个世纪。最近十年，伴随着深度学习的发展，AI的边界又有了极大的拓展，同样也展现出了改进BIM设计流程的最大潜力。  
长期以来，AI在BIM中的应用，更多的是在提升效率方面。如自动化一些重复性工作，检查和复制元素，增加LOD(Level Of Detail)等。随着激光扫描软硬件技术的发展，使用Lidar传感器进行Scan To Bim,已经能满足不少需求，但它仅仅是把扫描到的点云Mesh化来生成模型。现在我们增加一些AI的方法，来让这个过程更智能，让生成的模型更精准合理，使之能更好地辅助建模。

# 流程
用OpenCV读取监控摄像头的视频流，用FFmpeg编解码后基于深度学习作图像分析，之后合成新的流加上语义知识(BIM数据)一起返回给WEB前端，最后流媒体播放和结果展示。  
![rtsp](https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1676268133923_rtsp.png)

# 训练图像分类模型
人脑能很快分辨出一张图片是门，窗，还是楼梯。为了让计算机产生识别物体的“直觉”，和人类似地学习和推断信息，我们要么设置一系列规则给它执行，称之为"监督学习"；要么给它答案，让它自己尝试理解问题和目标，称之为"非监督学习"。编码正确的规则或提供计算机能理解的答案格式，都不是很轻松，直到深度学习的诞生。计算机现在能很准确地识别和分类图像。  
## 图像识别
我们知道，图像其实是一组包含了强度、亮度、颜色、形状、轮廓等信息的数据。由于相似的物体，通常含有相同的亮度、颜色等信息，计算机可以记住这些类型，当下次再看到时，就能识别这种类型，这就叫做"图像识别"。这是一种监督学习。
## 训练模型
图像识别模型，需要把图像当作输入，把它分解，然后通过神经网络(NN)创建能用于分类的标签(label), 即 Images(input) --> NN(layers) --> Labels(output)
# 举例
让我们来训练一个模型，来识别图像是否是东方明珠塔。  
<img src="https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1676429611775_shanghai_tower.jpg" width="40%"></img>  
1. 输入： 东方明珠塔图片  
2. NN： 模型首先看到很多像素，检测到它内容的边缘和轮廓。给模型“所见”生成猜想  
3. 输出： 东方明珠塔标签  

可以一个图片一个标签(单类)，也可以一个图片多个标签(多类)，还可以二分分类(双类,如东方明珠塔和非东方明珠塔)。多类图像识别还可以产生置信度分数。  
接下来，我们创建图像识别模型。  
当今用于图像识别的最常用算法是CNN(Convolutional Neural Network)。经过ImageNet数以百万计的视觉识别(1000类)挑战后，它的效率基本接近人类。  
CNN识别物体的流程和人类类似，在图像像素的基础上，逐渐地通过卷积提取出重要特征,以下引用维基百科示意图
![Convolutions](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*jUP5jJMkJOYq-jCzsfXtow.png)  
接下来就是创建模型了。
# 图像识别流程
PyTorch是最受欢迎的机器学习框架之一，它极大简化了从科研原型到产品部署的流程。它构建图像分类模型分5个步骤:  
1. 加载并规范化训练数据和测试数据  
2. 定义CNN  
3. 定义loss函数和optimizer  
4. 在训练数据上训练模型  
5. 在测试数据上测试模型  

## 加载数据
```python
class Custom_Dataset(Dataset):
    def __init__(self,  txtpath):
        ''' txtpath img和label以逗号分隔，每一个训练数据是一行 '''
        self.train_imgs, self.labels = [], [] 
        with open(txtpath) as fr:
            line = fr.readline()
            self.train_imgs.append(line.split(',')[0])
            self.labels.append(line.split(',')[1])
     
    def __len__(self):
        return len(self.train_imgs)
    
    def __getitem__(self, index):
        img = cv2.imread(self.train_imgs[index])
        label = self.labels[index]
        return img, label
# dataloader可直接生成
custom_dataset = Custom_Dataset(txtpath)
dataloader = Dataloader(custom_dataset, batchsize = 2, shuffle = True)
```
这里加载自定义的image和label数据  

| id | img_path | label | label_encoded |
| --- | --- | --- | --- |
| 0 | Train_0.jpg | 门 | 0 |
| 1 | Train_1.jpg | 窗 | 1 |
| 2 | Train_2.jpg | 门 | 0 |
| 3 | Train_3.jpg | 楼梯 | 2 |
| 4 | Train_4.jpg | 钢筋 | 3 |  

## 定义CNN
```python
class Net(nn.Module):
    '''Models a simple Convolutinal Neural Network'''
    def __init__(self):
        '''initialize the network'''
        super(Net, self).__init__()
        # 3 input image channel, 6 output channels, 5x5 square convolution kernel
        self.conv1 = nn.Conv2d(3,6,5)
        self.pool = nn.MaxPool2d(2,2)
        self.conv2 = nn.Conv2d(6,16,5)
        self.fc1 = nn.Linear(16*5*5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        '''the forward propagation algorithm'''
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16*5*5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x

net = Net()
print(net)
```
首先input(x)传递给conv1对象，然后传递给ReLU(Rectified Linear Unit)激活函数，然后传给max pooling函数，以此类推。
```
input -> conv2d -> relu -> maxpool2d -> conv2d -> relu -> maxpool2d -> view ->
linear -> relu -> linear -> relu -> linear
```
我们简单解释下conv, maxpool, fc, relu等算法  
### Conv(Convolutional Layers)
![conv](https://miro.medium.com/v2/resize:fit:1204/format:webp/1*E0Ak5ONdfHy8TMvjpi3Isg.png)  
在由多个输入平面组成的输入信号上应用二维卷积，将多通道信号转换为特征图。
### MaxPool
![maxpool](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*Kh5Tv5trKt0cn5qEXfqZyQ.png)  
最大池化的目的是对图像的维度进行下采样，以允许对图像的某些区域的特征进行假设。
### Fc(Fully Connected layers)
![fc](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*nnJtrpvKagz6vC_DOa4slQ.png)  
全连接层意味着前一层的每个神经元都连接到后一层的所有神经元，或上一层的输出全部对接下一层的输入。
### ReLU(Rectified Linear Unit)
![relu](https://miro.medium.com/v2/resize:fit:1200/format:webp/1*jOU3PnNiB0YIH1Y_t-iXng.png)  
ReLU用作Conv层和Fc层的激活函数。它将输入的总和转换为单个输出，如果输入为负则输出为零，如果为正则输出输入。
## 定义loss函数和optimizer
```python
criterion = nn.CrossEntropyLoss()
optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
```
### CrossEntropyLoss
PyTorch里的交叉熵损失用来训练分类问题，它结合了log softmax和Negative Log-Likelihood.
### Optimizer
PyTorch的optim里包含了一系列优化算法。我们首先构造一个优化器来保存对象的当前状态，它会根据计算出的梯度更新参数，循环一直持续到训练结束。这是神经网络“学习”并反向传播更新权重的基本方法。

## 训练神经网络
```python
start = torch.cuda.Event(enable_timing=True)
end = torch.cuda.Event(enable_timing=True)

start.record()

for epoch in range(2):
    running_loss = 0.0
    for i,data in enumerate(trainloader, 0):
        inputs, labels = data 

        optimizer.zero_grad()

        outputs = net(inputs)
        loss = criterion(outputs, labels)
        loss.backward()
        optimizer.step()

        running_loss += loss.item()
        if i % 2000 == 1999:
            print(f'[{epoch+1},{i+1}] loss: {running_loss / 2000}')

end.record()

torch.cuda.synchronize()
print('训练完毕')
print(start.elapsed_time(end))
```
```
[1,  2000] loss: 2.157 
[1,  4000] loss: 1.861 
[1,  6000] loss: 1.687 
[1,  8000] loss: 1.571 
[1, 10000] loss: 1.500 
[1, 12000] loss: 1.450 
[2,  2000] loss: 1.412 
[2,  4000] loss: 1.331 
[2,  6000] loss: 1.339 
[2,  8000] loss: 1.306 
[2, 10000] loss: 1.273 
[2, 12000] loss: 1.281 
训练完毕 
139636.6875
```
### 保存模型
```python
PATH = './models/bim.pth'
torch.save(net.state_dict(), PATH)
```
### 重载模型
```python
net = Net()
net.load_state_dict(torch.load(PATH))
```
## 测试神经网络
```python
dataiter = iter(testloader)
images, labels = next(dataiter)
imshow(torchvision.utils.make_grid(images))
print('识别结果: ', ' '.join(classes[labels[j]] for j in range(4)))
```
```
识别结果: 门 楼梯 窗 窗
```
```python
correct = 0
total = 0
with torch.no_grad():
    for data in testloader:
        images, labels = data
        outputs = net(images)
        _, predicted = torch.max(outputs.data, 1)
        total += labels.size(0)
        correct += (predicted == labels).sum().item()

print(f'1000张图片的准确度测试结果: {100 * correct / total}%')
```
```
1000张图片的准确度测试结果: 72%
```
# 如何提高准确性
1. 1.调整超参  
2. 2.使用不同的优化器  
3. 3.图像数据增强  
4. 4.尝试更复杂数据  
5. 5.处理过度拟合  
6. 6.寻找更多数据  
7. 7.参考其他人的良好实现

以上就是本文的全部内容。