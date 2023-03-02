---
title: "WebGL2 - Program 시작 09 - Object Picking 02 - [ 13 ]"
date: 2023-03-01T19:50:57+09:00
draft: false
tags : ["Web","WebGL", "Language","Javascript", "Programming","Math"]
topics : []
description : "WebGL 물체 선택 ( Picking Ray - Tracing 정리 )"
---
   사용자가 선택한 위치의 Pixel 정보를 기반으로 구성하고 있는 물체를 식별하는 방법은 구성의 단순함 측면에서, 구현의 편리성 측면에서 좋은 방법 같습니다.    
   이런 방법외에 사용할 수 있는 방법이 마우스가 클릭한 위치에서 광선을 만들어 해당 위치에 물체가 있는지, 있다면 어느 물체가 가까운지 찾아서 물체를 선택하는 방법이 있습니다.    
   다소 복잡한 연산을 통해 물체를 찾는 방법이지만, 광선을 추적하는 기본 알고리즘을 정리할 수 있는 부분이라, 간단히 정리해 보고자 합니다.    
   실제 WebGL로 구현하는 것 보다는 추적하는 과정을 예제로 구성하여 추적해 보려고 합니다.   Sphere(구) 와의 충돌 정도까지 정리하고자 합니다.    

# 위치변환 과정  
   ### Canvas 좌표( Screen )
   이전 예제에서 사용하던 좌표를 가지고 하나씩 확인해 보고자 합니다.    
   canvas 의 좌표는 왼쪽 상단을 기준으로 0,0 에서 우측 하단으로 width, height 의 값으로 진행됩니다.    
   어떤 좌표를 mouse 로 클릭하게 되면 위치는 (event.offsetX, event.offsetY) 값으로 가져올 수 있는데 이 값은 위 좌표 표면에서의 값입니다. 
   ( 해당 Canvas 가 다른 dom 객체의 하위 등으로 scroll 이 발생하는 등의 상황에서는 offset 정보 만으로 정확한 위치를 찾을 수 없습니다. )    
   canvas 의 width 가 1024, height 가 768 일때 사용자의 mouse click 위치가 554(553.8853...), 178(177.9512...) 라면 대략 중심에서 조금 오른쪽 상단 중간
   정도를 클릭한 위치일 것입니다.   좌표계 마다 중심점과 시작 종료 지점을 확인하는게 중요한데 일단 사용한 스크린은 2차원이고, 
   가로로 0 ~ 1024 의 왼쪽에서 오른쪽 진행이고, 세로로 0 ~ 768 의 위에서 아래의 진행방향입니다.    
   이 지점을 클릭하였다고 가정하고 진행해 보겠습니다.   
   ### NDC (Clip 공간에서의 좌표)  
   사각형 상자를 연상하면 좋을 것 같습니다.  x 는 왼쪽에서 오른쪽을 -1 ~ 1, y 는 아래에서 위로 -1 ~ 1 이고 깊이는 앞에서 뒤로 -1 ~ 1 의 네모난 상자를 연상하면 
   유사한 모양이 될 것 같습니다.  이 공간은 이전의 예제에서 Projection Matrix 에 의해 변환된 공간이었습니다. 
   시야각 (fovy), 종횡비 (aspect), 가까운위치(near), 먼위치(far) 를 기준으로 구성한 Matrix 에 의해 구성된 공간 이었습니다.    
   먼저 Screen 에서의 좌표를 생각해 보겠습니다.   앞서 클릭한 마우스 위치를 screenX, screenY 라고 하면,   
   x 좌표는 screenX * 2 / canvasWidth -1, y 좌표는 1-screenY*2/canvasHeight 이고, z은 시작점이니 -1 로 주어 집니다.  
   위의 값으로 대략 구성하면 x = (553.885*2/1024-1) = 0.0818...  y = (1-177.951*2/768) = 0.5366... 정도의 값이 나옵니다.   이 좌표가 NDC 에 표기되는 x, y 좌표입니다.    
   그런데 이 좌표는 원래 Projection 에 의해서 -1~1 사이의 좌표로 변환된 것이고 , 이것에 관여한 부분이 Projection Matrix 였습니다. 
   ### Camera Space ( View Space )
   앞서 Object Picking 에 사용하였던 Projection 을 잠시 다시 확인해 보겠습니다.    
   ``` javascript 
        const near = 0.1;
        const par  = 1000;
        const fovy = 90*Math.PI/180;
        const aspect = canvasWidth/canvasHeight;
        const cv = 1.0/Math.tan(fovy/2);

        const projectionMatrix = TypedMatrixUtils.makePerspectiveMatrix(fovy, aspect, near, par);
   ```
   NDC 에서 구성된 좌표를 프로젝션이 적용되기전 Camera 공간의 좌표로 옮겨가기 위해서는 위에서 사용한 projection Matrix 의 inverse matrix 를 활용하면 
   camera 공간의 위치로 환원할 수 있습니다.    
   대략 그렇게 구성한 위치가 x = 0.1091..., y = 0.5366... 정도 입니다.  카메라 공간에서는 Z 의 방향이 -Z 축을 바라보도록 구성하였기 때문에 
   Ray 의 시작점은 (0.1091, 0.5366,-1,1 ) 이고, 방향은  (0.1091, 0.5366,-1, 0 ) 입니다.     
   위와 같은 방식으로 구성하여도 되지만, 다른 방법으로 스크린 좌표에서 직접 카메라 공간까지 오는 방법은 다음과 같은 방식도 가능합니다.   
   어쩌면 거리를 표현할 때 더 적합한 방법이 될 수 있습니다.  near 값을 최초 시작 지점으로 구성하게 되는 방법입니다.   아래 참조한 유튜브 동영상 강의에서 제시하는 
   방법입니다.     
   강의 에서는 y 좌표가 아래에서 위로 올라가는 방향인데, web canvas 좌표는 위에서 아래로 가능 방향이라 부호를 뒤집어 붙인 부분만 차이가 있습니다.  
   ``` javascript 
        const cv = 1/Math.tan(fovy/2);
        const aspect = width/height;
        const xc = (near/(cv/aspect))*(2*screenX/width - 1);
        const yc = -(near/(cv))*(2*screenY/height - 1);

        const startPos = vec4(xc,yc,-near,1);
        const startRay = vec4(xc/near,yc/near,-1,0); 
   ``` 
   이런 방법으로 구성한 Ray 는 시작점이 ( 0.0109, 0.0537, -0.1, 1) 이고, 방향은  (0.1091, 0.5366,-1, 0 )  로 위와 동일합니다.   -near 위치로 구성되어 있어서 
   그 값만 보정하면 위의 위치값과 동일한 값을 가르키게 됩니다.    

   ### World 공간 
   카메라 공간에서 바라보는 위치에서 물체가 처음 놓인 위치로(각 Object 마다 배치된) 변환해서 보기 위해서는 Camera matrix 의 역변환이 필요합니다.    
   카메라 Matrix 의 inverse를 계산에 의해서 구성할 수도 있으나, 처음 카메라 공간을 만드는 Matrix 를 구성하는 과정이, translation 후 rotation을 하여 구성하였습니다.  
   rotation 은 u,v, n 을 구한후 열백터로 나열할 부분을 transpose( 역변환 역활) 해서 행백터 처럼 적용하였고,  eye position 을 음수로(역으로) 만들어 translate 를 수행하였습니다.     
   그렇기 때문에 ratation 의 역변환 하고, 이 후 translation 의 역변환을 적용하면, 전체 역변환 Matrix 를 구할 수 있습니다.      
   물론 직접 계산에 의해 Inverse Matrix 를 구성할 수도 있습니다.    아래의 코드는 위 방식으로 Inverse 를 구성하는 방법입니다.  
   ``` javascript
        export const makeCameraInverseMatrix3D = ( eye, at, up ) => {
            //  z 축의 방향 at 에서 eye 방향으로 설정
            let nObj = makeNormalizeVector( makeVectorMinusValues(eye,at) );

            //  n(z) vector 에서 up 으로 진행 u(x) vector 를 구함 cross product 는 두 벡터 평면에 수직
            let uObj = makeNormalizeVector( makeVectorCrossProductValues(up, nObj));

            //  u(x) 에서 n(z) 축 방향으로 cross product v(y) 방향 vector 를 구함
            //  이미 normalize 된 수직인 두 벡터의 cross product 결과는 normalize 된 벡터 
            let vObj = makeVectorCrossProductValues(nObj,uObj);

            //  world 공간의 원점을 통합하기 위해서 translate 이후 rotation 진행 => rotation 후 translation 
            //  translate 는 eye 값을 -부호 붙이값 - 다시 환원
            //  rotation 은 u, v, n 의 transpose 값으로 얻음 - 다시환원
            const rotate = new Float32Array([
                uObj[0], vObj[0], nObj[0], eye[0],
                uObj[1], vObj[1], nObj[1], eye[1],
                uObj[2], vObj[2], nObj[2], eye[2],
                0, 0, 0, 1            
            ]);
            rotate.rows = 4;
            rotate.cols = 4;
            return rotate;
        };

   ``` 
   이렇게 구성된 값으로 world 공간에서의 시작점과, 방향을 구성해 보면 대략  위치 (0.0109, -14.9073, 9.9346, 1), 방향 ( 0.1091, 0.9272, -0.6545, 0) 값을 얻게 됩니다. 
   이 값을 각 구성한 object 의 local world space 의 역변환을 구성하여 계산하면, 각 object 의 광선을 구성할 수 있습니다.   
   object 별로 ray 가 구성되고, 그 ray 와 충돌을 확인하면 사용자가 선택한 위치에 해당 object 가 존재하는지 여부를 확인할 수 있습니다.  
   screen 공간에서 view 공간을 통해 현재 world 공간으로 전환하는 함수 입니다. 
   ``` javascript 

        /**
         * 
         * @param {*} screenX : canvas base offsetX 0 ~ width ( left to right)
         * @param {*} screenY : canvas base offsetY 0 ~ height ( top to bottom )
         * @param {*} width : fullWidth
         * @param {*} height : fullHeight 
         * @param {*} fovy : radian value
         * @param {*} near : projection matrix 의 가까운 지점 
         * @param {*} eye : camera eye pos
         * @param {*} at : camera target pos
         * @param {*} up : camera 기울기 방향
         */
        export const makeRayTracingViewPosValues = ( screenX,screenY,width,height, fovy, near, eye, at, up ) => {
            const cv = cot(fovy/2);
            const aspect = width/height;
            const xc = (near/(cv/aspect))*(2*screenX/width - 1);
            const yc = -(near/(cv))*(2*screenY/height - 1);

            const viewInverseMatrix = makeCameraInverseMatrix3D(eye,at,up);        

            const startPos = vec4(xc,yc,-near,1);
            const startRay = vec4(xc/near,yc/near,-1,0); 

            const viewPos = multiplyMatrix(viewInverseMatrix, startPos);
            const viewRay = multiplyMatrix(viewInverseMatrix, startRay);        

            return {
                viewPos : viewPos,
                viewRay : viewRay,
            };
        };

   ``` 

   ### Local Object Space 

   앞선 예제에서 움직이는 사각형의 시작은 [2,2,-2] 의 좌표 공간에서 시작하였습니다.    해당 좌표의 육면체는 object 공간에서 -1~1 사이의 값을 지니는데 
   그냥 [0,0,0] 좌표라고 할수도 있습니다.   
   scale 을 변환시키지 않았다면, 고유한 local world 공간에서의 Matrix 는 trasnlation 을 적용한 위치 입니다.  그 위치를 역변환 하여 위의 위치와 방향에 적용하면 
   위치는 (-1.9891, -16.9073, 11.9346, 1) 이고 방향은 ( 0.1091, 0.9272, -0.6545, 0) 입니다.    
   RayPosition + t * RayDirection 이 0,0,0,0 이 되게 하려면, 거리인 t 가 18.235756... 이면 원점에 도달하게 됩니다.   해당 위치에 물체가 있다는 것이지요 ....

   ``` javascript 
   
        /**
         * 
         * @param {*} viewPosValues : viewPos, viewRay 
         * @param {*} localWorldMatrix : object local world matrix
         */
        export const makeRayTracingLocalWorldPosValues = ( viewPosValues, localWorldMatrix ) => {

            const worldInverseMatrix = makeInverseMatrix(localWorldMatrix);
            const worldPos = multiplyMatrix(worldInverseMatrix, viewPosValues.viewPos);
            const worldRay = multiplyMatrix(worldInverseMatrix, viewPosValues.viewRay);        

            return {
                worldPos : worldPos,
                worldRay : worldRay,
            };
        };
   
   ```
   사실 위의 놓인 값은 예제에서 처음 시작한 위치를 계산하여 역으로 추적한 내용입니다.    
   소스를 보는게 어쩌면 더 이해가 빠를지 모르겠습니다. 

   ``` javascript 

        const canvasWidth = 1024;
        const canvasHeight = 768;

        let eye = [0.0, -15.0, 10.0];
        let at = [0.0, 0.0, -20.0];
        let up = [0.0, 1.0, 0.0];

        const near = 0.1;
        const par  = 1000;
        const fovy = 90*Math.PI/180;
        const aspect = canvasWidth/canvasHeight;
        const cv = 1.0/Math.tan(fovy/2);
        
        const orgPos = TypedMatrixUtils.vec4(0,0,0,1);

        const worldMatrix = TypedMatrixUtils.makeTranslateMatrix3D(2,2, -2);
        const worldInverseMatrix = TypedMatrixUtils.makeInverseMatrix(worldMatrix);

        //  view
        const viewMatrix = TypedMatrixUtils.makeCameraMatrix3D( eye, at, up );
        const viewInverseMatrix = TypedMatrixUtils.makeCameraInverseMatrix3D(eye,at,up);

        const projectionMatrix = TypedMatrixUtils.makePerspectiveMatrix(fovy, aspect, near, par);
        const projInverseMatrix = TypedMatrixUtils.makeInverseMatrix(projectionMatrix);


        const wPos = TypedMatrixUtils.multiplyMatrix(worldMatrix, orgPos);
        const vPos = TypedMatrixUtils.multiplyMatrix(viewMatrix, wPos);
        const pPos = TypedMatrixUtils.multiplyMatrix(projectionMatrix, vPos);
        for ( let i = 0; i < 4; i++ ) {
            pPos[i] /= pPos[3];
        }

        // mouse click points 
        const screenX = canvasWidth/2 * ( 1 + (pPos[0]));
        const screenY = (1-pPos[1])/2*canvasHeight;

        let xc = (near/(cv/aspect))*(2*screenX/canvasWidth - 1);
        let yc = -(near/(cv))*(2*screenY/canvasHeight - 1);

        const startPos = TypedMatrixUtils.vec4(xc,yc,-near,1);
        const startRay = TypedMatrixUtils.vec4(xc/near,yc/near,-1,0); 

        const viewPos = TypedMatrixUtils.multiplyMatrix(viewInverseMatrix, startPos);
        const viewRay = TypedMatrixUtils.multiplyMatrix(viewInverseMatrix, startRay);        

        const worldPos = TypedMatrixUtils.multiplyMatrix(worldInverseMatrix, viewPos);
        const worldRay = TypedMatrixUtils.multiplyMatrix(worldInverseMatrix, viewRay);        

        //  위의 과정을 두개의 함수로 처리함 ...
        const viewObj = TypedMatrixUtils.makeRayTracingViewPosValues ( screenX,screenY,canvasWidth,canvasHeight, fovy, near, eye, at, up );
        const worldObj = TypedMatrixUtils.makeRayTracingLocalWorldPosValues( viewObj, worldMatrix );

        const rayPos = TypedMatrixUtils.makeVec3(worldObj.worldPos); 
        const rayDir = TypedMatrixUtils.makeVec3(worldObj.worldRay);       
        const center = TypedMatrixUtils.vec3(0.0,0.0,0.0) ;
        const radius = 1.0;

        //  구와 충돌 여부 판정 
        const hitObj = TypedMatrixUtils.traceRayInterceptionForSphere(rayPos, rayDir, center, radius);

   ``` 

   ### Sphere Line 충돌
   광선과 삼각형, 광선과 구는 조금더 정리가 필요할 것 같습니다.   
   대략 구와의 교점을 구하는 부분을 정리하면, 구의 방정식을 풀어서 정리하는 방법을 사용합니다.    
   ( x - cx )^2 + ( y - cy ) ^2 + ( z -cz )^2 = r^2  의 식에서 x, y, z 은 위치, cx,cy, cz 은 중심위치, r 은 반지름 입니다.    
   해당 구안에 포함되어 있으려면 x,y,z 의 위치가 Ray x,y,z 으로 구성된 식이 x 에 관해서 ( rx + t * rdx ) 라는 식이 있다면 rx 는 시작위치, 
   t 는 진행 거리, rdx 는 x 축의 광선 방향 이 구 안에 있다면 교차점이 있는 것이고 없다면 교차 하지 않기 때문에 충돌하지 않는다로 판정합니다.    
   결국 거리인 t 를 기준으로 하는 방정식을 도출하면 t 에 관련한 2차 방정식이 나오고, 이는 근의 방정식으로 해를 구하게 됩니다.    

   벡터 연산으로 계산하면 계산이 훨씬 간결해 지는데 유도 과정을 확인하려면 하나씩 풀어서 보는게 좋을 수도 있습니다. 
   대략 위의 연산과정을 통해 충돌여부를 판정하게 됩니다.    
   삼각형 충돌을 정리할 때 풀어서 정리해 봐야 할 것 같습니다. ^^


# 참고 사이트

   공개된 유튜브 강의 내용중 가장 많이 도움을 받은 사이트 입니다.
   #### [https://www.youtube.com/watch?v=c7la2Tt_cOc](https://www.youtube.com/watch?v=c7la2Tt_cOc)  

   낙서처럼 보이는 처음 화면이 거의 모든것을 이야기 해 주는 사이트 입니다. 
   #### [https://antongerdelan.net/opengl/raycasting.html](https://antongerdelan.net/opengl/raycasting.html)

   구와 직선의 교차에 대한 Wiki Site 입니다. 
   #### [https://en.wikipedia.org/wiki/Line%E2%80%93sphere_intersection](https://en.wikipedia.org/wiki/Line%E2%80%93sphere_intersection)

   WebGL(OpenGL) 이 아니고 유료강의지만, Ray Tracing에 관한 설명과 실습이 정말 잘되어 있는 Site 입니다.    
   그래픽스새싹1만 들었는데, 구성과 전달력에 감탄하며 들었던 사이트 입니다.    여력이 되면 나머지도 수강할 생각입니다. (^^)
   #### [https://honglab.co.kr/](https://honglab.co.kr/)
       
