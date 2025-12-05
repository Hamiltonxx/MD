# 概览
ChatGPT分三块:  
1. GPT-3, 执行自然语言任务 (有的感情的语言机器)  
2. CodeX, 自然语言翻译成代码 (码农失业加速器)  
3. DALL.E, 创建和编辑图像 (言生图)  

## 模型与Token
OpenAI提供了很多模型，有自然语言生成、代码生成、语义搜索和分类等。付费使用，可以让你定制。  
completion endpoint是API的核心部分。你输入文本作为prompt,比如"用js写一段快速排序算法",它给你返回一段代码。设计好prompt是用好模型的基础，通常提供一些指令或示例。  
模型把文本分解成token来理解和处理。token可以是字、单词或者几个字母。一个请求里的token数是输入数和输出数的总和。  
可以简单认为token数就是字数。在英文里，1000个token等于4000个字母或750个单词。 token数 = 单词数 x 4/3  
大多数模型的token数限制是2048,也就是1500单词。我们常用的text-davinci-003则是4000token, code-davinci-002是8000token。你的输入和输出总和不能超过这个限制。
## 接口调用
```python
import openai
openai.api_key = chatgpt_token
prompt = "用js写一段快速排序算法"
model = "text-davinci-003"  # "davinci", "code-davinci-002"
max_tokens = 4000
temperature = 0
completion = openai.Completion.create(prompt=prompt,temperature=temperature,max_tokens=max_tokens,model=model)
resptext = completion.choices[0].text
print(resptext)
```
## 图片模型定价
| 分辨率 | 价格 |
| --- | --- |
| 1024x1024 | $0.02/图 |
| 512x512 | $0.018/图 |
| 256x256 | $0.016/图 |  

## 语言模型定价
| 模型 | 价格 | 特点 |
| --- | --- | --- |
| Ada | $0.0004/一千token | 最快 |
| Babbage | $0.0005/一千token | |
| Curie | $0.002/一千token | 比Davinci快 |
| Davinci | $0.02/一千token | 最强大 |  

如果只想要最好的结果，就用Davinci。
## 微调(fine-tuned)模型定价
提供你的训练数据，微调(fine-tune)OpenAI的基础模型来创建你的自定义模型。这时只针对你请求里的token数来定价。

# 接口合一
为了方便起见，我们希望用一个接口、一个模型来处理全部文本问题。  
这带来一些问题:  
  1. 如何区别答案是文本和代码。因为是代码的话我们希望能高亮显示  
  2. 如何分段等答案的展示样式问题

<img src="https://pro-developer.oss-cn-shanghai.aliyuncs.com/74/img/1677488994433_chatgptapi.png" width="60%"></img>  
调了一些接口后，发现答案好像是Markdown格式。  
这下就好办了，因为可以复用我们的Markdown2HTML。

# ~~上下文语境~~
持续对话时，我们通常希望聊天机器人能记住上下文语境。  
一种简单的做法是, 把前面的“问”和“答”全都加入下一次的“问”中
```python
prompt = "chat message 1\\n" + "chat message 2\\n" + ... + "your last message\\n"
# 别忘了在 openai.Completion.create里设置stop变量
stop = ["\\n"]
```
这时会有一个问题，对话数过多时，Token数限制很容易被超，怎么办？ 我们设计一个简单的算法，伪代码如下
```
1. 用户发送(Question/Q), ChatGPT回答(Answer/A)
2. 缓存 Q/A pair
3. 数组里最多只缓存就近的3组，且倒序
4. 3的数组和新Q拼接成prompt字符串, 来调用接口
5. 重复 1
```

# GPT-3.5


# 注意事项
1. 由于是调用国外接口，网络可能会有波动。加上接口调用太火爆，可能会遇到调用不成功的情况，这时需要前端再次发起请求。  
2. 