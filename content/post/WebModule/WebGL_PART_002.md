---
title: "WebGL2 - Vector 연산 01 [ 02 ]"
date: 2022-12-19T21:35:47+09:00
draft: false
tags : ["Web","WebGL", "Language","Javascript", "Math", "Vector"]
topics : []
description : "Web GL 에서 사용하는 Vector 간략소개 "
---

# WebGL 벡터란 ?
   
   프로그래밍 관점에서 보면, WebGL 에서 사용하는 벡터란 ,    
   대부분 x,y 로 표기되는 원소가 두개인 배열 혹은 x,y,z 으로 표기 되는 원소가 3개인 배열 정도로 이해 할 수 있을 것 같습니다.    
   배열을 기준으로 보면 1차원 배열의 원소의 갯수만 차이가 있는 것이고, 데이터가 [1,2,3] 이 있을 때 행(Row) 을 기준으로 보면 행백터 이고, 
   열(Column) 을 기준 으로 보면 열백터로 지칭하고 있습니다. 아래의 예에서와 같이 세로, 가로로 이해하면 편할것 같습니다. 
   
   ``` javascript
   [1  열백터          [1,2,3] 행백터
   2
   3], 
   ```
   WegGL 에서는 열기준의 계산이 이뤄지기 때문에 향후 행렬 계산할 때 약간의 주의가 필요합니다. 
   행렬은 추후 이야기 하겠지만, 가로 세로가 복수개로 구성된 배열이기 때문에 2차원 배열 Matrix 를 생각하시면 되고, 
   벡터는 가로 혹은 세로가 1인 1차원 배열을 생각 하시면 될 것 같습니다. 
   
# 벡터 예시
   
   X,Y축을 기준으로 평면에 있는 어떤점을 한번 생각해 보겠습니다. 
   2차원 평면이 X:0, Y:0 점을 기준으로 [1,0] 의 좌표(일반적으로 첫번째 원소가 X, 다음 원소가 Y) 는 X축으로 1만큼 
   움직이고, Y 방향으로 0만큼 움지임(안움직임)을 의미 한다고 생각 할 수 있습니다.    
   이때 [1,0] 좌표의 길이는 Y방향으로 움직이지 않았고 X 방향으로 1만큼 움직였으니 길이는 1이라는 것을 알 수 있습니다. 
   아래의 수식에서와 같이 2차원 평면에서의 길이는 x,y 의 제곱을 더해 루트로 구한 값이고,   
   3차원 공간에서의 길이는 x,y,z 의 제곱을 구해 모두 더한 값에 루트로 값을 구할 수 있습니다.     
   $$
     2차원 = \sqrt{x^2+y^2},  3차원 = \sqrt{x^2+y^2+z^2}
   $$

   약간만 더 생각해 보면, 2차원 평면에서 a 좌표가 [1, 0] 이고 b 좌표가 [0, 1] 이라면, a 좌표는 x축으로 1만큼 이동한 것이고, 
   b 좌표는 x가 0 이므로 Y 축으로 1 이동한 좌표를 연상할 수 있습니다. 
   직관적으로 두 좌표 모두 길이가 1이라는 것을 알 수 있습니다.  이제 좌표 [2, 2] 인 c 점을 상상해 보겠습니다.   
   x 방향으로 2, y 방향으로 2 움직인 좌표라는 것을 알 수 있습니다.    
   a, b 좌표가 각각 x축 방향, y 축 방향으로만 움직였으니, a와 b 좌표는 직각을 이루고 있을 것입니다. c 는 [2, 2] 이기 때문에 
   45도 각도로 우상향하는 지점에 위치해 있는 좌표라고 생각 할 수 있습니다.    
   길이는 2.828427 ... 이고 각 x,y 좌표값을 길이로 나눠주면 0.707106... 의 값이 나옵니다.    
   c의 좌표를 길이로 나눈 좌표는 [0.707106... , 0.707106...] 의 값이고 이것의 길이를 구하면 1이 나옵니다.    
   이렇게 좌표의 길이가 1이 되도록 구성해 주는 것을 단위 벡터라고 하고    
   WebGL 의 연산에서 많이 사용되고 있습니다.   
   이런 방식을 normalize 한다고 이야기 하고 있습니다.   
   $$ \sqrt{2^2 + 2^2} = 2.828427 ..., {2 \over \sqrt{8}} = 0.707106...  $$ 
   

# Javascript 로 Vector 의 길이 정규화(Normalize) 구현
   
   ## 백터의 길이 구하기

   ``` javascript
    /**
     * 백터의 길이를 계산하기 위한 함수
     * @param {*} v1 
     * @returns 
     */
    export const getVectorLength = ( v1 ) => {
        if ( !isValidArrayValues(v1)  )
            return undefined;
        
        let sum = 0.0;
        for ( let i = 0; i < v1.length; i++ ) {
            sum += (v1[i]*v1[i]);
        }
        return Math.sqrt(sum);
    };
   ```

   ## 백터 정규화 ( Unit Vector 만들기 )

   ``` javascript 
       /**
     * Normalize 하기 위한 함수
     * @param {*} v1 
     * @param {*} needRound 
     * @returns 
     */
    export const makeNormalizeVector = ( v1, needRound ) => {
        if ( !isValidArrayValues(v1)  )
            return undefined;

        const len = v1.length;
        const result = new Float32Array(len);

        const lv = getVectorLength(v1);
        alert ( lv );
        if ( lv == 0 ) {
            return result;
        }
        if ( needRound ) {
            for ( let i = 0; i < len; i++ ) {
                result[i] = makeRoundValues(v1[i]/lv);
            }
        } else {
            for ( let i = 0; i < len; i++ ) {
                result[i] = v1[i]/lv;
            }
        }
        return result;       
    };
   ```

   ## 사용한 함수
   
   ``` javascript
    const isValidArrayValues = ( v1 ) => {
        if ( !v1 || !v1.length  )
            return false;
        return true;
    };

    /**
     * 2차원 배열이라도( Matrix ) Typed Array 로 사용될 경우 1차원 형식으로 구성
     * 그렇기 때문에 1차원 배열로 간주함
     * @param {*} mat 
     * @param {*} roundValue 
     * @param {*} needClone 
     * @returns 
     */
    export const makeRoundValues = ( mat, roundValue, needClone ) => {
        if ( !isValidArrayValues(mat)) {
            return mat;
        }

        if ( !roundValue || roundValue < 0 ) {
            roundValue = 100000; // javascript 에서 부동 소수점 연산 오류를 피하기 위한 값
        }

        const len = mat.length;
        let result = mat;
        if ( needClone ) {
            result = new Float32Array(len);
            if ( mat.rows ) {
                result.rows = mat.rows;
            }
            if ( mat.cols ) {
                result.cols = mat.cols;
            }
        }
        for ( let i = 0; i < len; i++ ) {
            result[i] = Math.round(mat[i]*roundValue)/roundValue;
        }
        return result;
    };
   ```

   ## 페이지에서 사용하기

   ``` html

    <script type="module">
        import * as TypedMatrixUtils from "../js/TypedMatrixUtils.js"

        const v1 = new Float32Array([1,2,3]);
        const normal = TypedMatrixUtils.makeNormalizeVector(v1,true);
        const nLen = TypedMatrixUtils.getVectorLength(normal);
        console.log ( v1 + "\n" + normal + "\n" + nLen );
    </script>

   ```


 


   

   


