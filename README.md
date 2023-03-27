# 我不生产代码, 我只是代码的搬运工

stable-diffusion一开源, 激起千层浪.

stable-diffusion开源地址: [stabilityai (Stability AI)](https://huggingface.co/stabilityai)

## Before

安装python, 安装pip

```shell
python3 -V
Python 3.9.6
pip3 -V
pip 23.0.1 from /Users/david/Library/Python/3.9/lib/python/site-packages/pip (python 3.9)
```

## step 1. 注册hugging face

https://huggingface.co/

模型界的github. 好比西方不能没有耶路撒冷, AI界不能没有huggingface.

* 注册之后, 用户设置中生成token, 后面会用token访问huggingface api下载模型

* 本地安装huggingface-cli
  
  `pip install --upgrade huggingface_hub`

* 登录huggingface-cli
  
  `huggingface-cli login --token $HUGGINGFACE_TOKEN`

## step 2. text-to-image on Apple M1

苹果在M1、M2(A11)时代, 在自家设备上提供了自己的神经网络引擎, 即Apple Neural Engine(ANE), 在这个引擎架构上, 性能提升, 消耗下降等等(具体参考[Deploying Transformers on the Apple Neural Engine - Apple Machine Learning Research](https://machinelearning.apple.com/research/neural-engine-transformers)). 这也是为什么要将其他的模型转换为苹果自己的模型(后缀: .mlpackage). 同理, 其他类型的AIGC, 如果要在M1、M2上运行, 理论上也要转换模型.

苹果官方已经提供了将开源的model转换为Apple machine learning models

* Install

```python
git clone https://github.com/apple/ml-stable-diffusion
pip3 install requirement.txt
```

* 转换模型

```python
#转换模型 将开放模型转换成 Apple Core ML 模型
python3 -m python_coreml_stable_diffusion.torch2coreml --convert-unet --convert-text-encoder --convert-vae-decoder --convert-safety-checker -o ./models
```

* 使用其他模型

```python
#默认是CompVis/stable-diffusion-v1-4模型,如果是其他模型, 追加参数: `--model-version`
python3 -m python_coreml_stable_diffusion.torch2coreml --convert-unet --convert-text-encoder --convert-vae-decoder --convert-safety-checker --model-version runwayml/stable-diffusion-v1-5 -o ./models
```

or

```python
python3 -m python_coreml_stable_diffusion.torch2coreml --convert-unet --convert-text-encoder --convert-vae-decoder --convert-safety-checker --model-version stabilityai/stable-diffusion-2-1 -o ./models
```

* Run

```python
#默认的CompVis/stable-diffusion-v1-4模型
python -m python_coreml_stable_diffusion.pipeline --prompt "A dog is in space" -i ./models -o ./output --compute-unit ALL --seed 93
```

```python
#如果换model, 生成的时候也要制定同一个model
python -m python_coreml_stable_diffusion.pipeline --model-version stabilityai/stable-diffusion-2-1 --prompt "A dog is in space" -i ./models -o ./output --compute-unit ALL --seed 93
```

* Result
  
  ![](/Users/david/Library/Application%20Support/marktext/images/2023-03-27-10-04-38-image.png)
