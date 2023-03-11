---
title: "WebGL2 - Program 시작 11 - 회전 - [ 15 ]"
date: 2023-03-11T11:11:25+09:00
draft: false
tags : ["Web","WebGL", "Language","Javascript", "Programming","Math"]
topics : []
description : "WebGL 회전 변환 ( 카메라 중심 변환 )"
---

   이전 WebGL 의 예시된 Sample 에서도 간단한 움직임을 표시한 적이 있었습니다.    
   물체 자체를 움직이기 위해서 이동(Translation), 혹은 회전(Rotation) 혹은 이 둘의 결합으로 움직임을 표현하기도 하고, 
   물체가 아닌 카메라의 위치 이동을 통해 물체의 다른 면을 주시하게 하여 변화된 화면을 구성해 보기도 하였습니다.    
   어느 대상을 중심으로 움직임을 표현하는가에 따라 구성 방법은 달라지겠지만, 움직임을 표현하기 위한 회전은 공통적으로, 
   X, Y, Z 축을 대상으로 각각 어느 정도의 각도로 움직이는 가를 표현 하였습니다.    
   이런 방식의 회전을 오일러 각도를 이용한 변환이라고 하며, 직관적이기 때문에 구현이 상대적으로 편리한 측면이 있습니다.    
   다만, 약간의 문제가 있는데 3축을 중심으로 변환이 이뤄지다 보니, 어느축을 먼저 선택하였는지에 따라 회전이 다르게 나타나고, 
   더 중요하게는 바깥쪽의 축과, 안쪽의 축이 중간축의 변환에 의해 서로의 축이 겹치면서 회전축 하나를 잃어 버리는 현상이 나타 납니다.   
   짐벌락(Gimbal lock)이라고하며, 이런 현상을 해결하는 방법으로 쿼터니언을 이용한 변환을 사용하기도 합니다.    
   
   #### [카메라 위치 변환 예제 - 마우스로 드래그 하면 VIEW 위치 변환 예제](/html/WebGL2/WebGL_PART_014_01.html)   
   예제에는 회전한 값을 저장하는 로직이 없기 때문에 다시 클릭하면 다시 변환이 이뤄집니다.    


# X,Y,Z 축을 기반한 회전
   회전(Rotation)을 실제로 계산해 보겠습니다.   이전에 구성했던 Rotation 함수를 사용할 것인데, 처음 주어진 지점이 (1,1,1,1) 이라고 하겠습니다.   
   x,y,z 이 모두 1이고, 마지막 1은 단위를 4 x 4 행렬로 계산할 수 있도록 1을 추가하였습니다.     
   각도는 45도를 각 축별로 변환하는 것으로 하고, 변환하는 방법은 축의 순서를 기준으로 x,y,z 으로의 변환 하나와, z,y,x 로의 변환 하나를 비교해 보겠습니다.    

   #### 첫번째 회전의 결과는 1.2071.. , 1.2071.. , 0.2928.. , 1 입니다. 
   #### 두번째 회전의 결과는 0.7071.. , 0.5      , 1.5      , 1 입니다. 

   회전 순서에 의해서 전혀 다른 결과가 나오고 있습니다.    
   계산하는 방법은 다음의 소스를 참조하시면 좋을 것 같습니다.   
   ``` javascript 

        const points = TypedMatrixUtils.vec4(1,1,1,1);
        const degree = 45;
        const theta = (Math.PI*degree/180);

        const xRotate = TypedMatrixUtils.makeRotateXMatrix3D(theta);
        const yRotate = TypedMatrixUtils.makeRotateYMatrix3D(theta);
        const zRotate = TypedMatrixUtils.makeRotateZMatrix3D(theta); 
        
        let rvf01 = TypedMatrixUtils.multiplyMatrix(xRotate, points);
        let rvf02 = TypedMatrixUtils.multiplyMatrix(zRotate, points);

        rvf01 = TypedMatrixUtils.multiplyMatrix(yRotate, rvf01);
        rvf02 = TypedMatrixUtils.multiplyMatrix(yRotate, rvf02);

        rvf01 = TypedMatrixUtils.multiplyMatrix(zRotate, rvf01);
        rvf02 = TypedMatrixUtils.multiplyMatrix(xRotate, rvf02);

        //  이런 방법으로 구성할 수도 있습니다. 
        let result1 = TypedMatrixUtils.multiplyMatrix(yRotate,xRotate);
        result1 = TypedMatrixUtils.multiplyMatrix(zRotate,result1);

        let result2 = TypedMatrixUtils.multiplyMatrix(yRotate,zRotate);
        result2 = TypedMatrixUtils.multiplyMatrix(xRotate,result2);     
        
        let rf01 = TypedMatrixUtils.multiplyMatrix(result1, points);
        let rf02 = TypedMatrixUtils.multiplyMatrix(result2, points);

   ``` 
   순서만 정확히 일치 시키면 rotation 하나씩 계산하나, 모든 rotation matrix 를 계산후 계산하나 결과는 같습니다.   
   사용한 함수는 이전글에서 사용하였던 함수라 생략하였습니다.    

   물체를 왼쪽 오른쪽 혹은 위로, 아래로 등 방향을 특정해서 구성할 때는 좋은 방법이고 이해하기도 편하지만, 여러 축을 활용하여 원하는 지점에 
   회전하도록 구성할 때 순서와 위치를 잘 고려 하여야 하기 때문에 사용하기 어려운 점이 있을것 같습니다.     

