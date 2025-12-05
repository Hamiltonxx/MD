# Using Stable Diffusion with Python

https://reader-service.fcdn.sk/?source=5355b6910ef782771cc6d6e9ea3ed95ef59854e77bcf6778f7c879c4e25f4b9d&download_location=https%3A%2F%2Fsinglelogin.re%2Fdl%2F28815544%2Fe4ba7b

# Introduction

Stable Diffusion 的关键特征:

1. 根据文本描述生成高质量的图片
2. diffusion 过程是更稳定可靠的生图方式
3. 有 10K+预训练的模型可用
4. 大量的新研究和新应用
5. 开源

## Diffusion 模型演变

长久以来计算机视觉领域的机器学习都是被 CNN 和 ResNet 主导。 它们能高效完成对象识别和人脸识别等任务。 这些模型也被广泛应用于工业、农业、自动驾驶等。  
但这些模型有个明显的缺陷: 它们识别的东西局限于训练数据集。 要识别一个新物体，必须先到训练数据集里增加新标签，再重新训练和微调。  
Google 开发的 Transformer 模型，使计算机视觉领域发生了革命性变化。 它不像传统方法那样，依赖于预定义标签、计算损失、通过反向传播更新神经网络权重，Transformer 模型以及 Attension 机制，用训练数据本身实现训练和打标签。  
研究和工程人员迅速意识到 Transformer 模型的巨大潜力。 在一篇著名的机器学习论文《Attention Is All You Need》里，作者写道:
We are excited about the future of Attention-based models and plan to apply them to other tasks. We plan to extend the Transformer to problems involving input and output modalities other than text and to investigate local, restricted Attention mechanisms to efficiently handle large inputs and outputs such as images, audio, and video.  
OpenAI 的研究人员抓住了这股力量，创建了 CLIP 模型。 使用 Attention 机制和 Transformer 模型来训练一个图像分类模型。 该模型在无需标签数据的情况下分类各种图像。 它是第一个大规模图像分类模型， 由取自 internet 的 400M 组图像-文字训练而成。 确实，规模起了关键作用。 之前其他模型只用了 200K 图片，而 CLIP 团队则用了 400M，当然图片来自 public internet。 结果令人大吃一惊，CLIP 使得图像识别和分割不再受限于预定义标签。 鉴于 CLIP 大规模模型带来的变化，研究人员开始思考是否也可用于文本生成图像。  
如果仅仅用 CLIP,我们仍然无法通过文字生成逼真图像。于是 2020 年 6 月，Jonathan 等人发表了论文《Denoising Diffusion Probabilistic Models》,介绍了一种基于 diffusion 的概率模型，可用来图像生成。 diffusion 一词借用了热力学术语，原义是粒子从高密度区域向低密度区域运动。 机器学习研究人员借鉴这个概念去噪和采样。 从一张噪声较多的图像开始，逐步去除噪声来优化图像，逐渐转换为更清晰版本。 模型也称 denoising diffusion probabilistic model.
方法很巧妙，给定任意图片，一定数量正态分布噪声图片叠加到原始图片， 使它变成完全噪声化图片， 然后在 CLIP 模型指导下，训练一个模型来逆转这个 diffusion 过程。  
这样，OpenAI 在 2022 年 4 月推出了 DALL-E 2, 世人被它创造图像的能力和质量所震撼。 又过几个月，Stability AI 发布了 DALL-E 2 的开源竟品 Stable Diffusion.

# diffusers

```python
import os
os.environ["HF_TOKEN"] = ""
import torch
from diffusers import StableDiffusion3Pipeline

pipe = StableDiffusion3Pipeline.from_pretrained("stabilityai/stable-diffusion-3-medium-diffusers", torch_dtype=torch.float16)
pipe = pipe.to("cuda")

image = pipe("A cat holding a sign that says hello world", negative_prompt="", num_inference_steps=28, guidance_scale=7.0,).images[0]
image.save('hellowordcat.jpg','JPEG')
```

## Guidance scale

Guidance scale or Classifier-Free Guidance(CFG) is a parameter that controls the adherence of the generated image to the text prompt. A higher guidance scale will force the image to be more aligned with the prompt, while a lower guidance scale will give more space for Stable Diffusion to decide what to put into the image.  
typically between 7 and 8.5.

# Theory behind Diffusion Models

forward diffusion

```python
import numpy as np
import matplotlib.pyplot as plt
from PIL import Image
from itertools import accumulate

def get_product_accumulate(numbers):
    return list(accumulate(numbers, lambda x,y: x*y))

# Load an image
image = plt.imread("dog.png")
image = image * 2 - 1

num_iterations = 16
beta = 0.05
betas = [beta]*num_iterations

alpha_list = [1 - beta for beta in betas]
alpha_bar_list = get_product_accumulate(alpha_list)

target_index = 5
x_target = (np.sqrt(alpha_bar_list[target_index]) * image + np.sqrt(1-alpha_bar_list[target_index]) * np.random.normal(0,1,image.shape))
x_target = (x_target+1)/2
x_target = Image.fromarray((x_target*255).astype('uint8'), 'RGB')
x_target.save('noiseddog.jpg')
```

# How Stable Diffusion Works

In short, latent space is the vector representation of an object.

## step-by-step process

