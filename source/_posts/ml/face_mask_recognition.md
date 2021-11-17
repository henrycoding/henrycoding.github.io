---
title: 口罩识别（Vgg炼丹）带来的经验
categories: 炼丹记
date: 2021年11月17日 14点09分
tags:
---

好久没有炼丹了， 感觉想炼丹玩玩，但是感觉有好多的Bug，距离成长为一个炼丹师还有很长的距离。
此次炼丹遇到了一些问题， 感觉还是有很大的参考意义的，现在记录一下。

网络模型用的是 torchvision中的Vgg

```python
resnet = resnest50(pretrained=True)
vgg = vgg19_bn(num_classes=1000, pretrained='imagenet')
net = Sequential(vgg, Linear(1000, 500),Linear(500, 1), nn.Sigmoid())
```


1. PyTorch里面的ImageFolder，进行数据加载的时用到了transform， 里面有一个to_tensor,  这里面的数据其实是进行了Channel归一化的， 并进行了一些数据通道的转置，因此在**推理的时候也需要用to_tensor把image转成tensor，这也间接的回答了一个问题： 训练数据和推理数据必须是一致的，维度、范围也要一致， 对数据的归一化在推理的时候也要进行归一化**

```python
folder = ImageFolder(f'./data/train',
                     transform=transforms.Compose([transforms.CenterCrop(400), transforms.ToTensor()]))
data_loader = DataLoader(folder, batch_size=4, shuffle=False)
```

2. 数据增强。因为本身数据集就是从百度爬取的， 数据集质量本身就不是很好，因此需要对数据集进行数据增强。数据集增强有很多的方法，包括**平移变换，伸缩变换，随机剪裁，亮度变换，色彩变换**等。

3. 二分类问题可以用BCELoss进行， CrossEntroyLoss 可能效果不是很好。

4. 预训练模型在本任务中效果一般，不知道是为啥， 在分类效果不好的时候，尝试扩大数据集。

其实这次实验主要说明了数据集的处理对结果是十分重要的， 所谓的人工智能其实还没有那么智能，只能识别固定的数据集里面有的模式，不会自己进行所谓的自动学习， 因此对数据的**对数据的清洗和加工，以及数据的增强，其实对实验效果的影响十分的大**



{% img /image/face_mask_recognition.png %}
