---
title: "WebGL2 - Program 시작 08 - Object Picking 01 - [ 12 ]"
date: 2023-02-18T20:28:06+09:00
draft: false
tags : ["Web","WebGL", "Language","Javascript", "Programming"]
topics : []
description : "WebGL 물체 선택 ( Picking 구현방법중 하나 )"
---

   프로그램이 사용자와 상호 작용을 하기 위해서는 User가 물체 혹은 대상을 선택할 수 있어야 하고, 그 선택한 항목을 기준으로 
   특정한 일을 수행하도록 하는 것이 필요할 것 같습니다.    
   일반 Application 이나 Web 등에서 Button Click, 글 Click 등을 통해 사용자 Action 을 구성하게 됩니다.    
   WebGL 에서도 사용자가 무엇인가를 선택한다면, 선택한 항목이 무엇인지 알 수 있어야 하고, 이를 Picking 이라고 합니다.     
   문제는 우리가 어떤 항목을 선택하기 위해서 WebGL로 구성된 화면에서 마우스를 클릭하였다고 하면, 직접적으로 우리가 알 수 있는 내용은 
   선택한 pixel 의 색상값 만을 확인할 수 있을 뿐입니다.    물체가 처음 구성된 vertex 값은 -1, 1 사이의 object 공간에서, 우리가 볼 수 있는 
   world 공간 ( world matrix 로 대표됨 ) 으로 이동하고, 보는 위치에 따라( camera matrix, view matrix ) 재 조정된 후 NDC 라고 불리는 
   화면에 ( Projetion matrix ) 출력된 결과를 우리는 보게 됩니다.  이렇게 변환된 위치에서 우리는 단지 해당 위치의 색상값 만을 보게 되는데  
   어떻게 물체를 식별할 수 있을 까요?   가장 정확한 방법은 선택한 위치에서 z 방향으로 직선으로 진행하는 ray 를 쏘아 그 위치에 있는 물체를 가까운 순으로 
   확인하는 방법일 것입니다. 좋은 방법이지만, 물체는 object 공간에서 확인할 수 있기 때문에 ray 를 해당 공간으로 변환해서 삼각형 mesh 와의 충돌을 확인해야 합니다.   
   여러 물체가 ray 에 부딛칠 수 있기 때문에 부딛힌 위치를 저장한 후 가장 가까운 물체를 확인하여여 합니다.   조금 복잡한 과정을 통해서 확인이 가능하기 때문에 여력이 
   될 때 정리해 보겠습니다.    
   다른 방법중 하나는 그림자를 만들때 사용하였던 것 처럼 물체마다 고유한 아이디를 부여하고(수치값), 그 값을 색상으로 설정하여 
   그린 후 선택한 위치의 색상을 얻어와서 어떤 아이디를 가진 물체인지를 찾는 방법입니다.  아래의 사이트에서 구성한 방법을 참조하였습니다.   
   #### [https://webgl2fundamentals.org/webgl/lessons/webgl-picking.html](https://webgl2fundamentals.org/webgl/lessons/webgl-picking.html)
   이 방법을 중점적으로 정리해 보겠습니다. 
      
   #### 구현 [/html/WebGL2/WebGL_PART_012_01.html](/html/WebGL2/WebGL_PART_012_01.html)   

