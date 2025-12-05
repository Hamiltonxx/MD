# 数据格式
OSBIM是以见科技用来描述从三维建模软件导出的几何信息的一种文件格式。通过将Revit、Bentley以及其他格式的BIM文件中的几何、位置、材质、属性等信息整体抽取并重新组织为OSBIM数据格式的方式，来实现多格式文件统一为单格式并在以见软件平台上统一展示和应用。  
下图为OSBIM的数据格式，分为两部分来描述  
上半部分是文件结构，是最后输出的osbim文件夹的标准本事  
下半部分是对象结构，是在建模软件中提取数据后需要构件的对象层次，并且主对象最终会被用于序列化为osbim的主文件  
![OSBIM](https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1677045224650_OSBIM_Flowchart.jpg)

对象结构Scene下有三个对象分别是：Assets, Objects, Metadata。
## Assets
存储该模型中的Mesh、Material、Texture资源信息和对应的链接位置

## Objects
存储该模型中的几何构件位置、几何信息和BIM属性，其中形状和材质通过引用Assets中的ID的方式记录，其他属性直接在Objects内记录值

## Metadata
存储全局参数、基点属性等全局信息

# 文件代码示例
## 样例文件
[OSBIM测试.zip](https://pro-developer.oss-cn-shanghai.aliyuncs.com/res/OSBIM%E6%B5%8B%E8%AF%95.zip)
## 数据格式示例
```json
{
  "Assets": {
    "MeshData": [
      {"Id": 0, "VCnt": 504, "Url": "~测试/O/0_504_888.osmesh", "VertexLayout": [], "IndexLayout": [], "MatIdLayout": []},
      {"Id": 1, "VCnt": 72, "Url": "~测试/O/1_72_132.osmesh", "VertexLayout": [], "IndexLayout": [], "MatIdLayout": []},
      {"Id": 2, "VCnt": 72, "Url": "~测试/O/2_72_120.osmesh", "VertexLayout": [], "IndexLayout": [], "MatIdLayout": []},
      ......
      {"Id": 33, "VCnt": 24, "Url": "~测试/O/33_24_36.osmesh", "VertexLayout": [], "IndexLayout": [], "MatIdLayout": []}
    ],
    "TextureData": [
      {"Url": "~测试/T/1.jpg", "Id": 1},
      {"Url": "~测试/T/2.jpg", "Id": 2},
      {"Url": "~测试/T/3.jpg", "Id": 3}
    ],
    "MaterialData": [
      {"Name": "default material","TexData": [],"Id": -1,"Transparent": 1.0,"Gloss": 0.0,"TillingX": 1.0,"TillingY": 1.0,"OffsetX": 0.0,"OffsetY": 0.0,"Color": [120.0,120.0,120.0,255.0]},
      {"Name": "default material for room","TexData": [],"Id": 0,"Transparent": 0.7,"Gloss": 0.0,"TillingX": 1.0,"TillingY": 1.0,"OffsetX": 0.0,"OffsetY": 0.0,"Color": [120.0,120.0,120.0,178.5]},
      ......
      {"Name": "外墙钢板","TexData": [],"Id": 316731,"Transparent": 1.0,"Gloss": 0.0,"TillingX": 1.0,"TillingY": 1.0,"OffsetX": 0.0,"OffsetY": 0.0,"Color": [161.0,161.0,160.0,255.0]},
      {"Name": "路","TexData": [{"Id": 3,"TextureType": 0}],"Id": 435007,"Transparent": 1.0,"Gloss": 0.0,"TillingX": 1.0,"TillingY": 1.0,"OffsetX": 0.0,"OffsetY": 0.0,"Color": [255.0,255.0,255.0,255.0]}
    ]
  },
  "Objects": [
    {
      "Name": "测试",
      "MeshId": -1,
      "Volume": 0.0,
      "P": [0.0,0.0,0.0],
      "R": [0.0,0.0,0.0],
      "S": [1.0,1.0,1.0],
      "O": [0.0,0.0,0.0],
      "C": [],
      "Children": [
        {
          "Name": "1F(±0.000)",
          "MeshId": -1,
          "Volume": 0.0,
          "P": [0.0,0.0,0.0],
          "R": [0.0,0.0,0.0],
          "S": [1.0,1.0,1.0],
          "O": [0.0,0.0,0.0],
          "C": [],
          "Children": [
            {
              "Name": "Floors",
              "MeshId": -1,
              "Volume": 0.0,
              "P": [0.0,0.0,0.0],
              "R": [0.0,0.0,0.0],
              "S": [1.0,1.0,1.0],
              "O": [0.0,0.0,0.0],
              "C": [],
              "Children": [
                {
                  "Id": "e8db5a56-38b7-4e74-934d-1673f9431ac5-0008f60e",
                  "Name": "设备基础 - 150mm_587278",
                  "MeshId": 0,
                  "Volume": 28.6885376,
                  "P": [0.0,0.0,0.0],
                  "R": [0.0,0.0,0.0],
                  "S": [1.0,1.0,1.0],
                  "O": [-24.0,0.075,70.5593],
                  "C": [
                    {
                      "DataType": 1,
                      "Name": "BimData",
                      "K": ["Uuid","Guid","IntegerValue","LevelId","CategoryId","Category","Element"],
                      "V": ["e8db5a5638b74e74934d1673f9431ac50008f60e","413ea2e6-a7bf-4133-9536-9ac2b8583723","587278","311","-2000032","Floors","设备基础 - 150mm"]
                    }
                  ],
                  "Children": [],
                  "ObjMat": [
                    {"Id": 758}
                  ]
                },
                {
                  "Id": "8e1e97ad-2e4a-4f84-85eb-c1befe00adb6-000ae859",
                  "Name": "卵石散水_714841",
                  "MeshId": 6,
                  "Volume": 1.19535577,
                  "P": [0.0,0.0,0.0],
                  "R": [0.0,0.0,0.0],
                  "S": [1.0,1.0,1.0],
                  "O": [-33.5500031,-0.25,70.5593],
                  "C": [
                    {
                      "DataType": 1,
                      "Name": "BimData",
                      "K": ["Uuid","Guid","IntegerValue","LevelId","CategoryId","Category","Element"],
                      "V": ["8e1e97ad2e4a4f8485ebc1befe00adb6000ae859","0b1f21a6-e1a9-4cf6-beda-b14aa4b132e7","714841","311","-2000032","Floors","卵石散水"]
                    }
                  ],
                  "Children": [],
                  "ObjMat": [
                    {"Id": 702218}
                  ]
                },
                ......
              ],
              "ObjMat": []
            },
            {
              "Name": "Walls",
              "MeshId": -1,
              "Volume": 0.0,
              "P": [0.0,0.0,0.0],
              "R": [0.0,0.0,0.0],
              "S": [1.0,1.0,1.0],
              "O": [0.0,0.0,0.0],
              "C": [],
              "Children": [
                {
                  "Id": "e8db5a56-38b7-4e74-934d-1673f9431ac5-0008fbef",
                  "Name": "内墙200_588783",
                  "MeshId": 1,
                  "Volume": 31.88218,
                  "P": [0.0,0.0,0.0],
                  "R": [0.0,0.0,0.0],
                  "S": [1.0,1.0,1.0],
                  "O": [-13.95,2.725,75.92515],
                  "C": [
                    {
                      "DataType": 1,
                      "Name": "BimData",
                      "K": ["Uuid","Guid","IntegerValue","LevelId","CategoryId","Category","Element"],
                      "V": ["e8db5a5638b74e74934d1673f9431ac50008fbef","cbee1623-effd-43c7-b752-0180bec8cb04","588783","311","-2000011","Walls","内墙200"]
                    }
                  ],
                  "Children": [],
                  "ObjMat": [
                    {"Id": 318711}
                  ]
                },
                {
                  "Id": "e8db5a56-38b7-4e74-934d-1673f9431ac5-0008fc55",
                  "Name": "内墙200_588885",
                  "MeshId": 2,
                  "Volume": 12.7784643,
                  "P": [0.0,0.0,0.0],
                  "R": [0.0,0.0,0.0],
                  "S": [1.0,1.0,1.0],
                  "O": [-33.05,2.67252326,70.5593],
                  "C": [
                    {
                      "DataType": 1,
                      "Name": "BimData",
                      "K": ["Uuid","Guid","IntegerValue","LevelId","CategoryId","Category","Element"],
                      "V": ["e8db5a5638b74e74934d1673f9431ac50008fc55","a3860588-2ee0-4d0c-aaad-f14b729f4f34","588885","311","-2000011","Walls","内墙200"]
                    }
                  ],
                  "Children": [],
                  "ObjMat": [
                    {"Id": 318711}
                  ]
                },
                ......
              ],
              "ObjMat": []
            },
            {
              "Name": "Doors",
              ......
            },
            {
              "Name": "Windows",
              ......
            }
          ],
          "ObjMat": []
        }
      ],
      "ObjMat": []
    }
  ],
  "MetaData": {
    "Version": 1,
    "GlobalParam": {
      "Name": "BimGlobalParam",
      "K": ["Addin Version"],
      "V": ["3.19"]
    },
    "BimPoints": {
      "Name": "BimPoints",
      "K": ["SurveyPoint","BasePoint","ByPoint"],
      "V": ["0,0,0","0,0,0","0"]
    },
    "BimBoundingBox": {
      "Name": "BimBoundingBox",
      "K": ["minX","minY","minZ","maxX","maxY","maxZ"],
      "V": ["-40.59999","-0.35","47.275","-13.7","11.1","90.72501"]
    }
  }
}
```
