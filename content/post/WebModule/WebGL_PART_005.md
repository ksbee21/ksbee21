---
title: "WebGL2 - Program 시작 01 [ 05 ]"
date: 2022-12-23T19:18:51+09:00
draft: false
tags : ["Web","WebGL", "Language","Javascript", "Programming"]
topics : []
description : "WebGL 프로그램 시작 01"
---

# 프로그램을 위한 준비
   
   ### 제한사항 
   현재 WebGL2 는 IE를 제외한 대부분의 Browser 에서 사용가능합니다.   
   Web 에서 3D를 구현하는 방법도 OpenGL 기반의 WebGL 에서,  더 다양한 지원이 가능한  
   WebGPU 등의 표준화가 진행중입니다.    
   현재 사용중인 Web 에서 3D를 구성하는 방법에도 변화가 있을 수 있는 상황입니다.    

   언제나 그렇듯 변화가 많을 경우, API 하나 하나 공부하는 것은 그리 효율적인지 않은 방법 같습니다.    
   동작의 기본 원리를 이해해 두면, 향후 어떤 변화가 나타나도 적응하는데 유리할 것 같습니다.    
   WebGL 관련 코드는 WebGL 2.0 을 기준으로 합니다.    
   굳이 하위 버전에 대한 호환성을 고려하지 않을 예정 입니다.    

   ### 참조 사이트 
   Three.js 를 구성한 분이 WebGL 에 관해 설명해 놓은 사이트 입니다.    [WebGL3Fundamentals](https://webgl2fundamentals.org/webgl/lessons/ko/webgl-fundamentals.html) 
   앞서서 링크를 걸기도 하였지만, 이 사이트 만큼 Web 기반 3D에 대해 풍부히 설명해 놓은 사이트는 찾지 못했습니다. 
   MDM 사이트나, 그외 이런 저런 사이트가 많이 있지만, Web 기반이라는 전제 에서 볼 때, 참조하기 가장 좋은 사이트가 아닌가 합니다. 

# WebGL 에서 사용하는 Data 

   ### 좌표( Position - Vertices )
   표현하고자 하는 물제가 있을 때 물체의 위치를 ( x,y,z ) 좌표로 표시합니다.   
   이렇게 구성된 꼭지점(좌표)를 연결해서 물체를 표현하게 됩니다.   
   WebGL 에서는 이런 꼭지점 3개를 연결해서 삼각형을 만들고, 그 삼각형을 그리는 것으로 물체를 표현 합니다.    

   WebGL 에서 좌표는 x, y, z 축 모두 -1 ~ 1 사이의 범위를 표현합니다.   
   x 좌표는 왼쪽에서 오른쪽으로 증가하고, y 좌표는 아래에서 위로 증가 합니다.   
   z 좌표도 뒤에서 앞으로 증가하는 구조인데 모니터의 출력이 반대방향이라 나중에 이를 보정하는 행렬을    
   구성하게 됩니다. 

   예를 들어 a (-1, -1, 0), b (1, -1, 0), c (0, 1, 0 ) 의 좌표 3개가 있다면 z축의 값이 모두 0 이므로  
   x, y 의 평면에서의 위치와 같습니다.   대략 이등변 삼각형의 꼭지점이라고 생각하면 좋을 것 같습니다.   

   ### 색상 , Texture 
   앞서 언급한 꼭지점에서 사용하는 색상값을 지정할 수 있습니다.   
   색상은 ( R,G,B,A ) - (빨간색, 녹색, 파란색, 투명도 ) 를 기준으로 합니다. 
   범위는 (0~1) 사이로 Web에서 사용하는 기준으로 보면 (0~255)색상을 0~1 사이의 소수값으로 표기합니다.    
   색상과 유사하게 그림 등의 위치정보를 기반으로 색상을 가져올 수 있습니다.   
   이때 사용하는것이 Texture 입니다.   꼭지점이 그림에서 어느 위치 인지 알려 주면, 그 그림에서 해당 위치의 
   색상정보를 매칭하여 그려 주게 됩니다.   
   결국 그림의 색상 값이 적용되는 것입니다.    
   Texture 는 2차원 평면 데이터를 기준으로 하기 때문에 x,y 좌표가 꼭지점과 매핑 됩니다. 
   범위는 (0~1)로 왼쪽 아래가 (0,0) 이고 위로 (0,1) - 왼쪽 상단, 오른쪽 상단 ( 1, 1), 오른쪽 아래 ( 1, 0 )
   입니다.    

   ### 법선 (Normal) 벡터
   그림을 그리는데 직접 사용하지는 않지만, 빛, 표면에 수직 방향으로 사용할 수 있는 모든 것에 적용될 수 있는 
   아주 중요한 데이터 입니다.    
   삼각형을 기준으로 할 때 WebGL 에서는 오른손 법칙을 적용하기 때문에 반시계 방향의 꼭지점으로 Cross Product 
   하면 삼각형에 수직인 법선 벡터를 구할 수 있습니다. ( Cross Product 는 이전 게시물 참조 )
   물론, 이것은 삼각형 노말이고, 꼭지점에 대한 Normal 은 공유하는 삼각형 노말의 평균을 구하면 유사값을 
   구할 수 있을 것 같습니다.   ( 구(Speher) 라면 중심점에서 표면으로 방향이 Normal 방향 )   
   
   위 a, b, c position 을 아래의 코드를 통해 계산하면 (0, 0, 1) 이라는 Normal vector 를 얻게 됩니다.   
   양의 방향 ( z ) 축으로 Normal Vector 가 구성되어 있습니다.    태양빛 등이 (0, 0, -1) 방향으로 주어졌다면, 
   가장 많은 빛을 삼각형이 받고 있다고 보아도 좋을 것 같습니다.   
   정오의 태양이 머리를 비추고 있는 모습 입니다. ( ^^ )

   ``` javascript
    export const makeTriangleNormals = (stdVector, firstVector, secondVector) => {
		if ( !stdVector || !firstVector || !secondVector ) {
			return;
		}

		let v1 = makeVectorMinusValues(firstVector, stdVector);
		let v2 = makeVectorMinusValues(secondVector, stdVector);

		let result = makeVectorCrossProductValues(v1, v2);
		return  makeNormalizeVector(result,true);
	}
   ```

# Data Sample 
      
   육면체( Cube ) 에 관한 데이터는 Web Site 이곳 저곳에서 찾아 볼 수 있습니다.    
   테스트를 위해 MDN Site 에서 기본적인 육면체 정보를 가져와 보겠습니다.   
   ### Position 
   ``` javascript 
        const positions = [
            // Front face
            -1.0, -1.0, 1.0, 1.0, -1.0, 1.0, 1.0, 1.0, 1.0, -1.0, 1.0, 1.0,
            // Back face
            -1.0, -1.0, -1.0, -1.0, 1.0, -1.0, 1.0, 1.0, -1.0, 1.0, -1.0, -1.0,
            // Top face
            -1.0, 1.0, -1.0, -1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, -1.0,
            // Bottom face
            -1.0, -1.0, -1.0, 1.0, -1.0, -1.0, 1.0, -1.0, 1.0, -1.0, -1.0, 1.0,
            // Right face
            1.0, -1.0, -1.0, 1.0, 1.0, -1.0, 1.0, 1.0, 1.0, 1.0, -1.0, 1.0,
            // Left face
            -1.0, -1.0, -1.0, -1.0, -1.0, 1.0, -1.0, 1.0, 1.0, -1.0, 1.0, -1.0,
        ];    
   ``` 
   ### Color 
   ``` javascript
    const faceColors = [
         [1.0, 1.0, 1.0, 1.0], // Front face: white
         [1.0, 0.0, 0.0, 1.0], // Back face: red
         [0.0, 1.0, 0.0, 1.0], // Top face: green
         [0.0, 0.0, 1.0, 1.0], // Bottom face: blue
         [1.0, 1.0, 0.0, 1.0], // Right face: yellow
         [1.0, 0.0, 1.0, 1.0], // Left face: purple
    ];

    // Convert the array of colors into a table for all the vertices.

    var colors = [];

    for (var j = 0; j < faceColors.length; ++j) {
        const c = faceColors[j];
        // Repeat each color four times for the four vertices of the face
        colors = colors.concat(c, c, c, c);
    }   
   ```
   ### Index 
   ``` javascript 
    const indices = [
        0,1,2,      0,2,3, // front
        4,5,6,      4,6,7, // back
        8,9,10,     8,10,11, // top
        12,13,14,   12,14,15, // bottom
        16,17,18,   16,18,19, // right
        20,21,22,   20,22,23, // left
    ];   
   ```

   ### Texture Coord 
   ``` javascript 
    const textureCoordinates = [
        // Front
        0.0, 0.0, 1.0, 0.0, 1.0, 1.0, 0.0, 1.0,
        // Back
        0.0, 0.0, 1.0, 0.0, 1.0, 1.0, 0.0, 1.0,
        // Top
        0.0, 0.0, 1.0, 0.0, 1.0, 1.0, 0.0, 1.0,
        // Bottom
        0.0, 0.0, 1.0, 0.0, 1.0, 1.0, 0.0, 1.0,
        // Right
        0.0, 0.0, 1.0, 0.0, 1.0, 1.0, 0.0, 1.0,
        // Left
        0.0, 0.0, 1.0, 0.0, 1.0, 1.0, 0.0, 1.0,
    ];
   ```   

   ### Vertex Normal 
   ``` javascript
  const vertexNormals = [
    // Front
    0.0, 0.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 1.0,
    // Back
    0.0, 0.0, -1.0, 0.0, 0.0, -1.0, 0.0, 0.0, -1.0, 0.0, 0.0, -1.0,
    // Top
    0.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 1.0, 0.0,
    // Bottom
    0.0, -1.0, 0.0, 0.0, -1.0, 0.0, 0.0, -1.0, 0.0, 0.0, -1.0, 0.0,
    // Right
    1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 1.0, 0.0, 0.0,
    // Left
    -1.0, 0.0, 0.0, -1.0, 0.0, 0.0, -1.0, 0.0, 0.0, -1.0, 0.0, 0.0,
  ];
   ```

   ### 간단한 설명 및 다음글(예정) 소개

   위치 정보는 x,y,z 의 3개씩 사각형의 꼭지점을 가지고 있습니다. 6면체이기 때문에 
   6 x 4 x 3 = ( 면의갯수 x 꼭지점갯수 x 꼭지점당좌표갯수) = 72 개입니다. 면과 꼭지점만 보면 24개 입니다.    
   색상은 6 x 4 x 4 = ( 면의갯수 x 꼭지점갯수 x 꼭지점당좌표갯수) = 96 개입니다. 면과 꼭지점만 보면 24개 입니다.    
   texture 는 6 x 4 x 2 = ( 면의갯수 x 꼭지점갯수 x 꼭지점당좌표갯수) = 48 개입니다. 면과 꼭지점만 보면 24개 입니다.    
   normals 은 6 x 4 x 3 = ( 면의갯수 x 꼭지점갯수 x 꼭지점당좌표갯수) = 72 개입니다. 면과 꼭지점만 보면 24개 입니다.    
   
   24개의 공통항목이 있고, 각 꼭지점당 사용갯수에 따라 3, 4, 2, 3 개 입니다.   이 뒤의 숫자가 넘겨줄 형식을 결정합니다. 
   vector 의 경우 vec2, vec3, vec4 등으로 달리 표기 될 수 있습니다.  ( WebGL 에서 제공하는 자료형 )

   이런 정보를 가지고 무엇을 할 수 있을 까요?   
   WebGL 에서는 좌표, 색상, 법선 정보를 받아들어 GPU 에서 화면에 출력할 pixel 정보를 구성해서 최종적으로 모니터 화면에 
   특정색으로 출력하는 역활을 담당합니다.    
   처음 위치정보 등을 받아 들이는 것이 Vertex Shader라는 OpenGL 전용 언어인 GLSL 로 구성된 내용이 필요하고, 
   색상을 결정짓고 출력하는 Fragment Shader 라는 프로그램이 쌍으로 필요합니다.    

   다음은 2 두개를 구성후 간단히 Cube 를 그려 보는 내용을 프로그램 해 볼까 합니다. 


    











