
---
layout: post
title: 柏松图像混合
---
原理：区域内部，梯度等于目标图片梯度；区域边缘，像素值和原图相等
$$\min_f \sum_{p\in\Omega}\sum_{q\in N_p \cap \Omega}(f_p-f_q-v_{pq})^2$$
其中$v_{pq}$是目标图片在$[p, q]$方向的梯度，可以用$\textbf{v}(\frac{p+q}{2})\cdot \overrightarrow{pq}$求。

[Poisson Image Editing](http://www.irisa.fr/vista/Papers/2003_siggraph_perez.pdf)
