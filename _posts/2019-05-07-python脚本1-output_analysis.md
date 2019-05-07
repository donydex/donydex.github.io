---
layout: post
title: python脚本1-output_analysis
categories: blog
tags: [技术实战]
description: 

---



```python
## 求不同gateway平均值，柱状图对比
# coding:utf-8
import pandas as pd 
import numpy as np 
import matplotlib.pyplot as plt 
import os
import csv

# txt 转化为csv格式的文件，在windows上两个都需要加b，在linux1上测试结果是不需要加b，即可
def txt2csv(txt_path, csv_path):
    txt_file = txt_path
    csv_file = csv_path
    # use 'with' if the program isn't going to immediately terminate
    # so you don't leave files open
    # the 'b' is necessary on Windows
    # it prevents \x1a, Ctrl-z, from ending the stream prematurely
    # and also stops Python converting to / from different line terminators
    # On other platforms, it has no effect
    in_txt = csv.reader(open(txt_file, 'r'), delimiter = '\t')
    out_csv = csv.writer(open(csv_file, 'w'))
    out_csv.writerows(in_txt)
    
# 处理csv格式的文件，返回group之后的mean的数据
def get_mean_time(csv_path):
    #第一行
    data = pd.read_csv(csv_path, header = None)
    #修改columns
    data.columns = ['gateway', 'time']
    #索引有问题，修改为数字
    data['index'] = data['gateway'].str.split('/gateway/gateway')
    data['index'] = [int(x[1]) for x in data['index']]
    #设置index这一列为索引列，按照index进行group
    data = data.set_index(data['index'])
    mean_time = data.groupby(data['index']).mean()
    return mean_time

if __name__ == '__main__':
    # file_path = '../data/output.txt'
    txt_path1 = '../data/output.txt'
    csv_path1 = '../data/output.csv'
    txt_path2 = '../data2/output.txt'
    csv_path2 = '../data2/output.csv'
    img_save_path = '../img/output/'
    txt2csv(txt_path1, csv_path1)
    txt2csv(txt_path2, csv_path2)
    # data1 画图
    mean_time1 = get_mean_time(csv_path1)
    mean_time_value1 = mean_time1['time'].values
    mean_time_index1 = mean_time1.index
    # data2 画图
    mean_time2 = get_mean_time(csv_path2)
    mean_time_value2 = mean_time2['time'].values
    mean_time_index2 = mean_time2.index  
#     print(mean_time_value1)
    plt.figure(figsize=(15, 10))
#     plt.bar(mean_time)
#     mean_time.plot.bar()
    plt.bar(mean_time_index1, mean_time_value1, label= 'Time-Data1')
    plt.bar(mean_time_index2, mean_time_value2, label= 'Time-Data2')
    plt.legend()
    plt.savefig(img_save_path + 'output.jpg')
    plt.show()
    # pandas 画图直接设置图片大小
#     mean_time.plot.bar(figsize=(20, 10))
    os.system('zip -q -r ' + img_save_path + 'output.zip '+ img_save_path)
```

