# 文生图
文生图模型我们采用最新的FLUX模型，为了兼顾生成速度，我们用它的衍生版 flux1-dev-Q8_0.gguf。  
参数只保留prompt(必须)，width(非必须)，height(非必须)。  
width,height不再设限,可以是1920x1080,1536x1536等。但目前也不考虑极端值。  
prompt只有positive,没有negative。且不需要额外写detailed, 4k...等。

# 图生图
定义的功能，如果有FLUX模型的workflow, 我们也优先用FLUX. 不然仍旧用Stable Diffusion系列的模型和workflow。

# 队列
我们统一用Comfyui提供的队列。如果用Comfyui的web页面，能看到现在队列中(排队)的有多少任务。

# 进度
这块之前没有实现，只是用了一张"进行中"的图。  
进度具体可以分队列进度和生图进度。队列进度用restful api可以很快查询。 生图进度要用websocket实现传输。

# 测试服务器
api baseurl用https://aigc.cirray.cn/comfyui/(国内), 而非www.bongos.ai(海外)。  
单独创建分支发在test.bongos.ai上。

# json参数
前端需要先拿到workflow.json,每个功能点对应一个(或多个)workflow。  
修改json参数里的相关值(如prompt)再POST。  
这些workflow都需要先在GPU上调试通过。

# 接口列表
## POST /prompt
生图
```python
with open('fluxq8.json') as f:
    data = json.load(f)
data["46"]["inputs"] = {"width":1920,"height":1080,"batch_size":1}
data["47"]["inputs"]["text"] = "The irridescent inside of a large alien ship. Photo quality color perfect lighting"
resp = httpx.post(f"{url}/prompt", json={"prompt":data}, verify=False)
prompt_id = resp.json()['prompt_id']
print(prompt_id)
```
拿到workflow.json, 修改 prompt,width,height参数，POST给 {url}/prompt, 拿到prompt_id。  
生图都用这个接口。  

## GET /prompt
队列(排队)信息
```python
resp = httpx.get(f"{url}/prompt", verify=False)
resp.json()
```

## GET /history/{prompt_id}
生图是否出结果
```python
resp = httpx.get(f"https://aigc.cirray.cn/comfyui/history/{prompt_id}", verify=False)
respjson = resp.json()
if respjson:
    outputimg = respjson[prompt_id]["outputs"]["63"]["images"][0]["filename"]
else:
    outputimg = ""
```

## outputimg
查看结果图片
```python
f"https://aigc.cirray.cn/comfyui/view?filename={outputimg}&type=output"
```

# websocket
客户端可以直接拿到进度和图像数据。这样图片文件可以不用再存储GPU服务器，会更经济高效一些。
```python
ws = websocket.WebSocket()
ws.connect("ws://{}/ws?clientId={}".format(server_address, client_id))
# 通过 POST /prompt 拿到 prompt_id
output_images = {}
current_node = ""
while True:
    out = ws.recv()
    if isinstance(out, str):
        message = json.loads(out)
        if message['type'] == 'executing':
            data = message['data']
            if data['prompt_id'] == prompt_id:
                if data['node'] is None:
                    break # Execution is done
                else:
                    current_node = data['node']
    else:
        if current_node == 'save_image_websocket_node':
            images_output = output_images.get(current_node,[])
            images.output.append(out[8:])
            output_images[current_node] = images_output
```
这种方式会增加一些调用难度，但值得尝试。
