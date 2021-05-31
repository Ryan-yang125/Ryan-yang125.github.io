---
layout: post
title: "review of interview"
subtitle: "without organise"
date: 2021-05-31
author: "RyanYang"
header-img: "img/post-bg-default.jpg"
tags: [杂]
---

**chrome gpu：**

1. main bitmap， cached bitmap， decode bitmap

2. Index color， 不直接存颜色bit，二是调色板的地址

3. 加速只用于video和graphcis

4. Render

​	a.instance SkImage and cached skim age

​	b. Raster skimage => index color => bitmapImage, allocated memory

​	c.GPU bitmap => texture, with cached: GPU内存空间，只读的，可绘制单位（包含宽、高，色彩通道等） ，

、由GPU专门的硬件texture处理

**P1:渲染：**

node => renderObject > renderLayer（css transform） => Graphcis Layer，（1对多，合成过程）

rO=>rL:

1.root

2.css postion

3.transparent

4.canvas

5.gif/video

r=>G怎么分层：

1. CSS filter, transform, z-index

2.CSS animation

3.video, gif(decoding)

4. Canvas, acc 2d context

**P2.Compositer:**

Where Gpu in

将gl tree中bitmap转为texture，vbuffer

**Immutable**

1. 区分data和time，容易回溯
2. 复用内存
3. 节省CPU，不存在复杂的diff，无非自底向上构建新树，原先的都在
4. 时间旅行，基于1，放进一个数组管理
  5.并发安全，不用额外添加锁
5. FP
6. 实践：
  a. shouldComponentUpdate，只渲染更新部分，传统deepcopy耗时

**如何进行文档懒加载？**

（获取显示区域）=> 长文本提高性能 => 文档如同构建DOM树，递归的， 这个过程没办法优化，但我们可以优化文档本身的内容，把他拆分成连续的部分。
问题：HTML字符形式无法切分，因为它是对称。
我们退一步，将文档进行Change化，就可以很简单的划分了，同时可以根据操作计算对应文档的长度。
语言是如何parser的
反观代码编辑：
每一行为一个单位，很容易判断当前那些行在区域内

**为什么长文本模式下性能显著提升**
1.cke长文本局限:
	自动保存
	没有这个逻辑判断：因为做不了，富文本设计的dom操作远比代码编辑器复杂
2.codemirror怎么做：
	parser只渲染当前view窗口内
	有一套逻辑用于判断文档是否活跃，用于停止整个parser流程