# 구(Sphere) 표면에서의 이동 
   3D 를 표현하는 인터넷 사이트에서 사용자가 마우스로 위치를 선택후 이동하면 자연스러운 회전을 보여주는 사이트가 많이 있습니다.   
   각기 만든 방식을 정확히 알 수는 없지만, 위의 축을 기준으로 회전을 표현하는 방식은 순서나, 축이 겹치는 문제가 발생할 수 있기 때문에 
   아마도 사용하지 않았을 것 같습니다. ( 물론 사이트에 따라 해당 방식을 사용하였을 수도 있습니다. ^^ )    
   어떻게 자연스러운 회전을 구현할 수 있을지 정리해 보고자 합니다.    
   3D 공간에서는 2D 와 달리 축이 하나 더 있습니다. X,Y,Z 으로 구성된 공간을 생각해 볼 수 있습니다.    
   그 공간을 구라고 가정해 보면 스크린에서 선택한 위치의 x,y,z 을 구성할 수 있습니다.   x 축을 -1 ~ 1, y 축을 -1 ~ 1, z 축을 -1 ~ 1 사이의 값을 지닌
   구라고 할 때 스크린에서 보는 좌표인 x, y 를 기준으로 실제 사용할 x, y, z 좌표를 가정 할 수 있다면 시작 지점과 종료 지점을 통해 2개의 x,y,z 좌표를 
   계산해 낼 수 있을 것입니다.   Arcball 이라는 개념으로 2차원 x,y 좌표를 구의 x,y를 선택한 것으로 보고 z 을 구하는 방법 입니다.   
   반지름이 1인 반원의 구가 스크린에 있다라고 생각 하면, 좌표는 X 는 -1 ~ 1 의 범위를 지닐 것이고, Y 도 -1 ~ 1 사이의 값을 지니는데 반원이기 때문에 
   화면에 보이는 Z 은 0 ~ 1 사이의 값이 된다고 볼 수 있습니다.    
   구의 공식은 x ^ 2 + y ^2 + z ^2 = r ^2 이고, sqrt( x ^ 2 + y ^2 + z ^2 ) = r 입니다.   이 공식으로 부터 z 의 값은 z ^2 = r^2 - ( x^2 + y^2 ) 임을 알 수 있습니다.    
   $$
    \begin{aligned}
        x^2 + y^2 + z^2 = r^2 \quad \sqrt{x^2+y^2+z^2} = r \\\
        z^2 = r^2-(x^2+y^2) \quad z = \sqrt{r^2 - (x^2+y^2)}
    \end{aligned}
   $$  
   이것을 javascript 구성한 function 입니다. unit vector 로 구성하고 있습니다. 
   ``` javascript 
    
    /**
     * 
     * @param {*} cx : mouse click x 좌표 ( 0 ~ fw ) 왼쪽에서 오른쪽 방향
     * @param {*} cy : mouse click y 좌표 ( 0 ~ fh ) 위쪽에서 아래쪽 방향
     * @param {*} fw : 전체 가로 길이
     * @param {*} fh : 전체 세로 길이
     * @returns 
     */
    export const makeArcballValues = (cx, cy, fw, fh) => {
        let tx = ((2*cx)/fw - 1.0);
        let ty = (1.0- (2*cy)/fh);
        let tSum = tx*tx + ty*ty;
        if ( tSum <= 1.0 ) {
            return vec3(tx, ty, Math.sqrt(1-tSum));
        } else {
            return makeNormalizeVector(vec3(tx,ty,0));
        }
    };
   ```
   시작점과, 끝점의 좌표를 알면 두개의 vector 를 가져올 수 있습니다.   
   직전 포스팅 에서도 언급되었듯이, 두 vector 의 cross product 의 결과는 두 점이 놓인 평면에 수직인 vector 가 만들어 집니다.  
   이 vector 를 normalize 하면 unit vector 가 만들어 지게 되며, 이 벡터가 회전 축을 의미하게 됩니다.    
   Vector dot product 의 결과는 유닛벡터일때 cosine theta 값이 됩니다.  이 특징을 활용하면 시작점과 끝점을 각각 unit vector 
   로 구성하게 만들고, dot product 결과 cos theta 값을 얻고, cross product 결과를 normalize 하여 회전축을 얻을 수 있습니다.  
   $$
    \begin{aligned}
      a \cdot b = cos(\theta) * \vert\vert a \vert\vert * \vert\vert b \vert\vert \quad, \quad \vert\vert a \vert\vert = \vert\vert b \vert\vert = 1  이면 \quad a \cdot b = cos(\theta) \\\
    \end{aligned}
   $$  
   javascript 구성한 함수 입니다.   

   ``` javascript 

    export const calculateAxisAngles = (sx,sy, ex,ey,fw,fh) => {
        const vs = makeArcballValues(sx,sy, fw, fh);
        const ve = makeArcballValues(ex,ey, fw, fh);
        const rdv = Math.acos(Math.max(-1.0,Math.min(1.0, makeDotProductVectors(vs, ve))));
        const vCross = makeNormalizeVector(makeVectorCrossProductValues(vs,ve));
        return makeNormalizeVector(makeQuataianValueFormAxisAngle(rdv,vCross));
    };

   ``` 
   위의 내용으로 두개의 축 vs 와 ve 를 arcball 기법을 통해 얻어 올 수 있었습니다.   
   두 축으로 부터 회전축과 theta 갑을 가져올 수 있었습니다.    
   코사인 쎄타 값은 내장 함수인 Math.acos 함수로 부터 theta 값을 가져올 수 있습니다. (radian 표기) 이 값이 회전 각도가 됩니다.  
   이 함수 에서는 아직 언급되지 않은 makeQuataianValueFormAxisAngle 함수가 있습니다.    
   x,y,z 각 축을 대상으로 회전을 하지 않고 구해진 임의의 축으로 부터 회전하기 위해서 쿼터니언(Quaternion) 이라고 불리는 사원수에 대해 
   확인이 필요할 것 같습니다.     

