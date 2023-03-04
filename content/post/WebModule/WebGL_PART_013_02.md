---
title: "WebGL2 - Program 시작 10 - Ray 충돌 - [ 14 ]"
date: 2023-03-02T19:58:43+09:00
draft: false
tags : ["Web","WebGL", "Language","Javascript", "Programming","Math"]
topics : []
description : "WebGL 물체 선택 ( Picking Ray - Tracing 충돌 정리 )"
---
   앞선 글에서 구(Sphere) 와 Ray의 충돌까지 정리해 보려고 하다 보니, 공간의 역변환을 정리하는 것도 간단한 일이 아니어서 
   간략하게 언급만 하였습니다.    
   개인적으로 수학을 잘하는 것이 아니기 때문에 주어진 공식을 활용하는 정도면 될 것 같긴 하지만, Ray Tracing 은 충돌 같은 것에서만 
   사용하는 것이 아니라, 굴절, 반사 등에 의해 현실적인 묘사를 위해 사용되고 있는 기법이라, 조금 더 정리할 필요성이 있어 보입니다.    
   이해를 위한 부분이라 어쩔수 없이 수식을 나열하듯 전개해 보려고 합니다.   검증은 정말 간단한 예제를 만들어 구성해 볼 예정 입니다.   

# 구(Sphere) 와 광선(Ray) 의 충돌 검출
   광선은 시작점이 있고, 시작점에서 뻗어 나가는 방향이 있습니다.    3차원에서 시작점을 startRay 이라고 지칭하겠습니다.  방향은  
   directionRay 이라고 이름 지어 놓겠습니다.    예시로 광선은 startRay 는 [0, 0, 5] 이고 방향은 [0, 0, -1] 로 음의 방향으로  
   뻗어 나가는 광선을 가정하겠습니다.    
   구(Sphere) 는 중심점이 있고, 반지름이 있어야 합니다. 중심점을 CenterPosition 이라고 하고 반지름을 Radius 라고 지칭하겠습니다.  
   예제로는 단순하게 CenterPosition[cx, cy, cz] 이 [ 0, 0, 0 ] 이고 Radius 는 1 로 정해 놓고 시작해 보겠습니다.    

   ### 기본 공식 
   $$
      \begin{aligned}
      ( x - cx )^2 + ( y - cy )^2 + ( z - cz )^2 = r^2 \\\
      Ray = startRay + t \times directionRay \\\ 
      a t^2 + b t + c = 0 \quad 일때 \\\
      t = \frac{-b \pm \sqrt{b^2 - 4ac} }{2a} \\\
      \end{aligned}
   $$
   
   위 공식에서 각 지점을 정리해 보겠습니다.   
   광선 R 의 startRay 인 시작점을 [rx, ry, rz] 이라고 정의 하겠습니다.   directionRay 는 [rdx, rdy, rdz] 으로 정의해 보겠습니다.    
   광선이 진행한 거리는 위의 수식에서 처럼 t 라고 정의해 보겠습니다.   
   풀이해 보면 광선 R 은 X 방향으로 rx + t x rdx, Y 방향으로 ry + t x rdy , z 방향으로 rz + t x rdz 이라고 표현할 수 있습니다.    
   #### x = rx + t x rdx, x 는 원의 방정식의 x
   #### y = ry + t x rdy, y 는 원의 방정식의 y
   #### z = rz + t x rdz, z 은 원의 방정식의 z 

   먼저 원의 방정식을 풀어써 보겠습니다. 
   $$
      \begin{aligned}
      ( x - cx )^2 + ( y - cy )^2 + ( z - cz )^2 = r^2 \\\
      x^2 - 2\times x\times cx + cx^2 + y^2 - 2\times y\times cy + cy^2 + z^2 - 2\times z\times cz + cz^2 = r^2  \\\
      (x^2 + y^2 + z^2) - 2(x\times cx + y\times cy + z\times cz) + cx^2 + cy^2 + cz^2 = r^2  \\\
      \end{aligned}
   $$
   x 가 위에서 rx + t x rdx 의 광선식으로 정의할 수 있었습니다.   원과 교점을 만들려면 광선이 각 x, y, z 에 일치하여야 하기 때문입니다.   
   계산을 편하게 하기 위해 각 x^2, y^2 , z^2 을 광선 중심으로 풀이해 보면 아래와 같습니다.  
   $$
      \begin{aligned}
      t \times rdx + rx = x \\\
      t \times rdy + ry = y \\\
      t \times rdz + rz = z \\\      
      t^2\times rdx^2 + t \times 2 \times rdx \times rx + rx^2 = x^2 \\\
      t^2\times rdy^2 + t \times 2 \times rdy \times ry + ry^2 = y^2 \\\
      t^2\times rdz^2 + t \times 2 \times rdz \times rz + rz^2 = z^2 \\\
      t^2( rdx^2 + rdy^2 + rdz^2 ) + 2t( rdx \times rx + rdy \times ry + rdz \times rz) + rx^2+ry^2+rz^2 = x^2+y^2+z^2 \\\
      t( cx\times rdx + cy\times rdy + cz\times rdz) + cx\times rx + cy\times ry + cz\times rz = x\times cx + y\times cy + z\times cz \\\
      \end{aligned}
   $$

   조금 길어 졌지만 위 식을 기준으로 풀어써 보면 다음과 같습니다.  
   $$
      t^2( rdx^2 + rdy^2 + rdz^2 ) + 2t( rdx \times rx + rdy \times ry + rdz \times rz) + rx^2+ry^2+rz^2 \\\
      + -2t( cx\times rdx + cy\times rdy + cz\times rdz) + -2cx\times rx + -2cy\times ry + -2cz\times rz \\\
      + cx^2 + cy^2 + cz^2 = r^2 \\\
      t^2( rdx^2 + rdy^2 + rdz^2 ) + 2t( rdx (rx - cx)+ rdy (ry - cy) + rdz (rz-cz)) + cx^2 - 2cx\times rx + rx^2 + cy^2 -2cy\times ry + ry^2 + cz^2 - 2cz\times rz + rz^2 = r^2 \\\
      t^2( rdx^2 + rdy^2 + rdz^2 ) + 2t( rdx (rx - cx)+ rdy (ry - cy) + rdz (rz-cz)) + (cx - rx)^2 + (cy -ry)^2 + (cz- rz)^2 - r^2 = 0 \\\
   $$
   t 를 기준으로 보면 2차 함수 입니다. 근의 공식이 적용될 수 있습니다. 
   $$
      \begin{aligned}
      a = rdx^2 + rdy^2 + rdz^2 \\\
      b = 2( rdx (rx - cx)+ rdy (ry - cy) + rdz (rz-cz)) \\\
      c = (cx - rx)^2 + (cy -ry)^2 + (cz- rz)^2 - r^2 \\\ 
      \end{aligned}
   $$
   수식에서 제공된 모든 변수가 주어진 수칫 값으로 변환 될 수 있으니, a, b, c 의 값을 구할 수 있고, 이 값을 근의 공식에 대입하면 해를 구할 수 있습니다.    
   구 이기 때문에 접점에서는 하나의 해가 나올 것이고, 통과하면 2개의 해가 나오는데 값에 따라 구 안에서 시작인지 밖에서 시작인지, 반대 방향의 값인지 확인 할 수 있습니다.   
   (b*b - 4*a*c) 의 값이 음수가 나오면 계산할수가 없으니 해가 없는 것으로 간주할 수 있습니다.     
   위 수식을 자세히 보면 Vector 연산으로 변경하는 것도 가능합니다.   사실 Vector 로 변환하여 계산하는 것이 더 편할 수 있습니다.    
   아래는 Vector 연산으로 변환한 내용 입니다. 
   $$
      \begin{aligned}
      dr = directionRay \quad st = startRay \quad ct = centerPosition \quad 이라고 할때 \\\ 
      a =  \parallel dr \cdot dr  \parallel \\\
      b = 2 ( dr \cdot (st - ct) ) \\\
      c = (st - ct) \cdot (st - ct) - r^2 \\\ 
      \end{aligned}
   $$

   앞서 정의한 값으로 위의 값을 계산해 보겠습니다.   
   a = 1, b = -10, c = 24 의 값이 나옵니다.   (10+sqrt(100-96))/2 = 6, (10-sqrt(100-96))/2 = 4 입니다. 

