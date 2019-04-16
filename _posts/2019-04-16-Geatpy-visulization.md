---
layout: post
title: Geatpy 数据可视化
date: 2019-4-16
categories: blog
tags: [人工智能,技术总结]
description: 


---



## 数据可视化

​	接下来介绍Geatpy 的可视化功能。
​	当前Geatpy 提供3 个可视化输出的库函数：trcplot,sgaplot 和frontplot。分别是单目标进化算法的可视化输出、单目标优化进化过程动画输出，以及多目标进化算法的可视化输出。
​	值得注意的是，在进行可视化输出之前，最好在ipython 控制台执行以下语句：

```python
matplotlib qt5
```

​	使的绘制的图形在窗口中显示，否则动画效果就无法显示。

1. trcplot
    	该函数根据传入的进化追踪器来实现种群进化全过程中一些参数的可视化。比如绘制各代种群最优个体的目标函数值、适应度值、种群个体的解等。其语法如下：
    	trcplot(pop_trace, labels)
    	trcplot(pop_trace, labels, titles)
    	trcplot(pop_trace, labels, titles, save_path)

  ​	其中pop_trace 是一个进化追踪器，每一列代表一个参数，如第一列代表个体最优目标函数值等，每一行对应一个” 代”，比如第一行对应的是第一代种群的最优个体等等。

  ​	因为pop_trace 包含许多信息，我们可以根据这些信息绘制一张或多张图片。因此需要使用labels 和titles 来控制需要画多生张图、每张图包含多少个变量。

  ​	因此labels 和titles 都是list 类型的列表。labels 指代了图片中有哪些变量，这些变量的图例名称是什么。例如：

  1. 假设pop_trace 有2 列，含义分别是’a’ 和’b’，则labels = [[’a’],[’b’]], 表示要画2张图，每张图画2 个变量，图例分别是’a’ 和’b’。
  2. 假设pop_trace 有2 列，含义分别是’a’ 和’b’，则labels = [[’a’,’b’]], 表示要画1 张图，图中有2 个变量，图例分别是’a’ 和’b’。
  3. 假设pop_trace 有3 列，含义分别是’a’,’b’ 和’c’，则labels = [[’a’],[’b’,’c’]], 表示要画2 张图，第一张图有1 个变量，图例是’a’；第二张图有2 个变量，图例是’b’ 和’c’。

注：labels 的元素总数必须等于pop_trace 的列数

​	titles 表示各图片的标题，元素设为空字符串则表示不显示图片标题。

​	如titles = [’a’,”]，表示有2 张图片，标题分别是a 和不设标题

​	注：len(titles) 必须等于len(labels)

​	案例：
​	在sga_code_templet 算法模板中，有这样的一行代码：

​	

```
ga.trcplot(pop_trace, [['种群个体平均目标函数值',
'种群最优个体目标函数值']])
```

​	其中pop_trace 是一个n 行2 列的矩阵，第一列代表种群个体平均目标函数值，第二列代表种群最优个体目标函数值。因此传入trcplot 绘图函数的参数labels 设为[[’ 种群个体平均目标函数值’, ’ 种群最优个体目标函数值’]]，表示要画1 张图，这张图中同时绘制“种群个体平均目标函数值”以及“种群最优个体目标函数值”

​	titles 参数是缺省的，因此绘图将不显示标题。

​	绘图结果如下：

![1555404539484](C:\Users\25080\AppData\Roaming\Typora\typora-user-images\1555404539484.png)

## 2.sgaplot

​	该函数根据传入的数据集和进化代数gen 绘制动态图或进化结束后的静态图。其语法如下：

​	newAx = sgaplot(ValueSet, Label, saveFlag)
​	newAx = sgaplot(ValueSet, Label, saveFlag, ax)
​	newAx = sgaplot(ValueSet, Label, saveFlag, ax, gen)
​	newAx = sgaplot(ValueSet, Label, saveFlag, ax, gen, interval)
​	newAx = sgaplot(ValueSet, Label, saveFlag, ax, gen, interval, title)
​	newAx = sgaplot(ValueSet, Label, saveFlag, ax, gen, interval, title, save_path)
​	其中ValueSet 是一个numpy 的array 类型的列向量，一般传入该函数前是进化记录器pop_trace 的某一列数据。其实际含义由Label 确定。Label 是一个字符串，代表数据集ValueSet 的含义saveFlag 是布尔类型的标记，表示是否要保存图片。当要绘制动画时，必须设为False。
​	ax 是可选参数，在绘制动画的时候需要传入。其代表上一帧的动画。当画第一帧时，其值为None。
​	gen 是可选参数，表示当前进化代数，默认为None。该参数没有缺省或为非None时，图片将绘制动态图。		