# 임의의 회전축에서의 회전 
   쿼터니언을 실수부와 허수부로 구성된 복소수를 활용하고 있습니다.    회전을 위해서 회전축과 theta 값이 주어졌을 때 회전축이 단위 벡터 이면 
   u 가 회전축 단위 벡터 이고, theta 가 주어졌을 때 쿼터니언은 다음과 같은 공식으로 나타낼 수 있습니다.  
   $$
    \begin{aligned}
        q   = (q_v, q_w) , \quad q_v = 허수부, q_w = 실수부 \\\
        u = 회전축, \quad \theta = 회전량 \\\
        q   = (sin \frac{\theta}{2} u, cos \frac{\theta}{2} )
    \end{aligned}
   $$

   이 공식을 바탕으로 구성한 함수 입니다. 
   ``` javascript 
        export const makeQuataianValueFormAxisAngle = (theta, axis) => {
            if ( !axis || axis.length != 3) {
                return vec4(0,0,0,0);
            }

            const sv = Math.sin(theta)/2;
            const cv = Math.cos(theta)/2;
            const result = vec4(0,0,0,0);
            for( let i = 0; i < 3; i++ ) {
                result[i] = (axis[i]*sv);
            }
            result[3]= (cv);
            return result;
        }

   ``` 
   이렇게 구한 사원수를 기준으로 행렬식으로 구성하기 위한 공식은 다음과 같습니다.     
   $$
    \begin{vmatrix}
        1-2(q_y^2 + q_z^2)&2(q_x q_y - q_w q_z)&2(q_x q_z + q_w q_y)&0\\\
        2(q_x q_y + q_w q_z)&1-2(q_x^2 + q_z^2)&2(q_y q_z - q_w q_x)&0\\\
        2(q_x q_z - q_w q_y)&2(q_y q_z + q_w q_x)&1-2(q_x^2 + q_y^2)&0\\\        
        0&0&0&1\\\        
    \end{vmatrix}
   $$    

   이 공식을 기반으로 구성한 함수 입니다. 
   ``` javascript 
    export const makeQuaternionMatrix = (qx,qy,qz,qw) => {
		let qy2 = qy*qy;
		let qx2 = qx*qx;
		let qz2 = qz*qz;

		const result = new Float32Array([
			1 - 2*qy2 - 2*qz2,	2*qx*qy - 2*qz*qw,	2*qx*qz + 2*qy*qw, 0,
			2*qx*qy + 2*qz*qw, 1 - 2*qx2 - 2*qz2,	2*qy*qz - 2*qx*qw, 0,
			2*qx*qz - 2*qy*qw,	2*qy*qz + 2*qx*qw,	1 - 2*qx2 - 2*qy2, 0,
			0,0,0,1
        ]);
        result.rows = 4;
        result.cols = 4;
		return result;
	};
   
   ``` 
   처음에 기재해 놓았듯 카메라를 기준으로 쿼터니언 회전을 구현한 단순 예제 입니다.    
   #### [카메라 위치 변환 예제 - 마우스로 드래그 하면 VIEW 위치 변환 예제](/html/WebGL2/WebGL_PART_014_01.html)   
   예제에는 회전한 값을 저장하는 로직이 없기 때문에 다시 클릭하면 다시 변환이 이뤄집니다.


# 참조 사이트 
  #### [https://www.youtube.com/watch?v=XgE7tOSc7AU](https://www.youtube.com/watch?v=XgE7tOSc7AU) 
  #### 그래픽스 강의 사이트 입니다.  위 공식의 출처는 이 사이트 내용에서 참조 하였습니다.       
  Arcball 은 여기저기 참조하였지만, 위 사이트 에서도 참조하였습니다. 
  #### [https://www.youtube.com/watch?v=c7la2Tt_cOc&t=4401s](https://www.youtube.com/watch?v=c7la2Tt_cOc&t=4401s) 


   




   








