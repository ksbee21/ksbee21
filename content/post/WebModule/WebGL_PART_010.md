---
title: "WebGL2 - Program 시작 06 - Lighting - [ 10 ]"
date: 2023-01-22T21:34:09+09:00
draft: false
tags : ["Web","WebGL", "Language","Javascript", "Programming", "Math"]
topics : []
description : "WebGL 조명(Lighting) 기본 개념 ( Phong 모델 )"
---

# Phong Lighting 
   
   3D 관련하여 빛에 대한 자료를 찾아 보면, Phong 모델에 관한 글을 쉽게 접합 수 있습니다.    
   그만큼 많이 사용되고, 개념을 이해하는데 도움이 되기 때문에 많이 언급되는 것 같습니다.      
   Phong 모델에서 이야기 하는 난반사(Diffuse), 정반사(Specular), 배경광(Ambient), 발산광(Emit) 을 
   간단하게 정리해 보고 WegGL로 어떻게 구현할 수 있는지 정리해 보고자 합니다.    

   [이곳에서 적용된 예제를 확인할 수 있습니다.](/html/WebGL2/WebGL_PART_010_01.html)

   ### 난반사 ( Diffuse ) 
   빛이 물체에 부딛혀 여기저기로 흩어지는 상황을 모사하는 방법 입니다.   
   쉽게 생각해서 정오의 태양이 머리에 있을 때(머리와 직각)일 때 빛을 가장 많이 받을 수 있다는 가정에서 출발하고 있습니다.   
   저녁에 해 저물 무렵의 빛은 약하고 정오의 빛이 강한 원리와 비슷하다고 보면 좋을 것 같습니다 .  
   그것을 모사한 방법으로 앞서 삼각형 평면에 수직인 Normal(법선) 으로 꼭지점(Vertex) 의 Normal 을 구해 놓았는데, 이를 기반으로 계산하게 됩니다.  
   꼭지점의 Normal 은 길이가 1인 단위 벡터 이고, 빛의 방향도 단위 벡터로 방향만 표시 할 수 있습니다.   
   예를 들어 빛의 방향이 [0,0,-1] 이라는 의미는 x,y 가 원점인 곳에서 z 방향이 음의 방향으로 비추고 있다는 의미 입니다.   만약 꼭지점 Noraml 이 [0, 0, 1] 이면
   빛의 방향을 계산을 편하게 하기 위해 꼭지점에서 부터의 방향으로 변화 하면 [0,0,-1] * -1 [0, 0, 1] 이 됩니다. 두 방향을 각 항목을 곱하여 더하면 ( dot product ) 
   0 x 0 + 0 x 0 + 1 x 1 = 1 이 됩니다. 빛의 양을 1까지 받을 수 있으면 최대치의 빛을 받는 다는 의미 입니다.   
   이렇게 빛의 방향을 부딛히는 지점에서 부터로 변환하고, 해당 꼭지점의 Normal 과 dot product 를 수행하면 표면이 빛을 어느 정도 받을 수 있는지 알 수 있다는 것이
   난반사 입니다 .   범위는 0 ~ 1 사이이니, 빛을 받지 못하면 0으로 치환하게 됩니다.

   shader 에서 대부분의 계산을 수행하겠지만, javascript 로 구성한다면 다음과 같은 코드가 될 것 입니다. 

   ``` javascript 

        /**
         * 
         * @param {Float32Array} v1 
         * @param {Float32Array} v2 
         */
        export const makeDotProductVectors = (v1,v2) => {
            if ( !v1 || !v2 ) {
                return;
            }
            const len = v1.length;
            if ( !len || len < 1 || v2.length != len ) {
                return;
            }
            const result = 0.0;
            for ( let i = 0; i < len; i++ ) {
                result += v1[i]*v2[i];
            }
            return result;
        };

        let originalLightDir = new Float32Array([0,0,-1]);
        let lightDir        = new Float32Array([-0,-0,1]) ; //  originalLightDir * -1 
        let vertexNormal    = new Float32Array([0,0,1]);

        let diffValue = Math.max(makeDotProductVectors(lightDir, vertexNormal),0);
        //  color 값 곱하기 ...
   ```

   실제로 Fragment Shader 에서는 다음과 같이 사용됩니다. 

   ``` c++
                vec3 normal = normalize(vNormal);
                vec3 light = normalize(directionLight);
                vec3 matDiff = texture(uTexture,vTexCoord).rgb;
                vec3 diffuse = max(dot(light,normal),0.0)*matDiff;
   ```  
   빛의 방향은 이미 부딛히는 점에서 빛으로 향하도록 부호가 변경된 상태로 전달 됩니다.    
   내장 함수인 normalize 를 호출해 단위 벡터로 만들고 있습니다.    
   그림에서 보간된 위치의 pixel 값을 가져오고 있습니다. texture(uTexture,vTexCoord).rgb   
   내장된 dot product 함수를 이용해서 두 벡터의 cos(theta) 값을 가져오고, 그 값이 0 미만이 되지 않도록 구성후 추출한 pixel 값에 곱해 주고 있습니다. 
   ( max(dot(light,normal),0.0)*matDiff )   


   #### diffuse 를 사용하기 전과 후의 예제 입니다. 
   원본은 구에 간단한 Texture 를 입힌 화면입니다.       
   ![원본이미지](/imgs/gl_10_01.png)      
   Diffuse 조명을 사용했을 때의 이미지 입니다.  광원은 우측 상단 입니다. (1, 1, 1)    
   ![Diffuse 이미지](/imgs/gl_10_02.png) 

   ### 정반사 ( Specular ) 
   정반사는 빛이 물체에 부딛힌 후 들어온 각도와 동일한 각도로 빠져 나가는 반사광이 우리눈에 끼치는 영향 정도라고 생각할 수 있습니다.    
   실생활에서 보면 낮에 햇빛이 유리창에 비추어 눈부심을 느끼게 하는 경우를 생각해 보면 좋을 것 같습니다.   유리창이 있다고 다 그렇게 되는 것이 아니고, 
   햇빛이 유리창을 비출때 우리 눈이 햇빛이 유리창에 비친 각도와 공교롭게 반사각이 일치할 때 유리창에 비친 햇빛에 눈이 부신 경험이 있을 것 입니다.    
   입사각과, 반사각이 바라보는 시선과 일치할 때 그런 현상이 나타나곤 합니다. 그런 현상을 모사한 것입니다.        
   Phong 모델에서는 이것을 어떻게 계량화 하여 표현할까요?    
   평면위에 빛이 왼쪽에서 45도 방향으로 들어온다면 원칙적으로 빛의 방향은 왼쪽 위 45도 각도에서 들어 오는 것으로 생각할 수 있습니다.    평면이 평평하다면, Normal 이라고 불리는 값은 
   평면에 수직이 될 것입니다. 이럴 경우 반사각은 오른쪽 45도 방향이 될 것이고, 빛은 이 방향으로 반짝일 겁니다.    거울을 연상해 보시면 거울에 비친 햇빛이 반사되는 것과 유사하다는 것을 
   연상할 수 있습니다.    그 때 우리의 시선이 반사되는 각도인 오른쪽 45도에 놓여 있으면, 가장 강한 빛을 받을 것이지만, 조금 떨어져 있다면 그 빛을 약간만 받게 될 것입니다.    
   평면의 표면에서 수직으로 위로 올라간 방향이 Normal Vector 이고, 빛이 들어오는 방향이 Light Vector 인데 평면에서 Ligth 방향으로 빛의 방향을 변경해 보면 계산이 편하기 때문에 빛의 방향을 
   반대로 뒤집에서 표현합니다. Light Vector 이하 L , Normal Vector 이하 N, 이라고 표현하면 우리가 구하고자 하는 반사 Vector, Reflect Vector , 이하 R 은 다음과 같은 방식으로 구할 수 있습니다.   
   L 을 단위 벡터로 만들고, N 을 단위 벡터로 만든후 구 벡터를 Dot Product 하면 단일 값을 구할 수 있는데 이 값이 cos(theta) 입니다. N.L = cos(theta) N, 과 L 이 단위벡터라 길이가 1이라서 그렇습니다. 
   L 과 N 사이의 각을 입사각이라고 하고( cos(theta) ) 이 크기는 반사각인 R 과 N 사이의 각과 동일한 크기를 가집니다. ( 이것은 물리법칙으로 주어진 것입니다. )    
   N.L 이 cos(theta) 라고 했으니 cos(theta) * N 이면 평면 표면에서 N 방향으로 L 과 R 을 연결하는 중간 지점을 향하는 벡터를 구할 수 있습니다. 이를 편의상 A 라고 하겠습니다.    
   삼각함수의 cos(theta) = 밑변 / 빗변 이니 , cos(theta) * N 이렇게 하면 N 의 길이가 빗변이니 N 으로 향하는 어느 지점( A ) 에 이르는 벡터라고 볼 수 있습니다.    
   L 에서 A 로 향하는 벡터는 A - L 이라고 하고, S1 이라고 하겠습니다.    A 에서 R 로 향하는 벡터는 R - A = S2 라고 하겠습니다.    
   그런데 S1 하고 S2 의 크기가 같으니 두개를 연결하면, A - L = R - A 입니다.   우리가 구하고자 하는 것은 R 이니 R 로 정리하면 R = 2A - L 이 됩니다. 
   A 가  N*(N.L) 이었으니, 다 풀어서 보면 R = 2*N*(N.L) - L 로 표현할 수 있습니다. ( 빛의 방향이 평면에서 빛의 방향으로 뒤집어진 상태에서 계산한 것입니다.  )     


   그렇게 반사각을 구한 후에 보는 눈하고 dot product 를 구성하면 위의 diffuse 와 같이 비치는 세기를 알 수 있습니다.    여기서 조금 더 생각할 부분은, 
   모든 각도가 다 빛을 볼 수 있는 것은 아니니 dot product 의 결과를 몇승해서 시야 범위를 좁히는 과정을 거치게 됩니다.    예제에서는 32승을 사용하였습니다.  64승 정도를 추천하고 있습니다.    

   실제로 Fragment Shader 에는 다음과 같이 사용됩니다. 
   ``` c++
                vec3 reflactDir = 2.0*normal*dot(light,normal) - light;
                vec3 specular = pow( max( dot(reflactDir, viewDir),0.0),shineCoef ) * dirLightColor * specularColor;
   ```

   #### Specular 를 사용한 예제 입니다. 
   ![Specular 이미지](/imgs/gl_10_03.png) 

   ### Ambient( 배경광 ), Emit ( 자발광 ?)  
   배경광은 어두운 곳에서도 윤곽정도는 구분할 수 있는 것처럼 빛이 어디에서 오는지는 모르지만 구분할 수 있을 정도로 있다고 가정하고, 그냥 표현하는 빛의 세기 입니다. 
   rgb 의 배경색과, 그것을 반사하는 정도를 다시 rgb 별로 설정할 수 있지만, 예제에서는 실수로 동일한 정도를 반사하는 것으로 구성하였습니다.    

   자발광은 이름 그대로 자기가 빛을 내는 것을 모사한 것입니다.   주어진 값을 그대로 사용합니다. 
   
   #### Ambient 를 사용한 예제 입니다.     
   ![Ambient 이미지](/imgs/gl_10_04.png)
   너무 조금 주어 그리 큰 차이가 나진 않으나 아주 살짝 음영 부분이 밝아 졌습니다. ^^ 
   자발광 예제는 담지 않았습니다.   주어진 색상을 그대로 표현하는 것이라 별도 예제를 구성하지는 않았습니다.  

   #### 이 전체를 연결한 예제 입니다. 
   ![Sample 이미지](/imgs/gl_10_05.png)   

   ### 정리 
   Diffuse 는 난반사로, 빛의 방향과 노말(법선) 을 이용해서 받아들이는 빛의 세기를 결정 지었습니다.    
   Specular 는 빛의 방향과 Noraml 을 이용해 반사 벡터를 구하고, 반사벡터와 시야벡터를 Diffuse 처럼 dot product 를 세기를 정하는데, 
   너무 넓은 시야각을 보정하기 위해서  n 승을 해 줍니다. 예제에서는 32승을 하였습니다. 
   Ambient 는 완전 암흑이 아닌 빛이 어디에서 들어왔는지 모르지만 있는 상황을 모사한 것이고, 자발광은 말 그대로 자기 색을 가진 물체 입니다. 

   언급하진 않았지만, 빛을 멀리 떨어진 단방향으로만 정의 했지만, 백열등 같은 빛도 방향을 정의하고, 거리에 따른 감소를 어떻게 할 것인지만 정하면, 
   거의 동일한 로직으로 구현할 수 있을것 같습니다.   

