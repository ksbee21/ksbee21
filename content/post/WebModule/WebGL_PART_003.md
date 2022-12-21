---
title: "WebGL2 - Vector 연산 02 [ 03 ]"
date: 2022-12-20T21:26:00+09:00
draft: false
tags : ["Web","WebGL", "Language", "Math", "Vector"]
topics : []
description : "Web GL 에서 사용하는 Vector 특성 "
---

# 백터의 사칙 연산
   
   벡터의 사칙연산(더하기, 빼기, 곱하기, 나누기) 은 일반 수의 연산과 거의 동알하다고 생각하면 될 것 같습니다.    
   약간의 차이란, Vector 안에 있는 순서에 맞게 계산해 주는 부분만 조금 다를 뿐입니다. 

   가령 [1,2,3] 의 a vector 가 있고, [4,5,6] 의 b vector 가 있을 때 
   a + b = [1+4, 2+5, 3+6 ] 으로 표현할 수 있습니다.  a-b = [1-4, 2-5, 3-6] 이고, a*b, a/b 도 동일한 연산을 
   사용한다고 보면 될 것 같습니다.   
   위 계산에서 확인 할 수 있는 것 처럼, 일반 숫자 계산에서도 a * b = b * a 이고 a + b = b + a 처럼 순서가 상관없이 
   동일한 결과가 나올 것이라고 예상 할 수 있습니다.    
   백터에 특정값을 곱해 주거나, 나누어 주는 연산도 가능합니다.   

   ### 백터 더하기의 특징
   2차원 평면에서 이야기 하는 것이 이해가 쉽기 때문에 간단한 예를 만들어 보겠습니다. 
   a 좌표는 [ 0, 2] 이고 b 좌표는 [2, 0] 이라고 가정해 보겠습니다.    
   a 좌표가 Y 방향으로 2로 움직인 Y 축에 있는 좌표고, b 좌표는 X 방향으로 2 이동한 X 축에 있는 좌표 입니다. 
   a + b = [0+2, 2+0] = [2,2] 입니다. x축으로 2만큼, y 축으로 2만큼 움직인 좌표로 생각 할수 있습니다. 
   단순화 시키면 두 힘의 방향이 모여서 도달한 값 정도로 이해할 수 있을 것 같습니다. 

   ### 백터 빼기의 특징
   위에 예시되어 있는 a, b 의 좌표를 다시한번 생각해 보겠습니다.       
   a 좌표는 [0,0] 의 원점에서 부터 이동한 것이고, b 좌표도 마찬가지 입니다.        
   a - b = [ 0-2, 2-0 ] = [-2, 2] 의 값이 나옵니다.       
   이 값을 생각해 보면 원점기준으로 볼 때 x 축으로 -2 만큼 움직이고, y 축으로 2만큼 움직인 좌표 입니다.       
   백터의 길이(힘)를 구할 수 있고, 방향은 왼쪽으로 45도 방향으로 움직이고 있는 것을 연상 할 수 있습니다.        
   b 좌표 [ 2, 0 ] 을 기준으로 보면 [2-2, 0+2 ] = [0, 2] a 좌표 입니다.   
   a - b 의 의미는 b 에서 a 로 가는 방향과 힘의 vector 라고 말할 수 있을 것 같습니다.    
   빼기는 순서가 의미가 있는데 a - b 는 b 에서 a 방향으로 갈때의 vector 입니다.     
   나중에 연산의 순서가 중요해 질 때가 있는데 특히 vector 의 빼기가 그런것 같습니다.    