# 면(Plane) 과 광선(Ray)의 충돌 
   3D 에서 물체를 표현하는 단위는 사실 삼각형 입니다.   각 꼭지점을 기준으로 3개를 연결하면 삼각형이 나오고 이 단위가 
   화면에 출력하게 되는 기본 단위가 됩니다. ( 물론 pixel 은 점처럼 표현되는 단위 입니다. )    
   결국 관심있는 것은 광선이 삼각형에 충돌하는 가 하는 점인데 삼각형도 면이므로 면과의 충돌을 먼저 살펴 보고자 합니다.    
   면과 충돌하는 지점을 찾고, 그 지점이 삼각형에 포함되는지를 확인할 것인데 일단은 면과의 충돌 지점을 확인해 보려 합니다.    

   ### Noraml 은 면과 직교합니다.   
   법선이라 불리는 Normal 은 평평한 면과 직교하는 벡터 입니다.   벡터의 특징중의 하나는 직교하는 두 벡터를 Dot Product 하면 
   값은 0이 된다는 성질이 있습니다.   간단히 생각해 보면 x 축으로 (1, 0, 0), y 축으로 (0,1,0), z 축으로 (0, 0, 1) 의 값이 있다고 하면, 
   3개의 축은 서로 직교하고 있으며, Dot Product 하면 모두 0 의 값이 나오게 됩니다.  이 특징을 활용하여 계산하게 됩니다.    
   ### 면에 있는 두점을 연결하는 Vector 는 Normal 에 직교합니다. 
   동일한 면에 있는 T1 (0,1,0) , T2 ( -1, -1, 0) 이 있다고 가정해 보겠습니다.   이 두 점은 같은 평면에 있고, 이 평면의 Normal 이 (0,0,1 ) 이라고 
   알고 있다면 (T2 - T1 ) 과 Noraml 을 Dot Product 하면 0 값이 나오게 됩니다.  이 특징을 이용해서 다음과 같은 식을 구성할 수 있습니다. 
   $$
      \begin{aligned}
      dr = directionRay \quad st = startRay \quad Ray = st + t \times dr \\\
      ( p - p01 ) \cdot normal = 0 \\\
      ( (st + t \times dr) - p01 ) \cdot normal = 0 \\\
      ( ( st - p01 ) \cdot normal ) + t \times (dr \cdot normal ) = 0 \\\
      t =  \frac{( ( p01 - st ) \cdot normal )} {(dr \cdot normal)}
      \end{aligned}
   $$

   t 라는 거리를 알면, ray 의 시작점과 방향으로 면에 부딛히는 지점을 찾아 올 수 있습니다.    
   위의 예제로 구성해 보면 다음과 같습니다.    
   방향벡터가 (0, 0, -1), 시작점이 (0,0,5) 이고 normal 이 (0,0, 1),  p0 가 (0,1,0) 으로 주어졌다면 다음과 같은 수식을 만들 수 있습니다. 
   t  = (((0,1,0) - (0,0,5)) . ( 0, 0, 1) ) / ((0,0,-1).(0,0,1))  이 값을 계산하면 t = -5/-1 , t = 5 가 도출됩니다.    
   t 가 5 이면 (0,0,5) + (0,0,-1) * 5 의 값은 (0,0,0) 입니다.    

