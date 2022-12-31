---
title: "WebGL2 - Program 시작 03 [ 07 ]"
date: 2022-12-26T21:59:21+09:00
draft: false
tags : ["Web","WebGL", "Language","Javascript", "Programming", "Math"]
topics : []
description : "WebGL2 그리기 과정과 유니폼 Matrix"
---

# 프로그램 Rendeing 과정 
   
   앞서 WebGL 사용 방법에 대해 간단히 정리해 보았습니다.    
   다시 한번 정리하면
   1. canvas 객체를 구성합니다. 
   2. GL 객체를 가져옵니다. ( Canvas 를 통해 webgl2 를 가져옵니다. )
   3. GLSL Shader 소스(문자열) 을 Vertex Shader, Fragment Shader 를 구성합니다.
   4. Shader Program 을 각각 만들어 두개의 쌍을 사용할 GL Program 을 만들고 Compile 합니다.    일반적으로 프로그램 소스를 구성해서 Compile 하는 것과 유사한 흐름 입니다.  
   당연히 프로그램을 구성할 때 사용한 소스에서 정의한 input 과 output 규칙은 따라야 합니다. 
   5. 데이터를 구성합니다. ( 예제에서는 cube ) - 꼭지점 정보는 필수 입니다. 법선(normal), Color, Index 등을 구성하였습니다. ( 재사용 가능 )
   6. GPU 에 Attribute 와 Uniform 정보를 알려 주고 데이터를 GPU 로 복사 합니다. ( Buffer 로 만들어 Binding )
   7. GPU 에서 데이터를 사용 할 수 있는 방법을 적용합니다. 이때 WebGL2 에서 제공하는 Vertex Buffer Object 를 생성하고, 해당 객체를 Binding 하여 이후 사용할 내용을 저장하게 합니다. 
   8. Vertext, Color, Index 등의 정보를 사용할 수 있는 방법을 기록 합니다. 
   9. 이제 Rendeing 과정이 시작됩니다. 
   10. 화면을 클리어 합니다. ( 이전 소스에서는 검은색으로 clear )
   11. 사용할 gl program 을 선택합니다. 
   12. 사용할 vertex array object 를 binding 합나다. ( 이미 해당 객체에는 gpu 에서 사용할 수 있는 방법이 기록되어 있습니다. )
   13. 전체에서 사용할 uniform 내용을 넘겨 줍니다. ( ex - 설명전이지만, world matrix, viewMatrix, projection matrix ... )
   14. cube 등에서 사용할 지역 uniform 정보를 넘겨 줍니다. ( 여러 물체 일 경우 world 좌표가 모두 다를 수 있음 )
   15. 물체를 그립니다. ( drawcall - index or 직접 )
   16. 현재 binding 되어 있는 vertex array object 를 해제 합니다. 
   17. 현재 binding 되어 있는 program 을 해제 합니다. 

   대략 이런 흐름으로 진행될 수 있습니다. 
   위 과정에서 앞의 글에서 간단하게 언급하였던 Shader 와 데이터 초기화는 아주 간략하게 나마 소스와 내용을 이야기 하였으나   
   이번 글에서는 Rendering 과정에 맞춰서 정리해 보고자 합니다. 

