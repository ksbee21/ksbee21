---
title: "WebGL2 - Program 시작 02 [ 06 ]"
date: 2022-12-24T20:05:10+09:00
draft: false
tags : ["Web","WebGL", "Language","Javascript", "Programming"]
topics : []
description : "WebGL2로 화면 그리기"
---

# 처음 시작할 때 주의할 내용 

   WebGL을 처음 구성하려 할 때 가장 먼저 부딛히는 부분이 저 같은 경우, 익숙하지 않은    
   용어와 수식이 난무 하는 설명 이었습니다.    

   이미 수학적인 기초가 튼튼하게 다져진 분들은 상관이 없겠지만,  저처럼 익숙하지 않은 용어에,    
   덧붙인 수식 기호 등은 프로그램밍에 대한 접근을 막는 부분 이었습니다.   

   프로그램을 구현할 때 필요한 부분은 해당 수식에 대한 증명이 아니라, 원리에 대한 기본적인 이해와,   
   그 이해를 바탕으로 공식화된 수식을 활용할 수 있다면, 프로그램을 구성하는데 문제는 없어 보입니다.       
   일단, 공식으로 알려진 내용을 구현하고, 그를 활용하는 방법으로 접근해 보겠습니다.    

   예를 들어 회전행렬의 경우 3차원에서는 x,y,z 축을 중심으로 x 축을 기준으로 회전할 것인지,    
   y축인지, z 축인지를 정하고 그에 따른 행렬을 구할 수 있습니다.   
   알려져 있는 행렬공식을 활용하도록 하겠습니다.   
   ( 공식 자체의 유도과정은 Wiki 등의 사이트에서 찾아 보실 수 있습니다. )

   처음 시작해서 완성하게 될 예제 Sample 입니다. [Sample Site - Click](/html/WebGL_PART_006_S.html)    
   하나씩 확인해 보겠습니다.  ( WebGL2 기준 )  

