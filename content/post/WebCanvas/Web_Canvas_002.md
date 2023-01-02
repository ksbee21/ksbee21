---
title: "Web_Canvas 기반 도형 맞추기 Game 01"
date: 2023-01-01T21:08:32+09:00
draft: false
tags : ["Web","Canvas","Language","Javascript"]
topics : []
description : "Canvas 테트리스 도형구성하기 "
---

# Canvas 만들고자 하는 Game 은?

   HTML5 가 도입되면서, 정확히는 canvas 가 기술 표준으로 정해지면서, Web 에서 구성할 수 있는 Application 같은 프로그램을 만들 가능성이 많이 높아진것 같습니다.    
   하지만, 표(Table 같은 Grid) 와 이미지 정렬, Text 정보 등을 기반으로 하는 업무 시스템에서는 Canvas 와 같은 기술은 
   그렇게 많이 활용되지는 못하고 있는것 같습니다.    
   그나마, Chart 등에서 SVG 에서 Canvas 로 점차 기술적인 전환이 이뤄지고 있지만, 아직도 많은 시스템에서는 활용하지 있지 않은것도 사실입니다.     

   그렇지만 점차 Web 에서 간간히 이미지 처리, 간단한 게임등을 개발하고자 하는 필요성이 발생하고 있고, Chart 중 3D Rendering 이 필요한 곳에서는 
   Canvas WebGL 이 사용되기도 하는것 같습니다.    

   Canvas로 조금 재미있는 것을 찾아보다가, 도형 맞추기 게임을 벤치 마킹해서 Web 으로 간단한 Prototype Game을 만들어 볼까 합니다.    
   개인적으로 Game 이 익숙한 분야는 아니기 때문에, Sound 등 기타 내용은 모두 생략하고, 구현의 핵심만 Canvas로 만들어 보겠습니다.        
   코드가 길어 질 수 있어서 2개의 챕터로 구분해서 구현해 보도록 하겠습니다.    

   첫번째는 UI 구성이고, 다음은 Event 처리및 Game Logic 입니다.    
   편의상 나눈 것이고, 아마도 정리해서 글을 쓰다 보면 중복되지 않을까 합니다.  ^^

   [다음주소](/html/WebCanvas/Web_Canvas_002_01.html) 에서 확인해 보실 수 있습니다.    

