---
layout:     post
title:		Robotics:Aerial Robotics公开课 编程作业笔记(一) # 标题 
subtitle:  四轴飞行器的一维PID控制 	 #副标题
date:       2017-8-28              # 时间
author:     廖洽源                   # 作者
tags: [Robotics , Control , Coursera ]                              #标签
---

# Robotics: Aerial Robotics公开课 编程作业笔记(一)  
## 前言 
### 学完课程的感想    
我已经在Coursera网站上完成了三门公开课程.最具挑战性的课程便是Robotics专项课中的Robotics: Aerial Robotics,普通的高中数学课堂没有教授线性代数(week4还有变分法等高等数学的内容),物理中没有提及角动量等知识,更别说控制理论.幸好之前有线性代数和编程Arduino作为控制器的知识储备,终于在2017.9.23拿到了荣誉证书.  
这门课带我入门了基础的四轴飞行器运动学、动力学、控制学的内容,顺便重拾了已经忘干净了的Matlab,受益匪浅.
![荣誉证书](https://ws4.sinaimg.cn/large/006tKfTcgy1flmniz8untj31880y4h9i.jpg)
### 目的  
希望着篇文章能给没有上过这门课的同学一些帮助,在我的GitHub上面有课程所用的模拟器及实例代码,受限于荣誉准则,我就不把地址post出来了,请正在学习这门课程或者的同学独立完成作业!!!
### 荣誉准则  
受限于[Coursera荣誉准则](https://www.coursera.org/about/terms/honorcode)我不将作业、测验、考试、项目和其它作业的答案提供给其他任何人,文章所写的将会是课程编程作业的帮助文档中包含的及我完成作业的部分想法和提示.
## 正文  
### 1.Introduction    
整个编程及模拟都是基于Matlab进行的!!!  
这个编程练习的目标是让我们熟悉使用四轴飞行器模拟器和实现 比例微分（PD）控制器. 在这个练习中，我们将编写自己的PD控制器来控制四旋翼的高度.
要开始，我们需要下载起始代码并将其内容解压缩到我们要完成练习的目录中.
### 2.四轴飞行器模拟器   
#### 使用模拟器前需要知道的东西    
模拟器利用MATLAB的ODE解算器之一（称为ode45）模拟四旋翼飞行器的行.可以在Mathworks或其他在线资源中阅读更多的细节.  
然后,模拟器使用plot / plot3函数来帮助可视化每个时间步中四旋翼的当前状态.  
可以查看文件height_control.m以获取模拟代码。
在实现自己的功能前 先进入Matlab和模拟器目录中运行runsim.m 如果我们看到四旋翼从高度0下降，那么模拟器在计算机上面的运行没有问题，我们可以继续执行其他任务. 
![](https://ws4.sinaimg.cn/large/006tKfTcgy1fln9xx4rtxj31kw0zk17m.jpg) 
#### controller.m
控制器的代码在controller.m中,让我们来看看:  

```
function [u] = pd_controller(~, s, s_des, params)  
%PD_CONTROLLER  PD controller for the height  
%  
%   s: 2x1 vector containing the current state [z; v_z]  
%   s_des: 2x1 vector containing desired state [z; v_z]  
%   params: robot parameters 
  
% FILL IN YOUR CODE HERE  
u = 0;  
  
end   
```
可以看到
```
u = 0;
```
代表产生的机器人输入全部为零推力，因此四旋翼飞机由于重力而下降。  
而我们需要修改controller.m文件 使用PD控制来使飞行器稳定在模拟器给出的某个高度.
### 3.PD控制器  
四轴飞行器的z轴动力学方程式为: 

$$ \ddot z = \frac{u}{m} - g $$

因此PD控制器输入为:

$$ u = m(\ddot z _{des} + K _p e + K _v \dot e + g) $$  
  
\\( e \\)  和 \\( \dot e \\) 和 \\( \ddot e \\) 可以从飞行器的实时位置计算出来.  