# GL Rendering 초기화 
   
   처음 진행하는 것은 weggl 에서 화면을 정리하여 새롭게 그릴 준비를 하고, 
   물체의 색상을 선택하기 위해서 앞의 물체와 뒤의 물체중 그릴 물체의 색상을 정하기 위한 작업을 진행합니다.     
   해당 기능을 찾아 보면 여러 option 이 있고, 렌더링 성능의 문제등도 고려 하여야 한다 라고 하지만, 일단 이런 작업이 진행된다 정도로 이해할 수 있을 것 같습니다 . 

   ``` javascript 
            gl.clearColor(0.0, 0.0, 0.0, 1.0);  // Clear to black, fully opaque
			   gl.clearDepth(1.0);                 // Clear everything
			   gl.enable(gl.DEPTH_TEST);           // Enable depth testing
			   gl.depthFunc(gl.LEQUAL);            // Near things obscure far things

			   // Clear the canvas before we start drawing on it.

			   gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);
            gl.useProgram(obj.program);
                
            gl.bindVertexArray(obj.vao);

            for ( let i = 0; i < obj.uniformArray.length; i++ ) {
               GLUtils.setUniformValues( gl, obj.uniformArray[i].uLocation, 
                  obj.uniformArray[i].data, obj.uniformArray[i].dataType, obj.uniformArray[i].dataKind, obj.uniformArray[i].dataSize, true);
            }

            gl.drawElements(gl.TRIANGLES, obj.indexInfos.indexSize, gl.UNSIGNED_INT,0);
                
            gl.bindVertexArray(null);   //  clean item
            gl.useProgram(null);    //  clean program ( shader combined )
   ```
   gl.useProgram , gl.bindVertexArray 의 함수는 현재 예제의 프로그램과, 설정된 값을 사용하겠다는 의미 입니다.     
   for loop 에서 보시면 앞서 설정한 uniform 의 값을 assign 해주고 있습니다.    
   아무런 설정이 없으니, 초기 단위 행렬의 값이라 변화가 없을 것입니다.   
   행렬에 들어 있는 내용은 projectionMatrix, viewMatrix, worldMatrix 입니다.    
   이 글에서는 먼저 worldMatrix, 다음 projectionMatrix, 그리고 viewMatrix 순서로 간단하게 정리해 보고자 합니다. 
   논리적인 순서는 world, view(camera), projection 순서 이겠지만, 무엇인가 표출되는 것이 필요한 개발자의 속성(?) 때문에 
   위 순서로 정리하려고 합니다. 

   gl.drawElement 에서 인덱스로 넘겨준 값을 기준으로 결정된 색상이 출력됩니다.    
   위의 코드처럼 진행되면 결과는 빨간색 화면이 나타나게 됩니다.   그 과정을 찾아가 보겠습니다.     

   ### Cube 에 저장된 데이터 
   
   1. 위치 정보중 앞면과 뒷면만을 보면 아래와 같습니다.
   > 앞면 ( Front face )    
     -1.0,-1.0,1.0,  1.0,-1.0,1.0,  1.0,1.0,1.0,  -1.0,1.0,1.0,    
   > 뒷면 ( Back face )    
     -1.0,-1.0,-1.0,  -1.0,1.0,-1.0,  1.0,1.0,-1.0,  1.0,-1.0,-1.0,    
   > -1 ~ 1 사이의 값으로 구성되어 있습니다.    그리고 각 세번째 항목을 살펴보면 앞면은 1 이고 뒷면을 -1 입니다.    
   Z 좌표가 중심점 에서 눈앞으로 양수 그 뒤로 음수로 되어 있는 구조 입니다.    

   2. 색상의 앞과 뒷면의 값입니다.    
   > 앞면 ( Front face )    
     [1.0,  1.0,  1.0,  1.0], ( 하얀색 입니다. )
   > 뒷면 ( Back face )    
     [1.0,  0.0,  0.0,  1.0] ( 빨간색 입니다. )   
   > 각 색상은 0~1 사이의 실수값 입니다. ( float )  

   ### 저장된 데이터를 아무런 조작없이 출력하면 빨간색이 출력됩니다.    
   앞면이 힌색인데 출력되는 결과는 빨간색입니다.   
   뒷면이 출력되고 있습니다.     
   화면전체가 빨간색으로 출력되고 있으니 구별이 되지 않아 일단 물체의 사이즈를 줄여 보겠습니다. 
   
   ``` javascript 
                let worldMatrix             = TypedMatrixUtils.makeIdentityMatrix(4);
                let viewMatrix              = TypedMatrixUtils.makeIdentityMatrix(4);
                let projectionMatrix        = TypedMatrixUtils.makeIdentityMatrix(4);   

                //  [ 주석 1 ] : x,y,z 모두 0.4 로 축소 
                worldMatrix                 = TypedMatrixUtils.makeScaleMatrix3D(0.4,0.4,0.4);

                //  [ 주석 2 ] : x, y 방향으로 45도 변경
                let radian  = Math.PI*45/180; //    Math.PI * 0.25
                worldMatrix                 = TypedMatrixUtils.multiplyMatrix(worldMatrix, TypedMatrixUtils.makeRotateXMatrix3D(radian));
                worldMatrix                 = TypedMatrixUtils.multiplyMatrix(worldMatrix, TypedMatrixUtils.makeRotateYMatrix3D(radian));
                
                obj.uniformArray[0].data    = worldMatrix;
                obj.uniformArray[1].data    = viewMatrix;
                obj.uniformArray[2].data    = projectionMatrix;                

                for ( let i = 0; i < obj.uniformArray.length; i++ ) {
                    GLUtils.setUniformValues( gl, obj.uniformArray[i].uLocation, 
                        obj.uniformArray[i].data, obj.uniformArray[i].dataType, obj.uniformArray[i].dataKind, obj.uniformArray[i].dataSize, true);
                }

                gl.drawElements(gl.TRIANGLES, obj.indexInfos.indexSize, gl.UNSIGNED_INT,0);
   ```
   전체화면이 빨간색으로 보이는 것에서 오른쪽 중간의 빨간색으로 사이즈가 줄어 들었습니다.     
   입체감이 전혀 들지 않으니 x 방향과, y 방향으로 45 도 정도 틀어 보겠습니다.  
   ![scaleimage](/imgs/gl_07_00.png#small)   ![scaleimage](/imgs/gl_07_01.png#small)    ![scaleimage](/imgs/gl_07_02.png#small)   

   위 소스의 주석 1과 주석 2 부분을 순차적으로 적용하면 위 화면과 같은 결과를 얻을 수 있습니다.    

   ### 행렬 Matrix Javascript 
   ``` javascript 
         export const makeRotateXMatrix3D = ( theta ) => {
            let sv = Math.sin(theta);
            let cv = Math.cos(theta);

            const result = new Float32Array([
               1,  0,  0, 0,
               0,	cv, -sv, 0,
               0,  sv,  cv, 0,
               0,  0,  0, 1
            ]);
            result.rows = 4;
            result.cols = 4;
            return result;
         }

         export const makeRotateYMatrix3D = ( theta ) => {
            let sv = Math.sin(theta);
            let cv = Math.cos(theta);

            const result = new Float32Array([
               cv,  0,  sv,0,
               0,	 1, 0,0,
               -sv,  0,  cv,0,
               0,  0,  0, 1
            ]);
            result.rows = 4;
            result.cols = 4;
            return result;
         };

         export const makeRotateZMatrix3D = ( theta ) => {
            let sv = Math.sin(theta);
            let cv = Math.cos(theta);

            const result = new Float32Array([
               cv, -sv,   0, 0,
               sv,  cv,   0, 0,
               0,   0,   1, 0,
               0,  0,  0, 1,
            ]);
            result.rows = 4;
            result.cols = 4;
            return result;
         };

         export const makeScaleMatrix3D = ( sx, sy, sz ) => {
            const result = new Float32Array([
               sx, 0,  0,  0,
               0, sy,  0,  0, 
               0,  0, sz,	0,
               0,  0,  0,	1
            ]);
            result.rows = 4;
            result.cols = 4;
            return result;
         };


         export const makeTranslateMatrix3D = ( dx, dy, dz ) => {

            const result = new Float32Array([
               1, 0,  0,  dx,
               0, 1,  0,  dy, 
               0,  0, 1,  dz,
               0,  0,  0,	1
            ]);
            result.rows = 4;
            result.cols = 4;
            return result;
         };


   ``` 

   ### NDC 좌표  
   잠깐 확인해 보았지만,  앞면은 흰색인데, 뒷면의 빨간색이 출력되었습니다.    
   화면을 출력하는 마지막 단계에서 모니터의 좌표는 OpenGL 에서 사용하는 오른손 좌표계가 아니라,    
   x, y 는 같으나, z 축은 앞면이 -1, 뒤로가면서 +1 의 왼손 좌표계를 사용합니다.    
   그래서 해당 좌표를 뒤집어서 표현하여야 합니다.    
   물체의 원근법을 적용하는 projection matrix 를 구성할 때 z 좌표만 변경해서 구성하면 정상적인 화면이    
   출력 됩니다.    
   Projection 을 구성할 때 시야각(field of view), 종횡비 (aspect), near (출력할 근거리), far( 출력할 원거리 ) 를 기준으로   
   화면에 출력할 위치를 만들어 줍니다.    
   그런데 cube 의 원래 위치가 -1 ~ 1 사이이기 때문에 z 축을 보정해 주어도 가까운 거리 ( near ) 가 0이상의 값을 사용하게 되고, 
   그렇게 되면 뒷면의 -1 좌표가 보이게 됩니다.    

   ``` javascript 
                //  [ 주석 2 ] : x, y 방향으로 45도 변경
                let radian  = Math.PI*45/180; //    Math.PI * 0.25
                worldMatrix                 = TypedMatrixUtils.multiplyMatrix(worldMatrix, TypedMatrixUtils.makeRotateXMatrix3D(radian));
                worldMatrix                 = TypedMatrixUtils.multiplyMatrix(worldMatrix, TypedMatrixUtils.makeRotateYMatrix3D(radian));

                //  [ 주석 3 ] : translation 
                //worldMatrix = TypedMatrixUtils.multiplyMatrix(worldMatrix,TypedMatrixUtils.makeTranslateMatrix3D(0,0,-10));
                worldMatrix = TypedMatrixUtils.multiplyMatrix(TypedMatrixUtils.makeTranslateMatrix3D(0,0,-8), worldMatrix);

                //  [ 주석 4 ] : projection 
                projectionMatrix = TypedMatrixUtils.makePerspectiveMatrix(48*Math.PI/180, gl.canvas.width/gl.canvas.height, 0.1, 1000);
   ```
   화면은 빨간색 , 하얀색,  입체 순으로 나타나는데 이번 주석은 4번만 적용했을 때 빨간색이 나타납니다.   
   3번 주석 내용을 실행하면 하얀색 화면이 나타나고, 2번 주석까지 실행하면 입체가 나타납니다.    
   ![scaleimage](/imgs/gl_07_00.png#small)   ![scaleimage](/imgs/gl_07_03.png#small)    ![scaleimage](/imgs/gl_07_04.png#small)   

   ### Projecion 행렬 입니다. 

   ``` javascript

      const cot = (v) => {
         return 1.0/Math.tan(v);
      }

      export const makePerspectiveMatrix = ( fovy, aspect, near, far ) => {
         let cv = cot(fovy/2);
         var nf = 1 / (near - far);
         
         const result = new Float32Array([
            cv/aspect, 0, 0, 0,
            0, cv, 0,0,
            0, 0, -((far+near)/(far-near)), -(2*near*far/(far-near)), 
            0, 0, -1, 0
         ]);
         result.rows = 4;
         result.cols = 4;
         return result;
      }

   ```

   ### 기타  
   Open GL ( WebGL ) 에서는 열백터를 사용합니다.    
   데이터를 Float32Array 로 구성하는 과정에서 가로의 순서대로 데이터를 넘기면 행렬 계산에서 의도한 데로 결과가 
   나타나지 않는 현상에 마주치게 됩니다.    
   다행히 uniform matrix 를 webgl 로 구성할 때 transpose option 이 있어 이를 true 로 구성하면 가로 순서로 입력된 
   내용이 정상적으로 출력 되는 것을 확인하였습니다. ( 당연하겠지요 .. 순서를 변경했으니요 ... )   
   그래서 일단 정리를 위한 모듈은 일반적인 행렬을 설명하는 방식으로 모듈을 구성해 보았습니다.    
   transpose true 가 연산에서 그리 큰 성능저하를 보이지는 않을 것 같습니다.  ^^    

   행렬의 회전은 삼각함수에서 간단히 다뤄 보았기 때문에 여기서는 언급하지 않았습니다. ^^   

   실행 결과는 [이곳](/html/WebGL_PART_007_01.html) 에서 확인 할 수 있습니다. 
   
















