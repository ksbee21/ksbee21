---
title: "Canvas 로 Sphere 구성 하기"
date: 2023-01-22T22:26:52+09:00
draft: true
tags : []
topics : []
description : "WebGL 등에서 사용하는 구(Sphere) 만들기"
---

위키 사이트    
[https://en.wikipedia.org/wiki/Sphere](https://en.wikipedia.org/wiki/Sphere)

위키 Spherical coordinate Syste     
[https://en.wikipedia.org/wiki/Spherical_coordinate_system](https://en.wikipedia.org/wiki/Spherical_coordinate_system)

수식 


\begin{align}
   center ( x_9, y_0, z_0 ), points(x, y, z), r 은 반지름 &&\\\ 
   ( x-x_0)^2 + (y-y_0)^2 + (z-z_0)^2 = r^2 &&\\\
   x = x_0 + r\times sin\theta \times cos\phi &&\\\
   y = y_0 + r\times sin\theta \times sin\phi &&\\\
   z = z_0 + r\times cos\theta &&\\\
   x^2+y^2+z^2 = r^2  => center ( 0, 0, 0 )\quad 일 때 &&\\\
   r \geq 0, &&\\\
   0° ≤ \theta ≤ 180° (π\quad rad) &&\\\
   0° ≤ \phi < 360° (2π\quad rad) && 
\end{align}



fsfksdfj 