# Canvas Layer 구성  
   
   ### Layer 란 ? 
   #### 1. Canvas 데이터 구조

   화면에 항상 보여야 하는 부분이 있고, 사용자의 Action 을 받아들이고 변경해야 하는 영역이 있을 수 있습니다.    
   더 복잡한 Game 에서는 이런 Layer 를 잘게 구분해서 필요한 영역만 갱신하는 구조면 아무래도 성능면에서 이점이 있을 것 같습니다.    
   지금 만들고자 하는 Game은 그리 복잡한 내용이 아니기 때문에 두개의 Canvas Layer 를 구성해서 구현해 보려고 합니다.    
   
   그때 그때 참조하여야 하기 때문에 json 구조의 자료구조를 활요합니다.   class 나 function 구조로 구성해도 괜찮을 것 같지만,   
   핵심 구현 로직에만 집중하도록 하겠습니다.   
   여기서는 아래와 같은 자료 구조를 만들어 사용하도록 하겠습니다.     
   baseCanvas, activeCanvas 의 두개 Layer 로 구성할 예정 입니다.    

   ``` javascript 
         const canvasInfos = {
            baseCanvas : {
               canvas : null,
               ctx : null,
            },
            activeCanvas : {
               canvas : null,
               ctx : null,
            },
            fullWidth : -1,
            fullHeight : -1,
            cellSize : 30,
            columnSize : 12,
            rowSize : 12,
            startX : -1,
            startY : -1,
            strokeStyles : ['#AFAFAF', '#AFAFAF'],
            fillStyles : ['#EFEFEF','#FEFEFE'],

            markStartX : -1,
            markStartY : -1, 
            points : 0,
         };

   ```

   #### 2. 어떻게 구성할까요? 

   처음 화면이 로딩하는 시점에 두개의 canvas 생성합니다.    
   canvas 는 default 가 투명하기 때문에 같은 위치에 포개어 겹쳐 놓습니다.(z index 를 명시적으로 사용할 수도 있고, 동일 객체에 포개는 순서에 의해  Active canvas 를 구분할 수 도 있습니다. )      
   위에 놓인 객체만 Event 를 받아 들이기 때문에 아래에 있는 canvas 는 필요할 때 배경을 그리는 용도로 사용할 수 있습니다.    

   조금 복잡한 시스템이라면, Event Handling 을 위한 전용 Layer 도 생각해 볼 수 있지만, 현재 구현하고자 하는 시스템에서는  
   이 정도로 구성하여도 크게 문제가 되지 않을것 같습니다.    
   앞에 구성된 자료 구조에 로딩한 시점에 생성한 객체를 할당 합니다.    

   ``` javascript 

      function initBaseSizeUI() {

         var gap = 10;
         var w = window.innerWidth;
         var h = window.innerHeight;

         var outerDiv = document.getElementById("outerDiv");
         if ( !outerDiv ) {
            outerDiv = document.createElement("div");
            outerDiv.setAttribute("id","outerDiv");
         }

         outerDiv.style.width = (w-gap*2)+"px";
         outerDiv.style.height = (h-gap*2)+"px";
         outerDiv.style.margin = "0px";
         outerDiv.style.padding = "0px";
         outerDiv.style.border = gap + "px solid black";

         document.body.appendChild(outerDiv);


         var layerDiv = document.getElementById("myDiv");
         if ( !layerDiv ) {
            layerDiv = document.createElement("div");
            layerDiv.setAttribute("id","myDiv");
         }

         layerDiv.style.width = (w-gap*2)+"px";
         layerDiv.style.height = (h-gap*2)+"px";
         layerDiv.style.margin = "0px";
         layerDiv.style.padding = "0px";
         layerDiv.style.border = "0px solid black";
         layerDiv.style.position = "relative";

         outerDiv.appendChild(layerDiv);
         window.addEventListener("resize", initBaseSizeUI);

         var cw = layerDiv.clientWidth;
         var ch = layerDiv.clientHeight;


         var baseCanvas = document.getElementById("baseCanvas");
         if ( !baseCanvas ) {
            baseCanvas = document.createElement("canvas");
            baseCanvas.setAttribute("id", "baseCanvas");

            var baseContext = baseCanvas.getContext("2d");
            canvasInfos.baseCanvas.canvas = baseCanvas;
            canvasInfos.baseCanvas.ctx = baseContext;

            layerDiv.appendChild(baseCanvas);

         }

         var activeCanvas = document.getElementById("activeCanvas");
         if ( !activeCanvas ) {
            activeCanvas = document.createElement("canvas");
            activeCanvas.setAttribute("id","activeCanvas");
            activeCanvas.style.position = "absolute";
            activeCanvas.style.left = "0";
            activeCanvas.style.top = "0";
            var activeContext = activeCanvas.getContext("2d");

            canvasInfos.activeCanvas.canvas = activeCanvas;
            canvasInfos.activeCanvas.ctx = activeContext;

            layerDiv.appendChild(activeCanvas);
         }

         baseCanvas.style.width = cw+"px";
         baseCanvas.style.height = ch+"px";
         baseCanvas.width = cw;
         baseCanvas.height = ch;

         activeCanvas.style.width = cw+"px";
         activeCanvas.style.height = ch+"px";
         activeCanvas.width = cw;
         activeCanvas.height = ch;

         canvasInfos.fullWidth = cw;
         canvasInfos.fullHeight = ch;
         

         canvasInfos.startX = Math.floor((cw-(canvasInfos.cellSize*canvasInfos.columnSize))/2);
         canvasInfos.startY = Math.floor((ch-(canvasInfos.cellSize*canvasInfos.rowSize))/2);

         var baseH = canvasInfos.startY + canvasInfos.cellSize*canvasInfos.rowSize;
         baseH += Math.floor((ch - baseH - canvasInfos.cellSize*5)/2);

         canvasInfos.markStartX = Math.floor((cw-(canvasInfos.cellSize*5*4+30))/2);
         canvasInfos.markStartY = baseH;

         drawBaseCanvasUI();
         makeCanvasInfoValues();

         return layerDiv;
      }
   ```

   위 소스는 전체 사이즈를 알아와서, Border 의 크기만큼 축소된 Div 를 만들고, 그 안에 다시 div layer 를 구성해서, 2개이 canvas 객체를 만들어서 포개는 방식으로 진행한 것을 확인할 수 있습니다.    
   dhtml 에 관한 부분은 기회가 될 때 정리해 보도록 하겠습니다.    
   Canvas 에서 격자를 출력할 위치를 표현할 cell 크기와 갯수를 기준으로 표현할 위치를 구성 합니다 .    
   ``` javascript 
         canvasInfos.startX = Math.floor((cw-(canvasInfos.cellSize*canvasInfos.columnSize))/2);
         canvasInfos.startY = Math.floor((ch-(canvasInfos.cellSize*canvasInfos.rowSize))/2);
   ``` 
   사용자가 선택할 항목과 그 이벤트를 감지하기 위한 위치를 구성합니다.    
   ``` javascript 
         var baseH = canvasInfos.startY + canvasInfos.cellSize*canvasInfos.rowSize;
         baseH += Math.floor((ch - baseH - canvasInfos.cellSize*5)/2);

         canvasInfos.markStartX = Math.floor((cw-(canvasInfos.cellSize*5*4+30))/2);
         canvasInfos.markStartY = baseH;
   ``` 