# 구현된 중요 소스 부분 입니다. 

   ``` glsl 
            #version 300 es
            precision highp float;
    
            in vec2 vTexCoord;
            in vec3 vNormal;
            in vec4 vColors; 
            in vec3 vEyePos;            
    
            uniform sampler2D uTexture;

            uniform int uDisplayType;

            uniform vec3 directionLight;
            uniform vec3 dirLightColor;
            uniform vec3 specularColor;
            uniform vec3 ambientColor;
            uniform vec3 emitColor;
            
            uniform float shineCoef;
            uniform float ambientCoef;  //  rgb 로 제어하려면 vec3            
            uniform float emitCoef;     //  rgb 로 제어하려면 vec3
            
            out vec4 fragColor;
    
            void main() {
               
                vec3 normal = normalize(vNormal);
                vec3 viewDir = normalize(vEyePos);
               
                vec3 light = normalize(directionLight);
                vec3 matDiff = texture(uTexture,vTexCoord).rgb;
                vec3 diffuse = max(dot(light,normal),0.0)*matDiff;
             
                vec3 reflactDir = 2.0*normal*dot(light,normal) - light;
                vec3 specular = pow( max( dot(reflactDir, viewDir),0.0),shineCoef ) * dirLightColor * specularColor;
                
                vec3 ambient = ambientColor*ambientCoef;
                
                vec3 emit = emitColor*emitCoef;

                if ( uDisplayType == 1 ) {
                    fragColor = texture(uTexture,vTexCoord);
                } else if ( uDisplayType == 2 ) {
                    fragColor = vec4(diffuse,1.0);
                } else if ( uDisplayType == 3 ) {
                    fragColor = vec4(diffuse+specular,1.0);
                } else if ( uDisplayType == 4 ) {
                    fragColor = vec4(diffuse+ambient,1.0);
                } else {
                    fragColor = vec4(diffuse+specular+ambient+emit ,1.0);
                }
            }
   ```

   ``` javascript

            //  dataType : 1 : float, 2 : int, 3 : uint, 4 : boolean
            //  dataKind : 1 : value, 2 : vector , 3 : matrix 
            //  dataSize : 1 : 1, 2 : 2, 3 : 3, 4 : 4 => ex vec3, mat3 ...

            const uniformArray = [
                {uniformName : "worldMatrix", data : TypedMatrixUtils.makeIdentityMatrix(4), dataType : 1, dataKind : 3, dataSize : 4, uLocation:undefined, transpose:true},
                {uniformName : "viewMatrix", data : TypedMatrixUtils.makeIdentityMatrix(4), dataType : 1, dataKind : 3, dataSize : 4, uLocation:undefined, transpose:true},
                {uniformName : "projectionMatrix", data : TypedMatrixUtils.makeIdentityMatrix(4), dataType : 1, dataKind : 3, dataSize : 4, uLocation:undefined,transpose:true},                
                {uniformName : "uSampler", data : 0, dataType : 2, dataKind : 1, dataSize : 1, uLocation:undefined,transpose:false},         
                {uniformName : "uDisplayType", data : 5, dataType : 2, dataKind : 1, dataSize : 1, uLocation:undefined,transpose:false},                     

                {uniformName : "eyePos", data : new Float32Array([0.0,0.0,20.0]), dataType : 1, dataKind : 2, dataSize : 3, uLocation:undefined,transpose:false},                                                
                {uniformName : "directionLight", data : new Float32Array([1.0,1.0,1.0]), dataType : 1, dataKind : 2, dataSize : 3, uLocation:undefined,transpose:false},                                                                
                {uniformName : "dirLightColor", data : new Float32Array([1.0,1.0,0.1]), dataType : 1, dataKind : 2, dataSize : 3, uLocation:undefined,transpose:false},                                                                                
                {uniformName : "specularColor", data : new Float32Array([0.8,0.8,0.8]), dataType : 1, dataKind : 2, dataSize : 3, uLocation:undefined,transpose:false},                                                                                                
                {uniformName : "ambientColor", data : new Float32Array([0.1,0.1,0.1]), dataType : 1, dataKind : 2, dataSize : 3, uLocation:undefined,transpose:false},                                                                                                                
                {uniformName : "emitColor", data : new Float32Array([0.0,0.0,0.0]), dataType : 1, dataKind : 2, dataSize : 3, uLocation:undefined,transpose:false},     
                
                {uniformName : "shineCoef", data : 32.0, dataType : 1, dataKind : 1, dataSize : 1, uLocation:undefined,transpose:false},         
                {uniformName : "ambientCoef", data : 0.5, dataType : 1, dataKind : 1, dataSize : 1, uLocation:undefined,transpose:false},         
                {uniformName : "emitCoef", data : 0.1, dataType : 1, dataKind : 1, dataSize : 1, uLocation:undefined,transpose:false},                                                     
            ];

   ``` 


   [이곳에서 적용된 예제를 확인할 수 있습니다.](/html/WebGL2/WebGL_PART_010_01.html)







