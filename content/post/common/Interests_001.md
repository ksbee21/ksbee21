---
title: "프로그램 관심사항들 ...."
date: 2023-03-17T23:00:20+09:00
draft: false
tags : ["common","programming"]
topics : []
description : "2023년 3월 개인 관심사 ..."
---
# 잠깐 돌아보기   
   ---
   Naver, Daum, Google, 카카오톡, 유튜브 등 Network 으로 연결되어 있는 세상이 너무나 당연해서, 아주 오래전부터 지속되어 온듯한 생각이 들지만, 
   돌이켜 보면, 몇년 되지 않은 기술 들도 있네요 ...  Internet 이 보급되던 90년대 중반을 시작으로 보아도 30년이 넘지 않은 기술 들입니다.    
   Network 용어중 bps 는 bit per seconds 의 약어 입니다.   모뎀이 보급되던 초기 1200bps, 2400bps 등의 속도가 현재 100메가 bps 1기가 bps 로 
   비교가 되지 않게 성장하였습니다.   초기 모뎀은 1초에 한글같은 unicode 2byte 체계에서는 1200bps 기준 150byte 75자 정도를 표현할 수 있었습니다.    
   Internet 으로 세계가 연결되었다는 기술만 있고, 사용하기 불편한 90년대 중반에서 2000년도 .com Bubble 이 생겨날때 까지 4~5년 정도가 걸린 샘이네요 ...    
   text 기반의 html 이 php,asp 에서 java servlet,jsp, c# asp.net 등의 언어로 개발이 가능해지고, IIS, Apache 등의 Web Server 가 보급되며, Oracle, mssql 
   같은 DBMS 가 인프라 처럼 깔리기 시작하면서, Offline 업무가 Online 으로 전환되기 시작했습니다.    
   지금은 어떨까요? 알파고가 바둑으로 충격을 던져준 후, GTP-3,4로 AI 가 대세가 되고 있습니다.  AR, VR, XR 등 3D 기반의 Viewer 가 지금은 잠시 소강상태인 
   메타버스라는 이름으로 각광받고 있습니다.  Web 개발 방식도 이런 추세에 따라 변화하는 것 같습니다. 물론 지금도 Web 기반 업무시스템은 Oracle 등의 RDBMS 와 
   Web Server Spring MVC Framework 을 기반한 servlet, jsp 등이 많이 사용되기는 하지만, Javascrit 언어의 발전과, Web Spec 의 진화의 결과, 점차 Application 
   같은 Web 이 등장하는것 같습니다.   
   node.js 기반의 개발과, javascript es6 이상의 spec 에서 제공되는 다양한 기능은 그 흐름을 더 가속화 하는 듯이 보입니다.   
   html dom 을 직접 control 하기 보다, 가상의 dom 을 구성후, 변환된 영역을 한번에 rending 하는 방식으로 개발자의 자유도를 빼앗는(?) vue.js 나 react.js 같은 
   spec 이 대세로 자리잡은 지도 조금은 지난듯 합니다.( 물론 아직도 전통적인 방식을 고수하는 곳도 많이 있을것 같습니다. - 그런것이 의미 있기도 하고요 ...  )    
   
   조금 어지러울 정도로 변화하고 있다는 느낌, 엄청난 변화가 오는데 뭔지 실체를 알 수 없는 약간의 불안감 그런 복합적인 생각이 들곤 합니다. 
   길이 헷갈릴때는 잠시 멈춰서서 주변을 확인하고, 지도를 확인하고, 그리고 조금씩 탐색하며 이동하여야 하듯이, 잠시 멈추고, 무엇을 해 보고 싶고,
   어떤걸 할수 있는지, 현재 어디에 있는지 잠시 시간을 내어 정리해 보고자 합니다.     

