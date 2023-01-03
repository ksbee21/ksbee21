---
title: "Web Canvas 기반 도형 맞추기 Game 02"
date: 2023-01-03T21:49:38+09:00
draft: false
tags : ["Web","Canvas","Language","Javascript","Game"]
topics : []
description : "Canvas 테트리스 도형 Event"
---

# Canvas Game Event 구성 

   도형 구성과 배치를 위한 UI 가 구성되었으면, 이제 사용자가 선택후 Drag & Drop 으로 위치를 맞추고, 
   그 결과를 담아 내기 위한 Event 처리가 필요합니다.    
   요즘은 PC 가 아닌, 핸드폰에서도 Web 접근이 가능하기 때문에 Phone Device 를 위한 Event 처리를 위해서는 
   touchstart 등의 이벤트도 정리하여야 하지만, 이 글에서는 다루지 않겠습니다.    

   [다음주소](/html/WebCanvas/Web_Canvas_002_01.html) 에서 확인해 보실 수 있습니다.    

   #### Initialize 시점에 Event 구성 
   초기화 과정에 Canvas 에 Mouse Event 를 할당 하는 과정 입니다. 
   mousedown 에서 event 시작과 위치등을 기록합니다. 
   mousemove 에서 사용자에게 놓일 위치, 성공했을 때 삭제될 모형 등을 미리 보여주는 Event 를 처리 합니다. 
   mousedown 에서 실제로 삭제할 영역, 그리고 점수 등을 계산 한후 전체를 다시 그리는 함수를 호출합니다. 

   ``` javascript 
           canvasInfos.activeCanvas.canvas.addEventListener("mousedown", function(e) {   
            ...
        });
        canvasInfos.activeCanvas.canvas.addEventListener("mousemove", function(e) {
            ...
        });
        canvasInfos.activeCanvas.canvas.addEventListener("mouseup", function(e) {            
            ...
        });
   
   ```

   처음 초기화 단계에서 호출 되는 함수 입니다.     
   자료형은 앞 Post 에서 간단하게 언급한  mouseEventInfos,  gameSelectedInfos 가 event 진행중에 사용되고, 
   gameMatrixInfos 가 최종 화면을 갱신할 때 사용하게 됩니다.   

   #### 아래는 초기화 과정 소스 입니다.  

   ``` javascript 

        function initCanvas() {
            var gap = 10;
            var w = window.innerWidth;
            var h = window.innerHeight;

            initBaseSizeUI();

            var disArray = [[true,true],[true,false]];
            var size = 15;

            var strokeStyle = "white";
            var fillStyle = "#000080";
            var diff = 100;

            var ctx = canvasInfos.activeCanvas.ctx;
            ctx.clearRect(0,0,canvasInfos.w, canvasInfos.h);

            canvasInfos.activeCanvas.canvas.addEventListener("mousedown", function(e) {
                let cx = e.x;
                let cy = e.y;

                let idx = gameSelectedInfos.whichSelected(cx,cy);
                if ( idx >= 0 ) {
                    canvasInfos.activeCanvas.canvas.style.cursor = "move";
                    mouseEventInfos.which = idx;
                    mouseEventInfos.mouseDown = true;
                }
            }, false);

            canvasInfos.activeCanvas.canvas.addEventListener("mousemove", function(e) {
                if ( !mouseEventInfos.mouseDown ) {
                    return;
                }

                let cx = e.x;
                let cy = e.y;

                gameSelectedInfos.data[mouseEventInfos.which].movedX = cx;
                gameSelectedInfos.data[mouseEventInfos.which].movedY = cy;

                drawMovingSelectCells();
                resetMouseOverFlag();

                let sx = gameSelectedInfos.data[mouseEventInfos.which].moveStartX;
                let sy = gameSelectedInfos.data[mouseEventInfos.which].moveStartY;
                let ox = gameSelectedInfos.data[mouseEventInfos.which].x;
                let oy = gameSelectedInfos.data[mouseEventInfos.which].y;

                let flag = isInRangeShape(cx-(sx-ox),cy-(sy-oy), gameMatrixInfos.data, gameSelectedInfos.data[mouseEventInfos.which].typeArray, canvasInfos.cellSize, true );

                if ( flag ) {
                    checkDataOverValues();
                    drawBaseCanvasUI();
                    resetMouseOverFlag();
                }


            }, false);

            canvasInfos.activeCanvas.canvas.addEventListener("mouseup", function(e) {
                if ( !mouseEventInfos.mouseDown ) {
                    return;
                }


                gameSelectedInfos.data[mouseEventInfos.which].selected = false;

                let cx = e.x;
                let cy = e.y;

                gameSelectedInfos.data[mouseEventInfos.which].movedX = cx;
                gameSelectedInfos.data[mouseEventInfos.which].movedY = cy;


                let sx = gameSelectedInfos.data[mouseEventInfos.which].moveStartX;
                let sy = gameSelectedInfos.data[mouseEventInfos.which].moveStartY;
                let ox = gameSelectedInfos.data[mouseEventInfos.which].x;
                let oy = gameSelectedInfos.data[mouseEventInfos.which].y;

                let flag = isInRangeShape(cx-(sx-ox),cy-(sy-oy), gameMatrixInfos.data, gameSelectedInfos.data[mouseEventInfos.which].typeArray, canvasInfos.cellSize );

                if ( flag ) {
                    gameSelectedInfos.data[mouseEventInfos.which].isUsed = true;
                }
                drawMovingSelectCells();

                mouseEventInfos.mouseDown = false;
                mouseEventInfos.which = -1;

                drawBaseCanvasUI();
                canvasInfos.activeCanvas.canvas.style.cursor = "default";
                setTimeout(checkDataPointValues,500);

            }, false);
        }
   ```

   #### Mouse Move, Up 진행 과정 
   1. User 가 선택한 도형을 그립니다.  
   > drawMovingSelectCells 함수는 사용자가 이동한 위치를 기반으로 선택하 도형을 그리는 역활을 수행합니다.   

   2. 사용자 편의를 위해 Drop 이 가능한 위치를 표시합니다. 
   > isInRangeShape 함수에서 현재 도형의 위치를 기반으로 어디에 어떻게 적용되는지를 그려 줍니다.    
     대략 픽셀의 반이 걸리쳐 그 안의 내용을 기준으로 출력합니다.  
   
   3. 현재 위치 기준 점수를 올릴 수 있는 영역인지 확인합니다. 
   > checkDataOverValues 함수는 선택한 도형과, 위치를 기반으로 점수를 가져올 수 있는 영역 인지 판정합니다.   

   4. 사용자 선택이 완료 되면 약간의 시간을 주고 최종 결과를 확인합니다. 
   > checkDataPointValues 함수에서 전체를 재 구성하는 역활을 수행 합니다.   이 때 Game 점수를 누증 시킬 수 있습니다 .  



   #### 코드 입니다. 

   ``` javascript 

        function drawMovingSelectCells() {
            var canvas = canvasInfos.activeCanvas.canvas;
            var ctx = canvasInfos.activeCanvas.ctx;

            ctx.clearRect(0,0,canvas.width, canvas.height);
            ctx.save();

            var strokeStyle = "white";
            var fillStyle = "#000080";

            for ( let i = 0; i < gameSelectedInfos.data.length; i++ ) {
                if ( gameSelectedInfos.data[i].isUsed ) {
                    continue;
                }
                if ( gameSelectedInfos.data[i].selected ) {
                    let gapX = gameSelectedInfos.data[i].moveStartX - gameSelectedInfos.data[i].movedX;
                    let gapY = gameSelectedInfos.data[i].moveStartY - gameSelectedInfos.data[i].movedY;
                    let msx = gameSelectedInfos.data[i].x-gapX;
                    let msy = gameSelectedInfos.data[i].y-gapY;
                    drawCells(ctx,msx,msy, gameSelectedInfos.data[i].w, gameSelectedInfos.data[i].typeArray, strokeStyle, fillStyle);
                } else {
                    drawCells(ctx,gameSelectedInfos.data[i].x,gameSelectedInfos.data[i].y, gameSelectedInfos.data[i].w, gameSelectedInfos.data[i].typeArray, strokeStyle, fillStyle);
                }
            }
            ctx.restore();
        }

   ```

   ``` javascript 

        function isInRangeShape(cx,cy, dataArray, shape, size, isOver ) {

            let rows = dataArray.length;
            let cols = -1;
            if ( rows > 0 )	{
                cols = dataArray[0].length;
            }
            let gap = size/2;
            for ( let i = 0; i < rows; i++ ) {
                for ( let j = 0; j < cols; j++ ) {
                    if ( dataArray[i][j].x-gap < cx && dataArray[i][j].x + gap >= cx 
                        && dataArray[i][j].y-gap < cy && dataArray[i][j].y + gap >= cy ) {
                    
                        for ( let t = 0; t < shape.length; t++ ) {
                            for ( let u = 0; u < shape[t].length; u++ ) {
                                if ( t+i >= rows ) {
                                    return false;
                                }
                                if ( u+j >= cols ) {
                                    return false;
                                }
                                if ( shape[t][u] && dataArray[i+t][j+u].isFilled ) { 
                                    return false;
                                }
                            }
                        }
                        for ( let t = 0; t < shape.length; t++ ) {
                            for ( let u = 0; u < shape[t].length; u++ ) {
                                if ( shape[t][u] ) {
                                    if ( isOver ) {
                                        dataArray[i+t][j+u].isOver = true;
                                    } else {
                                        dataArray[i+t][j+u].isFilled = true;
                                    }
                                }
                            }
                        }
                        return true;
                    }
                }
            }
            return false;
        }

        function checkDataPointValues() {
            var points = 0;
            var repeat = 1;


            let rows = gameMatrixInfos.data.length;
            let cols = rows > 0 ? gameMatrixInfos.data[0].length : 0;

            var verticalArray	= [];
            var horizontalArray	= [];

            for ( let i = 0; i < rows; i++ ) {
                let flag = true;
                for ( let j = 0; j < cols; j++ ) {
                    if ( !gameMatrixInfos.data[i][j].isFilled ) {
                        flag = false;
                        break;
                    }
                }
                if ( flag ) {
                    verticalArray.push(i);
                }
            }

            for ( let i = 0; i < cols; i++ ) {
                let flag = true;
                for ( let j = 0; j < rows; j++ ) {
                    if ( !gameMatrixInfos.data[j][i].isFilled ) {
                        flag = false;
                        break;
                    }
                }
                if ( flag ) {
                    horizontalArray.push(i);
                }
            }

            for ( let i = 0; i < rows; i += 3 ) {
                for ( let j = 0; j < cols; j += 3 ) {
                    let flag = true;
                    for ( let t = 0; t < 3; t++ ) {
                        for ( let u = 0; u < 3; u++ ) {
                            if ( !gameMatrixInfos.data[i+t][j+u].isFilled ) {
                                flag = false;
                                break;
                            }
                        }
                        if ( !flag ) 
                            break;
                    }
                    if ( flag ) {
                        for ( let t = 0; t < 3; t++ ) {
                            for ( let u = 0; u < 3; u++ ) {
                                gameMatrixInfos.data[i+t][j+u].isFilled = false;
                            }
                        }

                        points += 9*repeat;
                        ++repeat;
                    }
                }
            }

            for ( let i = 0; i < verticalArray.length; i++ ) {
                for (let j = 0; j < cols; j++ ) {
                    gameMatrixInfos.data[verticalArray[i]][j].isFilled = false;
                }

                points += 12*repeat;
                ++repeat;

            }

            for ( let i = 0; i < horizontalArray.length; i++ ) {
                for (let j = 0; j < rows; j++ ) {
                    gameMatrixInfos.data[j][horizontalArray[i]].isFilled = false;
                }

                points += 12*repeat;
                ++repeat;

            }
            
            let isAll = true;
            for ( let i = 0; i < gameSelectedInfos.data.length; i++ ) {
                if ( !gameSelectedInfos.data[i].isUsed ) {
                    isAll = false;
                    break;
                }
            }

            if ( isAll ) {
                makeCanvasInfoValues();
            }
            drawBaseCanvasUI();
        }


        function checkDataOverValues() {

            let rows = gameMatrixInfos.data.length;
            let cols = rows > 0 ? gameMatrixInfos.data[0].length : 0;

            var verticalArray	= [];
            var horizontalArray	= [];

            for ( let i = 0; i < rows; i++ ) {
                let flag = true;
                for ( let j = 0; j < cols; j++ ) {
                    if ( !(gameMatrixInfos.data[i][j].isFilled || gameMatrixInfos.data[i][j].isOver) ) {
                        flag = false;
                        break;
                    }
                }
                if ( flag ) {
                    verticalArray.push(i);
                }
            }

            for ( let i = 0; i < cols; i++ ) {
                let flag = true;
                for ( let j = 0; j < rows; j++ ) {
                    if ( !(gameMatrixInfos.data[j][i].isFilled || gameMatrixInfos.data[j][i].isOver) ) {
                        flag = false;
                        break;
                    }
                }
                if ( flag ) {
                    horizontalArray.push(i);
                }
            }

            for ( let i = 0; i < rows; i += 3 ) {
                for ( let j = 0; j < cols; j += 3 ) {
                    let flag = true;
                    for ( let t = 0; t < 3; t++ ) {
                        for ( let u = 0; u < 3; u++ ) {
                            if ( !(gameMatrixInfos.data[i+t][j+u].isFilled || gameMatrixInfos.data[i+t][j+u].isOver) ) {
                                flag = false;
                                break;
                            }
                        }
                        if ( !flag ) 
                            break;
                    }
                    if ( flag ) {
                        for ( let t = 0; t < 3; t++ ) {
                            for ( let u = 0; u < 3; u++ ) {
                                gameMatrixInfos.data[i+t][j+u].isOver = true;
                            }
                        }
                    }
                }
            }

            for ( let i = 0; i < verticalArray.length; i++ ) {
                for (let j = 0; j < cols; j++ ) {
                    gameMatrixInfos.data[verticalArray[i]][j].isOver = true;
                }
            }

            for ( let i = 0; i < horizontalArray.length; i++ ) {
                for (let j = 0; j < rows; j++ ) {
                    gameMatrixInfos.data[j][horizontalArray[i]].isOver = true;
                }
            }
            
            drawBaseCanvasUI();
        }


        function resetMouseOverFlag() {
            let rows = gameMatrixInfos.data.length;
            let cols = gameMatrixInfos.data[0].length;
            for ( let i = 0; i < rows; i++ ) {
                for ( let j = 0; j < cols; j++ ) {
                    gameMatrixInfos.data[i][j].isOver = false;
                }
            }
        }


        function drawMovingSelectCells() {
            var canvas = canvasInfos.activeCanvas.canvas;
            var ctx = canvasInfos.activeCanvas.ctx;

            ctx.clearRect(0,0,canvas.width, canvas.height);
            ctx.save();

            var strokeStyle = "white";
            var fillStyle = "#000080";

            for ( let i = 0; i < gameSelectedInfos.data.length; i++ ) {
                if ( gameSelectedInfos.data[i].isUsed ) {
                    continue;
                }
                if ( gameSelectedInfos.data[i].selected ) {
                    let gapX = gameSelectedInfos.data[i].moveStartX - gameSelectedInfos.data[i].movedX;
                    let gapY = gameSelectedInfos.data[i].moveStartY - gameSelectedInfos.data[i].movedY;
                    let msx = gameSelectedInfos.data[i].x-gapX;
                    let msy = gameSelectedInfos.data[i].y-gapY;
                    drawCells(ctx,msx,msy, gameSelectedInfos.data[i].w, gameSelectedInfos.data[i].typeArray, strokeStyle, fillStyle);

                } else {

                    drawCells(ctx,gameSelectedInfos.data[i].x,gameSelectedInfos.data[i].y, gameSelectedInfos.data[i].w, gameSelectedInfos.data[i].typeArray, strokeStyle, fillStyle);
                }
            }


            ctx.restore();
        }


   ``` 

   #### 정리 
   1. Html Mouse Event 로 down, move, up 을 사용 하였습니다. 
   > down 에서 사용자 선택 초기 위치 등의 정보를 담고 있습니다.    
   > move 에서 사용자 움직임에 따른 Game Hint 가 구성됩니다.    
   > up 에서 점수 획득에 관련한 부분과, 최종적인 UI 구성이 완료 됩니다. 

   2. touchstart 등의 mobile 환경은 고려 하지 않았습니다. 
   > UI 부터 더 적게 구성하고 진행하여야 해서 적용하지 않습니다.    

   3. 한계 
   > 기본적인 로직만을 보기 위한 부분이라서 많은 부분은 생략 하였습니다.    
   event 를 mouse position 으로 할 때는 사이즈 변경등에 더 고려할 내용이 많으나, 일단 모두 무시 하였습니다.   
   > Mobile 환경은 고려 하지 않았습니다.   ^^





