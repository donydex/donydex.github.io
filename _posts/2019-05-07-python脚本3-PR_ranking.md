---
layout: post
title: python脚本3-PR_ranking
categories: blog
tags: [技术实战]
description: 

---

```python
# Page ranking 画出饼状图
# coding:utf-8

import pandas as pd 
import numpy as np 
import matplotlib.pyplot as plt 
import os

folder_path = '../data/PR_ranking/'
file_path = '../data/PR_ranking/Dstar2_.csv'
docker_dict = {}
img_save_path = '../img/PR_ranking/'

# 第一行不作属性，直接作列
# 修改columns,并指定api为index
def draw_pie_pic(file_path, file_name):
    data = pd.read_csv(file_path, header=None)
    data.columns = ['api', 'response time']
    data.set_index('api', inplace=True)
    total_time = data['response time'].sum()
    data['percent'] = data['response time']/total_time
    # 控制显示两位小数
    data['percent'] = (data['percent'] * 100).round(2)
    plt.figure(figsize=(10, 10))
    data_time = data['percent']
    # data_api = data['api']
    # autopct 这个参数控制显示数字
    labels = ['api_' + str(i) for i in range(len(data_time))]
    data_time.plot.pie(autopct = '%3.2f%%',shadow = False, labels = labels, startangle = 80)
    plt.title(file_name)
    plt.savefig(img_save_path + file_name + '.jpg')
    plt.show()

if __name__ == '__main__':
#     data = read_file(file_path, docker_dict)
    files= os.listdir(folder_path)
    for file in files:
    #     file_path = ""
        file_path = folder_path + file
        file_name = file[:-5]
        draw_pie_pic(file_path, file_name)
    # 打包需要的文件
    os.system('zip -q -r ' + img_save_path + 'PR_ranking.zip '+ img_save_path)
```