#  WebGL 용어 및 프로그램 구성 순서
   
   WebGL을 가장 단순하게 말하면, 모니터 화면 Pixel 에 색상을 결정하고 출력하는 프로그램 이라고 보아도    
   좋을것 같습니다.    

   다만, 그 색상을 결정하는 방법이 보간(Interpolation), 조명에 따른 색상 변화, 3차원 공간에서 앞에 있는지,   
   뒤에 있는지에 따른 색상을 결정하게 됩니다.   
   그것을 위해 몇가지 GPU 전용 프로그램을 사용하게 됩니다.    

   ### Shader 프로그램 
   프로그램 구문은 C 계열 언어와 상당히 유사한 모습을 보입니다.    
   꼭지점, 색상, Texture, 법선(Normal) 등의 정보를 받아 들이는 Vertex Shader 와 그 결과값으로 색상을 
   출력하는 Fragment Shader 의 2개가 거의 쌍처럼 동작하게 됩니다.    
   Vertex Shader 의 output 이 Fragment 의 input 으로 받아 들이게 됩니다.     

   #### Vertex Shader
   ``` c++
            #version 300 es
            uniform mat4 worldMatrix, viewMatrix, projectionMatrix;
            in vec3 position;
            in vec3 normal;
            in vec4 colors;
        
            out vec3 vNormal ;
            out vec4 vColors;
        
            void main() {
                gl_Position = projectionMatrix * viewMatrix  * worldMatrix * vec4(position, 1.0);
                vNormal = normalize(transpose(inverse(mat3(worldMatrix))) * normal);
                vColors = colors;
            }
   ```
   #### Fragment Shader 
   ``` c++
            #version 300 es
            precision highp float;
        
            in vec4 vColors; 
            in vec3 vNormal;
            out vec4 fragColor;
        
            void main() {
                fragColor = vColors;
            }
   ```
   표현을 위해 javascript 구문을 제외 하였지만, 문자열로 ` ` 표기로 감싸 가져와서 Compile 하게 됩니다.    
   지금 프로그램에서 주의 깊게 보아야 할 부분은, #version 300 es 라는 표현이고 이는 WebGL 2를 사용하겠다는    
   선언입니다.    ( Script 로 구성할 때 앞의 공백이 없어야 합니다. )

   uniform 이라고 선언된 변수는 해당 물체에 공통적으로 적용되는 항목을 구성할 때 사용합니다.    
   mat4 라는 것은 4x4 행렬 구조를 사용하겠다는 자료형 선언입니다.( world, view, projection 4x4 행렬 )   
   in vec3 는 받아들이는 자료형식이 vector 3 라는 것입니다. [1,2,3], [x,y,z] 등의 자료 구조 입니다.   
   out vec3 는 처리 결과를 Fragment Shader 에 넘겨 주겠다는 것입니다.   

   현재 Fragmemt Shader에서는 넘겨운 vColors 값을 그대로 사용하고 있습니다.    
   vColors 값은 Vertex Shader 에서 구성한 colors 값을 사용하고 해당 값은 받아 들인 값이니,   
   전달받은 색상을 그대로 사용하겠다는 의미 입니다.   

   ### WebGL 객체
   html 에서 제공하는 canvas 를 활용하여 webgl 객체 사용을 정의 합니다. 
   ``` javascript

    export const makeWebGL = (canvas) => {
        let gl = undefined;
        try {
            gl = canvas.getContext("webgl2") || canvas.getContext("webgl") || canvas.getContext("experimental-webgl");
        } catch ( e ) {
            alert ( "Unable to initialize WebGL. Your browser may not support it.");
            gl = null;
        }
        return gl;
    };
   ``` 
   이글에서는 webgl2 를 대상으로 합니다. - 하위 호환성은 고려하지 않습니다.   

   ### WebGL Program 객체
   앞서 이야기한 Shader Source 를 활용하여 Shader 객체를 만들고 그것을 통해 WebGL Program 을 구성합니다.    
   진행 과정에서 보듯 WebGL 프로그램은 Shader 의 내용과 밀접한 관계가 있습니다.   

   ``` javascript 

        export const createShader = (gl, type, source) => {
            const shader = gl.createShader(type);
            gl.shaderSource(shader, source);
            gl.compileShader(shader);
            let success = gl.getShaderParameter(shader, gl.COMPILE_STATUS);
            if ( success ) {
                return shader;
            }
            console.log(gl.getShaderInfoLog(shader));
            gl.deleteShader(shader);
        };

        export const createProgram = (gl, vertexShader, fragmentShader) => {
            const program = gl.createProgram();
            gl.attachShader(program, vertexShader);
            gl.attachShader(program, fragmentShader);
            gl.linkProgram(program);

            let success = gl.getProgramParameter(program, gl.LINK_STATUS);
            if ( success ) {
                return program;
            }
            console.log(gl.getProgramInfoLog(program));
            gl.deleteProgram(program);
        };

        export const createProgramBySource = ( gl, vs, fs ) => {
            const verShader = createShader(gl,gl.VERTEX_SHADER, vs );
            const fragShader = createShader(gl,gl.FRAGMENT_SHADER, fs );
            const program = createProgram(gl, verShader, fragShader );
            return program;
        };

   ```
   
   ### Uniform 정보 구성 
   uniform 은 공통적으로 사용할 데이터 입니다.   함수와 사용에 입니다. 

   #### GLUtils.js Uniforms 설정
   ``` javascript 
        export const setUniformLocations = (gl, program, uniformArray) => {
            if ( !uniformArray ) 
                return uniformArray;

            for ( let i = 0, iSize = uniformArray.length; i < iSize; i++ ) {
                uniformArray[i].uLocation = gl.getUniformLocation(program, uniformArray[i].uniformName );
            }
            return uniformArray;
        };



        export const setUniformFloatValue = ( gl, uLocation, uData, dataKind, dataSize, transpose ) => {
            if ( !gl || !uLocation ||  !uData )
                return false;

            switch ( dataKind ) {
                case 1 :    // value 
                    if ( dataSize == 1 ) {
                        gl.uniform1f(uLocation, uData);
                    } else if ( dataSize == 2 ) {
                        gl.uniform2f(uLocation, uData);
                    } else if ( dataSize == 3 ) {
                        gl.uniform3f(uLocation, uData);
                    } else if ( dataSize == 4 ) {
                        gl.uniform4f(uLocation, uData);
                    } else {
                        gl.uniform1f(uLocation, uData);
                    }
                    break;
                case 2 :    // vector  
                    if ( dataSize == 1) {
                        gl.uniform1fv(uLocation, uData);
                    } else if ( dataSize == 2 ) {
                        gl.uniform2fv(uLocation, uData);
                    } else if ( dataSize == 3 ) {
                        gl.uniform3fv(uLocation, uData);
                    } else if ( dataSize == 4 ) {
                        gl.uniform4fv(uLocation, uData);
                    } else {
                        gl.uniform4fv(uLocation, uData);
                    }
                    break;
                case 3 :    // matrix 
                    if ( dataSize == 2 ) {
                        gl.uniformMatrix2fv(uLocation, transpose, uData);
                    } else if ( dataSize == 3 ) {
                        gl.uniformMatrix3fv(uLocation, transpose, uData);
                    } else if ( dataSize == 4 ) {
                        gl.uniformMatrix4fv(uLocation, transpose, uData);
                        //alert ( "4 : " + uData + "\n" + transpose );
                    } else {
                        gl.uniformMatrix4fv(uLocation, transpose, uData);
                    }
                    break;
                default : 
                    gl.uniformMatrix4fv(uLocation, transpose, uData);        
                    break;
            }
        };

        export const setUniformValues = ( gl, uLocation, uData, dataType, dataKind, dataSize, transpose ) => {
            if ( !gl || !uLocation ||  !uData )
                return false;

            //  dataType : 1 : float, 2 : int, 3 : uint, 4 : boolean
            //  dataKind : 1 : value, 2 : vector , 3 : matrix 
            //  dataSize : 1 : 1, 2 : 2, 3 : 3, 4 : 4 => ex vec3, mat3 ...
            if ( dataType  == 1 ) {
                setUniformFloatValue(gl, uLocation, uData, dataKind, dataSize, transpose);
            } else if ( dataType == 2 ) {
                setUniformIntValue(gl, uLocation, uData, dataKind, dataSize);
            } else if ( dataType == 3 ) {
                setUniformUIntValue(gl, uLocation, uData, dataKind, dataSize);
            } else if ( dataType == 4 ) {
                setUniformBoolValue(gl, uLocation, uData, dataKind, dataSize);
            }
        }

   ```

   #### 페이지 호출 
   ``` javascript 
            const uniformArray = [
                {uniformName : "worldMatrix", data : TypedMatrixUtils.makeIdentityMatrix(4), dataType : 1, dataKind : 3, dataSize : 4, uLocation:undefined},
                {uniformName : "viewMatrix", data : TypedMatrixUtils.makeIdentityMatrix(4), dataType : 1, dataKind : 3, dataSize : 4, uLocation:undefined},
                {uniformName : "projectionMatrix", data : TypedMatrixUtils.makeIdentityMatrix(4), dataType : 1, dataKind : 3, dataSize : 4, uLocation:undefined},                
            ];

            GLUtils.setUniformLocations(gl, program, uniformArray);      
   ```


   ### Attribute 정보 구성 
   원칙적으로 Attribute 정보를 먼저 설정한고, 데이터를 바인딩하는 작업을 하게 되는데,    
   현재 프로그램에서는 초기화 과정에서 데이터 Binding 까지 적용하고 있습니다.    
   WebGL 2 의 중요한 기능중 하나인 Attribute 정보를 Vertex Array Object 에 넣어 놓고    
   재사용하기 위해서는 해당 모듈이 실행되기 전에 사용하고자 하는 Vertex Array Object 를 미리 만들고,    
   Binding 해 놓아야 합니다. 

   #### Vertex Array Object Binding 
   ``` javascript 
            const vao = gl.createVertexArray();
            gl.bindVertexArray(vao);
   ```

   #### Attribute 설정 
   ``` javascript
        export const setAttributeValue = (gl, program, attributeName, data, size, dataType, normalize, stride, offset) => {
            const gLocation = gl.getAttribLocation(program,attributeName);
            const buffer = gl.createBuffer();
            gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
            gl.bufferData(gl.ARRAY_BUFFER, data, gl.STATIC_DRAW);

            //  Vertex Array Buffer binding ... 

            gl.enableVertexAttribArray(gLocation);
            gl.vertexAttribPointer(gLocation, size, dataType, normalize, stride, offset);
            return gLocation;
        };

        export const setAttributeValues = (gl, program, attribArray ) => {
            if ( !attribArray || !attribArray.length )
                return undefined;

            
            for ( let i = 0, iSize = attribArray.length; i < iSize; i++ ) {
                attribArray[i].loc = setAttributeValue(gl, program, attribArray[i].attributeName, attribArray[i].data, attribArray[i].size, attribArray[i].dataType, attribArray[i].normalize, 
                    attribArray[i].stride, attribArray[i].offset);
            }
            return attribArray;
        };
   ```
   1. 위치찾기 - gl.getAttribLocation(program,attributeName);
   2. 버퍼설정 - gl.bindBuffer(gl.ARRAY_BUFFER, buffer); => gl.bufferData(gl.ARRAY_BUFFER, data, gl.STATIC_DRAW); 
   3. Vertex Array Binding - 기구성됨
   4. 데이터 사용 적용 - gl.enableVertexAttribArray(gLocation); => gl.vertexAttribPointer(gLocation, size, dataType, normalize, stride, offset);

   #### Index Data 구성 
   Index 가 아닌 방식으로 구현할 수 있으나, 아무래도 point 외의 정보도 한번에 효율적으로 로딩하기 위해서는 index 를 사용하는 방법이 더 좋을것 같습니다. 

   ``` javascript 
        export const setIndexInfos = ( gl,  indexInfo ) => {
            const indexBuffer = gl.createBuffer();
            gl.bindBuffer(gl.ELEMENT_ARRAY_BUFFER, indexBuffer);
            gl.bufferData(gl.ELEMENT_ARRAY_BUFFER, indexInfo.data, gl.STATIC_DRAW);
        }
   ```

   #### 페이지 에서 사용

   ``` javascript

        function render(obj) {
            const gl = obj.gl;

            let delta = 0;
            let curData = 0;

            function drawScene(time) {
                time *= 0.001;
                gl.clearColor(0.0, 0.0, 0.0, 1.0);  // Clear to black, fully opaque
			    gl.clearDepth(1.0);                 // Clear everything
			    gl.enable(gl.DEPTH_TEST);           // Enable depth testing
			    gl.depthFunc(gl.LEQUAL);            // Near things obscure far things

			    // Clear the canvas before we start drawing on it.

			    gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);
                gl.useProgram(obj.program);

                
                gl.bindVertexArray(obj.vao);

                if ( curData == 0 ) {
                    curData = time;
                } else {
                    delta = time - curData;
                }

                let wd = TypedMatrixUtils.makeTranslateMatrix3D(0,0,-8);
                wd.rows = 4;
                wd.cols = 4;
                
                wd = TypedMatrixUtils.multiplyMatrix(wd,TypedMatrixUtils.makeRotateXMatrix3D(delta) );
                wd = TypedMatrixUtils.multiplyMatrix(wd,TypedMatrixUtils.makeRotateYMatrix3D(delta) );

                let projection = TypedMatrixUtils.makePerspectiveMatrix(60*Math.PI/180, gl.canvas.width/gl.canvas.height, 0.01, 100);
                obj.uniformArray[2].data = projection;

                obj.uniformArray[0].data = wd;
                for ( let i = 0; i < obj.uniformArray.length; i++ ) {
                    GLUtils.setUniformValues( gl, obj.uniformArray[i].uLocation, 
                        obj.uniformArray[i].data, obj.uniformArray[i].dataType, obj.uniformArray[i].dataKind, obj.uniformArray[i].dataSize, true);
                }

                gl.drawElements(gl.TRIANGLES, obj.indexInfos.indexSize, gl.UNSIGNED_INT,0);
                
                gl.bindVertexArray(null);   //  clean item
                gl.useProgram(null);    //  clean program ( shader combined )
                requestAnimationFrame(drawScene);
            };

            requestAnimationFrame(drawScene);
        }

        function main() {
            const canvas = GLUtils.makeCanvasObject("myCanvas");
            const gl = GLUtils.makeWebGL(canvas);

            if ( !gl ) {
                alert ( "WEBGL 을 사용할 수 없습니다. ");
                return;
            }
            const cube = GLDataUtils.makeCubeData();
            const typeNum = 1;
            const program = GLUtils.createProgramByType(gl, typeNum);

            if ( !program ) {
                alert ( "WEBGL을 사용중 오류가 발생하였습니다. ");
                return;
            }

            const uniformArray = [
                {uniformName : "worldMatrix", data : TypedMatrixUtils.makeIdentityMatrix(4), dataType : 1, dataKind : 3, dataSize : 4, uLocation:undefined},
                {uniformName : "viewMatrix", data : TypedMatrixUtils.makeIdentityMatrix(4), dataType : 1, dataKind : 3, dataSize : 4, uLocation:undefined},
                {uniformName : "projectionMatrix", data : TypedMatrixUtils.makeIdentityMatrix(4), dataType : 1, dataKind : 3, dataSize : 4, uLocation:undefined},                
            ];

            const attributeArray = [
                {attributeName : "position", data : new Float32Array(cube.positions) , size: 3, dataType: gl.FLOAT, normalize:false, stride:0, offset:0, loc: undefined},
                {attributeName : "normal", data : new Float32Array(cube.normals) , size: 3, dataType: gl.FLOAT, normalize:false, stride:0, offset:0, loc: undefined},
                {attributeName : "colors", data : new Float32Array(cube.colors) , size: 4, dataType: gl.FLOAT, normalize:false, stride:0, offset:0, loc: undefined},        
            ];
            
            GLUtils.setUniformLocations(gl, program, uniformArray);            
            const vao = gl.createVertexArray();
            gl.bindVertexArray(vao);

            GLUtils.setAttributeValues(gl, program, attributeArray);
            const indexInfos = { data : new Uint32Array(cube.indices), indexSize : cube.indices.length , indexType : 2};

            GLUtils.setIndexInfos(gl, indexInfos);
            const obj = {
                gl:gl,
                program:program,
                vao:vao,
                uniformArray:uniformArray,
                attributeArray:attributeArray,
                indexInfos:indexInfos,
            }
            render(obj);
        }

        main();
   ```

### 기타 
   
   WebGL 프로그램이 어떤 방식으로 진행되는지 흐름을 보이는데 촛점을 맞춰 보았습니다.     
   다음 글에서는 지금 프로그램을 조금 더 상세히 정리해 보겠습니다. 



