---
title: "节奏"
date: 2016-12-05
categories: 
  - "essay"
---

已知两个简谐运动方程分别为

\\(x\_1=A\\cos({\\omega\_1}t)\\) \\(x\_2=A\\cos({\\omega\_2}t)\\)

两个运动的振幅相同，频率不同，运动叠加后，得到 $$x\_1+x\_2=2A\\cos({\\omega\_1+\\omega\_2\\over 2}t)\\cos({\\omega\_1-\\omega\_2\\over 2}t)$$[![beats](http://mjm1990.com/wp-content/uploads/2016/12/beats-1024x609.jpg)](http://mjm1990.com/wp-content/uploads/2016/12/beats.jpg)[](http://mjm1990.com/wp-content/uploads/2016/12/beats.jpg)

假设\\(A=1\\)，\\(\\omega\_1=2\\pi\\cdot256\\)，\\(\\omega\_2=2\\pi\\cdot254\\)，那么叠加后的运动方程如上图绘制的那样。较慢的项，频率是1Hz，较快的项频率是255Hz，从图像上明显可以看出能量集中在较慢项决定的半个周期内，所以能够听到明显的节奏。节奏的周期是半秒，频率是2Hz。

附注：

等式\\(\\sin(\\alpha+\\beta)=\\sin\\alpha\\cos\\beta+\\sin\\beta\\cos\\alpha\\)有两种证明方法。

方法一：

利用欧拉公式\\(e^{i\\cdot\\alpha}=\\cos\\alpha+i\\cdot\\sin\\alpha\\)

方法二：

利用三角形的面积公式\\(S={1\\over2}{|AB||AC|\\sin{A}}\\)
