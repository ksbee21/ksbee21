---
title: "WebGL2 - Program 시작 10 - Ray 충돌 - [ 14 ]"
date: 2023-03-02T19:58:43+09:00
draft: true
tags : ["Web","WebGL", "Language","Javascript", "Programming","Math"]
topics : []
description : "WebGL 물체 선택 ( Picking Ray - Tracing 충돌 정리 )"
---
   앞선 글에서 구(Sphere) 와 Ray의 충돌까지 정리해 보려고 하다 보니, 공간의 역변환을 정리하는 것도 간단한 일이 아니어서 
   간략하게 언급만 하였습니다.    
   개인적으로 수학을 잘하는 것이 아니기 때문에 주어진 공식을 활용하는 정도면 될 것 같긴 하지만, Ray Tracing 은 충돌 같은 것에서만 
   사용하는 것이 아니라, 굴절, 반사 등에 의해 현실적인 묘사를 위해 사용되고 있는 기법이라, 조금 더 정리할 필요성이 있어 보입니다.    
   잘 

