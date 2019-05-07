---
layout: post
title: python脚本2-docker_output_analysis_data2
categories: blog
tags: [技术实战]
description: 


---



```python
# 针对不同的docker（6个）画出cpu和mem的统计图
# coding:utf-8

import pandas as pd 
import numpy as np 
import matplotlib.pyplot as plt 
import os

file_path = '../data2/docker_output_2019.5.6.new.csv'
docker_dict = {}
img_save_path = '../img/docker_output_data2/'

def read_file(file_path, docker_dict):
    #读取csv文件中的数据
    file_path = '../data2/docker_output_2019.5.6.new.csv'
    data = pd.read_csv(file_path)
    # 按照docker进行分组，并且赋值到相应的字典
    groups = data.groupby(data['docker'])
    docker_dict = {}
    for i,group in enumerate(groups):
        docker_dict['docker_'+str(i)] = group
    return data, docker_dict

def draw_mean_data(data):
    # 画出平均所有docker的数据图
    docker_mean = data.groupby(data['docker']).mean()
    docker_mean.index = ['docker_'+ str(i) for i in np.arange(6)]
    docker_mean_cpu = docker_mean['cpu']
    docker_mean_cpu.plot.bar()
    plt.title('mean docker CPU')
    plt.xticks(rotation=360)
    plt.savefig(img_save_path + 'all_CPU_mean.jpg')
    plt.show()
    docker_mean_mem = docker_mean['mem']
    docker_mean_mem.plot.bar()
    plt.title('mean docker MEM')
    plt.xticks(rotation=360)
    plt.savefig(img_save_path + 'all_MEM_mean.jpg')
    plt.show()
    
def draw_docker(docker_name, docker_dict):
    #  输入序号，如:docker_1
    # tuple注意，每项有两个，第一项docker的id，第二项是对应的dataframe，所以取docker_dict[docker_name][1]
    data0 = docker_dict[docker_name][1]
    # cpu
    data_cpu = data0['cpu']
    # 重新设置series的index
    data_cpu.index = np.arange(len(data_cpu))
    # mem
    data_mem = data0['mem']
    data_mem.index = np.arange(len(data_mem))
    # 分开画CPU和MEM的，分别存储
    data_cpu.plot()
    plt.title(docker_name+":CPU")
    plt.savefig(img_save_path + docker_name + '_CPU.jpg')
    plt.show()
    data_mem.plot()
    plt.title(docker_name+":MEM")
    # plt.show()
    plt.savefig(img_save_path + docker_name + '_MEM.jpg')
    plt.show()
    # 画CPU和MEM一起的，一起存储
    data_cpu.plot(label='CPU')
    plt.title(docker_name+":CPU+MEM")
    data_mem.plot(label='MEM')
    plt.legend()
    plt.savefig(img_save_path + docker_name + '_CPU_MEM.jpg')
    plt.show()
    
if __name__ == '__main__':
    data, docker_dict = read_file(file_path, docker_dict)
    draw_mean_data(data)
    groups = data.groupby(data['docker'])
    for i in range(len(groups)):
        docker_name = 'docker_'+ str(i)
        draw_docker(docker_name, docker_dict)
    # 打包需要的文件
    os.system('zip -q -r ' + img_save_path + 'docker_output_img.zip '+ img_save_path)
```

