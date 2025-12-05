AIServer提供的功能与服务可以分为四大块:  
  1. 图像  
  2. 视频  
  3. 流  
  4. OCR  

# 图像
## 接口 
/detection/image  
## 输入
分两类:  
  1. 以http开头的网络图片地址  
  2. 用户本地上传oss后的路径，如 /74/sample.png  

## 检测方法  
1. edge(边缘检测),直接调用OpenCV的Canny方法  
2. crack(裂纹检测),调用yolov5的detect方法,预训练模型在 /WEIGHTROOT/crack.pth  
3. rebar(钢筋检测),调用yolov5的detect方法,预训练模型在 /WEIGHTROOT/rebar.pth  
4. mask(口罩检测),调用yolov5的detect方法,预训练模型在 /WEIGHTROOT/mask.pth
5. helmet(头盔检测),调用yolov5的detect方法,预训练模型在 /WEIGHTROOT/helmet.pth  
6. area(危险区域检测),调用yolov5的detect方法,预训练模型在 /WEIGHTROOT/dangerous_area.pth  
7. pedestrian(行人检测),预训练模型和危险区域用的同一个  
8. segmentation(语义分割),调用segmentation的seg_load和seg_infer,预训练模型在 /WEIGHTROOT/segmentation.pth
9. pano(全景) ，调用pano_xxx的方法，模型仍旧与1-8中的检测、分割一致，预训练模型在/WEIGHTROOT/。在pano_xxx函数中将全景大尺寸图片进行切分，输入到模型中进行相应的目标检测和语义分割。

## 输出
可以访问的图片URL地址, 如: https://onesight-developer.oss-cn-shanghai.aliyuncs.com/74/helloworld.jpg  
前端可以直接显示在页面上。

# 视频
## 下载
用户前端直传oss后，Server从OSS下载视频。
## 处理
用OpenCV读取视频文件，获取帧率、尺寸，根据检测方法对图片做检测，合成视频。合成进行中的视频会存放在PROJECT_ROOT/video_download/文件夹下，直至合成成功，上传到OSS上。整个过程阻塞运行。

## 上传
处理完的视频上传到OSS,生成前端可以访问的视频URL
## 删除
gpuservice后台开启video_monitor功能，每隔10分钟检查PROJECT_ROOT/video_download/文件夹下是否有卡住的视频，若有则删除。

# 流
## 流的查看
首先，拿到视频流的内网地址。然后，在Server上用FFmpeg读取，转化成.m3u8文件(动态视频文件)。通过穿透，让客户端的web播放器从外网访问到.m3u8文件。最后当播放器关闭或网页关闭时，杀掉FFmpeg进程。

## 流的检测
现在的问题是，用共享内存结合线程锁的方式，在flask框架下未做非阻塞的部署实践和测试，或许可以采取OCR多进程相似的方式，其中基于yolo的视频流实现已经放在了gitlab上，地址为：git@192.168.50.77:Niecong/rtsp-yolo.git. 当时存在的问题是过度占用cpu问题，但其实后来发现是被挖矿注入了后台程序，因此此实现应该不存在问题，可以重新进行部署和测试；后续也可以参考yolo的Stream拉流方式，通过多线程实现拉流、识别和推流。

# OCR
前端用户直传图片到OSS,Server从OSS处拿到图片，调用extract_words方法。该方法是部分修改了PaddleOCR2Pytorch的ocr方法，而所使用的模型也是来自Paddle。最后生成一张原图与文字识别图拼接的图片，以及一个储存了提取出的文字的txt。