# 도형 Cell UI 구성   
   전체적으로 기본 구조와 Canvas 객체를 만들어 적재하였다면, 이제 구체적으로 도형을 그리는 작업을 진행합니다.    
   도형을 그리는 방법은 구성에 따라 천차만별이지만,  이 소스에서는 격자(Matrix)를 구성하고, 그 격자에 true, false 값을 주어 
   true 일때 cell 을 칠하고 그렇지 않을 때 그리지 않는 방법으로 구성하였습니다.    
   사용할 수 있는 도형 종류는 만들기 나름이지만, 50개 정도 입니다. 

   ![shape](/imgs/shape_01.png)


   #### 도형을 구성하는 항목을 구성하는 소스 입니다. 
   
   배열에서 true, false 로 출력 여부를 확인합니다.      
   첫번째 항목을 보면, [true, true, true], [true, true, true], [true, true, true] 로 첫번째 모든게 그려진 도형 입니다.    

   ``` javascript 

        function getShapeTypeArray(typeNum) {
            var shapeArray = null;
            switch(typeNum) {
                case 1 : 
                    shapeArray = [
                                    [true,true,true],
                                    [true,true,true],
                                    [true,true,true]
                                ];
                    break;
                case 2 :
                    shapeArray = [[true]];
                    break;
                case 3:
                    shapeArray = [
                                    [true,true]
                                ];
                    break;
                case 4 :
                    shapeArray = [
                                    [true],
                                    [true]
                                ];
                    break;
                case 5:
                    shapeArray = [
                                    [true,true],
                                    [true,false],
                                ];
                    break;
                case 6 :
                    shapeArray = [
                                    [true,true],
                                    [false,true],
                                ];
                    break;	
                case 7:
                    shapeArray = [
                                    [true,false],
                                    [true,true],
                                ];
                    break;
                case 8 :
                    shapeArray = [
                                    [false,true],
                                    [true,true],
                                ];
                    break;							
                case 9:
                    shapeArray = [
                                    [true,true],
                                    [true,true],
                                ];
                    break;
                case 10 :
                    shapeArray = [
                                    [false,true],
                                    [true,false],
                                ];
                    break;
                case 11 :
                    shapeArray = [
                                    [true,false],
                                    [false,true],
                                ];
                    break;
                case 12 : 
                    shapeArray = [
                                    [true,true,true],
                                ];
                    break;
                case 13 :
                    shapeArray = [
                                    [true],
                                    [true],
                                    [true]
                                ];
                    break;
                case 14:
                    shapeArray = [
                                    [true,true,true],
                                    [true,false,false],
                                ];
                    break;
                case 15 :
                    shapeArray = [
                                    [true,true,true],
                                    [false,true,false],
                                ];
                    break;
                case 16 :
                    shapeArray = [
                                    [true,true,true],
                                    [false,false,true],
                                ];
                    break;
                case 17 :
                    shapeArray = [
                                    [true,true,true],
                                    [true,false,true],
                                ];
                    break;
                case 18:
                    shapeArray = [
                                    [true,false,false],
                                    [true,true,true],
                                ];
                    break;
                case 19:
                    shapeArray = [
                                    [false,true,false],
                                    [true,true,true],
                                ];
                    break;
                case 20:
                    shapeArray = [
                                    [false,false,true],
                                    [true,true,true],
                                ];
                    break;
                case 21 :
                    shapeArray = [
                                    [true,false,true],
                                    [true,true,true],
                                ];
                    break;
                case 22 :
                    shapeArray = [
                                    [true,true,false],
                                    [false,true,true],
                                ];
                    break;
                case 23 :
                    shapeArray = [
                                    [false,true,true],
                                    [true,true,false],
                                ];
                    break;
                case 24:
                    shapeArray = [
                                    [true,true],
                                    [true,false],
                                    [true,false]
                                ];
                    break;
                case 25:
                    shapeArray = [
                                    [true,true],
                                    [true,false],
                                    [true,true]
                                ];
                    break;
                case 26 :
                    shapeArray = [
                                    [true,false],
                                    [true,true],
                                    [true,false]
                                ];
                    break;
                case 27 :
                    shapeArray = [
                                    [true,false],
                                    [true,false],
                                    [true,true]
                                ];
                    break;
                case 28:
                    shapeArray = [
                                    [true,true],
                                    [false,true],
                                    [false,true]
                                ];
                    break;
                case 29:
                    shapeArray = [
                                    [true,true],
                                    [false,true],
                                    [true,true]
                                ];
                    break;
                case 30:
                    shapeArray = [
                                    [false,true],
                                    [true,true],
                                    [false,true]
                                ];
                    break;
                case 31 :
                    shapeArray = [
                                    [false,true],
                                    [false,true],
                                    [true,true]
                                ];
                    break;
                case 32 :
                    shapeArray = [
                                    [false,true],
                                    [true,true],
                                    [true,false]
                                ];
                    break;
                case 33 :
                    shapeArray = [
                                    [true,false],
                                    [true,true],
                                    [false,true]
                                ];
                    break;
                case 34 : 

                    shapeArray = [
                                    [true,true,true],
                                    [true,false,false],
                                    [true,false,false]
                                ];
                    break;
                case 35 : 
                    shapeArray = [
                                    [true,true,true],
                                    [false,false,true],
                                    [false,false,true]
                                ];
                    break;
                case 36 : 
                    shapeArray = [
                                    [true,true,true],
                                    [false,true,false],
                                    [false,true,false]
                                ];
                    break;
                case 37 : 
                    shapeArray = [
                                    [true,false,false],
                                    [true,true,true],
                                    [true,false,false]
                                ];
                    break;
                case 38 : 
                    shapeArray = [
                                    [false,false,true],
                                    [true,true,true],
                                    [false,false,true]
                                ];
                    break;
                case 39 : 
                    shapeArray = [
                                    [false,false,true],
                                    [false,false,true],
                                    [true,true,true]
                                ];
                    break;
                case 40 : 
                    shapeArray = [
                                    [true,false,false],
                                    [true,false,false],
                                    [true,true,true]
                                ];
                    break;
                case 41 : 
                    shapeArray = [
                                    [false,true,false],
                                    [false,true,false],
                                    [true,true,true]
                                ];
                    break;
                case 42 : 
                    shapeArray = [
                                    [true,false,false],
                                    [false,true,false],
                                    [false,false,true]
                                ];
                    break;
                case 43 : 
                    shapeArray = [
                                    [false,false,true],
                                    [false,true,false],
                                    [true,false,false]
                                ];
                    break;
                case 44 : 
                    shapeArray = [
                                    [false,true,false],
                                    [true,true,true],
                                    [false,true,false]
                                ];
                    break;
                case 45 : 
                    shapeArray = [
                                    [true,true,true,true],
                                ];
                    break;
                case 46 : 
                    shapeArray = [
                                    [true],[true],[true],[true],
                                ];
                    break;
                case 47 : 
                    shapeArray = [
                                    [true,true,true,true,true],
                                ];
                    break;
                case 48 : 
                    shapeArray = [
                                    [true],[true],[true],[true],[true],
                                ];
                    break;
                case 49 : 
                    shapeArray = [
                                    [false,true,false],
                                    [true,false,true],
                                    [false,true,false]
                                ];
                    break;
                case 50 : 
                    shapeArray = [
                                    [true,false,true],
                                    [false,false,false],
                                    [true,false,true]
                                ];
                    break;
                default : 
                    shapeArray = [
                                    [true,true,true],
                                    [true,true,true],
                                    [true,true,true]
                                ];
                    break;
            }
            return shapeArray;
        }




   ```

   #### 도형을 그리는 함수 입니다.    
   위의 도형을 그리는 함수 입니다.    
   예상하셨겠지만, canvas 에서 cell 크기 만큼 그려 주고 있습니다.    

   ``` javascript 

        function drawCell(ctx, x, y, w, h) {
            ctx.beginPath();
            ctx.fillRect(x,y,w,h);
            ctx.strokeRect(x,y,w,h);
            ctx.closePath();
        }

        function drawCells(ctx,x,y,size,disArray,strokeStyle,fillStyle,lineWidth) {
            ctx.save();
            if ( strokeStyle ) {
                ctx.strokeStyle = strokeStyle;
            }
            if ( fillStyle ) {
                ctx.fillStyle = fillStyle;
            }

            if ( !lineWidth ) {
                lineWidth = 0.2;
            }

            if ( disArray ) {
                for ( let i = 0, r = disArray.length; i < r; i++ ) {
                    for ( let j = 0, c = disArray[i].length; j < c; j++ ) {
                        if ( disArray[i][j] ) {
                            drawCell(ctx,x+size*j,y+size*i, size,size);
                        }
                    }
                }
            }
            ctx.restore();
        }

   ```