# Web 에서 진행되는 기술들 ... 
   ### Javascript, html5 
   ECMAScript 6 이전에도 프로그래밍 언어로 인식되기 시작하였지만, 2015년 이후 제안된 Spec 에서 모듈화, 비동기 방식에 대한 처리등 스크립트가 Library 화 할 수 있는 
   많은 방식이 제공되고 있습니다.   html5 Spec 과 함께 Web 이 변화하는 시작점이 된것 같습니다. import, async, () => {} , worker, websocket 등이 표준으로 제공되며, 
   canvas, webgl 등이 제공됩니다.  주관적인 느낌이지만, 현재 Javascript 는 많은 단점이 있지만, 그럼에도 불구하고, 가장 Modern 한 언어중 하나가 아닌가 합니다. 최소한 화면을 구성하기 위해
   다른 소스를 Copy 해서 사용할 언어는 아닌게 분명해 보입니다.     
   Spec 이 필요하진 않을 것 같지만, 혹시 필요할 때 찾아 볼 수 있도록 링크를 걸어 두겠습니다. 
   ##### [https://dev.w3.org/html5/spec-LC/](https://dev.w3.org/html5/spec-LC/) 
   ##### [https://html.spec.whatwg.org/multipage/](https://html.spec.whatwg.org/multipage/)
   ##### [https://262.ecma-international.org/](https://262.ecma-international.org/)
   ##### [https://262.ecma-international.org/6.0/](https://262.ecma-international.org/6.0/)

   실무적인 접근을 위한 이해는 MDN Site 만한 곳이 없는것 같습니다. (한글지원이 됩니다.)
   ##### [https://developer.mozilla.org/ko/docs/Web/HTML](https://developer.mozilla.org/ko/docs/Web/HTML)
   ##### [https://developer.mozilla.org/ko/docs/Web/JavaScript](https://developer.mozilla.org/ko/docs/Web/JavaScript) 

   추가적인 기능중 하나인 소켓입니다. 
   ##### [https://www.rfc-editor.org/rfc/rfc6455](https://www.rfc-editor.org/rfc/rfc6455)


   ### Node.js 
   Chrome V8 Javascript 엔진으로 만든 Javascript 런타임이라고 처음 노드를 소개하는 페이지에서 이야기 하고 있습니다.    
   그 자체로 훌륭한 개발환경을 제공하고 있지만, 실제로 더 많이 사용되는 부분은, Node 를 통한 배포와, 개발환경 구축등에서 더 
   많이 사용되고 있는것 같습니다.   Node로 Application 을 구성할 수 있고, System 의 Backend 구성을 하기도 하지만, 제 경우 
   다른 모듈, 예로 react 같은 환경을 구성할 때 더 많이 사용하였던 것 같습니다. 관심은 있지만, 깊이있게 검토해 보진 못한 영역입니다.    
   Web 개발영역 에서 새로운 개발 trend 를 선도한 기술이라고 보아도 무방할 것 같습니다.   
   물론 node 없이 개발하는 것 역시 당연히 가능합니다.   하지만, 대부분의 open source 가 node 개발환경과 통합되어 있습니다.  
   ##### [https://nodejs.org/ko](https://nodejs.org/ko)   간단한설명 [https://nodejs.org/ko/about](https://nodejs.org/ko/about)

   ### Web UI Framework 
   현재 시점에 대표적인 Framework 는 vue.js 와 react.js 인것 같습니다.    
   일반 html을 동적으로 구성하는 개발 방식으로 web frontend 개발이 불가능 할까요? 결론적으로 그렇지 않습니다.   
   동적으로 데이터를 받아서 특정 dom 에 loop 를 활용하여 100개의 list 를 구성한다고 할 때, appendNode, appendChild 등의 내장 함수를 사용할 수 있습니다.  
   해당 dom 의 innerHTML 로 assign 하는 것과 appendNode 등으로 구성하는 것 중 어느것이 성능에 도움이 될까요?    
   javascript 기능을 모듈화 하여 필요한 영역만 사용하게 하려면 어떤 방식으로 스크립트를 나눠야 할까요? 재사용은 어떻게 할까요? 여러명이 공동으로 작업할 때 유사한 개발의 품질을 어떻게 
   담보할 수 있을까요?   이런 질문에 답이 명확히 떠 오른다면, 굳이 ui framework 을 사용하지 않아도 될 것 같습니다.     
   vue 나 react 는 개발자에게 사용하는 방법을 제시하고, 통일성 있는 개발이 가능하게 합니다.    그러면서도 개발자간 수준 차이에도 불구하고, 일정 수준 이상의 성능을 
   보장하는 방식으로 구성되어 있습니다.  Virtual Dom 을 활용하여 변경 포인트만 갱신하게 하는 부분이 둘 모두 지원하는 기능입니다.    
   거기에 더하여 지원하는 다양한 open source 가 있습니다.  react 는 거의 유사한 방법으로 reactnative 를 통해 mobile 환경 개발도 지원하고 있습니다.    
   학습이 필요한 이유이기도 합니다.  개발에 적용할 기회가 있을때 심도깊게 접근해 봐야할 분야 같습니다.    
   vue, react 외에 웹브라우져간 차이및 version 간 차이를 보정해 주는 javascript compiler 인 babel 이 있습니다. 
   ##### [https://vuejs.org/](https://vuejs.org/) 
   ##### [https://ko.reactjs.org/](https://ko.reactjs.org/)
   ##### [https://babeljs.io/](https://babeljs.io/)

   ### Java 언어 기반의 Web 개발
   기업환경에서 Spring MVC 기반의 개발은 현재도 가장 많이 선호되는 기술이 아닐까 합니다. 
   어떻게 보면 개발 Trend 가 CBD(Component Based Development), SOA(Service Oriented Architecture), Microservice Architecture 등이 주목을 
   받으며 변화해와 현재에 이른것 같습니다.   CBD 가 자기 완결성을 지닌 모듈단위라면, SOA는 Business Logic 의 단위 업무 Service 로 이야기 할 수 있고, Microservice 는 
   독립적인 실행 서비스 단위로, 각 영역의 독립성이 중요하며 단위 완결성이라는 측면에서는 CBD 의 개념과도 유사한 측면이 있는것 같습니다.   
   현재 대세로 떠오른 microservice 개발을 지원하며, 전통적인 개발 방식도 역시 지원하고 있습니다.   사실 Spring 하나만 놓고 보아도 DataAccess, Servlet, Web Socket, 
   Rest, Security, Spring boot 등 학습해야할 내용이 차고도 넘칩니다.   6.0 대 Version 은 java 17 을 기반으로 하고, Servlet Package 가 jakarta 로 변경된 package 를 
   사용하여야 하기 때문에 WebServer가 해당 Package를 사용하는 Server 로 upgrade 되어야 합니다.  대부분의 현장에서 Spring Version 5, 심지어는 4,3까지 확인할 수 있는데 
   java8 에 의존한던 영역이 어떻게 변할지 보는것도 의미 있을것 같습니다.   
   UI Framework 도 그렇지만, Spring 도 단독으로 사용하는 경우는 거의 없습니다.   다른 Library 와 통합하여 사용하는데 무엇을 사용할지 정하는 것도 중요하기 때문에 주의 깊은 
   학습이 필요한 영역 같습니다.    
   ##### [https://spring.io/](https://spring.io/)

