---
layout:     post
title:		Robotics:Aerial Robotics公开课 编程作业笔记(Week2) # 标题 
subtitle:  1-D四轴飞行器PD控制 	 #副标题
date:       2017-8-28              # 时间
author:     廖洽源                   # 作者
tags: [Robotics , Control , Coursera ]                              #标签
---

# Robotics: Aerial Robotics公开课 编程作业笔记(Week2)  
## 前言 
### 学完课程的感想    
我已经在Coursera网站上完成了三门公开课程.最具挑战性的课程便是Robotics专项课中的Robotics: Aerial Robotics,普通的高中数学课堂没有教授线性代数(week4还有变分法等高等数学的内容),物理中没有提及角动量等知识,更别说控制理论.幸好之前有线性代数和编程Arduino作为控制器的知识储备,终于在2017.9.23拿到了荣誉证书.  
这门课带我入门了基础的四轴飞行器运动学、动力学、控制学的内容,顺便重拾了已经忘干净了的Matlab,受益匪浅.
![荣誉证书](https://ws4.sinaimg.cn/large/006tKfTcgy1flmniz8untj31880y4h9i.jpg)
### 目的  
希望着篇文章能给没有上过这门课的同学一些帮助,在我的GitHub上面有课程所用的模拟器及实例代码,受限于荣誉准则,我就不把地址post出来了,请正在学习这门课程或者的同学独立完成作业!!!
### 荣誉准则  
受限于[Coursera荣誉准则](https://www.coursera.org/about/terms/honorcode)我不将作业、测验、考试、项目和其它作业的答案提供给其他任何人,文章所写的将会是课程编程作业的帮助文档中包含的及我完成作业的部分想法和提示.
 
## 1.Introduction    
整个编程及模拟都是基于Matlab进行的!!!  
这个编程练习的目标是让我们熟悉使用四轴飞行器模拟器和实现 比例微分（PD）控制器. 在这个练习中，我们将编写自己的PD控制器来控制四旋翼的高度.
要开始，我们需要下载起始代码并将其内容解压缩到我们要完成练习的目录中.
## 2.四轴飞行器模拟器(Quadrotor Simulator)   
### 使用模拟器前需要知道的东西    
模拟器利用MATLAB的ODE解算器之一（称为ode45）模拟四旋翼飞行器的行.可以在Mathworks或其他在线资源中阅读更多的细节.  
然后,模拟器使用plot / plot3函数来帮助可视化每个时间步中四旋翼的当前状态.  
可以查看文件height_control.m以获取模拟代码。
在实现自己的功能前 先进入Matlab和模拟器目录中运行runsim.m 如果我们看到四旋翼从高度0下降，那么模拟器在计算机上面的运行没有问题，我们可以继续执行其他任务. 
![](https://ws4.sinaimg.cn/large/006tKfTcgy1fln9xx4rtxj31kw0zk17m.jpg) 
### controller.m
控制器的代码在 controller.m 中,让我们来看看:  

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
### 3.PD控制器理论  
四轴飞行器的z轴动力学方程式为: 
![](https://ws4.sinaimg.cn/large/006tKfTcgy1flvlqsx3qhj310e0r0wl3.jpg)
$$ \ddot z = \frac{u}{m} - g $$

可见我们面对的是一个非常简单的二阶线性系统: 
 
四轴飞行器的位置为 $$z$$ 动力系统的输入为 $$u$$ 而 $$\ddot z = u $$  
所以我们需要找到一个控制输入(也就是推力输出)函数使得某一时刻的实际位置(用一个函数来表示): $$x(t)$$ 和 目标位置: $$x^{des}(t)$$ 相同  
  
定义 误差, $$e(t) = z^{des}(t)- z(t)$$   
要使误差 $$e$$ 呈指数收敛到0 我们使用PD(proportional–derivative)即比例微分控制器.  
我们需要找到一个长得这样的控制输入函数$$u(t)$$: 
 
$$ u(t) = \ddot z^{des}(t) + K_p e(t) + K_v \dot e(t) $$  

第一项是前馈(feedforward)项 第二项是 比例(proportional)项 第三项是微分项(derivative)   
### 4.PD控制器实现
在实时控制时 控制输入函数 $$u(t)$$ 等价于:  
$$ u = m(\ddot z^{des} + K_p e + K_v \dot e + g) $$  

我没有使用前馈项  
$$ u = m( K_p e + K_v \dot e + g) $$  

如何得到 $$\ddot z^{des}$$ 和 $$e$$ 和 $$\dot e$$ 呢???  
再看一下 controller.m 文件 :

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
参数 ```s``` 是四轴飞行器的实时位置和位置的导数(速度)  
参数 ```s_des```是四轴飞行器的目标位置和目标位置的导数  
所以$$e$$和$$\dot e$$在程序中可以这样写:

```
err = s_des(1) - s(1);  
err_d = s_des(2) - s(2);
```
参数```params```是一个类 里面包含了四轴飞行器的各个属性 比如 :  

```
params.mass %质量  
params.gravity %所受引力常数
```  
最后控制输入函数就能写出来了 

```
u = xxxxxxxxxxx ; %受限于荣誉准则 关键代码不写 😑
```

我们的控制输入函数就只剩下 $$K_p$$ $$K_v$$ 两个未知量 ,这时候就要扔到模拟器里面跑然后调参了  
最后效果 :  
悬浮:
![](https://ws3.sinaimg.cn/large/006tKfTcgy1flvleylkizj31kw0zkqin.jpg)
上升到1m:
![](https://ws2.sinaimg.cn/large/006tKfTcgy1flvlftxi3ej31kw0zkds0.jpg)
## 预告
Week2(Week1 没有编程作业) 的编程作业 “1-D四轴飞行器控制” 是非常容易的, Week3 将会涉及到 x z 2-D控制 需要控制飞行器在某个平面画sin()图像 而 Week4将 涉及 3-D 控制,每周的笔记将陆续post上来.

