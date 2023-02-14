---
title: "WebGL2 - Program 시작 07 - Shadow mapping - [ 11 ]"
date: 2023-02-08T20:23:34+09:00
draft: false
tags : ["Web","WebGL", "Language","Javascript", "Programming"]
topics : []
description : "WebGL 그림자 기본 개념 ( Shadow Mapping )"
---

# Shadow Mapping 

   앞에서 Phong 모델에서 제시하는 빛에 대해 간단히 정리해 보았습니다.     
   빛이 비추는 방향이 물체에 어떻게 영향을 미치는가 하는 부분에서는 그럴듯하게 보이지만, 해당 모델에서는 처리하기 어려운 부분이
   빛이 비추는 반대편의 그림자 들입니다.    
   우리는 빛이 비추면 반대편에 그림자가 지는 당연한 세상에 살고 있지만, 컴퓨터에서는 어떻게 빛과 그림자를 표현할 수 있을까요 ?     
   원리는 이해하기 그리 어렵지 않습니다.    다만, 프로그램에서 구현하는 부분은 조금 신경써야 할 부분이 있습니다.     
   그 내용을 정리해 보고자 합니다.   


   [Texture 미포함 - 예제 사이트는 이곳을 클릭하여 확인해 보실 수 있습니다.](/html/WebGL2/WebGL_PART_011_01.html)       
   [Texture 포함 - 예제 사이트는 이곳을 클릭하여 확인해 보실 수 있습니다.](/html/WebGL2/WebGL_PART_011_02.html)       

   ### 그림자 만드는 기본 원리
   빛의 관점에서( 빛이 비추는 위치 ) 물체를 보면 , 빛이 직진하는데 방해물이 없으면 빛을 보는 것이고, 만약 그 중간에 방해물이 
   있고 해당 물체가 불투명 물체 이면 최종적인 물체는 빛을 볼 수 없다는 기본 원칙에서 출발 합니다.     
   물체의 관점에서 보면, 빛으로 향하는 직선( Ray 라고 지칭함 )이 방해물 없이 빛에 도달하면 직접 빛을 받는 것이고, 중간에 물체가 있다면, 
   빛을 받을 수 없다고 판정합니다.   이런 방식을 Ray Tracing 에서 사용하고 있다고 합니다.    좋은 방법이지만, 포인트 하나 하나 에서 
   그림자를 만들기 위해, 그리고 반사와 굴절광을 만들기 위해, 그리고 현실적인 화면을 위해 재귀적인 호출이 발생한다면, 아무래도 실시간으로 
   구성하는데에는 조금 무리가 갈것도 같습니다. ( 요즘은 Graphic card 가 좋아져서 Ray Tracing 이 가능하다고는 합니다. )     
   다른 방법으로는 조금 거칠지만, GPU 파이프 라인을 두번 활용하여, 첫번째는 빛의 관점에서 Rending 을 수행하여, 깊이 Buffer 를 구성한 
   texture 를 만들고, 두번째 정상적인 화면에서 첫번째 만든 texture 에 저장된 깊이 값을 비교하여 그림자 효과를 주는 방법이 있습니다.    
   이것을 Shadow Map 이라고 합니다.      
   OpenGL 이 제공하는 기능으로 첫번째는 화면에 아무것도 그리지 않고, 빛의 시각에서 물체를 구성하면 물체의 Z 값이 Texture 에 저장하게 됩니다.    
   이 저장된 Texture 는 0 ~ 1 사이의 가로 세로 범위를 가지는 그리고자 하는 물체 전체를 보관한 그림 입니다.    
   두번째는 원래 표현하고자 하는 물체를 그리면서, 첫번째 그린 그림에서 표현된 위치의 Z 값과, 현재 Z 값을 비교해 현재 값이 크거나 같으면 빛을 받지 못하는 것으로 표현하는 방법입니다.      
   말로만 정리하면 조금 헷갈릴 수도 있으니, 핵심적인 구현 부분만 먼저 살펴 보겠습니다.  
   
   ### 첫번째 Texture 구성 
   WebGL(OpenGL) 에서 제공하는 기능이라 Shader 소스를 보면서 확인해 보겠습니다.  

   #### Vertex Shader 
   ``` c++
            #version 300 es
            uniform mat4 worldMatrix, lightViewMatrix, lightProjectionMatrix;
            layout(location = 0) in vec3 positions;
            layout(location = 2) in vec4 colors;
            void main() {
                gl_Position = lightProjectionMatrix * lightViewMatrix  * worldMatrix * vec4(positions, 1.0);
            }
   ```
   확인해 보실 부분은 빛의 시각에서 물체의 위치를 구성하고 있는 부분입니다.     
   물제는 positions 정보로 넘어오고, 그 물체의 위치만 빛의 위치를 기준으로 조정하고 있습니다.    
   
   #### Fragment Shader 
   ``` c++
            #version 300 es
            precision highp float;
            void main() {
            }
   ```
   색상등을 결정하는 Fragment 는 아무런 일도 하지 않습니다.    
   내부에서 Depth Buffer 에 담는 것은 OpenGL에서 내부적으로 해주는 일이라 더이상 해야할 것이 없습니다.    

   #### 이부분을 어떻게 담을 수 있을까요?

   ``` javascript 

      export const createDepthFrameBuffer = (gl, depthTexture) => {
         const depthFramebuffer = gl.createFramebuffer();
         gl.bindFramebuffer( gl.FRAMEBUFFER, depthFramebuffer);
         gl.framebufferTexture2D(
            gl.FRAMEBUFFER, 
            gl.DEPTH_ATTACHMENT, 
            gl.TEXTURE_2D, 
            depthTexture, 
            0);

         return depthFramebuffer;
      };

      export const createShadowTextureMap = ( gl , SHADOW_WIDTH, SHADOW_HEIGHT ) => {
         if ( !SHADOW_WIDTH ) 
            SHADOW_WIDTH = 512;
         if ( !SHADOW_HEIGHT ) 
            SHADOW_HEIGHT = 512;

         const depthTexture = gl.createTexture();
         gl.bindTexture(gl.TEXTURE_2D, depthTexture);
         gl.texImage2D(
            gl.TEXTURE_2D, 
            0, //   mip map level
            gl.DEPTH_COMPONENT16, //gl.DEPTH_COMPONENT32F, //    gl.DEPTH_COMPONENT16
            SHADOW_WIDTH, 
            SHADOW_HEIGHT, 
            0, 
            gl.DEPTH_COMPONENT, 
            gl.UNSIGNED_SHORT, //gl.FLOAT,   //gl.UNSIGNED_SHORT
            null    //  data 
         );

         gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MAG_FILTER, gl.NEAREST);//gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MAG_FILTER, gl.LINEAR);
         gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.NEAREST);//gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
         gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
         gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);    

         //gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_COMPARE_MODE, gl.COMPARE_REF_TO_TEXTURE);  
         //gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_COMPARE_FUNC, gl.LEQUAL); 
         
         return {
            depthTexture : depthTexture, 
            SHADOW_WIDTH : SHADOW_WIDTH, 
            SHADOW_HEIGHT : SHADOW_HEIGHT,
         };
      };

   ``` 
   
   위 두개의 소스는 Depth 관련한 Texture 를 구성하는 함수와, FrameBuffer를 구성하는 함수 입니다.    
   조금 주의깊게 확인해 볼 부분이 gl.DEPTH_ATTACHMENT, 와 gl.DEPTH_COMPONENT, 입니다.    단순한 Texture 가 아닌, 
   Framebuffer 에 Attachment 할 수 있는 Depth Texture 를 구성한다는 의미 입니다.   몇가지 option 이 더 있지만,  이 두가지 항목이 중요한 것 같습니다.    
   함수를 두개로 구성한 이유는 Texture 를 재사용하기 위해서 입니다.   
   
   #### 첫번째 Rendering 
   ``` javascript 

            const depthTexture = GLUtils.createShadowTextureMap(gl, 1024, 1024);

            const framebuffer = GLUtils.createDepthFrameBuffer(gl, depthTexture);                     

			   gl.clearDepth(1.0);                 
            gl.enable(gl.CULL_FACE);
			   gl.enable(gl.DEPTH_TEST);           
			   gl.depthFunc(gl.LEQUAL);            

            gl.bindFramebuffer(gl.FRAMEBUFFER, framebuffer);                
            gl.viewport(0,0,SHADOW_WIDTH,SHADOW_HEIGHT);

            gl.clearColor(0.0, 0.0, 0.0, 1.0);  
			   gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);
           
            //  cull face front
            gl.cullFace(gl.BACK);  
            // 물체 그리기 
            sp.render(gl, true);
            gl.cullFace(gl.BACK);  
            //  cull face back

            gl.bindFramebuffer(gl.FRAMEBUFFER, null);

   ``` 
   위 소스에서 몇몇 option 기능은 없어도 정상 동작 합니다.    
   주의해서 확인이 필요한 부분은 gl.bindFramebuffer(gl.FRAMEBUFFER, framebuffer); 로 그릴 framebuffer 를 구성한후, 
   sp.render(gl, true); 이 부분은 물체를 그리는 앞서 구성된 Shader Program 을 기반으로 물체를 등록해 놓고 그리기 위한 부분입니다.   
   이렇게 되면 depthTexture 에 Depth 값을 담은 그림이 그려지게 됩니다.   
   gl.bindFramebuffer(gl.FRAMEBUFFER, null); 로 Framebuffer를 초기화 합니다.  

   이 과정이 종료되면 다음은 정상적인 그림을 그리게 됩니다.    

   ### 두번째 Texture 구성 
   두번째 Shader는 몇가지 기능을 추가해서 조금 복잡해 보일 수도 있습니다.   첫번째와 동일한 항목은 위치를 찾는데 필요한 항목입니다.   

   #### Vertex Shader 
   ``` c++
            #version 300 es
            uniform mat4 worldMatrix, viewMatrix, projectionMatrix;
            uniform mat4 lightViewMatrix, lightProjectionMatrix;
            uniform vec3 uLightPos;

            layout(location = 0) in vec3 positions;
            layout(location = 1) in vec3 normals;
            layout(location = 2) in vec4 colors;
            layout(location = 3) in vec2 texCoords;            
            
            out vec3 vNormal;
            out vec2 vTexCoord;
            out vec4 vColors;
            out vec4 vShadowCoord;
            out vec3 vLight;
        
            const mat4 tMat = mat4(
               0.5, 0.0, 0.0, 0.0, 
               0.0, 0.5, 0.0, 0.0,
               0.0, 0.0, 0.5, 0.0, 
               0.5, 0.5, 0.5, 1.0
            );
            
            void main() {
               vec3 worldPos =  (worldMatrix * vec4(positions, 1.0)).xyz;
               vLight = normalize(uLightPos-worldPos);

               vTexCoord = texCoords;
               vShadowCoord = tMat * lightProjectionMatrix * lightViewMatrix * vec4(worldPos, 1.0);
                    
               vColors = colors;
               gl_Position = projectionMatrix * viewMatrix  * vec4(worldPos, 1.0);     
               vNormal = normalize(transpose(inverse(mat3(worldMatrix))) * normals);                                   
            }

   ```
   vShadowCoord = tMat * lightProjectionMatrix * lightViewMatrix * vec4(worldPos, 1.0); 에서 빛의 관점에서 사용했던 코드와 거의 동일한데 tMat 이 사용된 이유는 
   -1 ~ 1 사이의 값을 0 ~ 1 사이의 texture 좌표로 변경하기 위함입니다.    
   앞서 저장된 texture 는 전체 화면을 담고 있습니다.   그리고 그 범위는 0 ~ 1 사이입니다.   변환된 위치는 texture 에서 자신의 위치 입니다.    
   위의 location = 0 등의 값은 Vertex Array Object 를 동일하게 사용할 때 같은 위치에서 값을 가져오기 위해서 사용합니다.    
   나머지는 꾸미기 위한 항목 들입니다.    

   #### Fragment Shader 
   ``` c++
            #version 300 es
            precision highp float;
            in vec3 vNormal;
            in vec4 vColors;
            in vec2 vTexCoord;
            in vec4 vShadowCoord;
            in vec3 vLight;

            uniform sampler2D shadowMap;
            uniform sampler2D uTexture;
            uniform float uBias;
            uniform int uDisplayType;

            out vec4 fragColor;
        
            void main() {
                vec3 normal = normalize(vNormal);
                vec3 light = normalize(vLight);
                vec3 shadowCoord = (vShadowCoord.xyz / vShadowCoord.w);
                //float vis = textureProj(shadowMap, vShadowCoord);

                float diff = max(dot(normal,light), 0.0);

                bool inRange =
                    shadowCoord.x >= 0.0 &&
                    shadowCoord.x <= 1.0 &&
                    shadowCoord.y >= 0.0 &&
                    shadowCoord.y <= 1.0;                
                float currentDepth = (shadowCoord.z - uBias);
                float currentDepth01 = shadowCoord.z;
                float currentDepth02 = (shadowCoord.z + uBias);   
                vec4 tColors = texture(shadowMap,shadowCoord.xy);
                float texDepth = texture(shadowMap,shadowCoord.xy).r;
                float shadows = (( inRange && currentDepth >= texDepth  ) ? 0.2 : 1.0);
                float shadows01 = (( inRange && currentDepth01 >= texDepth  ) ? 0.2 : 1.0);
                float shadows02 = (( inRange && currentDepth02 >= texDepth  ) ? 0.2 : 1.0);                                
                vec4 textureColor = texture(uTexture,vTexCoord);
                if ( uDisplayType == 0 ) {
                    fragColor = vec4(vColors.rgb * shadows , 1.0);
                } else if ( uDisplayType == 1 ) {
                    fragColor = vec4(vColors.rgb * shadows01 , 1.0);
                } else if ( uDisplayType == 2 ) {
                    fragColor = vec4(vColors.rgb * shadows02 , 1.0);
                } else if ( uDisplayType == 3 ) {
                    fragColor = vec4(textureColor.rgb * shadows * diff, 1.0);
                } else {
                    fragColor = vec4(vColors.rgb * shadows, 1.0);
                }
            }
   ```
   조금 복잡해 보이지만, 핵심은 uniform sampler2D shadowMap; 로 앞선 texture를 가져오는 부분과, vec3 shadowCoord = (vShadowCoord.xyz / vShadowCoord.w); 로 
   마지막 항목값으로 나누고, float currentDepth = (shadowCoord.z - uBias); 에서 빛이 비췄던 지역의 현재 Depth 값을 가져오는 부분입니다. bias 값을 주지 않으면, 
   보간 과정에서 중간에 있는 위치가 빛을 받는 부분이 굴곡진 것으로 나타날 수 있기 때문입니다.    보통 0.001 을 권하는데 저는 0.002 정도가 안정적으로 표현한 값인것 같습니다.    
   float texDepth = texture(shadowMap,shadowCoord.xy).r; 에서 r 값이 깊이 값을 저장하고 있습니다.   float shadows = (( inRange && currentDepth >= texDepth  ) ? 0.2 : 1.0); 에서 
   저장된 깊이값보다 현재 깊이 값이 커서 더 멀리 있다면 빛을 못받는 그림자이기 때문에 0.2 정도의 값을 주고, 그렇지 않으면 빛을 받는 것으로 표현한 것입니다.    

   bias 없이 구성하면 아래와 같은 화면을 볼 수 있습니다. 순차적으로 bias 없이, bias 0.002 , texture 조명 diffuse 적용 화면 입니다.  

   ![Without Bias](/imgs/gl_11_01.png#small), ![With Bias](/imgs/gl_11_02.png#small), ![Texture Light](/imgs/gl_11_03.png#small)

   

   #### 두번째 Rendering 
   ``` javascript 

                gl.enable(gl.DEPTH_TEST); 

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
   ```

   위에서 구성한 texture 를 binding 하고 있습니다.     
   gl.activeTexture(gl.TEXTURE0);  gl.bindTexture(gl.TEXTURE_2D, depthTexture);   
   gp 는 동일한 물체를 담은 정상적인 rending 입니다. gp 는 직전 shader 로 구성한 프로그램입니다.    

# Texture 를 포함한 소스 

   #### 전체 진행 소스 

   ``` javascript 

        import * as TypedMatrixUtils from '../../js/TypedMatrixUtils.js';
        import * as GLUtils from '../../js/GLUtils.js';
        import * as GLDataUtils from '../../js/GLDataUtils.js';

        function render(gl,gp,sp) {

            const SHADOW_WIDTH = 1024;
            const SHADOW_HEIGHT = 1024;

            const shadowInfos = GLUtils.createShadowTextureMap(gl,SHADOW_WIDTH, SHADOW_HEIGHT);
            const depthTexture = shadowInfos.depthTexture;

            setProgramUniformValues(gl, gp, sp);  

            let delta = 0;
            let curData = 0;

            function drawScene(time) {
                time *= 0.001;
             
                const framebuffer = GLUtils.createDepthFrameBuffer(gl, depthTexture);                     

			       gl.clearDepth(1.0);                 
                gl.enable(gl.CULL_FACE);
			       gl.enable(gl.DEPTH_TEST);           
			       gl.depthFunc(gl.LEQUAL);            

                gl.bindFramebuffer(gl.FRAMEBUFFER, framebuffer);                
                gl.viewport(0,0,SHADOW_WIDTH,SHADOW_HEIGHT);
                gl.clearColor(0.0, 0.0, 0.0, 1.0);  // Clear to black, fully opaque
			       gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);
           
                //  cull face front
                gl.cullFace(gl.BACK);  
                sp.render(gl, true);
                gl.cullFace(gl.BACK);  
                //  cull face back

                gl.bindFramebuffer(gl.FRAMEBUFFER, null);

                setProgramUniformValues(gl, gp, sp);      
                
                gl.enable(gl.DEPTH_TEST);           // Enable depth testing

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
                //gp.cleanAll(gl);
                requestAnimationFrame(drawScene);
            };

            requestAnimationFrame(drawScene);
        }

        function setProgramUniformValues( gl, pg, sg ) {

            let worldMatrix             = TypedMatrixUtils.makeIdentityMatrix(4);
            let viewMatrix              = TypedMatrixUtils.makeIdentityMatrix(4);
            let projectionMatrix        = TypedMatrixUtils.makeIdentityMatrix(4);   

            let lightProjectionMatrix        = TypedMatrixUtils.makeIdentityMatrix(4);               
            let lightViewMatrix        = TypedMatrixUtils.makeIdentityMatrix(4);                 



            let eye = [0.0, -15.0, 10.0];
            let at = [0.0, 0.0, -20.0];
            let up = [0.0, 1.0, 0.0];

            let eye02 = [-5.0, 10.0, 10.0];
            let at02 = [0.0, 0.0, -10.0];
            let up02 = [0.0, 1.0, 0.0];

            //  view
            viewMatrix = TypedMatrixUtils.makeCameraMatrix3D( eye, at, up );

            //  [ 주석 4 ] : projection 
            projectionMatrix = TypedMatrixUtils.makePerspectiveMatrix(90*Math.PI/180, gl.canvas.width/gl.canvas.height, 0.1, 1000);
            //projectionMatrix = TypedMatrixUtils.makeOrthographicMatrix(-30,30, -30,30, 0.1, 1000);

            lightProjectionMatrix = TypedMatrixUtils.makePerspectiveMatrix(90*Math.PI/180, gl.canvas.width/gl.canvas.height, 0.1, 1000);
            lightViewMatrix = TypedMatrixUtils.makeCameraMatrix3D( eye02, at02, up02 );

            console.log("lightViewMatrix", lightViewMatrix);

            

            pg.setUniformMatrix("worldMatrix",TypedMatrixUtils.makeIdentityMatrix(4));
            pg.setUniformMatrix("viewMatrix", viewMatrix);
            pg.setUniformMatrix("projectionMatrix", projectionMatrix);

            pg.setUniformMatrix("lightViewMatrix", lightViewMatrix);
            pg.setUniformMatrix("lightProjectionMatrix", lightProjectionMatrix);
            pg.setUniformMatrix("uLightPos", new Float32Array(eye02));

            sg.setUniformMatrix("lightViewMatrix", lightViewMatrix);
            sg.setUniformMatrix("lightProjectionMatrix", lightProjectionMatrix);
        }

        function main() {
            const canvas = GLUtils.makeCanvasObject("myCanvas");
            const gl = GLUtils.makeWebGL(canvas);

            if ( !gl ) {
                alert ( "WEBGL 을 사용할 수 없습니다. ");
                return;
            }
            const cube = GLDataUtils.makeCubeData();
            const typeNum = 5;
            const shadowNum = 4;
            const program = GLUtils.createProgramByType(gl, typeNum);
            const shadowProgram = GLUtils.createProgramByType(gl, shadowNum);

            if ( !program ) {
                alert ( "WEBGL을 사용중 오류가 발생하였습니다. ");
                return;
            }

            const uniformArray = [
                {uniformName : "worldMatrix", data : TypedMatrixUtils.makeIdentityMatrix(4), dataType : 1, dataKind : 3, dataSize : 4, uLocation:undefined, transpose:true},
                {uniformName : "viewMatrix", data : TypedMatrixUtils.makeIdentityMatrix(4), dataType : 1, dataKind : 3, dataSize : 4, uLocation:undefined, transpose:true},
                {uniformName : "projectionMatrix", data : TypedMatrixUtils.makeIdentityMatrix(4), dataType : 1, dataKind : 3, dataSize : 4, uLocation:undefined,transpose:true},                
                {uniformName : "lightViewMatrix", data : TypedMatrixUtils.makeIdentityMatrix(4), dataType : 1, dataKind : 3, dataSize : 4, uLocation:undefined, transpose:true},
                {uniformName : "lightProjectionMatrix", data : TypedMatrixUtils.makeIdentityMatrix(4), dataType : 1, dataKind : 3, dataSize : 4, uLocation:undefined,transpose:true},                
                {uniformName : "uTexture", data : 1, dataType : 2, dataKind : 1, dataSize : 1, uLocation:undefined,transpose:false},                                
                {uniformName : "shadowMap", data : 0, dataType : 2, dataKind : 1, dataSize : 1, uLocation:undefined,transpose:false},                   
                {uniformName : "uLightPos", data : new Float32Array([0.0,0.0,0.0]), dataType : 1, dataKind : 2, dataSize : 3, uLocation:undefined,transpose:false},                    
                {uniformName : "uDisplayType", data : 3, dataType : 2, dataKind : 1, dataSize : 1, uLocation:undefined,transpose:false},                                   
                {uniformName : "uBias", data : 0.002, dataType : 1, dataKind : 1, dataSize : 1, uLocation:undefined,transpose:false},                                    
            ];

            const attributeArray = [
                {attributeName : "positions", data : new Float32Array(cube.positions) , size: 3, dataType: gl.FLOAT, normalize:false, stride:0, offset:0, loc: undefined},
                {attributeName : "normals", data : new Float32Array(cube.normals) , size: 3, dataType: gl.FLOAT, normalize:false, stride:0, offset:0, loc: undefined},
                {attributeName : "colors", data : new Float32Array(cube.colors) , size: 4, dataType: gl.FLOAT, normalize:false, stride:0, offset:0, loc: undefined},      
                {attributeName : "texCoords", data : new Float32Array(cube.textures) , size: 2, dataType: gl.FLOAT, normalize:false, stride:0, offset:0, loc: undefined},      
            ];


            const checkerTexture = GLUtils.createCheckerTexture(gl);

            const url01 = "/imgs/wall.jpg";//"/imgs/8k_earth_daymap.jpg";
            const url03 = "/imgs/sea01.jpg";            
            const textureInfos = {
                texture : GLUtils.loadGLTextureData(gl, url01),
                index : 1,
                uTextureName : "uTexture",
            }

            const textureInfos02 = {
                texture : checkerTexture,
                index : 1,
                uTextureName : "uTexture",                
            }
            const textureInfos03 = {
                texture : GLUtils.loadGLTextureData(gl, url03),
                index : 1,
                uTextureName : "uTexture",                
            }

            
            const gProgram = new GLDataUtils.GLProgram("programID");
            gProgram.initResource(gl, program, uniformArray );

            const sProgram = new GLDataUtils.GLProgram("sprogramID");
            sProgram.initResource(gl, shadowProgram, uniformArray );
            const indexInfos = { data : new Uint32Array(cube.indices), indexSize : cube.indices.length , indexType :  gl.UNSIGNED_INT, offset:0};


            const gItem = new GLDataUtils.GLItem("itemID");
            gItem.initResource( gl, program, attributeArray, uniformArray, indexInfos, textureInfos, "worldMatrix");
            gItem.setLocalMatrix( TypedMatrixUtils.makeTranslateMatrix3D(2,2, -2));

            const gItem02 = new GLDataUtils.GLItem("itemID02");
            gItem02.initResource( gl, program, attributeArray, uniformArray, indexInfos, textureInfos02, "worldMatrix");
            gItem02.setLocalMatrix( TypedMatrixUtils.multiplyMatrix(TypedMatrixUtils.makeTranslateMatrix3D(0,0,-8), TypedMatrixUtils.makeScaleMatrix3D(2,2,2)));

            gItem02.makeRotateAnimations(3,0.02,-1);


            gItem.makeRotateAnimations(1, 0.05, 1);
            //gItem.makeRotateAnimations(2, 0.05, 1);        
            gItem.makeRotateAnimations(3, 0.05, 1);        
            
            gItem.makePendulumAnimation(3, 0.005, 1, -2, 2);            
            gItem.makeOrbitAnimations(3, 0.05, 1);   

            sProgram.appendItem(gItem);
            sProgram.appendItem(gItem02);
        
            gProgram.appendItem(gItem);
            gProgram.appendItem(gItem02);

            const plane = new GLDataUtils.BasicPlane('aa');
            const pData = plane.getCurrentData();
            const planeItem = new GLDataUtils.GLItem("planeItemID");

            const planeAttributeArray = [
                {attributeName : "positions", data : new Float32Array(pData.positions) , size: 3, dataType: gl.FLOAT, normalize:false, stride:0, offset:0, loc: undefined},
                {attributeName : "normals", data : new Float32Array(pData.normals) , size: 3, dataType: gl.FLOAT, normalize:false, stride:0, offset:0, loc: undefined},
                {attributeName : "colors", data : new Float32Array(pData.colors) , size: 4, dataType: gl.FLOAT, normalize:false, stride:0, offset:0, loc: undefined},  
                {attributeName : "texCoords", data :  new Float32Array(pData.textures), size: 2, dataType: gl.FLOAT, normalize:false, stride:0, offset:0, loc: undefined},                            
            ];

            const planeIndexInfos = { data : new Uint32Array(pData.indices), indexSize : pData.indices.length , indexType :  gl.UNSIGNED_INT, offset:0};
            planeItem.initResource( gl, program, planeAttributeArray, uniformArray, planeIndexInfos, textureInfos03, "worldMatrix");      

            planeItem.setLocalMatrix( TypedMatrixUtils.multiplyMatrix(TypedMatrixUtils.makeTranslateMatrix3D(0,0, -20), TypedMatrixUtils.makeScaleMatrix3D(20,20,20)) );
            gProgram.appendItem(planeItem)     ; 
            sProgram.appendItem(planeItem)     ;            
            
            setProgramUniformValues(gl, gProgram, sProgram);

            render(gl, gProgram, sProgram);
        }

        main();

   ```
   전체적인 진행 과정을 확인하는 정도로 봐 주시면 좋을 것 같습니다.    
   앞서 정리한 내용에서 texture 를 더 포함해 Rendering 하고 있습니다.    

   참조한 사이트는 이전에도 소개해 드린 WebGL2 사이트 입니다. 
   [https://webgl2fundamentals.org/webgl/lessons/ko/webgl-shadows.html](https://webgl2fundamentals.org/webgl/lessons/ko/webgl-shadows.html)    
   
   youtube 에 공개된 강의중 많은 도움을 받은 강의 입니다. 
   [https://www.youtube.com/watch?v=kCuEtQh91U8](https://www.youtube.com/watch?v=kCuEtQh91U8)    


   #### [Texture 미포함 - 예제 사이트는 이곳을 클릭하여 확인해 보실 수 있습니다.](/html/WebGL2/WebGL_PART_011_01.html)       
   #### [Texture 포함 - 예제 사이트는 이곳을 클릭하여 확인해 보실 수 있습니다.](/html/WebGL2/WebGL_PART_011_02.html)       





   
   





   