# WebGL readPixels 함수 
   기본형식의 함수는 readPixels(x,y,width,height, format, type, pixels) 의 형식입니다.   x, y 는 화면에서의 위치인데, 
   왼쪽 하단이 0,0 의 시작점입니다.    web 에서 canvas 좌표는 왼쪽 상단이 0,0 이기 때문에 WebGL 에서의 좌표로 변환하기 위해서는
   canvas 의 높이 - 현재 클릭한 Y Position 이 되어야 합니다.    
   width, height 는 단 하나의 pixel 정보만 필요하기 때문에 1, 1 로 설정합니다.    
   format 은 canvas 의 이미지 기본 포맷인 RGBA ( gl.RGBA ) 의 4개 데이터를 기준으로 구성하고, type 도 Uint8Array 포맷인, gl.UNSIGNED_BYTE 로 
   구성합니다. 데이타는 Uint8Array(4) 가 될 것 입니다.   아래는 구성한 소스 입니다.  
   ``` javascript 

        function getCurrentPixels(gl, mouseX, mouseY) {
            const posX = mouseX;
            const posY = gl.canvas.height - mouseY;
            const data = new Uint8Array(4);
            gl.readPixels(posX, posY, 1, 1, gl.RGBA, gl.UNSIGNED_BYTE, data);
            const id =  data[0] + (data[1] << 8) + (data[2] << 16) + (data[3] << 24);
            return id;
        }

        //  GLItem Class 함수
        setObjectColorIDInfos = ( uColorName, objectID ) => {
            this.uColorIDName = uColorName;
            this.objectColorID = objectID;

            this.uColorIDValues = new Float32Array([
                ((this.objectColorID >>  0) & 0xFF) / 0xFF,
                ((this.objectColorID >>  8) & 0xFF) / 0xFF,
                ((this.objectColorID >> 16) & 0xFF) / 0xFF,
                ((this.objectColorID >> 24) & 0xFF) / 0xFF,
            ]);

            const uniforms = (this.uniformMap.has(uColorName) ? this.uniformMap.get(uColorName) : 
                {uniformName : uColorName, data : this.uColorIDValues, dataType : 1, dataKind : 2, dataSize : 4, uLocation:undefined,transpose:false});               
            uniforms.data = this.uColorIDValues;
            this.uniformMap.set(uColorName, uniforms);
        };


   ``` 
   #### ((this.objectColorID >>  0) & 0xFF) / 0xFF 의 의미는 주어진 수치를 bit shift 한후 bit and 255(11111111) 한 값을 255 으로 나눠주면 0.0 ~ 1.0 사이의 값이 나오도록 구성한 내용입니다. 
   위와 같은 방식으로 4개의 배열에 0.0 ~ 1.0 사이의 값을 할당하게 됩니다. 객체의 갯수는 2^32 개를 가져올 수 있으니, 아마도 모자라지는 않을 것 같습니다.    
   이렇게 설정된 값을 가져오는 부분은 역순의 bit 연산을 하게 됩니다.    
   #### const id =  data[0] + (data[1] << 8) + (data[2] << 16) + (data[3] << 24); 의 의미는 0~255(11111111 이진수) 의 값을 위의 역순으로 shift 하여 원래 구성된 값을 가져오는 방법 입니다. 
   해당 위치에 선택한 아이디가 무엇인지 확인이 가능하게 됩니다.     

   참조한 사이트 입니다. 
   #### [https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/readPixels](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/readPixels)    
   #### [https://webglfundamentals.org/webgl/lessons/ko/webgl-readpixels.html](https://webglfundamentals.org/webgl/lessons/ko/webgl-readpixels.html)    

