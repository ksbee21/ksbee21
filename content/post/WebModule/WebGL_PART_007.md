---
title: "WebGL2 - Program 시작 03 [ 07 ]"
date: 2022-12-26T21:59:21+09:00
draft: true
tags : ["Web","WebGL", "Language","Javascript", "Programming", "Math"]
topics : []
description : "WebGL2 그리기 과정과 유니폼 Matrix"
---

# 프로그램 Rendeing 과정 
   
   앞서 WebGL 사용 방법에 대해 간단히 정리해 보았습니다.    
   다시 한번 정리하면
   1. canvas 객체를 구성합니다. 
   2. GL 객체를 가져옵니다. ( Canvas 를 통해 webgl2 를 가져옵니다. )
   3. GLSL Shader 소스(문자열) 을 Vertex Shader, Fragment Shader 를 구성합니다.
   4. Shader Program 을 각각 만들어 두개의 쌍을 사용할 GL Program 을 만들고 Compile 합니다.    일반적으로 프로그램 소스를 구성해서 Compile 하는 것과 유사한 흐름 입니다.  
   당연히 프로그램을 구성할 때 사용한 소스에서 정의한 input 과 output 규칙은 따라야 합니다. 
   5. 데이터를 구성합니다. ( 예제에서는 cube ) - 꼭지점 정보는 필수 입니다. 법선(normal), Color, Index 등을 구성하였습니다. ( 재사용 가능 )
   6. GPU 에 Attribute 와 Uniform 정보를 알려 주고 데이터를 GPU 로 복사 합니다. ( Buffer 로 만들어 Binding )
   7. GPU 에서 데이터를 사용 할 수 있는 방법을 적용합니다. 이때 WebGL2 에서 제공하는 Vertex Buffer Object 를 생성하고, 해당 객체를 Binding 하여 이후 사용할 내용을 저장하게 합니다. 
   8. Vertext, Color, Index 등의 정보를 사용할 수 있는 방법을 기록 합니다. 
   9. 이제 Rendeing 과정이 시작됩니다. 
   10. 화면을 클리어 합니다. ( 이전 소스에서는 검은색으로 clear )
   11. 사용할 gl program 을 선택합니다. 
   12. 사용할 vertex array object 를 binding 합나다. ( 이미 해당 객체에는 gpu 에서 사용할 수 있는 방법이 기록되어 있습니다. )
   13. 전체에서 사용할 uniform 내용을 넘겨 줍니다. ( ex - 설명전이지만, world matrix, viewMatrix, projection matrix ... )
   14. cube 등에서 사용할 지역 uniform 정보를 넘겨 줍니다. ( 여러 물체 일 경우 world 좌표가 모두 다를 수 있음 )
   15. 물체를 그립니다. ( drawcall - index or 직접 )
   16. 현재 binding 되어 있는 vertex array object 를 해제 합니다. 
   17. 현재 binding 되어 있는 program 을 해제 합니다. 

   대략 이런 흐름으로 진행될 수 있습니다. 
   위 과정을 조금 자세히 정리해 보겠습니다. 