1. Inputs: The model is trained using images, caption text, and time step embeddings
2. Image encoder: The encoder is a neural network that processes the input image and converts it into a more abstract and compressed representation - latent space.
3. Latent space: The encoder outputs a vector that represents the input image in the latent space. The latent space is typically a lower-dimensional space than the input space (the pixel space of the image), which allows for faster processing and more efficient representation of the input data. The whole training happens in the latent space.
4. Iterate N steps: The training process involves iterating through the latent space multiple times (N steps). This iterative process is where the model learns to refine the latent space representation and make small adjustments to match the desired output image.
5. UNet: After each iteration, the model uses UNet to generate an output image based on the current latent space vector. UNet generates the predicted noise and incorporates the input text embedding, step information, and potentially other embeddings.
6. The loss function: The model’s training process also involves a loss function. This measures the difference between the output image and the desired output image. As the model iterates, the loss is continually calculated, and the model makes adjustments to its weights to minimize this loss. This is how the model learns from its mistakes and improves over time.

Stable Diffusion not only supports text-guided image generation; it also supports image-guided generation.  
When we provide a text input, Stable Diffusion uses CLIP to generate an embedding vector, which will be fed into UNet, using the attention mechanism.  
When we provide an image as the guiding signal, the input image will be encoded to latent space and then concatenate with the randomly generated Gaussian noise.

With the two essential inputs provided text embeddings and the initial image latent noise, UNet kicks off to remove noise from the initial image in the latent space. After several denoising steps, with the help of a decoder, Stable Diffusion can output a vivid image in pixel space.

## Generating latent vectors using diffusers

1. Load an image
2. Pre-process the image
3. Load image data with torch and move to CUDA
4. Load the Stable Diffusion image encoder VAE
5. Encode the image into a latent vector
6. Decode latent to image

## Generating text embeddings using CLIP

To generate the text embeddings(文本转换为数值向量), we need first to tokenize the input text or prompt and encode the token IDs into embeddings

1. Get the prompt token IDs
2. Encode the token IDs into embeddings
3. Generate embedding for negative prompt embeddings
4. Concatenate prompt and negative prompt embedding into one vector

## Initializing the Stable Diffusion UNet

Before the UNet architecture, a convolution network was commonly used for image classification tasks. When using a convolution network, the output is a single class label. However, in many visual tasks, the desired output should include localization too, and the UNet model solved this problem.  
UNet's skip connections directly combine feature maps from different stages, allowing a model to effectively propagate information across various scales. This is crucial for denoising, as it ensures the model retains both fine-grained details and global context during noise removal. These features make UNet a good candidate for the denoising model.

# Using Stable Diffusion Models

```python
from diffusers import StableDiffusionPipeline
pipe = StableDiffusionPipeline.from_pretrained("modelid", torch_dtype=torch.float16, cache_dir="my_model_folder")
```

By default, the cache files will be stored in \home\user_name\.cache\huggingface\hub.
By default, PyTorch uses torch.float32 for matrix multiplications. For model inference, or at the stage of using Stable Diffusion to generate images, we can use the float16 type to not only increase the speed by about 100% but also save GPU memory with almost unnoticeable difference.  
Model types: .ckpt; .safetensor; Diffusers files  
The diffusers format takes the form of a folder that encompasses both weights and configuration files. When we use the Diffusers auto download function, Diffusers will store the files in the Diffusers format.

# Object Editing and Style Transfer

CLIPSeg: to detect the content of an image;  
Rembg: removes the background of an image;  
IP-Adapter: transfer the style from one image to another.

Before we can edit anything, we need to identify the boundary of the object we want to edit.  
To obtain the background mask, we will use the CLIPSeg model. CLIP is a powerful pre-trained model that learns to connect text and images.  
We can provide CLIPSeg with a simple description such as "the background of this picture", and CLIPSeg will return the mask of the targeted objects.

# Using Textual Inversion(TI)

Unlike LoRA, which is a fine-tuning technique applied to the text encoder and UNet attenction weights, TI is a technique to add new embedding space based on the trained data.

# ControlNet Tile image upscaling

ControlNet bears similarities to the image-to-image Stable Diffusion pipeline but boasts significantly greater potency.  
ControlNet employs one or more supplementary UNet models that work alongside the Stable Diffusion model. These UNet models process both the input prompt and the image concurrently, with results being merged back in each step of the UNet up-stage.  
When compared to the image-to-image upscaler, ControlNet Tile incorporates even more details, one step, and relatively lower VRAM usage.

In many ways, ControlNet is similar to the image-to-image pipeline, both image-to-image and ControlNet can be used to enhance images.  
However, ControlNet can "control" the image in a more precise way. Image you want to generate an image that uses a specific pose from another image or perfectly align objects within the scene to a specific reference point. This kind of precision is impossible with the out-of-the-box Stable Diffusion model.  
Besides, ControlNet models work with all other open source checkpoint models.

## Control the uncontrolled power of Stable Diffusion

ControlNet can detect edge, depth map or posture skeleton in your input image, combined with text prompts, can precisely guide the generation of images in Stable Diffusion.  
精确控制和编辑图像

## Difference between ControlNet and T2I-Adapter

ControlNet 支持更多类型，T2I-Adapter 更易部署和扩展

The biggest problem with this framework is that the edges are generated(gradient-guided) without considering semantic information and any interaction.

## paper

《Sketch-Guided Text-to-Image Diffusion Models》
《GLIGEN: Open-Set Grounded Text-to-Image Generation》
《Adding Conditional Control to Text-to-Image Diffusion Models》

## How well do ControlNet and T2I-Adapter

To achieve a expected generation in Stable Diffusion, a hundred Text prompt words may not be as accurate and efficient as one input image.

## ControlNet diffusers

With ControlNet, users can easily condition the generation with different spatial contexts such as a depth map, a segmentation map, a scribble, keypoints, and so on.

# ControlNet