# Page 에서 ObjectID 구성 및 MouseEvent 
   ### Object ID 구성
   Page 에서 Object 를 식별하기 위해 간단히 Map 으로 구성된 자료형을 만들어 보관 합니다.   
   해당 자료형은 id, value 인데 id는 숫자이며, value 은 GLItem Class 입니다.    소스의 내용은 아래와 같습니다. 

   ``` javascript 
    //  전역 선언부에 구성 
    const shapeObjects = new Map();
    ...

            const gItem = new GLDataUtils.GLItem("itemID");
            gItem.initResource( gl, program, attributeArray, uniformArray, indexInfos, textureInfos, "worldMatrix");
            gItem.setLocalMatrix( TypedMatrixUtils.makeTranslateMatrix3D(2,2, -2));
            gItem.setObjectColorIDInfos(uColorID, 1);
            shapeObjects.set(1, gItem);

            const gItem02 = new GLDataUtils.GLItem("itemID02");
            gItem02.initResource( gl, program, attributeArray, uniformArray, indexInfos, textureInfos02, "worldMatrix");
            gItem02.setLocalMatrix( TypedMatrixUtils.multiplyMatrix(TypedMatrixUtils.makeTranslateMatrix3D(0,0,-8), TypedMatrixUtils.makeScaleMatrix3D(2,2,2)));
            gItem02.setObjectColorIDInfos(uColorID, 2);
            shapeObjects.set(2, gItem02);

            const planeIndexInfos = { data : new Uint32Array(pData.indices), indexSize : pData.indices.length , indexType :  gl.UNSIGNED_INT, offset:0};
            planeItem.initResource( gl, program, planeAttributeArray, uniformArray, planeIndexInfos, textureInfos03, "worldMatrix");      
            planeItem.setObjectColorIDInfos(uColorID, 3);
            shapeObjects.set(3, planeItem);
   ``` 

   3개의 물체를 1, 2, 3 의 아이디로 구성하였습니다.    

   ### Mouse Down Event 구성
   mouse down 을 담을 수 있는 그릇과 Event 를 canvas 객체에 등록하였습니다. 
   event 의 좌표값은 clientX,Y - offsetX,Y, - pageX,Y - screenX,Y 등 이 있고, scroll 등을 고려하여 구성할 때는 조금더 신경써서 구성해야 하지만, 
   지금은 정말 간단히 구성해 놓은 것입니다.    실제 서비스에서는 좌표를 정밀히 조정하여야 합니다.  해상도를 포함해서요 ...^^    

   ``` javascript 
        const mouseEventObj = {
            mouseX : -1, 
            mouseY : -1, 
            isMouseDown : false,
            currentID : -1, 
        }

            canvas.addEventListener("mousedown", function(e) {
                mouseEventObj.mouseX = e.offsetX;
                mouseEventObj.mouseY = e.offsetY;
                mouseEventObj.isMouseDown = true;
            }, false);

   ``` 
   이제 데이터를 가져올 준비가 되었습니다.   다음은 WebGL 의 shader 를 활용해서 그리고 가져오는 부분입니다.    

