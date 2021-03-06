---
layout: post
title: 由SRP(可编程渲染管线)说开去
subtitle: Scriptable Render Pipeline 可编程渲染管线
date: 2018-05-21 12:00:00
author: 自来也
header-img: "img/in-post/default-bg.jpg"
categories: "技术文档"
tags:
  - 图形技术
---


> 前言：上上周UNITE，上一周开始看SRP，准备给大家分享，正好科普下渲染管线，于是有此文。

## 渲染管线（Render Pineline）  <!-- more -->

1. 示意图  
![img](/img/in-post/talk-srp/object-render-pipeline.png)

2. OPEN Render Pineline  
![img](/img/in-post/talk-srp/opengl-pipeline.png)


### A. DrawCall

### B. 渲染顺序
1. 画家算法->深度测试  
![img](/img/in-post/talk-srp/depth-test-1.png) 
![img](/img/in-post/talk-srp/depth-test-2.png) 
2. **先渲染**不透明物体（进行深度测试、写深度，通常意义下）  
![img](/img/in-post/talk-srp/depth-test-3.png) 
![img](/img/in-post/talk-srp/artist-algorithm.png) 
3. **后渲染**透明物体（进行深度测试、不写深度，通常意义下） 
![img](/img/in-post/talk-srp/transparent.png)  


### C. 裁剪以及光栅化
1. 视锥体  
![img](/img/in-post/talk-srp/camera.jpg)
2. 投影转换之后裁剪  
![img](/img/in-post/talk-srp/clipping.jpg)
3. 光栅化  
![img](/img/in-post/talk-srp/rasterization.jpg)


---


## 内置渲染管线 （Built-In Render Pipeline）

#### 1. 前向渲染
1. Forward Base  
主方向光

2. Forward Add  
第二展之外的方向光、点光、Spot Light

![img](/img/in-post/talk-srp/additional-pass.png)
![img](/img/in-post/talk-srp/forward-light-data.png)

缺点：每多一个灯光、每多一个阴影，所有DrawCall多一倍


![img](/img/in-post/talk-srp/forward-unity5.png)

[SRP小Demo参考链接](https://blogs.unity3d.com/cn/2018/01/31/srp-overview/)


#### 2. 延迟渲染

![img](/img/in-post/talk-srp/deferred-unity5.png)

缺点：
1. 不能使用MSAA
2. 不支持Blending
3. 需要硬件支持

## 可编程渲染管线 （Scriptable Render Pipeline）

### A. Render Pipeline总体流程
![img](/img/in-post/talk-srp/render-pipeline.png)

### B. Command Buffer
![img](/img/in-post/talk-srp/command-buffer.png)  

Command Buffer示例：  
![img](/img/in-post/talk-srp/post-outline-with-command-buffer.jpg)

### C. 自定义管线设置
![img](/img/in-post/talk-srp/RP-setting.png)


## HDRP & LWRP

### HDRP

1. 新的光照架构
![img](/img/in-post/talk-srp/color-temperature.png)
2. 全线性空间、光强遵循物理、Reflection Probe、面积光
![img](/img/in-post/talk-srp/hdrp-area-light.png)
![img](/img/in-post/talk-srp/gi-area-light.png)
3. 各向同性的散射GGX使得粗糙物体更加真实，添加次表面散射
![img](/img/in-post/talk-srp/isotropic-scattering-ggx.png)
4. 更多的内置着色器特性、以及贴花支持
![img](/img/in-post/talk-srp/material-type.png)
![img](/img/in-post/talk-srp/hdrp-new.png)


[HDRP详细参考链接](https://blogs.unity3d.com/cn/2018/03/16/the-high-definition-render-pipeline-focused-on-visual-quality/)


### LWRP
最主要的差别是在光照计算、GI、阴影。
![img](/img/in-post/talk-srp/lwrp-builtin-1.png)
![img](/img/in-post/talk-srp/lwrp-builtin-2.png)
![img](/img/in-post/talk-srp/lwrp-builtin-3.png)
![img](/img/in-post/talk-srp/lwrp-builtin-4.png)

Unity2018渲染示例：
![img](/img/in-post/talk-srp/builtin-forward-2018.png)
![img](/img/in-post/talk-srp/lwrp-2018.png)

[LWRP详细参考链接](https://blogs.unity3d.com/cn/2018/02/21/the-lightweight-render-pipeline-optimizing-real-time-performance/)

### 小结

![img](/img/in-post/talk-srp/sample-builtin.png)
![img](/img/in-post/talk-srp/sample-hdrp.png)

UNITE北京上关于SPR的讲演很水，[GDC上有一个关于SRP的分享](https://www.youtube.com/watch?v=zbjkEQMEShM)很好，推荐。

总体来说，不同的游戏、不同的平台得根据情况，去适用不同的渲染管线，如果游戏类型很专一，比如没有透明shader但是想用很多的灯光和阴影，就可以采用LWRP替代内置管线。或者根据具体情况订制。

## 附带CRT（Custom Render Texture）和 Shader Graph
### CRT
**Render Texture** + Material(shader) -> **new Texture**

![img](/img/in-post/unite-2018/crt-progress.png)
![img](/img/in-post/unite-2018/crt-test.gif)

### Shader Graph
![img](/img/in-post/unite-2018/ShaderGraphDissolve.jpg)