​	interval 是可选参数，表示两帧动画之间的间隔时间，默认为0.1，单位为’ 秒’。
​	title 是可选参数，表示图形的标题名称。
​	save_path 是string 类型的可选参数，表示保存图片的路径。
​	newAx 代表新的图形，是更新后的ax。

​	案例：
​	在解决一个单目标优化问题时绘制进化过程中的各代种群最优个体的目标函数值的变化动态图。并且在进化结束后绘制一个进化全过程的各代最优个体的目标函数值静态图。

```python
...
ax = None # 存储上一帧图片
# 开始进化
for gen in range(MAXGEN)
...
# 进化操作
...
# 记录最优个体
bestIdx = np.max(FitnV)
pop_trace[gen,1] = ObjV[bestIdx] # 记录当代目标函数的最优值
...
# 绘图
ax = ga.sgaplot(pop_trace[:,[1]],'种群最优个体目标函数值',
False, ax, gen)
# 进化结束
ga.sgaplot(pop_trace[:,[1]],'种群最优个体目标函数值',
True) # 绘制最终的帕累托前沿图
# end
```

运行结果动画部分截图如下：
![1555404706583](C:\Users\25080\AppData\Roaming\Typora\typora-user-images\1555404706583.png)

![1555404717488](C:\Users\25080\AppData\Roaming\Typora\typora-user-images\1555404717488.png)

​	进化结束后绘制的静态图为：

![1555404741022](C:\Users\25080\AppData\Roaming\Typora\typora-user-images\1555404741022.png)

## 3. frontplot

​	该函数根据帕累托最优集目标函数值矩阵NDSetObjV 绘制2 维或3 维的目标函数值散点图。并且可以在种群进化过程中绘制动态的帕累托最优解的2 维或3 维动画。

​	语法：
​	newAx = frontplot(NDSetObjV, saveFlag)
​	newAx = frontplot(NDSetObjV, saveFlag, ax)
​	newAx = frontplot(NDSetObjV, saveFlag, ax, gen)
​	newAx = frontplot(NDSetObjV, saveFlag, ax, gen, interval)
​	newAx = frontplot(NDSetObjV, saveFlag, ax, gen, interval, title)
​	newAx = frontplot(NDSetObjV, saveFlag, ax, gen, interval, title, save_path)
​	其中NDSetObjV 为存储帕累托最优解的目标函数函数值矩阵，一列对应一个目标函数值，其列数必须为2 或3。	

​	saveFlag 是布尔类型的标记，表示是否要保存图片。当要绘制动画时，必须设为False。

​	ax 是可选参数，在绘制动画的时候需要传入。其代表上一帧的动画。当画第一帧时，其值为None。		

​	gen 是可选参数，表示当前进化代数，默认为None。当该参数为非None 时，图片将在图例部分显示当前gen 的值以及前沿点数。

​	interval 是可选参数，表示两帧动画之间的间隔时间，默认为0.1，单位为’ 秒’。

​	title 是可选参数，表示图形的标题名称。

​	save_path 是string 类型的可选参数，表示保存图片的路径。

​	newAx 代表新的图形，是更新后的ax。

​	案例：
​	在解决3 目标优化问题时绘制进化过程中的帕累托前沿的动画，以观察搜索到的帕累托前沿的动态变化。并且在进化结束后绘制一个最终的帕累托前沿图。	

```python
...
ax = None
# 开始进化
for gen in range(MAXGEN)
...
# 进化操作
...
# 更新帕累托最优集目标函数值矩阵NDSetObjV
...
# 绘图
ax = ga.frontplot(NDSetObjV, False, ax, gen + 1)
# 进化结束
ga.frontplot(NDSetObjV, True) # 绘制最终的帕累托前沿图
# end
```

​	运行结果动画部分截图如下：

![1555404925538](C:\Users\25080\AppData\Roaming\Typora\typora-user-images\1555404925538.png)



最终的前沿面静态图为：

![1555404976477](C:\Users\25080\AppData\Roaming\Typora\typora-user-images\1555404976477.png)

