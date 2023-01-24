---
title: "WebGL2 - Program 시작 06 - Lighting - [ 10 ]"
date: 2023-01-22T21:34:09+09:00
draft: true
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
   입사각과, 반사각이 눈하고 우연히 일치할 때 그런 현상이 나타나곤 합니다.    
   그런 현상을 모사한 것입니다.     
   빛의 방향과, 꼭지점의 Normal 의 dot product 를 통해 알 수 있는 것은 cos(theta) 값을 알 수 있습니다. ( 두 벡터가 모두 단위 벡터 )   
   우리가 알고 싶은 것은 반사각인데 반사각을 구성하는 벡터는 빛벡터와 노멀벡터를 통해 구할 수 있습니다.    
   빛벡터를 L, Normal 벡터를 N 이라고 하면 2.0*N*dot(L,N) - L 로 구할 수 있습니다.     dot(L,N) 이 cos(theta) 이니 cos(theta) * N  하면 N 이 빗변의 길이와 같으니, 
   N 방향의 짧은 길이의 선분을 구할 수 있습니다.   입사각과 반사각이 같기 때문에 공유하는 N방향의 짧은 선분( 해당 위치에서 직각 삼각형 ) 을 이용해 유도해 나온 공식 입니다.   

   그렇게 반사각을 구한 후에 보는 눈하고 dot product 를 구성하면 위의 diffuse 와 같이 비치는 세기를 알 수 있습니다.    여기서 조금 더 생각할 부분은, 
   모든 각도가 다 빛을 볼 수 있는 것은 아니니 dot product 의 결과를 몇승해서 범위를 좁히는 과정을 거치게 됩니다.    예제에서는 32승을 사용하였습니다.  64승 정도를 추천하고 있습니다.    

   실제로 Fragment Shader 에는 다음과 같이 사용됩니다. 
   ``` c++
                vec3 reflactDir = 2.0*normal*dot(light,normal) - light;
                vec3 specular = pow( max( dot(reflactDir, viewDir),0.0),shineCoef ) * dirLightColor * specularColor;
   ```

   #### Specular 를 사용한 예제 입니다. 

   ![Specular 이미지](/imgs/gl_10_03.png) 
    

   ![Specular 이미지](/imgs/gl_10_04.png)

   ![Specular 이미지](/imgs/gl_10_05.png)   