# 삼각형(Triangle) Ray 충돌
   위에 사용한 방식을 동일하게 적용합니다.    
   다만, OpenGL 에서는 삼각형 꼭지점을 구성할 때 반시계 방향으로 순서를 구성하여야 합니다.  오른손 법칙을 적용하고 있기 때문에 방향에 따라 
   Cross Product 의 값의 방향이 반대가 되기 때문입니다.   그것만 조심하면 위에서 충돌을 일으킨 지점과 Cross Product 연산을 통해 삼각형 내부에 있는지, 
   있다면 각 꼭지점과 얼마나 비율적으로 가까운지 찾아 올 수 있게 됩니다.    그 방식을 살펴 보겠습니다.    
   삼각형 좌표를 T1(0,1,0), T2(-1,-1,0), T3(1,-1,0) 이라고 하겠습니다. 이 세 좌표로 먼저 normal 을 구하게 됩니다. 좌표는 T1,T2,T3 가 반시계 방향으로 
   구성되어 있음을 기억하여야 합니다.    
   T1 에서 T2 로 가는 벡터와 T1 에서 T3 로 가는 벡터를 구성해 보겠습니다. ( T2 - T1 ) = (-1, -2, 0) 이고 (T3 - T1) = (1,-2, 0 ) 입니다.    
   이값을 Cross Product 후 Normalize 하면 (0,0,1) 의 값을 얻을 수 있습니다. 이 값이 해당 평면의 noraml 입니다.      
   위에서 계산하였던 것과 같이 접점은 (0, 0, 0) 입니다. 그 값을 P(0,0,0) 이라고 하겠습니다.    
   눈 대중으로라도 대략 P 점이 삼각형 내부에 있다는 것을 우리는 알 수 있습니다.   컴퓨터는 그것을 앞서 구한 Noraml 과 P 점을 기점으로 하는 
   두개의 Vector 를 구성해 Cross Product 를 구한 값을 normal 과 dot product 를 하면 normal 과 같은 방향이면 양수가 그렇지 않으면 음수가 나오게 될 것입니다. 
   그 특성을 활용하여 삼각형 내 외부를 판정합니다.    
   주의하여야 할것은 CrossProduct 는 계산 순서의 선후에 따라 부호가 반대로 나오니 순서를 주의 깊게 구성하여야 합니다.     
   삼각형 내부에 있다고 가정 하면, T1 에서 T2로 가는 벡터와 ( T2 - T1 ) T1 에서 P 로 향하는 벡터를 (P-T1) Cross Product 하면 noraml 과 같은 방향일 테니 해당값을 
   normal 과 dot product 하면 양수(0 이상) 의 값이 나와야 합니다.  T2 에서는 T3 가 그렇고, T3 에서는 T1 이 동일한 조건을 충족 시켜야 합니다.      
   세가지 조건이 만족하면 해당 점 P는 삼각형 내부에 있는 것으로 판정할 수 있습니다.    
   그럼 앞선 예제로 데이터를 구성해 보겠습니다.     
   (T2-T1) X (P-T1) = (0, 0, 1) 이 나오고, noraml 과 dot product 결과는 1 입니다.    
   (T3-T2) X (P-T2) = (0, 0, 2) 이 나오고, noraml 과 dot product 결과는 2 입니다.      
   (T1-T3) X (P-T3) = (0, 0, 1) 이 나오고, noraml 과 dot product 결과는 1 입니다.     
   모든 결과값이 양수 이기 때문에 P 는 삼각형 내부에 있는 점으로 판정할 수 있습니다.    
   ( 참고로, 방향만 정확하게 구성할 수 있다면, 꼭 위와 같이 하지 않아도 됩니다. 예를 들어 P에서 T1,T2 로 향하는 벡터를 구성해서 테스트 할 수도 있습니다.)        

   #### 삼각형 내부에 있다고 할 때 각 점에서의 영향력은? 
   이제 삼각형 내부에 있는지 아닌지를 판별 할 수 있습니다.   계산 수치값이 너무 작은 범위일 경우나, 판정을 위해 분모가 0인 경우를 예외처리한다면, 
   판정을 위한 함수는 구성할 수 있는 상황입니다.    
   여기서 각 점의 데이터(색상등) 를 보간하고자 한다면, 부딛히는 점 P 가 T1, T2, T3 에 어떤 비율로 영향력을 가지는지 알 수 있어야 합니다.    
   이때 사용하는 것이 위에서 계산한 Cross Product 결과 입니다.    Cross Product 의 결과는 그 길이값이 사다리꼴의 넓이 입니다.    삼각형의 넓이는 1/2 이기 
   때문에 각 결과의 길이를 구한후 2로 나누면 결과를 얻을 수 있습니다.    우리는 각 점의 영향력을 알고 싶은 것이기 때문에 굳이 2로 나눌 필요도 없습니다.    
   전체 면적을 구한후, 그 면적에서 영역이 찾이하는 정도를 비율로 구성하면 각 지점에서의 영향력을 알 수 있습니다.    
   조금 주의하여야 할 부분은 (T2-T1)X(P-T1) 의 결과 면적이 넓다면 이는 T3의 영향력이 크다는 의미라는 점입니다.   P점이 T3 에 가깝다는 의미니까요 .... 
   마찬가지로 T1, T2 도 역시 그렇습니다.   이 부분만 조심하면 전체가 1로 구성된 각 점의 영향력도 구성 할 수 있습니다.    

   #### 아래는 그 과정을 나열식으로 구성한 내용입니다.    

   ``` javascript 

        const T1 = TypedMatrixUtils.vec3(0,1,0);
        const T2 = TypedMatrixUtils.vec3(-1,-1,0);        
        const T3 = TypedMatrixUtils.vec3(1,-1,0);                

        const tt1 = TypedMatrixUtils.makeVectorMinusValues(T2,T1);
        const tt2 = TypedMatrixUtils.makeVectorMinusValues(T3,T1);
        const normal  = TypedMatrixUtils.makeNormalizeVector(TypedMatrixUtils.makeVectorCrossProductValues(tt1, tt2));

        const P = TypedMatrixUtils.vec3(0,0,0);                

        const aPos1 = TypedMatrixUtils.makeVectorCrossProductValues( TypedMatrixUtils.makeVectorMinusValues(T2,T1), TypedMatrixUtils.makeVectorMinusValues(P,T1) ) ; // T3 Ratio aPos1 이 크면 T3 에 가깝다는 의미 
        const aPos2 = TypedMatrixUtils.makeVectorCrossProductValues( TypedMatrixUtils.makeVectorMinusValues(T3,T2), TypedMatrixUtils.makeVectorMinusValues(P,T2) ) ; // T1 Ratio aPos2 이 크면 T1 에 가깝다는 의미 
        const aPos3 = TypedMatrixUtils.makeVectorCrossProductValues( TypedMatrixUtils.makeVectorMinusValues(T1,T3), TypedMatrixUtils.makeVectorMinusValues(P,T3) ) ; // T2 Ratio aPos3 이 크면 T2 에 가깝다는 의미 

        const a1Dir = TypedMatrixUtils.makeVectorDotProductValues(aPos1, normal);  
        const a2Dir = TypedMatrixUtils.makeVectorDotProductValues(aPos2, normal);        
        const a3Dir = TypedMatrixUtils.makeVectorDotProductValues(aPos3, normal);   
        
        const a01 = TypedMatrixUtils.getVectorLength(aPos2);
        const a02 = TypedMatrixUtils.getVectorLength(aPos3);        
        const a03 = TypedMatrixUtils.getVectorLength(aPos1);            
        
        const aSum = (a01+a02+a03);
        const a1Ratio = (a01/aSum);
        const a2Ratio = (a02/aSum);
        const a3Ratio = (1-a1Ratio-a2Ratio); // (a03/aSum)


   ``` 
 
# 참고 사이트 
   #### [https://en.wikipedia.org/wiki/Line%E2%80%93sphere_intersection](https://en.wikipedia.org/wiki/Line%E2%80%93sphere_intersection)
   #### [https://en.wikipedia.org/wiki/Line%E2%80%93plane_intersection](https://en.wikipedia.org/wiki/Line%E2%80%93plane_intersection)
   #### [https://www.scratchapixel.com/lessons/3d-basic-rendering/ray-tracing-rendering-a-triangle/ray-triangle-intersection-geometric-solution.html](https://www.scratchapixel.com/lessons/3d-basic-rendering/ray-tracing-rendering-a-triangle/ray-triangle-intersection-geometric-solution.html)