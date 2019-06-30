---
layout: post
title: 直方图均衡
---
设随机变量$R$是关于随机变量$S$的函数，$R=T(S)$。

$S$的概率密度函数是$f_S(x)$，分布函数是$F_S(x)$。

那么$R$的分布函数是$F_R(x)=P(R\le x)=P(T(S)\le x)$，当函数$T()$单调递增时，$=P(S\le T^{-1}(x))=F_S(T^{-1}(x))$

对上式求导得$R$的概率密度函数是$f_R(x)=\frac{f_S(x)}{T^{'}(x)}$。

所以，当取变换函数$T()$为$F_S()$时，$R$的概率密度是1，是个均匀分布（像素取值为$[0, 1]$）

结论：用分布函数做像素值的变换函数，能实现直方图均衡化。

```python
# coding=utf-8  
from PIL import Image  
import numpy as np  
import matplotlib.pyplot as plt  
  
img = Image.open('image.jpg').convert('L')  
img = np.array(img, dtype=np.uint8)  
  
# 直方图均衡化  
imhist, bins = np.histogram(img, bins=range(1, 257))  
imhist = imhist / float(imhist.sum().item())  
cumhist = imhist.cumsum()  
eqlzd_img = cumhist[img]  
eqlzd_img = (eqlzd_img * 255).astype(np.uint8)  
  
# 画图  
"""  
import matplotlib.font_manager as fm  
myfont = fm.FontProperties(fname='STHeiti.ttc')  
# image  
plt.subplot(2, 2, 1)  
plt.imshow(img, cmap='gray')  
plt.title(u'变换前', fontproperties=myfont)  
# histogram  
plt.subplot(2, 2, 2)  
plt.bar(left=range(255), height=imhist, width=1)  
# image  
plt.subplot(2, 2, 3)  
plt.imshow(eqlzd_img, cmap='gray')  
plt.title(u'变换后', fontproperties=myfont)  
# histogram  
imhist, bins = np.histogram(eqlzd_img, bins=range(1, 257))  
imhist = imhist / float(imhist.sum().item())  
plt.subplot(2, 2, 4)  
plt.bar(left=range(255), height=imhist, width=1)  
plt.show()  
"""
```