# 개인적으로 관심있는 Web 기반 기술들
   개인적으로 실제 업무에서 사용하지는 않지만, 관심이 가는 많은 기술 들이 있습니다. 
   확인이 필요하고, 학습이 필요한 영역인데 시간적인 한계가 있어 더 나가지 못하니, 이런 부분이 혼란의 원인이 된 것인지도 모르겠습니다.
   기술의 발전속도가 정말 빠르다는 생각이 든 영역이기도 합니다.   
   ### 1.tensorFlow.js 
   2017년경 AI 모듈을 테스트 용도로 하나씩 만들어 개인 PC 로 돌려 본적이 있습니다.   간단한 데이터의 학습이 5일 이상 걸리는 것을 확인한 후, 
   개발을 잘못한 점도 크겠지만, 개발 Infra 가 갖춰진 곳에서만 진행해 볼 수 있다는 생각이 들어 더이상 진행해 보지 않았습니다.   
   그런데 Web 에서 AI 를 그것도 tensorflow 를 활용하여 AI 모듈을 구성할 수 있는 방법이 있는 것을 확인하였습니다.    
   WEB 에서 AI 학습이 가능하고, 이미 학습된 data 를 통해 일정 수준의 서비스가 가능한 모듈을 구성할 수 있지 않을까 생각했습니다.  
   local gpu 자원을 webgl 을 통해 접근하고, 기 학습된 내용을 바탕으로 약간의 추가적인 학습이 더해지면 재미있는 site 를 만들 수 도 있겠다는 생각이 들었습니다.   
   tensorflow 를 활용하는 javascript 입니다.    시간이 날때 깊이있게 찾아봐야 할 분야 같습니다. 
   ##### [https://www.tensorflow.org/js?hl=ko](https://www.tensorflow.org/js?hl=ko)

   ### 2. WebRTC 
   Web 관련 프로젝트를 진행해 보신 분들은 http, https 라는 프로토콜과, http1.0, 1.1, 2, 3으로  규약이 진화해 온것을 아실 것입니다.   
   속도나, 낭비를 줄이기 위한 여러 기법과 표준이 제정되어 왔지만, 연결을 오래 지속 시킬 순 있어도 기본적으로는 connectionless 프로토콜 입니다. 
   html 5 에 이르러 WebSocket 이 표준으로 올라 오면서 기본 연결이 아닌 websocket 연결로 지속적인 connection 을 연결하여 사용할 수 있도록 하였지만, 
   기본 구조는 open,  request, response, close 의  구조를 지니고 있습니다.  
   이런 Web 환경에서 Browser 간 pear to pear 통신이 가능하도록 표준화한 프로토콜이 WebRTC 입니다.   단순히 연결만 지원하는 것이 아닌, 
   화상과, 음성 통신이 가능하도록 구성되어 있으니, 어떻게 구현하는 가에 따라 흥미로운 사이트 개발이 가능할 것 같습니다.   
   하나 하나가 다 깊이있게 보아야 하는 영역이라, 언제 진행할 수 있을지 모르겠습니다. ^^ 

   ##### [https://webrtc.org/?hl=ko](https://webrtc.org/?hl=ko)
   ##### [https://developer.mozilla.org/ko/docs/Web/API/WebRTC_API](https://developer.mozilla.org/ko/docs/Web/API/WebRTC_API)

   ### 3. Web 3D
   Web 에서 3D 데이터를 표현한다는 것은 local 자원 사용이 여의치 않은 web 환경에서 상상하기 힘든 기술 이었습니다.   SVG 기반 기술을 바탕으로한  
   d3.js 가 다양한 기능을 제공하며, Chart 등의 영역에서 두각을 나타내고 있을 때 3d chart 기반의 모듈에서 조금씩 사용하던 것이 WebGL 이었는데, 
   얼마 지나지 않아, Canvas 기반의 차트가 많이 소개 되면서 덩달아, 3D 가 Web 에서 조금씩 소개 되는 단계에 이른것 같습니다.    
   WebGL, WebGL2 를 거쳐 이제는 OpenGL 기반이 아닌 WebGPU 까지 부각되는 것을 과정에 있는 것을 보면, 3D 관련 Web 기술인 WebGL은 아직 그 날개를 펼치기도 전에 
   WebGPU 같은 표준으로 대체되는 것은 아닌지 모르겠습니다.    OpenGL이 2015년에서 성장을 멈추었듯, 다른 3D 기반을 포용하는 WebGPU 로 변화하는 듯한 느낌이 들기도 합니다.  
   WebGL 의 Shader Program 과 수학적인 원리를 학습하는것이, WebGPU 환경에서 도움이 안되는 것은 아니겠지만, 어차피 변화의 과정에 있다면, 아직 지원하는 브라우져도 제대로 
   없는 한계가 있지만, 미래 기술로는 WebGPU 를 중점적으로 살펴보는게 더 좋을 듯도 합니다.    
   WebGL 을 학습하면서, Javascript Library 를 조금씩 구성해 볼까 하는 생각을 하다가도, 기술적인 변화의 시기에 있다는 느낌이 들어 멈칫 거리게 되는것 같습니다.    
   WebGPU를 찾아 보다 보면, rust 언어가 간간히 보이기도 합니다.   c, c++ 을 대체할 수 있다고 이야기 하는 언어라 흥미도 있지만, webgpu 에 사용할 수 있는 언어라는 
   것으로도 의미가 있어 보입니다.    
   할수 있는것은 별로 없는데 해야할 것만 정말 많이 보이네요 ...  IT 분야에 개발자로 지낸다는 것은 이런일의 연속인것 같습니다. ^^^  
   (물론 저같은 사람이 그렇고, 정말 잘 하시는 분들은 그렇지 않겠지요 .... )  
   그러다 보니, Web 에서 3D 개발은 학습차원에서야 기초부터 보는게 중요하겠지만, 실용적인 개발 차원에서는 잘 짜여진 라이브러리를 사용법을 잘익혀 놓는 것은 어떨까 
   하는 생각도 들게 됩니다.   해당 Library 를 분석하다 보면, 필요한 부분을 직접 구성할 때도 구조를 잡거나 하는 일에 도움이 되고, 직접적으로는 기본적인 구현은 
   Library 를 활용하는게 더 편하기 때문입니다.   
   현재 까지 나온 라이브러리중 대표적인 것이 three.js  와 babylon.js 인데 babylon 팀이 WebGPU 포럼에서도 활동하는 것을 볼 때 개인적으로를 babylon.js 가 지금 
   시점에서는 더 의미 있는게 아닐까 합니다. 
   아래의 예제는 ms 에서 babylon.js 를 사용하여 피아노 연주를 구성하는 Sample site 입니다.  3D 와 Click Event 로 피아노를 연주할 수 있는 예제 입니다.  
   ##### [https://learn.microsoft.com/ko-kr/windows/mixed-reality/develop/javascript/tutorials/babylonjs-webxr-piano/introduction-01](https://learn.microsoft.com/ko-kr/windows/mixed-reality/develop/javascript/tutorials/babylonjs-webxr-piano/introduction-01)

   Web 에서 3D 만 하더라도 이렇게 보아야 할 내용이 많은것 같습니다.   

   ##### webgl spec [https://www.khronos.org/webgl/](https://www.khronos.org/webgl/)
   ##### Babylong.js [https://www.babylonjs.com/](https://www.babylonjs.com/) 
   ##### WebGPU Spec [https://www.w3.org/TR/webgpu/](https://www.w3.org/TR/webgpu/)
   ##### WebGPU Chrome [https://developer.chrome.com/docs/web-platform/webgpu/](https://developer.chrome.com/docs/web-platform/webgpu/)
   ##### WebGPU -rust [https://sotrh.github.io/learn-wgpu/#what-is-wgpu](https://sotrh.github.io/learn-wgpu/#what-is-wgpu)



웹 영역만 간단히 살펴 보아도 글이 너무 길어 지는 것 같습니다.   
시간을 두고 조금씩 살펴 봐야 할 것 같습니다. ^^         