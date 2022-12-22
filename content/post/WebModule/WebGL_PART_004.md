---
title: "WebGL2 - Matrix 연산 01 [ 04 ]"
date: 2022-12-21T22:47:18+09:00
draft: false
tags : ["Web","WebGL", "Language", "Math", "Matrix", "Javascript"]
topics : []
description : "Web GL 에서 사용하는 Matrix 소개 "
---


# WebGL 에서 행렬이란 ?
$$
\begin{bmatrix}1&2&3\\\4&5&6\\\7&8&9 \end{bmatrix} \begin{bmatrix}1\\\4\\\7\\\ \end{bmatrix}  \begin{bmatrix}1&2&3 \end{bmatrix}
$$

   위 표에서 전체 행렬이 1 ~ 9 까지의 내용을 담은 3 x 3 행렬 이라면, 1,4,7 은 열( Column ) 이고 1,2,3 은 행(Row) 라고 불립니다.    
   위 표의 행렬은 행 3개와 열 3개로 이뤄진 3 x 3 행렬인 셈입니다.   

   WebGL(3D 프로그램) 에서 행렬이 왜 사용되는 걸까요?    
   어떤 물체가 100개의 꼭지점을 갇고, 그 꼭지점을 연결한 위치 정보를 지니고 있다고 가정하겠습니다.   
   해당 물체의 이동, 축소, 확대, 회전이 발생한다면, 우리는 해당 물체가 처음 가지고 있는 꼭지점에서 어디로 이동하고, 어떻게    
   회전하였는지 등을 알기만 하면 이동과 회전에 대한 데이터를 통해 원래의 꼭지점이 변경된 위치를 계산 할 수 있습니다.    
   이렇게 회전, 이동, 축소, 확대 등의 계산을 위해서 행렬을 만들어 연산을 수행하게 됩니다.    

   꼭지점이 100개 라도, 축소, 확대 등이 공통적으로 적용된다면, 해당 행렬 하나를 구성해서 모든 꼭지점에    
   동일한 연산을 수행하게 할 수 있습니다.    

   이럴때 행렬을 사용하게 됩니다.    

# 행렬계산 예시

   간단하게 평면을 대상으로 예시를 구성해 보겠습니다.    
   공간에서 좌표 a 는 [2, 2] 라고 가정해 보겠습니다. x 축으로 2, y 축으로 2 입니다.    
   이 a 좌표를 x 방향으로 5 방향으로 이동하고, y 방향으로 3이동한 위치를 유추해 보겠습니다.    
   그냥 보아도 [2, 2] 에서 [2+5, 2+3] 으로 이동한 것을 연상해 볼 수 있을 것입니다.    
   별도로 계산 하지 않아도 알 수 있는 부분인데 이를 행렬의 곱셈으로 표기 할 수 있습니다.   
   WebGL 은 열백터를 기준으로 하니 다음과 같은 행렬 연산으로 표기 할 수 있습니다.  
   $$
      \begin{bmatrix} 1&0&5\\\0&1&3\\\0&0&1 \end{bmatrix} \times \begin{bmatrix}2\\\2\\\1\\\ \end{bmatrix} =  \begin{bmatrix} 7\\\5\\\1 \end{bmatrix}
   $$

   여기서 잠깐 의구심이 드는것이 x축으로 5 이동, y  축으로 3 이동이면, 굳이 행렬로 구하지 않아도 쉽게 구할 수 있는데 왜 행렬을 이용하는가 입니다.    
   아직 설명하지 않았지만, 행렬의 곱셈은 조금 복잡한 연산을 수행하여야 하는데요 .... 

   그 이유는 축소, 확대, 회전을 행렬식을 이용해서 계산해 줄 수 있기 때문입니다.   
   다음의 예시된 값을 확인해 보겠습니다. 

   $$
      \begin{bmatrix} 0&-1&5\\\1&0&3\\\0&0&1 \end{bmatrix} \times \begin{bmatrix}2\\\2\\\1\\\ \end{bmatrix} =  \begin{bmatrix} 3\\\5\\\1 \end{bmatrix}
   $$  

   이 값은 a 좌표인 [2, 2] 가 시계 반대방향으로 90도 회전한 후 [-2, 2 ] x 축으로 5 이동한 후 , y 축으로 3 이동하면 [ 3, 5 ] 좌표로 이동 한다는 의미 입니다.    
   먼저 회전이 이뤄지고, 그 후 이동이 진행 되었습니다.    만약 이동이 먼저 이뤄지고 회전이 이뤄지면 조금 다른 결과가 나오게 됩니다.    

   $$
      \begin{bmatrix} 0&-1&0\\\1&0&0\\\0&0&1 \end{bmatrix} \times  \begin{bmatrix} 1&0&5\\\0&1&3\\\0&0&1 \end{bmatrix} \times \begin{bmatrix}2\\\2\\\1\\\ \end{bmatrix}  =  \begin{bmatrix} -5\\\7\\\1 \end{bmatrix}
   $$  

   이 값은 a 좌표인 [2, 2] 가 [ 5+2, 2+3 ] 으로 이동후 반시계 방향으로 90도 회전하면 값은 [ -5, 7 ] 로 이동한다는 의미 입니다.    
   계산식에서 보듯 회전후 이동과 이동후 회전은 다른 결과를 가져 옵니다.   계산 진행 순서가 중요한 이유 입니다.    
   ( 순서만 정확히 지키면 오른쪽을 먼저 계산후 왼쪽을 계산해도, 왼쪽을 계산후 오른쪽을 계산 해도 결과는 같습니다. - 겹합법칙이 성립됩니다. ) 

