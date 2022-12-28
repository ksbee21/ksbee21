---
title: "Canvas 를 이용한 삼각함수 이해 01"
date: 2022-12-27T23:10:59+09:00
draft: false
tags : ["Web","Canvas","Javascript","Math","삼각함수"]
topics : []
description : "수식 없이 이해하기"
---

# Html5 Canvas 
   
   Web 에서 일반적인 언어의 Graphics 를 사용하는 것과 유사한 작업을 가능하게 해주는 모듈이 추가된지도 꽤 오랜 시간이 흘렀습니다.    
   돌이켜 보면 SVG 라는 도형을 그리는 표준이 d3.js 라는 뛰어난 라이브러리로, Chart 등에서 맹위를 떨치던 시간이 그리 오래 전이 
   아닌데 벌써, 트랜드의 한축이 넘어가는 느낌이 듭니다.    

   현재 많은 차트 라이브러리에서 SVG 에서 점차 CANVAS 로 옮겨가는 추세 입니다.    
   svg는 web 에서 느린 자원이 아닙니다.  게다가 가 도형별 shape 별 event 처리가 canvas 에 비해 훨씬 수월 합니다.    
   canvas 는 일반적인 언어의 Graphics 객체를 직접 다루는 것과 유사하게 접근하여야 하기 때문에 모든 형태를 직접 그려 주고, 그에 따른 
   Event 처리를 하나 하나 처리해 주어야 합니다.    
   이런 불편함에도, 빠른 속도와, 상상할 수 있는 대부분의 작업이 Web 에서 가능하다는 장점으로, Web Chart 등의 Application 과 유사한 
   영역에서 조금씩 자리를 넓혀가는 것 같습니다. ( 물론 WebGL 도 포함 해서요 .... )

   ### 간단한 Canvas 예제 
   아래의 그림은 canvas 를 활용하여 그려본 삼각형입니다.    
   Canvas 에 대한 기초적인 정리가 가능하고, 삼각함수에 대한 정리도 가능할 것 같아서 간단히 작성하여 보았습니다.    
``` javascript 

        function makeCanvas(width, height) {
            const canvas = document.createElement("canvas");
            canvas.width = width;
            canvas.height = height;
            canvas.style.width = width + "px";
            canvas.style.height = height + "px";
            return canvas;
        }

   ```

   ``` javascript

        function makeTriangleUI( width, height) {
            const gaps = 80;
            const tSize = width - gaps*2;
            const lineLen = Math.round(Math.sqrt(tSize*tSize*2));
            const degrees = 45;
            const radian = (Math.PI*degrees/180);
            const points = [];
            const lineNames = [{name:'a',x:0,y:0},{name:'b',x:0,y:0},{name:'c',x:0,y:0}];
            let tx = 0;
            let ty = 0;
            points.push([tx,ty]);
            lineNames[0].x = Math.floor(tSize/2);
            lineNames[0].y = 20;
            tx = Math.cos(radian)*lineLen;
            ty = 0;
            points.push([tx,ty]);

            tx = Math.cos(radian)*lineLen;
            ty = Math.sin(radian)*lineLen;

            lineNames[1].x = tSize + 15;
            lineNames[1].y = -Math.round(ty/2);

            lineNames[2].x = Math.floor(Math.cos(radian)*lineLen/2) - 30;
            lineNames[2].y = -Math.round(ty/2);

            points.push([tx,-ty]);

            const canvas = makeCanvas(width, height);
            const ctx = canvas.getContext("2d");
            ctx.clearRect(0,0,width,height);
            ctx.save();

            ctx.translate(gaps,tSize+gaps); //new 0,0 base

            ctx.beginPath();

            ctx.fillStyle = "#EEEE22";
            ctx.strokeStyle = "#000080";
            ctx.lineWidth = 2;
            ctx.moveTo(0,0);
            ctx.arc(0,0, 50, 0, -radian, true);
            ctx.lineTo(0,0);
            ctx.closePath();
            ctx.fill();
            ctx.stroke();

            
            ctx.beginPath();
            ctx.lineWidth = 5;
            ctx.lineJoin = "round";
            ctx.strokeStyle = "#000080";
            ctx.font = "bold 18px consolas";
            ctx.textAlign = "center";
            ctx.textBaseline = "middle";

            //  theta 
            ctx.fillStyle = "#000080";
            ctx.fillText("θ", Math.cos(radian/2)*60, -Math.sin(radian/2)*60 );

            const sizeRect = 30;
            ctx.strokeRect(points[1][0]-sizeRect,points[1][1]-sizeRect,sizeRect,sizeRect);
            ctx.stroke();
            
            for ( let i = 0; i < 3; i++ ) {
                if ( i == 0 ) {
                    ctx.moveTo(points[0][0], points[0][1]);
                } else {
                    ctx.lineTo(points[i][0], points[i][1]);
                }
                ctx.save();
                ctx.fillStyle = "#EE22FE";
                ctx.fillText(lineNames[i].name, lineNames[i].x, lineNames[i].y );
                ctx.restore();
            }
            ctx.closePath();
            ctx.stroke();
            ctx.restore();
            document.body.appendChild(canvas);
            console.log(canvas.toDataURL());
        }
   ``` 

   Canvas 를 그리는 부분은 잠시 놓아 두고 보면, 그림을 그릴 때 사용한 부분이 degree 에서 radian 으로 변환하여 사용한 것고, Math.sin, Math.cos 함수를 그리는 용도로 사용하고 있습니다.    
   javascript 에서는 sine, cosine, tangent 와 그 역함수 등을 api 로 제공하고 있습니다.    물론 대부분의 언어에서 언급한 함수는 제공하고 있습니다.    
   

   아래는 그 결과 입니다.  이 예시를 보면서 간단하게 삼각함수를 정리해 보고자 합니다.    

   ![삼각형](data:image/png;base64,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)

   
   