# 백터의 내적, 벡터곱
   
   ### 백터 내적 
   WebGL 백터 연산에서 가장 많이 사용하게될 부분이 내적과 백터곱 입니다. 
   내적은 Dot Product ( Inner Product ) 라고 불리며 두 백터의 같은 순서 원소 끼리 곱하여 모두 더한 값입니다. 
   $$ 
        a\cdot b = \sum{a_i \times b_i} = {a_1 \times b_1 + a_2 \times b_2 + a_3 \times b_3 + \dots + a_n \times b_n} \\\
        a\cdot b = \begin{Vmatrix}a\end{Vmatrix} \\; \begin{Vmatrix}b\end{Vmatrix} cos\theta
   $$
   삼각함수의 코사인은 직각삼각형의 (가로/빗변) 의 비율입니다.    
   삼각형을 기준으로 벡터 a 의 길이가 빗변의 길이라고 한다면 코사인이 ( (가로/빗변) * 빗변 ) 이니, 가로의 길이만 남게 됩니다.   
   이것과 벡터 b의 길이를 곱한 값이 내적이라는 것을 기억해 두면 향후 계산을 이해하는데 도움이 될 것 같습니다.     

   ### 백터곱 ( Cross Product )
   벡터곱은 Vector Product ( Cross Product ) 라고 불리며 원소가 3개인 Vector 에서 사용하는 연산입니다. 
   a 가 [ 1, 2, 3] 이고 b 가 [ 4, 5, 6 ] 일 때 a x b = [ 2*6 - 3*5, 3*4 - 1*6, 1*5-2*4 ] 입니다.    
   연산의 결과가 3개 짜리 배열(벡터) 로 표현하게 됩니다. 
   $$
        a \times b = [ a_2 \times b_3 - a_3 \times b_2, a_3 \times b_1 - a_1 \times b_3, a_1 \times b_2 - a_2 \times b_1] \\\
        a \times b = \begin{Vmatrix}a\end{Vmatrix} \begin{Vmatrix}b\end{Vmatrix} sin\theta
   $$
   벡터곱은 공식에서 사인값을 곱해 주고 있습니다. 사인은 ( 높이/빗변 ) 이기 때문에 위에서와 같이 벡터 a의 길이를 빗변으로 보면   
   높이 x 가로 로 평행사변형의 넓이가 됩니다.   다 떠나서 뭔가 면적에 관련된 값이라는 것만 확인해 두면 좋을 것 같습니다.    

   ### 내적의 특징 
   두 백터가 직교(수직) 하면 값이 0 이 됩니다.    
   두 벡터 사이의 각도가 90 도 이내의 예각이면 값은 양수가 나옵니다.    
   두 벡터 사이의 각도가 90 도를 넘거가면 ( 둔각 ) 값은 음수가 나옵니다.    
   계산 결과가 단일값 ( Scalar ) 형태로 나타 납니다.    
   수식에서 두 백터의 길이와 코사인 값으로 값을 이야기 하는데 각 벡터가 단위(Unit)    
   벡터이면 각각 길이가 1이기 때문에 두 벡터의 내적의 값이 코사인 Theta 값으로 사용할 수 있습니다.    
   나중에 해당 부분을 기재할 일이 있을 때 다시 한번 확인해 보겠습니다.   
   WebGL 에서 빛이 표면을 비출 때 내적의 특징을 이용하면 어느정도 빛을 반사하는지 계산할 수 있습니다.    
      

   ### 벡터곱의 특징 
   WebGL 에서 벡터곱의 가장 큰 특징은 a, b 의 벡터를 cross product 하면 a, b 에 수직인 vector 를 얻을 수 있다는 점입니다.    
   벡터곱은 x,y,z 을 기준으로 하는 3차원 공간 벡터에서만 계산을 하고 있습니다.    
   예를 들어 a 가 [1, 0, 0] 이고 b 가 [ 0, 1, 0] 일때 a x b = [ 0x0-0x1, 0x0-1x0, 1x1-0x0] = [ 0,0,1] 입니다.    
   a x b 의 결과는 a 와 b 에 수직인 벡터가 만들어 지게 됩니다.    
   그러나 주의 할 것은 만약 b x a = [ 1x0-0x0, 0x0-0x0, 0x0 - 1x1] = [0, 0, -1] 로 z 축의 방향이 -1 입니다.    
   길이는 같지만, 방향은 반대의 결과가 만들어 집니다.    
   WebGL 은 오른손 법칙을 따릅니다.   해당 부분은 그림 설명이 중요하기 때문에 링크로 대체 하겠습니다.    
   
   [한글위키](https://ko.wikipedia.org/wiki/%EC%98%A4%EB%A5%B8%EC%86%90_%EB%B2%95%EC%B9%99)
   
   구글 검색 하다 설명이 잘되어 있는 한글 사이트가 있어서 링크 걸었습니다.    
   [구글검색](https://m.blog.naver.com/bmw9707121/221746745710)  


# Javascript Vector 연산 구현 

   ### Vector 사칙연산 

   ``` javascript 
    const validateVectorLength = (v1, v2) => {
        if ( !isValidArrayValues(v1) ) 
            return -1;
        if ( !isValidArrayValues(v2) ) 
            return -1;
        let len = v1.length;
        if ( len != v2.length )
            return -1;
        return len; 
    };

    /**
     * 노출되지 않고 계산하는 함수
     * @param {*} v1 
     * @param {*} v2 
     * @param {*} calcType : 1은 더하기, 2는 빼기 -- 1이 아니면 무조건 빼기로 구성
     * @returns 
     */
    const makeVectorPlusMinus = (v1, v2, calcType ) => {
        let len = validateVectorLength(v1,v2);
        if ( len <= 0 )
            return undefined;

        const result = new Float32Array(len);
        for ( let i = 0; i < len; i++ ) {
            if ( calcType == 1 ) {  // vector plus
                result[i] = v1[i] + v2[i];
            } else {    //  default vector minus 
                result[i] = v1[i] - v2[i];
            }
        }
        return result;
    };

    /**
     * 
     * @param {*} v1 
     * @param {*} v2 
     * @returns 
     */
    export const makeVectorPlusValues = (v1,v2) => {
        return makeVectorPlusMinus(v1,v2, 1);
    };

    /**
     * 
     * @param {*} v1 
     * @param {*} v2 
     * @returns 
     */
    export const makeVectorMinusValues = (v1,v2) => {
        return makeVectorPlusMinus(v1,v2, 2);
    };

    const makeVectorMuliply = (v1,v2,calcType) => {
        let len = validateVectorLength(v1,v2);
        if ( len <= 0 )
            return undefined;

        const result = new Float32Array(len);
        for ( let i = 0; i < len; i++ ) {
            if ( calcType == 1 ) {  // vector muliply
                result[i] = v1[i] * v2[i];
            } else {    //  default vector divide 
                result[i] = v1[i]/v2[i];
            }
        }
        return result;
    };

    const makeVectorMuliplyScala = (v1,scalarValue,calcType) => {
        if ( !isValidArrayValues(v1) || !scalarValue )
            return undefined;

        let len = v1.length;

        const result = new Float32Array(len);
        for ( let i = 0; i < len; i++ ) {
            if ( calcType == 1 ) {  // vector muliply
                result[i] = v1[i] * scalarValue;
            } else {    //  default vector divide 
                result[i] = v1[i]/scalarValue;
            }
        }
        return result;
    };

    export const makeVectorMultiplyValues = (v1,v2) => {
        return makeVectorMuliply(v1,v2,1);
    };
    export const makeVectorDivideValues = (v1,v2) => {
        return makeVectorMuliply(v1,v2,2);
    };
    export const makeVectorMultiplyScalarValues = (v1,scalarValue) => {
        return makeVectorMuliplyScala(v1,scalarValue,1);
    };
    export const makeVectorDivideScalarValues = (v1,scalarValue) => {
        return makeVectorMuliplyScala(v1,scalarValue,2);
    };
   ```

   ### Vector Inner Product  
   ``` javascript 
    /**
     * Vector Inner Product , Dot Product Value
     * @param {*} v1 
     * @param {*} v2 
     * @returns : scalar value 
     */
    export const makeVectorDotProductValues = (v1,v2) => {
        let len = validateVectorLength(v1,v2);
        if ( len <= 0 )
            return undefined;

        let result = 0.0;
        for ( let i = 0; i < len; i++ ) {
            result += (v1[i]*v2[i]);
        }
        return result;
    };

    /**
     * 두 백터의 Dot Product 결과를 통해 cosine theta 값을 가져오기 위한 함수
     * @param {*} v1 
     * @param {*} v2 
     * @returns : cosine theta 값
     */
    export const getCosineValue = (v1,v2) => {
        let len = validateVectorLength(v1,v2);
        if ( len <= 0 )
            return undefined;
        const v1Len = getVectorLength(v1);
        const v2Len = getVectorLength(v2);
        const dotValue = makeVectorDotProductValues(v1,v2);

        return dotValue/(v1Len*v2Len);
    };
   ```


   ### Vector Cross Product
   ``` javascript
    /**
     * Vector Cross Product 
     * @param {*} v1 
     * @param {*} v2 
     * @returns Vector 
     */
    export const makeVectorCrossProductValues = (v1,v2) => {
        let len = validateVectorLength(v1,v2);
        if ( len != 3 )
            return undefined;

        const result = new Float32Array(len);
        result[0] = ( v1[1]*v2[2] - v1[2]*v2[1]);
        result[1] = ( v1[2]*v2[0] - v1[0]*v2[2]);
        result[2] = ( v1[0]*v2[1] - v1[1]*v2[0]);        
        return result;
    };

    /**
     * Cross Product 값을 이용하여 sin theta 값을 가져오기 위한 함수
     * @param {*} v1 
     * @param {*} v2 
     * @returns 
     */
    export const getSinValue = (v1, v2) => {
        let len = validateVectorLength(v1,v2);
        if ( len != 3 )
            return undefined;
        const v1Len = getVectorLength(v1);
        const v2Len = getVectorLength(v2);
        const crossValue = getVectorLength(makeVectorCrossProductValues(v1,v2));

        return crossValue/(v1Len*v2Len);
    };


   ```


   ### 페이지에서 사용
   ``` html

    <script type="module">
        import * as TypedMatrixUtils from "../js/TypedMatrixUtils.js"

        //  v1 에서 v2 의 방향은 시계 반대 방향 오른솝 법칙을 적용할 경우 cross product 의 결과는 양의 z 방향
        const v1 = new Float32Array([1,0,0]);
        const v2 = new Float32Array([1,Math.sqrt(3),0]);   //    60도 

        const dotValue = TypedMatrixUtils.makeVectorDotProductValues(v1,v2);
        const cosineTheta = TypedMatrixUtils.getCosineValue(v1,v2);
        const cosineDegree = Math.round( Math.acos(cosineTheta)*180/Math.PI * 10000)/10000;

        const crossValue = TypedMatrixUtils.makeVectorCrossProductValues(v1,v2);
        const crossArea = TypedMatrixUtils.getVectorLength(crossValue);
        const sinTheta = TypedMatrixUtils.getSinValue(v1,v2);
        const sinDegree = Math.round( Math.asin(sinTheta)*180/Math.PI * 10000)/10000;

        console.log( "Dot Value : " + dotValue + " , 각도 " + cosineDegree + "\nCross Value : " + crossValue + " , 각도 : " + sinDegree + "\n면적 : " + crossArea );
    </script>

   ```

   ### 기타
   Vector 연산을 중심으로 기술 하려고 하였지만, 삼각함수가 조금 언급되었습니다.    
   다음 Matrix 연산을 구현하면서 삼각함수를 약간 더 사용하게 될 텐데 그때 조금 상세하게 
   정리해 보도록 하겠습니다.   