# Game UI 구성.  
   
   이제 UI 는 게임판과, 사용자가 선택할 도형을 random 하게 나오도록 구성하면 
   UI 에서 할 수 있는 것은 대략 완성된 상태가 될 것입니다.    
   물론 점수판등 정말 Game으로 구성하기 위해 필요한 부분은 훨씬 더 많지만, 이번 모듈에서는 
   다루지 않을 주제 이기 때문에 이정도로 UI 구성은 완료할 예정 입니다.   

   ![Game Board](/imgs/shape_02.png) 

   #### Game 판 구성
   Game 에서 사용자가 Drag 해서 도형을 맞출 판을 구성합니다.    
   사용자의 Event 가 진행중일 때 이미 그려진 상태를 유지 할 것이기 때문에  밑에 배경처럼 보이도록 baseCanvas 에서 그림을 그려줍니다.   

   ``` javascript 

        function drawBaseCanvasUI() {
            var canvas = canvasInfos.baseCanvas.canvas;
            var ctx = canvasInfos.baseCanvas.ctx;

            var sx = canvasInfos.startX;
            var sy = canvasInfos.startY;
            var size = canvasInfos.cellSize;
            var cols = canvasInfos.columnSize;
            var rows = canvasInfos.rowSize;

            let idx = 0;

            ctx.clearRect(0,0,canvas.width, canvas.height);

            ctx.save();
            let remakeFlag = true;
            if ( gameMatrixInfos.isCompleted ) {
                remakeFlag = false;
            }

            for( let i = 0; i < rows; i++ ) {

                if ( !gameMatrixInfos.isCompleted  ) {
                    gameMatrixInfos.data.push([]);
                }
                if ( i % 3 == 0 ) { 
                    idx = (idx == 0 ? 1 : 0);
                }
                for ( let j = 0; j < cols; j++ ) {
                    if ( j % 3 == 0 ) {
                        idx = (idx == 0 ? 1 : 0);
                        let strokeStyle = canvasInfos.strokeStyles[idx];
                        let fillStyle = canvasInfos.fillStyles[idx];
                        
                        ctx.strokeStyle = strokeStyle;
                        ctx.fillStyle = fillStyle;

                    }
                    let px = sx+j*size;
                    let py = sy+i*size;
                    if ( gameMatrixInfos.isCompleted  ) {
                        gameMatrixInfos.data[i][j].x = px;
                        gameMatrixInfos.data[i][j].y = py;

                        if ( gameMatrixInfos.data[i][j].isOver ) {
                            ctx.save();
                            ctx.strokeStyle = 	gameMatrixInfos.strokeStyles[2];
                            ctx.fillStyle = gameMatrixInfos.fillStyles[2];
                            drawCell(ctx,px,py,size,size);
                            ctx.restore();
                        } else if ( gameMatrixInfos.data[i][j].isFilled ) {
                            ctx.save();
                            ctx.strokeStyle = 	gameMatrixInfos.strokeStyles[1];
                            ctx.fillStyle = gameMatrixInfos.fillStyles[1];
                            drawCell(ctx,px,py,size,size);
                            ctx.restore();
                        } else {
                            drawCell(ctx,px,py,size,size);
                        }
                    } else {
                        gameMatrixInfos.data[i].push(gameMatrixInfos.makeCellPosition(px,py,size,size,false,false));
                        drawCell(ctx,px,py,size,size);
                    }
                }
            }

            gameMatrixInfos.isCompleted = true;
            ctx.restore();
        }

   ```

   위 소스를 보면 gameMatrixInfos 가 사용되고 있습니다.   
   초기화 하면서 빈 값을 입력하고 있습니다.    나중에 사용자가 drag & drop 으로 데이터를 채워 넣으면 넣은데로 그려줄 예정 입니다. 
   그리는 함수는 그리는 역활만 충실히 수행합니다.    


   ``` javascript 
        const gameMatrixInfos = {
            data : [],
            makeCellPosition : function(x,y,w,h,fill,over) {
                return {
                    x : x,
                    y : y,
                    w : w,
                    h : h,
                    isFilled : fill,
                    isOver : over,
                }
            },
            isCompleted : false,

            strokeStyles : ['#FEFEFE', '#FEFEFE', '#FF00FF'],
            fillStyles : ['#777777','#000080','#77FE77'],
        }

   ```

   #### 사용자가 선택할 도형 Random 구성
   시스템은 사용자가 도형을 선택 할 수 있도록 random 하게 그려 줘야 합니다.    
   그 역활을 구성하는 UI 입니다.    

   ``` javascript 
        function makeCanvasInfoValues() {
            var canvas = canvasInfos.activeCanvas.canvas;
            var ctx = canvasInfos.activeCanvas.ctx;

            ctx.clearRect(0,0,canvas.width, canvas.height);
            ctx.save();
            gameSelectedInfos.data.length = 0;
            for ( let i = 0; i < 4; i++ ) {
                let type = Math.round(Math.random()*49)+2;
                let x = 160*i+canvasInfos.markStartX;
                let y = canvasInfos.markStartY;
                let size = canvasInfos.cellSize;
                let typeArray = getShapeTypeArray(type);
                gameSelectedInfos.data.push ( gameSelectedInfos.makeCellPosition(x,y,size,size,type,typeArray, false,false) );
            }

            var strokeStyle = "white";
            var fillStyle = "#000080";

            for ( let i = 0; i < gameSelectedInfos.data.length; i++ ) {
                drawCells(ctx,gameSelectedInfos.data[i].x,gameSelectedInfos.data[i].y, gameSelectedInfos.data[i].w, gameSelectedInfos.data[i].typeArray, strokeStyle, fillStyle);
            }
            ctx.restore();
        }

   ```

   사용자 Event 를 받아 들여야 하기 때문에 activeCanvas 인 위에 있는 canvas 에 그림을 그려 줍니다.    
   무엇을 선택하였는지 확인하여야 하기 때문에 gameSelectedInfos 자료형을 사용하고 있습니다.     

   ``` javascript 
        const gameSelectedInfos = {
            data : [],

            makeCellPosition : function(x,y,w,h,type,typeArray, moved,matched) {
                return {
                    x : x,
                    y : y,
                    w : w,
                    h : h,
                    type : type,
                    typeArray : typeArray,
                    movedX : -1,
                    movedY : -1,
                    moved : moved,
                    matched : matched,
                    selected : false,
                    moveStartX : -1,
                    moveStartY : -1,
                    isUsed : false,
                }
            },
            isCompleted : function() {
            },

            whichSelected : function(cx,cy) {
                let len = this.data.length;
                if (!len ) {
                    return -1;
                }

                for ( let i = 0; i < len; i++ ) {
                    if ( this.data[i].isUsed ) {
                        continue;
                    }
                    let tArray = this.data[i].typeArray;
                    let tx = this.data[i].x;
                    let ty = this.data[i].y;
                    let tSize = this.data[i].w;

                    if ( cx < tx || ty > cy ) {
                        continue;
                    }

                    for ( let t = 0; t < tArray.length; t++ ) {
                        for ( let u = 0; u < tArray[t].length; u++ ) {
                            if ( tArray[t][u] ) {
                                if ( tx+tSize*u <= cx && tx+tSize*u+tSize >= cx 
                                    && ty+tSize*t <= cy && ty+tSize*(t+1) >= cy ) {
                                        this.data[i].selected = true;
                                        this.data[i].moveStartX = cx;
                                        this.data[i].moveStartY = cy;
                                    return i;
                                }
                            }
                        }
                    }
                }

                return -1;
            }
        }

        const mouseEventInfos = {
            which : -1, 
            startX : -1,
            startY : -1,
            mouseDown : false,
        }



   ``` 

   mouse event 에서 선택한 위치와 항목 등을 알 수 있도록 자료형도 함께 만들어 보았습니다.    

   이제 대략적인 UI 가 구성되었으니, 다음은 사용자의 도형선택, 이동, 그리고 drag&drop 으로 매칭되는 구조를 구성해 보도록 하겠습니다.    
   다음글에서 정리할 예정 입니다.    







   


   