# 삼각함수 
   
   가능한 이런저런 수식 없이 말로 풀어서 정리해 보려고 합니다.    
   몇가지 기본적인 공식은 함수를 이해하기 위해 필요하기 때문에 먼저 언급하고 진행하겠습니다.    
   제가 아는 중학교 수학 수준에서 정리해 보려고 합니다.    

   ### 선행 공식   
   중학교때 배우는 피타고라스 정의 입니다.     
   다른 하나는 삼각형의 비율, 삼각비를 지칭하는 명칭 입니다. (sine, cosine, tangent )   
   
   
   $$
   직각삼각형 \quad c = 빗변, a = 밑변, b = 높이, h = 높이, ( 직각삼각형의 경우 b = h ),  \theta = 각도(radian) \\\
   피타고라스 공식 : c=\sqrt{a^2+b^2}, \quad c^2 = a^2 + b^2 \\\
   sin\theta = \frac{b}{c} , \quad cos\theta = \frac{a}{c} , \quad tan\theta = \frac {b}{a} \\\
   삼각형의 면적 = a \times h \times \frac{1}{2} = a \times b \times \frac{1}{2} ( 직각삼각형 ) \\\
   1 (radian) = 52.2957... (도) \quad \pi(radian) = 180 (도) \quad 1 (radian) = \frac{1 \times 180} {\pi} \\\
   $$ 
   

   sin, cos, tan 이 길이를 기준으로 한 비율이라는 것, 삼각형의 면적구하기 공식, 피타고라스 정의 정도가 앞으로 나올 이야기를 이해하는 
   기준이 될 것 같습니다.    

   ### 생각정리 
   1. 직각삼각형의 각도가 0 에서 증가하여 90 도까지 변경한다고 가정해 보겠습니다.    
   2. theta 가 0도 이면, 가로와 빗변이 같습니다.   삼각형이 아닌 직선이 되겠네요 .... 이때 a = c 이니, a/c = 1 이 됩니다. cos 의 값이 1로 가장 큽니다. sin 은 b = 0 이니 b/c = 0 입니다. 
   3. theta 가 90 도면 위와 반대로 b = c 이고 a = 0 입니다. cos 의 값은 0이고, sin 은 1 입니다. 
   4. 위의 내용을 참조로 0 ~ 90 도 까지 빗변(c) 를 각 sine 과 cosine 의 값에 곱해 주면 어떤 현상이 나타날까요?  앞서의 정의에서 삼각함수를 비율로 표현했습니다.  모두 분모가 빗변(c) 이기 때문에 분모가 약분되어 없어지면 sine 높이와 cosine 가로 의 위치를 알 수 있습니다. 
   5. 좌표값을 x, y 라고 할 때 빗변(c) - 원의 반지름 을 곱해주면 , sin theta 는 y, cos theta 는 x 위치가 됩니다. 
   6. 0도 일때 sine 이 0, cosine 이 1 인것을 상기하면, 각도가 증가할 때 x, y 의 위치는 오른쪽 수평선에서 위로 왼쪽으로 다시 아래로 도는 시계 반대 방향을 생각해 볼 수 있습니다.    
   7. 여기까지의 내용을 정리하면, sine 은 y 좌표와 연결되고, cosine 은 x 좌표와 매칭되어 오른쪽 수평선에서 시계 반대방향으로 원을 그릴 수 있다는 것을 예상할 수 있습니다.    
   8. 앞서 canvas 로 삼각형을 그린 소스에서 살펴 보아도 Math.sin(theta) = y 값과, Math.cos(theta) = x 값과 매핑되는 것을 확인 할 수 있습니다. 
   
   canvas 의 좌표계가 왼쪽 상단이 0, 0 이고 x는 오른쪽으로 증가하는 정상적인 흐름이지만, y 는 아래로 증가하는 구조로 되어 있습니다. 그래서 y 좌표는 뒤집어서 처리하였습니다.    
   이미 많이 접해 보신 분들은 너무 당연한 부분이겠지만,  기본적인 내용 부터 정리하고자 간단히 작성해 보았습니다. 
   위의 코드에서 0 ~ 360 도를 radian 으로 변경해서 cos, sin 을 활용하면 , x, y 좌표를 얻고 이를 연결하면 원이 됩니다.
   다음에 시간이 될 때 Canvas 를 활용하여 원, 회전 등을 정리해 보려 합니다. ^^   

