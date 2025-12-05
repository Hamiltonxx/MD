# 图像算法调研
计算机视觉是人工智能的重要领域。一方面，数据的呈现形式通常是RGB三通道图像，像素色彩带给人的直观感觉比自然语言处理的语义特征鲜明得多，另一方面，工业界对计算机视觉算法的广泛需求，横跨了图像分类、目标检测、语义分割和实例分割。从yolo系列到transformer系列，模型的应用、算法的迭代、场景的探寻，都有不小的探究和应用价值。
## 环境搭建
- 环境搭建是一切深度学习的开端，有时候搭建GPU环境要耗费好几天的大好青春(当然自己写算子的大牛除外)。在beginner账号下的venv环境，内置pytorch1.13nightly和cuda11.2，与本机的RTX3090算力相配套。可运行activate激活venv环境
```shell
. ~/projects/venv/bin/actiavte
```
## 安全帽检测
- 目标检测部分拟全部采用yolov5进行算法搭建和部署。yolo系列举足轻重的地位不必多说，yolov5作为工程经验的总结，体量比yolov4小了一半以上，精度(mAP)却雄风依旧在。而且近几年推出了pytorch版本的实现，模型的预训练权重也公开在github上，可谓是造福一方。智能图像整个大的检测模块拟利用官方预训练好的yolov5s进行第一版的迁移学习和部署。这个模型的原始输出为：如果图片或视频中人带着安全帽，则检测类型为hat，如果人没戴安全帽，则检测类型为person。故部署在生产环境中时，如果检测到person,则做相应的报警，表示检测到有工人未佩戴安全帽。本权重文件很小，只有15.6MB，推理速度快，但可能存在误检，先部署上线，看看情况再迭代。项目地址：
[Helmet-Detection-Yolov5-master](https://github.com/FanDady/Helmet-Detection-YoloV5)
- 项目克隆地址为
```shell
~/beginner/Helmet-Detection-YoloV5-master/helmet_yolov5
```
同目录下有yolov5s的预训练权重<font color='red'>helmet.pt</font>,可以直接用于detect，在同目录下命令为:
```shell
python detect.py --weights helmet.pt
```
### 后处理之非极大值抑制(Non-Max_Suppression,NMS)
- 在detect.py中，NMS对每一张图像经过Model后的若干锚框进行后处理，采用IoU_threshold和Confidence_threshold的方法消除重复的、置信度低的锚框，分析这一部分的代码很有必要，因为在实际部署中，Model结构是不变的，后处理部分可能需要进行改动。
    - detect.py中对NMS的实现方法为调用torchvision.ops.nms()；针对各个类别，根据class_confidence排序，并根据各锚框IoU(交并比)进行同类别抑制，最后排序输出。
### 后处理之预警提示
- 还没有完善的预警方案，但可以实现一个简单的预警功能。在plot_one_box()函数中增加判断条件，如果检测到person类，则在图片或视频的左上角打印“please wear helmet”的提示。也可以换成中文提示，需要下载对应的字体库到与plots.py同目录下。
### 模型部署
#### Git使用
现在已经在develop上开启的服务代码库在gitlab上，
```shell
git config -l //config list
git config --system --list // default config
git config --global --list //usually name & email
git add -> git commit -> git push  //upload
git pull -> git reset -> git checkout //download
git init //initialize
git branch +new branch
git checkout -b [new branch]
git push -u origin [new branch]
```
#### 在webrtc上进行部署
- helmet_detect已经部署在yjopenapi的webrtc下，server.py的视频处理类中增加了相应的elif分支，可任意传入一帧图像，返回一帧带检测框的图像，并且如果检测到没戴安全帽的人，则在画面左上角打印预警信息。
- 预训练权重未放在yjopenai库中，其路径为~/projects/detection/helmet.pt，如果权重路径被修改，则只需要在helmet_detct()函数中修改为绝对路径即可
- 对helmet_detect.py进行一下注释，阅读过程中完整的注释在webrtc/helmet_yolov5/detect.py中
```python
import cv2
import torch
import torch.backends.cudnn as cudnn
import numpy as np
from models.experimental import attempt_load
from utils.datasets import LoadStreams, LoadImages
from utils.general import  check_img_size, check_requirements, check_imshow, colorstr, non_max_suppression, \
    apply_classifier, scale_coords, xyxy2xywh, strip_optimizer, set_logging, increment_path, save_one_box
from utils.plots import colors, plot_one_box
from utils.torch_utils import select_device, load_classifier, time_sync
from utils.augmentations import letterbox

@torch.no_grad()                                             #不开启梯度计算
def helmet_detct(img0,
        weights = '/home/yjserver/projects/yjopenapi/webrtc/demo/helmet_yolov5/helmet.pt', #模型保存路径，一定要写绝对路劲！！！！！！！！！！！
        imgsz = 640,                                         #rescale后模型的大小
        conf_thres = 0.25,                                   #置信度阈值，在NMS中用到了两次，如果检测框有冗余，在不考虑重新训练模型的情况下，则考虑调大此数值
        iou_thres = 0.45,                                    #交并比阈值，一般不需要调整
        max_det = 1000,                                      #图片中检测框最大数量
        device  = '0',                                       #做推理的设备选择，0表示单GPU，cpu表示用CPU
        agnostic_nms = False,                                #不考虑类别的NMS，一般不开启
        augment = False,                                     #图片增强，推理时不开启
        line_thickness = 3,                                  #检测框的线型属性
        half = False,                                        #半精度推理，即从FP32调整到FP16，速度加快，但精度下降
        visualize = False,                                   #图片在模型中各个层的特征输出，推理时关闭
        classes = None,                                      #不考虑类别的NMS，一般不开启
        ):
    device = select_device(device)  #检查设备
    model = attempt_load(weights, map_location=device)  #生成yolov5s模型实例并加载权重
    stride = int(model.stride.max())  #Pretrain模型自身带有的步长属性
    names = model.module.names if hasattr(model, 'module') else model.names  #[persen,hat]对应索引为[0,1]
    if half:
        model.half()  #是否开启半精度
    imgsz = check_img_size(imgsz, s=stride)  #检查图片大小参数，并调整至步长的整数倍
    img = letterbox(img0, imgsz, stride=stride)[0]  #对原始图像进行padding和rescale
    img = img.transpose((2,0,1))[::-1]  #调整通道顺序
    img = np.ascontiguousarray(img)  #在底层行存储上使得图片像素连续
    if device.type != 'cpu':         #如果是GPU,则先用零矩阵刷一遍模型
        model(torch.zeros(1, 3, imgsz, imgsz).to(device).type_as(next(model.parameters())))
    img = torch.from_numpy(img).to(device)  #迁移图像至模型所在的设备
    img = img.half() if half else img.float()  #半精度
    img /= 255.0                               #图片像素归一化
    if len(img.shape) == 3:                    #增加batch维度
        img = img[None]
    pred = model(img, augment=augment, visualize=visualize)[0]  #模型预测，pred是若干个检测框；pred.shape是[batchsize,num_bbox,7]，7是xyxy+conf+cls_conf构成的维度
    pred = non_max_suppression(pred, conf_thres, iou_thres, classes, agnostic_nms, max_det=max_det)  #非极大值抑制操作，返回的是最合理的预测框列表，列表中每一个元素是xyxy+conf+cls组成的1x6列向量，这里的conf已经是去除了条件概率，是一个全概率，包括是否有物体和有物体情况下物体类别概率的相乘
    for i, det in enumerate(pred):     #遍历Pred中的预测框进行后处理
        if len(det):                   #如果经过NMS后仍有bbox
            det[:,:4] = scale_coords(img.shape[2:], det[:, :4], img0.shape).round() #将bbox尺寸转回到原图尺寸上
            for *xyxy, conf, cls in reversed(det):      #对bbox的1x6的列向量进行解包
                c = int(cls)                            #c是类别索引
                label =  f'{names[c]} {conf:.2f}'       #label包括类别名称和置信度取值，将输出在图片中
                plot_one_box(xyxy, img0, label=label, color=colors(c, True), line_thickness=line_thickness) #plot_one_box先只支持简单输出，并只支持英文输出，如果想要修改字体的话，可以下载对应的ttc文件，进一步修改cv2.PutText的fontStyle属性即可
    return img0

```
在server.py中，不仅要增加相应的分支，还要兼顾路径问题。因此在import处增加这些代码：
```python
import sys
sys.path.append('./helmet_yolov5')
from helmet_yolov5.helmet_detect import helmet_detct
```
#### 部署中存在问题概览
- 模型checkpoint一定要写绝对路径！！！！！！！！！
- 加载模型过后，仍然无法进行inference,但调试后仍然报错：
```python
AttributeError: 'Upsample' object has no attribute 'recompute_scale_factor'
```
    这也是为什么在beginner环境中可以跑通，但yjserver环境中跑不通的根本所在。一开始的时候，已经在beginner环境下修改了这个bug，但在yjserver下没有修改。
    修改本bug的方法：将/home/yjserver/projects/venv/lib/python3.10/site-packages/torch/nn/modules/upsampling.py", line 154的return Upsampling部分去掉。
    bug出现的原因：yolov5并不适配高版本的pytorch，但低版本的pytorch和cuda又不适配RTX3090，因此只能退而求其次，对源文件进行修改。
    bug的危险性：修改bug直接对Upsampling.py进行了修改，难以预测是否会影响到后续算法部署。
- 输出画面有卡顿延迟
    - yolov5s的参数虽然少，模型虽然小，但仍然需要1s的时间导入模型，因此每一帧图片都导入模型的话，肯定会出现延迟。
    需要改变server.py和helmet_detct.py的逻辑，将权重加载单独提取为一个load函数，提前进行checkpoint加载，在用户使用安全帽检测时，只调用detect函数,函数传入每一帧图片和已经导入参数的model。
    在server.py中改变的逻辑是，在服务开启时将model导入一次，并且以参数的形式送入VideoTransform类中，等服务down掉后，model会销毁。之后的模型部署也按类似的套路，若有更好的方法则更新之。
#### 在钢筋检测部署后，我们舍弃了安全帽检测的预训练模型，重新训练了一个Yolov5s V6.0模型，而直接从github下载的预训练模型是基于Yolov5 V5.0的。两者共同加载到内存的时候发生了错乱，模型中的Detect只能用最先导入的那部分。因此统一模型版本至V6.0，解决了此bug。怀疑此bug出现的原因与torch.load加载方法有关，但现阶段还没找到bug的具体来源。重新部署的安全帽检测模型后处理过程与更换之前无甚分别，只是模型内部进行了优化。
#### 模型精度评价
- 采用precision和recall综合评价一个多类别检测模型，两者分别反应了预测结果中符合真实标签的占比和真实标签中预测正确的占比。两者的计算公式如下：
$$precision=\frac{True\_positive}{True\_positive+False\_positive}$$
$$recall=\frac{True\_positive}{True\_positive+False\_negative}$$
如果想要综合评价precision和recall，可以用调和平均数做一个综合判断。调和平均数的意义是能够“惩罚”更拉胯的评价分数，得到的分数往往比算术平均值要低，目标检测中一般被叫做F1-score:
$$F1\_score=\frac{1}{\frac{1}{precision}+\frac{1}{recall}}$$
## 钢筋数量检测
- 经过对标注文件的检查，发现提供的数据集只有Train的部分可用，这部分的Annotations是完整的，Test的部分每张图片只有一根钢筋被标注了，不能用作训练集进行训练，用于validation也比较鸡肋。
- 采用最新版的yolov5 6.0, 很多层相较于之前的版本有改动，比如backbone新增了SPPF，这充分说明了yolov5还焕发着生机与活力。预训练模型基于coco数据集。
- 单卡3090训练，batchsize可以调整至64，丝滑无比。
- 利用cv2.puttext()在图片左上角简单输出图片中的钢筋数目
- 环境测试：之前部署安全帽检测的yolov5时对Upsampling.py的154行进行了修改，现将钢筋检测最新的Yolov5迁移到beginner环境下进行测试。
    - 需要pip intall Ipython psutil，detect.py可以对cv2.imread输入的图片进行预测。
- 我们拍摄的钢筋视角是在一捆钢筋的一端，数钢筋的端部。这也比较符合常理，因为人工也得这么数。钢筋之间挨得很近，但其实并不重合。这就暗含着我们可以在推测的时候，将IoU_thres调整地高一些。这样做最大的好处是消掉了一个低置信度锚框框住两个或多个钢筋的情况，另外置信度高的正确预测框也不会因此而被擦除。训练所用的图片只有250张，在epoch=200的训练中，很容易发生过拟合，得到梯度的局部最优解。在developer的实际部署过程中，也看到了误检(即False Negative)的现象，甚至把我的衣领和阴影都识别成了钢筋。现在的钢筋检测模型应用场景比较单一，对摄像头的角度和距离要求都比较高，两者与训练集越接近越好。


    <img src='https://pro-developer.oss-cn-shanghai.aliyuncs.com//202/img/1657261750250_rebar.jpg' width="700" alt="rebar" align=center /> <img src='https://pro-developer.oss-cn-shanghai.aliyuncs.com//202/img/1657261790344_count_rebar.jpg' width="700" alt="rebar" align=center />
