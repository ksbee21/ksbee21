---
title: "WebGL2 - Program 시작 04 [ 08 ]"
date: 2023-01-07T22:12:46+09:00
draft: false
tags : ["Web","WebGL", "Language","Javascript", "Programming", "Math"]
topics : []
description : "WebGL2 Camera Matrix"
---

# WebGL 에서 Camera 란 ?
   
   우리가 사진기( 아마도 핸드폰이겠지요 ^^ )로 물체를 찍는 다고 가정해 보겠습니다.    
   핸드폰을 들고, 물체를 보면, 핸드폰 화면에는 찍을 대상이 출력되고 있을 겁니다.    
   이때 카메라 위치가 눈에 해당하고, 찍을 물체가 대상이되고, 핸드폰을 위로 혹은 약간 기울여서 보게 되는게 
   사진에 찍힐 모습이 될 것 같습니다.    
   이를 단순화 하면, eye (사진기위치), at( 사진찍을 물체), up ( 핸드폰을 어떻게 기울였는지 - 대부분 위로 ) 에 따라 
   사진속의 내용이 어떻게 표현될지가 결정 될 것 같습니다.     

   이를 모사하여 화면에 보일 내용을 보여주는 것이 View Matrix ( Camera Matrix ) 의 역활입니다.    


   ### 공간에 대한 간단한 생각

   아래는 앞서 구성했던 Vertex shader 의 uniform 일부 입니다.  
   ``` c++
            #version 300 es
            uniform mat4 worldMatrix, viewMatrix, projectionMatrix;
   ``` 
   world, view, projection 이라는 이름으로 구성되어 있습니다 .    
   각각을 간단히 정리해 보겠습니다.    

   #### World Space ( worldMatrix )
   표현하고자 하는 물체의 좌표는 앞서의 예에서 -1, 1 사이에 x,y,z 좌표를 지닌 물체 였습니다.    
   여러 물체를 그려야 한다면, -1, 1 사이에 모든 대상이 몰려 있으면 큰 물체 하나만 표현이 될 것 같습니다.    
   그래서 우리가 그릴 공간에 표현하고자 하는 물체가 겹치게 않게 위치를 조정해 놓는 작업을 하게 됩니다.    
   그 방법이 물체마다, 왼쪽 오른쪽으로 이동( x position) ,  위로 아래로 이동 ( y position ) , 뒤로 앞으로 이동 ( z position ) 하면서 
   물체끼리 겹쳐서 나타나지 않는 일이 없도록 구성해 볼 수 있습니다.   
   어떤 공간에 물체 하나 하나의 위치를 배치해 주는 역확을 담당하는것이 worldMatrix 의 역활입니다.     
   당연히 물체 하나 하나 마다 각기 달리 적용이 되어야 합니다.    

   #### View Space( Camera Space - viewMatrix )
   공간에 배치된 화면을 어떻게 사진기에 담을까 하는 것이 카메라 공간의 가장 중요한 부분입니다.    
   물체들이 배치 되어 있는 공간에서, 우리가 보는 눈이 어디에 있고( eye ), 초점 대상이 어디에 있는지 ( at ) 그리고 
   카메라를 똑바로 들지 약간 기울일지 ( up ) 에 따라서 배치된 물건이 사진기에 달리 찍히게 됩니다.    
   이를 표현하는 것인 viewMatrix 의 역활 입니다. 

   #### Projection Space ( Clip Space - projectionMatrix )
   우리가 찍은 사진은 사진기 렌즈에 따라 달라지기는 해도, 대부부 멀리 초점을 맞추면, 보는 시야가 좁아 지면서 먼 물체에 초점을 맞추고, 
   가까운 곳에 초점을 맞추면 비교적 넓은 광경을 사진기에 담을 수 있습니다.    
   우리의 시야가 미치는 범위를 보고, 그 안에서 사진을 찍을 수 있는 것 처럼, 화면에 볼 수 있는 ( 표현할 수 있는 ) 영역을 구성하고,  
   원근감을 주기도 하는 역활을 담당하는 것이 projectionMatrix 의 역활 입니다.    
   이 시점에서 모니터에 우리가 표현하고자 하는 대상을 잘 표현할 수 있게 변환하는 작업을 담당합니다.   