# ID 값 프로그램 그리기
   ### Shader 소스
   앞서 그림자 그리기 위해 사용한 내용에서 조금 더 첨부하여 구성해 보겠습니다.   
   Vertex, Fragment Shader 가 필요합니다.   당연히 이 Shader에서 그리는 위치는 최종적인 위치와 동일하게 구성되어야 합니다. 

   ``` c++
            //  vertex shader 
            #version 300 es
            uniform mat4 worldMatrix, viewMatrix, projectionMatrix;
            layout(location = 0) in vec3 positions;
            void main() {
                gl_Position = projectionMatrix * viewMatrix  * worldMatrix * vec4(positions, 1.0);
            }

            //  fragment shader 
            #version 300 es
            precision highp float;
            uniform vec4 uColorID;
            out vec4 fragColors;
            void main() {
                fragColors = uColorID;
            }

   ```    
   vertex shader 에서 worldMatrix, viewMatrix, projectionMatrix 는 실제 그림을 그리는 것과 동일합니다.   position 정보만 필요합니다. 
   주의깊게 확인할 부분은  fragment shader 의  uniform vec4 uColorID;  입니다. 물체마다 동일 색상을(동일아이디)를 구성합니다.    
   짐작하시겠지만, 물체에서 해당 내용을 pixels 정보로 가져온후 shift 연산을 통해 수치값을 가져오면 어떤 물체가 선택되었는지 확인이 가능하게 됩니다.    
   이 부분을 어떻게 rendering 하게 될까요 ?

   ### Rendering 소스
   ``` javascript  

        function createRenderTexture(gl) {
            const renderTexture = gl.createTexture();

            gl.bindTexture(gl.TEXTURE_2D, renderTexture);
            gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
            gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
            gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);            

            return renderTexture;
        }

        function createRenderbuffer(gl) {
            const renderBuffer = gl.createRenderbuffer();
            gl.bindRenderbuffer(gl.RENDERBUFFER, renderBuffer);
            return renderBuffer;
        }

        function makeFramebufferAttachmentSizes(gl, renderTexture, renderBuffer, width, height) {
            gl.bindTexture(gl.TEXTURE_2D, renderTexture);
            const level = 0;
            const border = 0;
            gl.texImage2D(gl.TEXTURE_2D, level, gl.RGBA, width, height, border, gl.RGBA,  gl.UNSIGNED_BYTE, null );
            gl.bindRenderbuffer(gl.RENDERBUFFER, renderBuffer);
            gl.renderbufferStorage(gl.RENDERBUFFER, gl.DEPTH_COMPONENT16, width, height);
        }

        function renderPickingFrameBuffer(gl,renderTexture, renderBuffer, width, height) {
            const frameBuffer = gl.createFramebuffer();
            gl.bindFramebuffer(gl.FRAMEBUFFER, frameBuffer);

            gl.framebufferTexture2D(gl.FRAMEBUFFER, gl.COLOR_ATTACHMENT0, gl.TEXTURE_2D, renderTexture, 0);
            gl.framebufferRenderbuffer(gl.FRAMEBUFFER, gl.DEPTH_ATTACHMENT, gl.RENDERBUFFER, renderBuffer);
            return frameBuffer;
        }

   ```
   그리는 과정에서 최종적인 그림 이전에 rendering buffer를 활용하여 물체를 그리고 그 물체의 색상을 아이디의 vec4 형식으로 변환하여 
   그 값을 가져오게 하기 위한 부분입니다.  아래는 이를 활용하여 그리는 부분입니다.  

   ``` javascript 
   
            const renderTexture = createRenderTexture(gl);
            const renderBuffer = createRenderbuffer(gl);

            makeFramebufferAttachmentSizes(gl, renderTexture, renderBuffer, gl.canvas.width, gl.canvas.height);

            const pickingBuffer = renderPickingFrameBuffer(gl,renderTexture, renderBuffer, gl.canvas.width, gl.canvas.height);
            //  생략 ......

                gl.enable(gl.DEPTH_TEST);           // Enable depth testing
                gl.enable(gl.CULL_FACE);                

                if ( mouseEventObj.isMouseDown ) {
                    gl.bindFramebuffer(gl.FRAMEBUFFER, pickingBuffer);                    
                    gl.viewport(0, 0, gl.canvas.width, gl.canvas.height);
                    gl.clearColor(0, 0, 0, 0);
                    gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);
                    //renderPickingFrameBuffer(gl, renderTexture, renderBuffer, gl.canvas.width, gl.canvas.height);
                    pick.render(gl, false);

                    let selectedID = getCurrentPixels(gl, mouseEventObj.mouseX, mouseEventObj.mouseY);
                    for ( let key of shapeObjects.keys() ) {
                        shapeObjects.get(key).setDisplayType(3);
                    }
                    if ( shapeObjects.has(selectedID) ) {
                        shapeObjects.get(selectedID).setDisplayType(0);
                    }

                    mouseEventObj.isMouseDown = false;

                    pick.cleanAll(gl);
                    gl.bindFramebuffer(gl.FRAMEBUFFER, null);                
                }

                gl.viewport(0, 0, gl.canvas.width, gl.canvas.height);
                gl.clearColor(1, 0, 1, 1);
                gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);

                gl.activeTexture(gl.TEXTURE0);
                gl.bindTexture(gl.TEXTURE_2D, depthTexture);

                if ( curData == 0 ) {
                    curData = time;
                } else {
                    delta = time - curData;
                }

                gp.render(gl, false);
                gp.cleanAll(gl);
                requestAnimationFrame(drawScene);

   ```
   선언부에 renderBuffer, renderTexture, pickingBuffer 를 구성한 후, mouse down 이 되었을 때만 그립니다.     
   let selectedID = getCurrentPixels(gl, mouseEventObj.mouseX, mouseEventObj.mouseY); 에서 id 를 가져오고, 
   모든 객체를 display 상태가 texture 를 가진 내용으로 rendering 하도록 합니다.   선택한 내용은 texture 없는 
   원색이 되도록 합니다.   shapeObjects.get(selectedID).setDisplayType(0);  
   앞서 예제와 동일한데 선택 영역만 추가 되어 있습니다.   순서대로 원본, 물체1선택, 평면선택 입니다. 마우스가 클릭되어 
   아무것도 선택한 물체가 없으면 모두 초기화 됩니다.    

   ![/imgs/gl_12_01.png](/imgs/gl_12_01.png#small)  ![/imgs/gl_12_02.png](/imgs/gl_12_02.png#small)   ![/imgs/gl_12_03.png](/imgs/gl_12_03.png#small)     

   #### 구현 [/html/WebGL2/WebGL_PART_012_01.html](/html/WebGL2/WebGL_PART_012_01.html)  사이트 에서 확인하실 수 있습니다.    





   
   


