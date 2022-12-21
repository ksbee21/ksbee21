---
title: "WebGL2 - 개발방법 [ 01 ]"
date: 2022-12-18T20:58:24+09:00
draft: false
tags : ["Web","WebGL", "Language"]
topics : []
description : "WebGL 소개 및 필요성 "
---

# WebGL2 에 대한 간단한 소개
   
   현재 Web에서 3D를 사용할 수 있는 방법은 WebGL2가 가장 최근에 확정된 규약이고,    
   해당 기술은 OpenGL ES 300 을 기준으로 구성되어 있습니다.   

   WEBGPU 라는 Spec 이 공표되고 테스트가 진행 중이지만, 현재까지 Chrome 등의 Browser 에서
   Default 로 지원되지는 않습니다.    

   WebGL2 는 IE를 제외한 대부분의 Browser 에서 지원하고 있기 때문에, Web에서 3D를 구현하려면, 
   WebGL2를 활용하는게 좋을 것 같습니다. 

# WebGL2 Library 
   
   WebGL 을 사용하는 가장 간단한 방법은 잘 구성된, 모듈을 사용하는 것이 가장 빠르고 편한 방법입니다.   
   대표적으로 아래의 모듈들이 있습니다. 

   1. three.js 
      [THREE] <https://threejs.org/> 사이트에서 내용을 확인할 수 있습니다. 
      3D 의 경우 오래전부터 Web에서는 가장 인기있는 모듈이었습니다.    
      다만 현재는 Web 환경도 변화하고 있고, 3D Rendering 외에 물리엔진등이 필요할 경우 사용하는데 조금
      어려울 수도 있습니다.
   2. babylon.js
      [BABYLON] <https://www.babylonjs.com/> 사이트에서 내용을 확인할 수 있습니다. 
      모듈을 하나씩 따라서 확인하다 보면, 거의 Game 엔진 같은 느낌을 주는 Web Library 입니다. 
      threee.js 에서도 WebGPU 관련한 모듈이 있지만, babylon 의 경우 설명서에 WebGPU VR 등의 
      다양한 최신 기술들을 보여주고 있습니다. 

   그외 몇가지 모듈이 있기는 하지만, 위 2개가 대표적인 Web 3d library 라고 보아도 크게 문제는 없을 것 같습니다. 

# WebGL을 공부하는게 필요할까요? 
   
   잘 구성된 Library를 찾아서 진행하다 보면, 조금 애매한 상황이 놓이기도 합니다.    
   3D 모델이 구성되어 있고, Loader 를 통해 파일을 읽어 3D 환경을 만들어서 구현해야 하는 상황에서는    
   위 라이브러리를 사용하는 것이 가장 손쉽고, 정확하게 개발하는 방법이 될 것 같습니다.    

   하지만, 지도에 주어진 좌표( 위도,  경도 )가 있고, 해당 좌표 위에서 특정한 가상의 건물을 세우고, 
   그 좌표를 주어진 것이 아니라 계산을 통해서 올려야 한다면, 조금 다른 방법을 사용해야 할지도 모릅니다. 

   3D Position, Normal, Color, Texture 등이 모두 주어지고, 공간에 배치한 후 동작하게 만드는 모듈이라면
   직접 계산하고 만들어야 하는 그리 큰 필요성이 없겠지만, 동적으로 만들고 결합하고, 화면에 출력해야 하는 
   모듈이라면 WebGL 을 직접 컨트롤 해야 할 수 있기 때문입니다. 

   상황에 따라 달라 질 수 있는 부분이지만, WebGL 을 이해하고, 위 라이브러리를 활용한다면, 
   더 쉽게 더 고급 기능을 사용할 수 있지 않을까 합니다. 

   그런 이유로 WebGL을 학습해 보는 것은 의미가 있을 것 같습니다. 
   저는 그렇게 생각해서 정리해 보고자 합니다. 

# WebGL2를 학습하기 위한 사이트
   
   1. WegGL2 Fundamentals     
      URL : <https://webgl2fundamentals.org/webgl/lessons/ko/webgl-fundamentals.html>    
      WebGL 을 공부하기 위해서는 가장 좋은 Site 가 아닌가 합니다.    
      THREE.JS 를 구성한 분들이 설명하고 있는 사이트로 참조할 내용이 많이 있습니다.    
   2. MDN WEBGL    
      URL : <https://developer.mozilla.org/ko/docs/Web/API/WebGL_API/Tutorial/Getting_started_with_WebGL>   
      WebGL 1에 대한 Tutorial 과 설명이 있습니다. WebGL2 에 대한 설명도 충실한 사이트 입니다.    
      표준에 대한 참조로 활용이 가능할 것 같습니다. 

# 학습의 이유 및 앞으로 할일

   Web 환경에서 지금까지는 Html Table 등으로 대표되는 정돈된 문서 형식이 업무시스템의 주류를 이뤄 온것으로 생각하고 있습니다.    
   향후 Network 환경이 더 좋아지고, 개인 PC, Mobile Phone 의 성능이 조금더 나아지면, Network 기반의 대표적인 프로토콜인 http 
   기반의 Web 도 변화가 있지 않을까 합니다.   

   그 대표적인 변화가 아마도 Metabus 등으로 이야기 되는 Web의 3D 표현 방법이 아닐까 합니다.    
   그런 생각으로 조금씩 아래와 같이 학습해 가고자 합니다.    


   1. WebGL 에서 사용할 간단한 수학    
      사실 수학을 잘 하지 못하는 사람으로서 수식을 증명하고, 설명하는 것은 할 수도 없습니다.  
      다만, WebGL을 사용하기 위해서, 혹은 3D 프로그램을 구성하기 위해서 필요한 영역을 조금씩 
      학습해 보고자 합니다. 
   2. WebGL2 기능 및 문법    
      WebGL2에서 사용할 문법과, GPU를 활용하기 위한 OpenGL의 Shader를 기초적인 활용법은 
      확인이 필요할 것 같습니다. 
   3. 기타    
      조명, Texture 등 사용에 필요한 영역을 조금씩 접근해 보고자 합니다. 









   