# 헹렬 용어 및 Javascript Type Array 

   WebGL 에서 사용하는 행렬은 주로 3 x 3 행렬, 4 x 4 행렬 등 정사각형 형태의 행렬을 주로 사용합니다.   
   이를 정방행렬(Square Matrix) 이라고 지칭하고, 그중 대각선이 1로 채워지고 나머지가 0인 값을 지닌 행렬을 단위행렬(Identity Matrix ) 라고 부릅니다.    
   WebGL 에서 행렬 연산을 할 때 가끔 행렬의 순서를 뒤집어 사용해야 할 때가 있습니다.  이를 전치행렬 ( Transpose Matrix ) 이라고 부릅니다.   
   WebGL2 에서 내부적으로 지원되는 기능이 있어 구현이 필요할 까 생각이 드는 행렬중 역행렬이 있습니다.   역행렬은 원본 행렬을 곱했을 때 
   단위행렬의 결과를 가져오게 하는 행렬입니다.    구하는 방법은 몇가지 있지만, 개인적으로 가우스 소거법을 활용하는게 프로그램에서는    
   가장 효율적일 것 같습니다.   
   구현에 앞서 용어를 나열한 이유는 행렬이 낯선 분들은 용어에서 부터 접근이 쉽지 않기 때문입니다.   제가 용어가 익숙하지 않아 헤맸던 경험이 
   많았기 때문입니다.    

   Javascript 의 배열은 C++ 등의 배열과는 조금 차이가 있습니다.   
   javascript 언어의 특징이 Type이 명시적으로 사용되지 않습니다. 그렇다 보니 [] 배열기호를 사용하여도, int array 인지 object array 인지 알 수 없고,   
   그렇다 보니 c, c++ 등에서 같은 데이터 유형의 연속적인 메모리 공간 할당이 아예 불가능하게 됩니다.   
   이를 보완하고자 Float32Array 등 Type 이 지정된 Array 를 사용할 수 있도록 되었습니다.    
   WebGL 에서는 이런 Type 형식이 지정된 배열을 사용하게 됩니다.   
   하지만 한가지 문제점이 있는데 Type Array 는 1차원 배열 입니다.   4x4 등의 Matrix 를 활용하려면 2차원 배열이 등을 사용하는게 훨씬 직관적이지만, 
   Javascrpt 의 Type Array 를 사용할 경우 조금 문제가 있습니다.    
   그래서 WebGL 등에서는 mat4, mat3x4 등의 이름으로 어떤 형식인지를 미리 지정하여 자료형을 구성해 놓았습니다.    
   좋은 방법이지만, 모듈화 시킬때 하나씩 자료형을 만드는게 개인적으로 귀찮고, 번거로운 일이라, 다음과 같이 구성해 보았습니다. 
   
   ``` javascript
   const fArray = new Float32Array(16);
   fArray.rows = 4;
   fArray.cols = 4;
   ```

   형식화된 배열을 사용하지만, javascript 객체에 속성을 추가하는 방법으로 rows, cols 를 추가하여 어떤 Matrix 인지 알 수 있도록 구현해 보았습니다. 