# Camera Space ( View Space )
   이전 예제에서 worldSpace 를 통해 위치 이동, 회전등을 표현해 보았습니다.   실세계에서 우리가 있는 위치가 주소로 표현되고, 이 주소는 
   물리적으로 위도 경도의 어디 쯤에 위치해 있는 것이죠...   이것이 어떤 의미에서 worldspace 가 지닌 의미라고 보아도 될 것 같습니다.    
   우리의 시야가 전세계를 볼 수 없듯이 물리적으로 모니터에 표현할 물체를 한정 시키기 위해, 그리고 필요하다면, 멀리 있는 물체는 작게, 가까운 물체는 크게 보이는 
   효과를 위해 달성하기 위해, 구성하는 것이 projection matrix 이고, 그것의 역활이라고 보아도 좋을 것 같습니다.      
   시야각을 어느정도 표현할 지, 종횡비에 따라 가로는 어디까지 표현할지, 
   표현한 가장 가까운 물체와 가장 먼 물체의 위치를 설정하여 모니터 같은 출력 장치에 표현하기 위한 matrix 입니다.    
   
   ### 카메라 공간은 조금 깊게 생각해 볼 여지가 있습니다. 
   물체가 표현되는 것은 우리가 어느 위치에서 대상을 보고 있는냐에 따라 다르게 보입니다.  산의 정상에서 보는 경치와, 산의 중턱에서 보는 장면이 같을 수가 없습니다.    
   차 안에서 밖을 보는 것과, 동일한 차를 밖에서 디자인 혹은 운전을 감상하는 것은 완전히 다른 화면입니다.    
   우리가 표현해야할 대상과 상황에 따라 물리적으로 확정된 물체를 그때 그때 다르게 표현해야할 필요성이 많아 집니다.   이럴 때 사용하는 matrix 가 view matrix( camera matrix ) 입니다.    
   필요에 따라 보는 시각을 달리해 줘야 할 때가 많다는 것을 의미 합니다.     
   때문에 해당 Matrix가 어떻게 구성되는 것인지 조금 이해하고 사용한다면 좋을것 같아서 정리해 보려 합니다.    

   #### 물체가 3차원 공간에 표현되려면 x,y,z 좌표가 있어야 합니다. 
   보는 눈을 기준으로 좌표를 만드려고 합니다.    
   eye( 보는 눈 ), at ( 대상 ), up ( 눈이 보는 대상을 똑바로 위로 보는지 기울여서 보는지) 의 3가지 정보는 필요합니다.    
   eye 를 기준으로 x(u), y(v), z(n) 축을 만든다는 것은 각 축이 서로에 대해 직각으로 구성된다는 전제가 있어야 합니다.    
   1. 눈이 대상을 보는 것이지만, 대상에서 눈 방향으로 직선을 만들어 보겠습니다.  
   > 대상에서 눈으로 진행되는 것이니까 (eye-at) 방향의 vector 가 구성되게 됩니다.    
   이 경우 방향은 눈앞이 minus 방향, 눈을 기준으로 뒤로 plus 방향으로 구성하게 됩니다. 
   ```  javascript 
        //  z 축의 방향 at 에서 eye 방향으로 설정
        let nObj = makeNormalizeVector( makeVectorMinusValues(eye,at) );
   ```

   2. z 축에서 up 방향으로 오른손 법칙을 적용하면 x 축 방향이 나옵니다. 
   > up 의 방향이 카메라를 기울인 각도라면, z 축의 양의 방향에서 up 방향으로 cross product 를 구성하면,(오른손법칙) z 축에 수직인 x(u) 축을 얻을 수 있습니다.   
   ``` javascript 
        //  n(z) vector 에서 up 으로 진행 u(x) vector 를 구함 cross product 는 두 벡터 평면에 수직
        let uObj = makeNormalizeVector( makeVectorCrossProductValues(up, nObj));
   ```
   함수에서 cross product 는 계산 방향이 중요합니다. 시작이 뒤에 목적지가 앞에 오게 됩니다.  

   3. x(u) 축의 양의 방향에서 z(n) 축의 양의 방향으로 오른손 법칙을 적용하면 y(v) 축이 나옵니다. 
   > x(u) 와 z(n) 축에 수직인 vector 가 y(v) 축 방향이고, 이는 cross product 를 통해 얻을 수 있습니다. 
   ``` javascript 
        //  u(x) 에서 n(z) 축 방향으로 cross product v(y) 방향 vector 를 구함
        //  이미 normalize 된 수직인 두 벡터의 cross product 결과는 normalize 된 벡터 
        let vObj = makeVectorCrossProductValues(nObj,uObj);
   ```
   위의 nObj, uObj 는 normalize 를 해 주었지만, 이미 단위벡터인 u, n 을 cross product 하면 결과가 단위 벡터라 별도로 normalize 를 하지 않았습니다.    

   #### 이렇게 구해진 좌표를 world 공간의 x, y, z 과 일치 시킵니다.  
   world 공간에서 주어진 x,y,z 과 카메라 공간에서 구해진 x(u),y(v),z(n) 을 일치시키려면 카메라 를 world 공간으로 이동한 후, world 공간과 각도를 일치 시키면, 
   이후 모든 계산은 이렇게 구해진 camera matrix 를 통해 보이는 좌표를 계산 할 수 있습니다.    

   1. 먼저 world 공간과 이동에 의해 좌표를 일치 시킵니다.   
   앞서 구성한 카메라 공간 좌표는 눈을 기준으로 만든 겁니다.    
   그러니까 world 공간의 0, 0, 0 의 좌표와 일치 시키기 위해서는 eye 의 -x, -y, -z 으로 이동하면 world 공간과 같은 점을 보게 할 수 있습니다.  
   ``` javascript 
        //  world 공간의 원점을 통합하기 위해서 translate 이후 rotation 진행
        //  translate 는 eye 값을 -부호 붙이값 
        const translate = makeTranslateMatrix3D(-eye[0], -eye[1], -eye[2]) ;
   ```

   2. 이제 이 좌표에서 world 공간 x,y,z과 일치 시킵니다.   
   앞선 구한 값이 u(x), v(y), n(z) 은 opengl 이 열백터 이기 때문에 아래와 같이 만들수 있습니다.   
   ``` javascript
   [
      u[0], v[0], z[0],
      u[1], v[1], z[1],
      u[2], v[2], z[2],
   ]
   ```
   그런데 world 좌표와 x,y,z을 일치 시키기 위해서 u,v,n을 전치 행렬로 구성하면 회전 행렬을 만들 수 있습니다.  
   이 경우  전치 행렬이 역행렬 역활을 하게 되는 거지요 그래서 아래와 같은 행렬을 구성할 수 있습니다.  
   ``` javascript 
   [
      u[0],u[1],u[2],
      v[0],v[1],v[2],
      n[0],n[1],n[2]
   ]
   ```
   그 결과 입니다. 
   ``` javascript 
        //  rotation 은 u, v, n 의 transpose 값으로 얻음
        const rotate = new Float32Array([
            uObj[0], uObj[1], uObj[2], 0,
            vObj[0], vObj[1], vObj[2], 0,
            nObj[0], nObj[1], nObj[2], 0,
            0, 0, 0, 1            
        ]);
   ```

   3. 이렇게 구해진 좌표를 통합하여 하나의 matrix 로 구성합니다. 
   이동이 먼저 그리고 회전 입니다. 
   ``` javascript 
        const result = multiplyMatrix(rotate, translate);
   ```

   #### 이렇게 구성된 카메라를 구하는 script 입니다. 

   ``` javascript 

      export const makeCameraMatrix3D = ( eye, at, up ) => {
         //  z 축의 방향 at 에서 eye 방향으로 설정
         let nObj = makeNormalizeVector( makeVectorMinusValues(eye,at) );

         //  n(z) vector 에서 up 으로 진행 u(x) vector 를 구함 cross product 는 두 벡터 평면에 수직
         let uObj = makeNormalizeVector( makeVectorCrossProductValues(up, nObj));

         //  u(x) 에서 n(z) 축 방향으로 cross product v(y) 방향 vector 를 구함
         //  이미 normalize 된 수직인 두 벡터의 cross product 결과는 normalize 된 벡터 
         let vObj = makeVectorCrossProductValues(nObj,uObj);

         //  world 공간의 원점을 통합하기 위해서 translate 이후 rotation 진행
         //  translate 는 eye 값을 -부호 붙이값 
         //  rotation 은 u, v, n 의 transpose 값으로 얻음
         const translate = makeTranslateMatrix3D(-eye[0], -eye[1], -eye[2]) ;
         const rotate = new Float32Array([
               uObj[0], uObj[1], uObj[2], 0,
               vObj[0], vObj[1], vObj[2], 0,
               nObj[0], nObj[1], nObj[2], 0,
               0, 0, 0, 1            
         ]);
         rotate.rows = 4;
         rotate.cols = 4;

         const result = multiplyMatrix(rotate, translate);
         return result;
      };

   ```

