# 定制训练
搜集数据集，训练一个定制的YOLOv5模型来识别对象。
1. 收集图像数据集并标记数据集。  
2. 导出数据集到YOLOv5。  
3. 训练YOLOv5来识别对象。  
4. 评价YOLOv5模型性能。  
5. 运行测试推理来查看模型。
## 安装依赖
```shell
git clone https://github.com/ultralytics/yolov5
cd yolov5
pip install -r requirements.txt
```
## 组装数据集