# 행렬의 곱셈
   
   행렬의 곱셈은 조금 헷갈리는 면이 없지 않습니다.   천천히 살펴보면 어려운 부분은 아닌데, 조금 어지럽게 보이는 부분이 있는 것 같습니다. 
   특히 1차원 배열로 구성된 Matrix 연산을 위해서는 위치 정보에 대한 정확한 파악이 필요해서 헷갈릴 수는 있는데 원리만 알면 어렵지는 않습니다. 
   
   $$
      \begin{bmatrix} 1&0&5\\\0&1&3\\\0&0&1 \end{bmatrix} \times \begin{bmatrix}2\\\2\\\1\\\ \end{bmatrix} =  \begin{bmatrix} 7\\\5\\\1 \end{bmatrix}
      \\\
      \begin{bmatrix} 1 \times 2 + 0 \times 2 + 5 \times 1 \end{bmatrix} = \begin{bmatrix} 7 \end{bmatrix}
      \\\
      \begin{bmatrix} 0 \times 2 + 1 \times 2 + 3 \times 1 \end{bmatrix} = \begin{bmatrix} 5 \end{bmatrix}
      \\\
      \begin{bmatrix} 0 \times 2 + 0 \times 2 + 1 \times 1 \end{bmatrix} = \begin{bmatrix} 1 \end{bmatrix}
   $$
   첫번째 Matrix 의 행을 두번째 Matrix 의 열과 각 순서쌍을 곱해서 모두 더한 값이 계산된 값입니다.    
   앞의 행렬이 n1 x m1 의 행렬일 때 뒤의 행렬이 n2 x m2 의 행렬이면 m1 == n2 와 같다면 계산이 가능합니다.    
   역으로 m1 != n2 가 다르다면 행렬 곱셈은 성립하지 않습니다.   결과는 n1 x m2 행렬이 만들어 지게 됩니다.    

   아무래도 코드를 보면 좀더 쉽게 이해 되실 수 있을것 같습니다.    
   다만, 1차원 배열을 행렬 계산으로 변경해야 하기 때문에 소스 코드에서 위치 정보를 주의 깊게 보아야 할 것 같습니다.  


# Javascript 구현 
   
   ### 단위 행렬( Identity Matrix )

   ``` javascript
    export const makeIdentityMatrix = (m) => {
        const result = new Float32Array(m*m);
        result.rows = m;
        result.cols = m;

        for ( let i = 0; i < m; i++ ) {
            result[i*m+i] = 1;
        }
        return result;
    };
   ```

   ### 전치행렬 ( Transpose Matrix )

   ``` javascript 
    export const makeTransposeMatrix = ( matrix ) => {
		if ( !matrix ) {
			alert( "NO DATA" );
			return;
		}

        const rows = matrix.rows;
        if ( !rows )    //  내부에서 사용하는 형식이 아님
            return;
        const cols = matrix.cols;
        if ( !cols )    //  내부에서 사용하는 형식이 아님
            return;

        const result = new Float32Array(matrix.length);
        result.rows = cols;
        result.cols = rows;

        for ( let i = 0; i < cols; i++ ) {
            for ( let j = 0; j < rows; j++ ) {
                let idx = i*rows+j;
                let idx01 = j*cols+i;
                result[idx] = matrix[idx01];
            }
        }

		return result;
	};
   ```

   ### 행렬 곱셈 

   ``` javascript 
    export const multiplyMatrix = (m1,m2) => {
        if ( !m1 || !m2 ) 
            return undefined;   //  계산 할 수 없음 
        
        if (!m1.cols || !m2.rows || m1.cols != m2.rows ) 
            return undefined;
        
        const row1 = m1.rows;
        const col1 = m1.cols;
        const col2 = m2.cols;
        const result = new Float32Array(row1*col2);
        result.rows = row1;
        result.cols = col2;

        for ( let r = 0; r < row1; r++ ) {
            for ( let c = 0; c < col2; c++ ) {
                let idx = r*col2+c;
                for ( let t = 0; t < col1; t++ ) {
                    let idx01 = r*col1+t;
                    let idx02 = t*col2+c;
                    result[idx] += m1[idx01]*m2[idx02];
                }
            }
        }
        return result;
    };

   ```


   ### 기타 

   역행렬, 행렬의 덧셈 뺄셈, scalar 곱 등 필요한 연산 작업이 있으나, 
   행렬을 이해하는데 중요하다고 생각 되는 영역을 ( WebGL 연산에서 ) 먼저 구현해 보았습니다. 

   글을 구성하는 시점에 재구성한 함수들이라 향후 기회가 될 때 정비하고 설명이 필요한 부분을 
   조금 더 정돈해 보도록 하겠습니다.   
   행렬 연산에서 앞서의 개념과 곱셈을 이해하면 WebGL에서 연산은 거의 가능할 것 같습니다.   
   향후 WebGL 구현 부분에서 다시 언급하겠지만, WebGL은 열백터를 사용하고 있습니다.   
   1차원 배열의 데이터 전개 순서가 수학책 등에서 보여주는 순서와 약간 차이가 있습니다. 
   그때 전치 행렬은 사용하면 순서가 일치하게 됩니다.    
   WebGL 의 uniform 을 적용할 때 transpose 를 적용할 것인지 말 것이지 정하는 옵션이 있는데 
   그 때 다시 설명 하도록 하겠습니다. 