# 이것으로 무엇을 할 수 있을까요?
   
   3D를 공부할 때, 공간 전환의 이야기가 나오면 헷갈리고, 연산을 위한 수식이 의미하는 바를 정확하게 알지 못하면서 따라하다 보니, 
   혼란이 더 가중되었던 것 같습니다.    
   앞의 글들에서 간단히 정리해 보았지만, 벡터 끼리 연산에서 빼기는 향하는곳 - 시작한곳 이니 시작에서 목표로의 방향이 됩니다.    
   두 벡터의 cross product 는 두 벡터에 수직 방향의 벡터를 가져오게 되는데 순서가 중요합니다. 대상이 앞에 시작이 뒤에 오게 되면 
   시작에서 대상으로의 수직 방향을 얻을 수 있습니다.    

   ### 움직이는 물체를 중심으로 세상을 표현할때
   주인공 혹은 특정 물체의 입장에서 움직임은 world 좌표에서의 움직임으로 표시할 수 있습니다.    
   이 때 처음 위치에서 움직인 위치 까지를 delta 라고 하면 그 값을 처음 eye 에 적용하고, at 에 적용하면 eye, at 의 거리는 줄지 않지만, 
   카메라 시각이 처음 물체에서 본 것과 비슷하게 유지될 수 있을 것 같습니다.    
   주인공을 따라가는 카메라, 차에서 본 풍경 등이 이렇게 구성될 수 있을 것 같습니다.  

   ### 대상은 그대로 인데 세상이 돌아갈때
   물체가 회전하는 것은 물체 자체에 회전을 구성해서 구현할 수 있습니다.     
   가끔 영화에서 대상은 그대로 인데 빙글빙글 도는 세상을 표현하는 장면을 연출하기도 합니다.   
   이때 카메라의 at 은 고정해 놓고, eye 만 좌표를 변경하면 빙글빙글 도는 세상을 만들어 보일 수도 있을 것 같습니다. 

   ### 위 둘은 예시 일 뿐 훨씬 다양한 효과를 카메라를 통해 구현할 수 있지 않을 까 생각해서 조금 자세히 정리해 보았습니다.    

   [다음은 -Click- 간단한 예시](/html/WebGL_PART_008_01.html) 입니다.    
   앞서와 비슷하게 도는 물체 이지만, 이번에는 자체적으로 도는 것이 아니라 카메라를 회전하여 도는 효과를 구현해 보았습니다.   


   ``` javascript 
                //   주석 3  : translation 
                //worldMatrix = TypedMatrixUtils.multiplyMatrix(worldMatrix,TypedMatrixUtils.makeTranslateMatrix3D(1,0,-8));
                worldMatrix = TypedMatrixUtils.multiplyMatrix(TypedMatrixUtils.makeTranslateMatrix3D(0,0,-8), worldMatrix);
                //worldMatrix = TypedMatrixUtils.multiplyMatrix(TypedMatrixUtils.makeRotateYMatrix3D(radian), worldMatrix);

                let eye = [0, 0, 2];

                let v = Math.sin(delta) * 10-8 ;
                let c = -Math.cos(delta) * 10 ;
                eye = [c, 0, v];
                //eye = [0,0, 200];
                let at = [0, 0, -8];
                let up = [0, 1, 0];

                //  view
                viewMatrix = TypedMatrixUtils.makeCameraMatrix3D( eye, at, up );

                //  [ 주석 4 ] : projection 
                projectionMatrix = TypedMatrixUtils.makePerspectiveMatrix(48*Math.PI/180, gl.canvas.width/gl.canvas.height, 0.1, 1000);

   ``` 







   






