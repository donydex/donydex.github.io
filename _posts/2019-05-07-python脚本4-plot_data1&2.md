---
layout: post
title: python脚本4-plot_data1_2
categories: blog
tags: [技术实战]
description: 
---

```python
# coding:utf-8

import pandas as pd 
import numpy as np 
import matplotlib.pyplot as plt 
import os
import matplotlib.image as mpimg # mpimg 用于读取图片

## 两个路径
img_path1 = '../img/docker_output/'
img_path2 = '../img/docker_output_data2/'
img_save_path = '../img/docker_output_data1_2/'

files= os.listdir(img_path1)
for file in files:
    if '.jpg' in file:
    #     file_path = ""
        file_path1 = img_path1 + file
        file_path2 = img_path2 + file
        plt.figure(figsize=(25, 25))
        plt.subplot(121)
        plt.title('Data1')
        pic1 = mpimg.imread(file_path1) 
        plt.imshow(pic1)
        plt.subplot(122)
        plt.title('Data2')
        pic2 = mpimg.imread(file_path2) 
        plt.imshow(pic2)
        plt.savefig(img_save_path + 'merge_' + file)
        os.system('zip -q -r ' + img_save_path + 'merge_docker_output.zip '+ img_save_path)
        plt.show()
```