# 镜现
和调度服协定的接口规范参看 [外问业务服](https://dev.yijianar.com/open-doc?fileId=751590822)  
分成0图像、1视频、2OCR三种类别。目前只有OCR完成对接。
OCR识别模型与开发者平台相同，皆为最轻量化的检测模型、识别模型与角度模型的组合。后处理中包含Binary-kmeans进行异常值剔除。整个后处理流程在PROJECT_ROOT/cad_ocr/extract_real_size.py中，每个函数皆有详细的注释，若需优化，可从main函数开始阅读。另外，为了适配flask下pytorch开启多进程提高处理速度的需求，以命令行的形式开启了非阻塞的多进程。

# 显存使用率占用监控

由于整个gpuservice架设在一台RTX3090上，难免会因为日常工作和对外服务使得显存吃满。因此采用pynvml进行显存监控，逻辑是如果显存占用超过20G，则非阻塞地响应开发者平台的用户请求：请稍后再试。

# 图像算法后续调整与迁移训练参数详解

现在已上线或在测试中的算法有目标检测、语义分割、OCR、超分等模型。现省略对此四种模型原理的解析，针对需要了解或可能需要调节的参数进行介绍。

## 目标检测

开发者平台上的检测任务皆基于yolov5s。整个yolo系列的模型架构相似，训练参数相似，后处理部分几乎完全相同。模型架构可参考github的实现，模型迁移训练可以参考https://docs.ultralytics.com/。后处理部分可参考如下带注释的代码部分，便于快速了解：

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
        weights = '/home/yjserver/projects/yjopenapi/webrtc/demo/helmet_yolov5/helmet.pt', #模型保存路径
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

## 语义分割

语义分割采用bisenet模型，这是一个轻量化的2D语义分割模型。需要搞清楚后处理流程。特别地，因为很可能后续需要迁移训练，这里仍需要介绍自定义的dataset模块，方便针对更多的像素类别分类进行修改补充。

后处理函数，在PROJECT_ROOT/segmentation/seg_inference.py中：

```python
def seg_infer(img,net,palette):
        im = img
        #np.random.seed(123)
        #palette = np.random.randint(0, 256, (7, 3), dtype=np.uint8) #7 是类别数
        to_tensor = T.ToTensor() #转换为torch.tensor
        im = im[:,:,::-1] #BGR->RGB
        im = to_tensor(dict(im=im, lb=None))['im'].unsqueeze(0).cuda() #转移到gpu
        org_size = im.size()[2:] #进行resize处理
        new_size = [512,512]
        im = F.interpolate(im, size=new_size, align_corners=False, mode='bilinear')
        #time0 = time.time()
        out = net(im)[0] #得到预测结果
        out = F.interpolate(out, size=org_size, align_corners=False, mode='bilinear') #进行尺寸回算
        out = out.argmax(dim=1)
        out = out.squeeze().detach().cpu().numpy()
        #time1 = time.time()
        #print(f'time:{time1-time0}')
        #0=background,1=floor,2=wall,3=ceiling, 4=door ,5=window,6=column
        pred = palette[out]
        alpha = 0.4
        beta = 1-alpha
        gamma = 0
        img = cv2.addWeighted(img,alpha,pred,beta,gamma) #原图与像素分类图进行叠加
        img = draw_legend(img,palette)
        return img
```

自定义的dataset，在PROJECT_ROOT/segmentation/data/customer_dataset.py中

```python
import segmentation.lib.data.transform_cv2 as T
from segmentation.lib.data.base_dataset import BaseDataset
import numpy as np


class CustomerDataset(BaseDataset):

    def __init__(self, dataroot, annpath, trans_func=None, mode='train'):
        super(CustomerDataset, self).__init__(
                dataroot, annpath, trans_func, mode)
        self.lb_ignore = 255
        self.n_cats = 7 # 这里的类别数对应着现在的 门 窗 柱 地板 天花板 墙 未知
        self.lb_map = np.arange(256).astype(np.uint8)
        #### 这里的lb_map需要根据不同的数据集进行改动，改动方式是：
        #### 语义分割的数据经过labelme标注工具标注完成后，生成的标签文件实际上是不同的色彩，
        #### 下面的 0 38 75 113 14 52 89是labelme对于7类物体标注的色彩标签，因此,
        #### 这个标签的个数、中括号的key值都需要根据labelme得出的标签图片来实际确定
        
        
        self.lb_map[0] = 0
        self.lb_map[38] = 1
        self.lb_map[75] = 2
        self.lb_map[113] = 3
        self.lb_map[14] = 4
        self.lb_map[52] = 5
        self.lb_map[89] = 6

        self.to_tensor = T.ToTensor()
```

## OCR

现在OCR上线的有两部分，皆为基于PaddleOCR预训练模型：单纯的提取图纸中的文字，以行的形式进行归类，并生成txt文件；另外是对镜现OCR图纸真实尺寸映射的支持，这两部分都在开发者平台项目中，分配了不同的接口。由于CAD图纸上的文字比较规整，且PaddleOCR的模型优化已达到完全可用的程度，后续除非有难以辨认的手写字体需要迁移训练，否则不建议重新训练模型，反而直接应用PaddleOCR即可。

现针对OCR的应用和后处理进行介绍。

OCR的应用在PROJECT_ROOT/cad_ocr/PaddleOCR2Pytorch/tools/predict_system.py中，而其参数设置在PROJECT_ROOT/cad_ocr/PaddleOCR2Pytorch/tools/predict_utility.py中

其中，OCR的全套识别集成为了一个text_sys，具体识别代码如下：

```python
def patches_recognize(patches):
    args = utility.parse_args()
    # patches 是一个字典
    text_sys = TextSystem(args)
    # True 为测试开启，实际在Pipeline工作中将其关闭
    is_visualize = True
    font_path = args.vis_font_path
    # drop_score在pipeline中也需要
    drop_score = args.drop_score
    coord_boxes_text_score_dict = {}
    #sum = 0
    for coord, patch in patches.items():
        # dt_boxes为list, 内含sub_list，为顺时针转一圈的坐标
        # rec_res为list, 内含sub_tuple, 第一个元素为text, 第二个元素为score
        dt_boxes, rec_res = text_sys(patch)
        #sum += len(dt_boxes)
        coord_boxes_text_score_dict[coord] = [dt_boxes, rec_res]
        #print(coord_boxes_text_score_dict)
        if is_visualize:
            image = Image.fromarray(cv2.cvtColor(patch, cv2.COLOR_BGR2RGB))
            boxes = dt_boxes
            txts = [rec_res[i][0] for i in range(len(rec_res))]
            scores = [rec_res[i][1] for i in range(len(rec_res))]
            draw_img = draw_ocr_box_txt(
                image,
                boxes,
                txts,
                scores,
                drop_score=drop_score,
                font_path=font_path)
            draw_img_save = f"{PRJHOME}/CAD_OCR/PaddleOCR2Pytorch/inference_results/test/"
            if not os.path.exists(draw_img_save):
                os.makedirs(draw_img_save)
            cv2.imwrite(
                os.path.join(draw_img_save, f'{coord}_test.png'),
                draw_img[:, :, ::-1])
    #print(coord_boxes_text_score_dict)
    return coord_boxes_text_score_dict
```

开发者平台上文字提取的后处理部分十分简单，只需要将检测出的文字进行后续的拼接合成。对接镜现的OCR部分比较复杂，需要用精细化的手段统计OCR输出的文字，后处理代码在PROJECT_ROOT/cad_ocr/extract_real_size.py中，内部的参数介绍非常详细，可从main函数开始查看。

# 目标检测、语义分割的边缘端部署

尝试将yolov5的onnx模型导入unity的barracuda框架中进行边缘端的部署，并在公司的安卓测试机上进行测试。测试发现，精度并没有任何损失，但FPS只有3。后续如果有边缘端部署的需要，可考虑在M1芯片上进行测试，应该效果会好非常多；另外可以用最新的yolov8-tiny模型，模型参数量进一步下降，速度会有进一步提升；还可以考虑修改网络结构，根据barracuda作者的建议，多用depthwise的卷积，减少全卷积，会大大减少嵌套循环的数量，减少计算量，但需要花费时间人为修改模型，重新训练。

对于bisenet语义分割，由于模型参数量多，还远远不到可以边缘端部署的程度。后续需要考虑剪枝和模型蒸馏，进一步缩小模型尺寸。

由于unity-barracuda的目标检测项目非常大，为了保证项目的完整性，将此项目整体上传到了nas上面。具体路径为：聂聪/yolov5-unity-main.zip，内置了目标检测和语义分割的C#实现。其中的C#代码，关键的每一行基本都有注释，方便查阅和修改。