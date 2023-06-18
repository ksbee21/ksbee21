---
title: "Python 언어 비교 정리 02"
date: 2023-06-15T19:25:10+09:00
draft: false
tags : ["Language","Python", "Javascript","Java"]
topics : []
description : "Python 언어 - Http Request - 비교"
---

# Python - HTTP, JSON 모듈 준비
프로그래밍 언어를 공부할 때는 해당 언어를 사용하기 위해서 문법을 학습하여야 합니다.   
처음 프로그래밍 언어를 공부하는 경우에는, 시간이 걸리더라도, 가급적 기초 문법책을 완독하는 것이 필요한 것일 수도 있습니다.   
그렇지만, 다른 언어를 어느 정도 알고 있는 경우에는 모듈별로 접근해 보는 학습 방법도 나름 유용한 경우도 있는것 같습니다.   
지금 정리하는 Python 은 그때 그때 문법을 찾아 보면서, 모듈 단위의 학습 방법을 찾아 정리해 보고자 합니다.    

처음 시작하였던 최소제곱법을 이야기 한것은, 공학용 프로그램을 할 때 사용하는 Python 의 특징과 C 등의 언어로 구성된 Python 모듈을 소개하는 것이 었다면, 
이 글에서는 Http 관련한 모듈을 타 언어와 비교를 통해 확인해 보고자 합니다.    

아래는 Python 학습을 위해 무료로 접근할 수 있는 Site 들입니다. 특히 한글로 책자처럼 제공하는 사이트를 구성하신 대단한 분들이 계시네요 ...
#### Python 한글 공식 문서 - [https://docs.python.org/ko/3/](https://docs.python.org/ko/3/)
#### 한글로 된 Python 사이트 - [https://wikidocs.net/](https://wikidocs.net/) [https://wikidocs.net/book/1](https://wikidocs.net/book/1)

### HTTP, JSON
Web Programming 의 전송기반은 http protocol 입니다.  Server 가 있고, Client 인 크롬 같은 Browser 가 있습니다.    
Node 의 Express 나 Tomcat, IIS 등은 모두 Web Server 역활을 수행하고, IE, Chrome 등은 Client 역활을 담당합니다.    
데이터의 전송은 html 로 구성된 text contents 이지만, 현재 우리가 접하는 대부분의 Web 은 html 을 동적으로 재 구성하여 표현하고 있고, 
이때 사용하는 대표적인 데이터 Format 이 JSON 입니다.  key, value 의 구성이기 때문에 데이터를 추춯하기 편하고, size 를 최소한으로 유지할 수 있기 때문입니다.   
http 는 1.0, 1.1 의 open request, response close 의 기본 포맷에서 한번전송에 여러 형식을 담을 수 있는 http 2 version 으로 발전하였고, 
2까지 사용하던 tcp 에서 udp 와 ip 변경에도 그 원천을 파악할 수 있는 id 기반의 http3 까지 표준이 확장되고 있습니다.
http3은 영상, 소리 등의 실시간 전송을 위한 포맷을 지원하기 위한 프로토콜이니, http2 까지가 일반적으로 사용할 수 있는 표준 프로토콜이라고 보아도 좋을것 같습니다.    
우리가 https://www.google.com 이라는 사이트를 browser 에서 요청하면, chrome 같은 브라우져는 dns를 거쳐 구글이 구성해 놓은 Server 한곳에 페이지 정보를 요청하고 있는 것입니다. 
이때 일반적으로 전송을 요청하는 메소드가 GET 이며, 데이터의 저장등을 요청하는 방식으로 POST Method를 사용합니다. ( 그외 PUT, DELETE 등이 있으나 많이 사용하지 않습니다.)       
GET 과 POST 의 형식적인 차이는 URL 에 parameter 가 &key=value 형식으로 전송하는지, body 영역에 &key=value 형식으로 전송하는지에 대한 차이가 있습니다. ( 아래의 참조 사이트에서 자세한 내용을 확인할 수 있습니다. )       
#### HTTP 참조 [https://developer.mozilla.org/ko/docs/Web/HTTP/Overview](https://developer.mozilla.org/ko/docs/Web/HTTP/Overview)
#### JSON 참조 [https://developer.mozilla.org/ko/docs/Learn/JavaScript/Objects/JSON](https://developer.mozilla.org/ko/docs/Learn/JavaScript/Objects/JSON)


# Python 기준 언어별 비교
Python 에서 Http 관련한 내용을 찾아보면 대표적으로 검색되는 것인 requests 라는 모듈입니다.    
내장된 urllib 이라는 모듈로도 개발이 가능하지만, 많이 사용하는 request 를 기준으로 확인해 보겠습니다.  
테스트를 위한 사이트는 Web Server 어떤 것이던 가능하지만, http 관련 테스트를 편리하게 할 수 있는 사이트가 있어서 
몇가지 내용은 해당 사이트를 통해서 확인해 보겠습니다.    
#### http(s) Packet 관련 테스트 - [https://httpbin.org/](https://httpbin.org/)
python 내장 함수인 urllib 및 이글에서 사용하게될 requests 관련 내용은 아래 link sites 에서 확인할 수 있습니다.
#### python 내장 모듈 - #### [https://docs.python.org/ko/3/howto/urllib2.html](https://docs.python.org/ko/3/howto/urllib2.html)
#### Request 내장 모듈 - [https://pypi.org/project/requests/](https://pypi.org/project/requests/)



### python request 
위 사이트 에서 확인이 가능하시겠지만, 설치는 pip 으로 설치한다고 할 때 아래의 구문을 통해 설치가 가능합니다.  ( 가급적 관리자 모드 )

``` python
    pip install requests
```
requests 첫 페이지에 포함된 예제가 많은 접점을 보여주고 있기 때문에 해당 예제를 잠시 살펴보겠습니다.(조금 변경 합니다.)
``` python
    >>> import requests
    >>> basePath = 'https://httpbin.org'
    >>> authPath = '/basic-auth/user/pass'
    >>> response = requests.get(basePath+authPath, auth=('user','pass'))
    >>> response.status_code
    200
    >>> response.headers['content-type']
    'application/json'
    >>> response.headers
    {'Date': 'Sat, 17 Jun 2023 05:13:50 GMT', 'Content-Type': 'application/json', 'Content-Length': '47', 'Connection': 'keep-alive', 'Server': 'gunicorn/19.9.0', 'Access-Control-Allow-Origin': '*', 'Access-Control-Allow-Credentials': 'true'}    
    >>> response.text
    '{\n  "authenticated": true, \n  "user": "user"\n}\n'
    >>> response.json()
    {'authenticated': True, 'user': 'user'}
```
위 코드를 살펴 보면 basePath 애서 ***https*** 로 접근하고 있습니다.  ***http*** 로 변경해서 접근해도 동일한 결과를 보여주고 있습니다.   
하지만, 두가지 접근 방식은 SSL 의 보안을 구성하는 부분에서 다른 접근이 필요하지만, requests 모듈에서는 거의 동일한 호출로 구성할 수 있도록
지원하고 있는 것 같습니다.    
requests.get 은 요청이 get 방식으로 전송을 요청한다는 것이며, 그 결과는 response 에 결과를 담고 있습니다. response는 상태코드, headers, body 의 영역으로 데이터를 담고 있습니다.    
상태는 404 의 페이지 찾지못허는 경우, 500번대의 Server Error, 200 번의 성공 코드 등이 있습니다.   
headers 에는 body data 에 대한 기초 정보가 있고, body 에 server 에서 넘겨온 문자열이 있습니다. ( 2진 데이터 base64 encoding )   
response.text 의 결과는 body 영역에 구성된 text 파일을 그대로 출력합니다.   '\n' 등의 줄바꿈 문자 등 전체를 text 로 표현합니다. 하지만, response.json() 함수는 해당 문자열을 json 형식으로 변환합니다. 
``` python
    >>> jObj = response.json()
    >>> jObj
    {'authenticated': True, 'user': 'user'}
    >>> jObj['authenticated']
    True
    >>> type(jObj)
    <class 'dict'>    
```
위에서 연속된 예제를 보면 response.json() 함수를 통해 얻게 되는 데이터는 python 의 dictionary 입니다. 

``` python
    >>> response = requests.get(basePath)
    >>> response.headers
    {'Date': 'Sat, 17 Jun 2023 06:21:10 GMT', 'Content-Type': 'text/html; charset=utf-8', 'Content-Length': '9593', 'Connection': 'keep-alive', 'Server': 'gunicorn/19.9.0', 'Access-Control-Allow-Origin': '*', 'Access-Control-Allow-Credentials': 'true'}
    >>> print(response.text)
```
위의 코드에서 주목해야할 부분은 Content-Type : text/html ... utf8 등의 항목을 보게 됩니다. 가져온 값이 html 문자열로 구성되어 있는 것을 print 함수를 통해 확인해 볼 수 있습니다.     
site 에서 제공하는 예제중 get, post 만 조금더 살펴 보겠습니다.   

``` python
    >>> payload = {'key1' : 'value1', 'key2' : ['value2','value3']}  
    >>> response = requests.get(basePath+'/get', params=payload)   
    >>> print(response.url)
    https://httpbin.org/get?key1=value1&key2=value2&key2=value3

    >>> response = requests.post(basePath+'/post',data=payload)
    >>> print(response.url)
    https://httpbin.org/post


    >>> response.content
    b'{\n  "args": {}, \n  "data": "", \n  "files": {}, \n  "form": {\n    "key1": "value1", \n    "key2": [\n      "value2", \n      "value3"\n    ]\n  }, \n  "headers": {\n    "Accept": "*/*", \n    "Accept-Encoding": "gzip, deflate", \n    "Content-Length": "35", \n    "Content-Type": "application/x-www-form-urlencoded", \n    "Host": "httpbin.org", \n    "User-Agent": "python-requests/2.31.0", \n    "X-Amzn-Trace-Id": "Root=1-648d67a8-39a6e18a6d74c943377d8ded"\n  }, \n  "json": null, \n  "origin": "xxx.xxx.xxx.xxx", \n  "url": "https://httpbin.org/post"\n}\n'

    >>> response.raw
    <urllib3.response.HTTPResponse object at 0x0000028DB8C73460>    

```
간략하게 위의 코드를 확인해 보면, 전송할 데이터가 url 에 포함되는지, 그렇지 않은지에 대한 차이만 존재합니다.   사실 post 전송은 
동일한 데이터가 body 에 포함되어 전송될 수 있기 때문에 get 방식보다 많은 데이터를 전송할 수 있고, 경우에 따라서는 base64 encoding 된 
binaray data 도 전송할 수 있습니다.   
requests 모듈에서는 bytes 단위의 데이터를 접근하려면 [response].content 형식으로 확인할 수도 있습니다.    
혹시 내장 기능이 필요하면, response.raw 를 호출해 보면 urllib 저의 경우 urllib3 모듈이 반환됩니다.   내장된 기능을 접근하기 위해서 사용할 수도 있습니다. 
더 다양한 기능이 있지만, 이선에서 정리하고 비교를 위해 유사한 방식으로 다른 언어로 접근해 보겠습니다. 


### Javascript fetch 
javascript 에서는 일반적으로 ajax 라는 방식으로 알려진 XMLHttpRequest 를 활용하여 Server와 통신을 합니다.   
이런 방식을 바탕으로 사용하기 편리하게 만들어진 많은 Library 가 있습니다.  하지만, fetch 라는 api를 표준으로 제공하고 있고, 
그 기능이 XMLHttpRequest 넘어서, 다양한 기능을 제공하고 있기 때문에 이 글에서는 Fetch API 를 기반으로 확인해 보겠습니다.    
Web 관련하여 참조할 수 있는 대표적인 Site 는 mdn site 입니다. 
#### Fetch API - MDM : [https://developer.mozilla.org/ko/docs/Web/API/Fetch_API](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API)

위 Python 예제와 유사하게 구성해 보겠습니다. 

먼저 공통 영역과 권한을 가져오기 위한 부분을 구성해 보겠습니다. 

``` javascript

        const basePath = "http://httpbin.org";
        const authPath = "/basic-auth/user/pass";
        const displayObj = document.getElementById("preArea");

        const makePositon = (title, str) => {
            displayObj.innerText = displayObj.innerText + "\n\n======" + title + "======\n";
            if ( str ) {
                displayObj.innerText = displayObj.innerText + str;
            }
        }

        const loadAuthValue = async (url,username, password) => {
            let headers = new Headers();
            headers.set('Authorization', 'Basic ' + btoa( username + ":" + password ));
            headers.set("Origin","http://localhost:1313");
            const response = await fetch(url,{
                method:'GET',
                mode : "cors",
                headers:headers,
            });
            const text = await response.json();
            makePositon(" Authority ", JSON.stringify(text));
        };

        loadAuthValue(basePath+authPath,"user","pass");        
```
python 의 request 보다는 설정내용이 조금 더 많지만, 유사한 형식을 취하고 있습니다.    
인증관련하여 header 영역에 설정하는 내용이 있습니다.   btoa 함수는 Base64 encoding 을 위한 함수 입니다.    
이글의 주제는 아니지만, cors 와 origin 등은 domain 이 같아야 하는 부분을 mapping 하기 위한 부분입니다.
JSON 에 관련한 함수로 가장 많이 사용했던것이 JSON.parse 와 JSON.stringify 입니다.   parse 함수는 문자 그대록 문자열을 json 객체로 변환하는 함수 이고, 
stringify 는 json 객체를 문자열로 전환하는 함수 입니다.    


대략 그 결과는 아래와 같습니다. response 자체에서 json(), text() 등의 함수를 통해 본문의 내용을 가져올 수 있습니다.  
``` javascript 
    ====== Authority ======
    {"authenticated":true,"user":"user"}
```

Get 방식도 크게 다르지 않지만, 구성할 때 url 에 포함되어야 하기 때문에 그 변환과정 구현이 있습니다. 
requests 에서는 이 부분을 알아서 해 주기 때문에 굳이 다시 변경할 필요가 개발자에게는 없습니다.  

``` javascript

        const payload = {'key1' : 'value1', 'key2' : ['value2','value3']};
        const loadGetValue = async (url, data) => {
            try {
                let urlStr = url;
                if ( data ) {
                    let flag = true;
                    for ( key in data ) {
                        let mark = "&";
                        if ( flag ) {
                            mark = "?";
                        }
                        if ( Array.isArray(data[key])) {
                            for ( let i = 0; i < data[key].length; i++ ) {
                                urlStr += mark+key+"="+data[key][i];
                            }
                        } else {
                            urlStr += mark+key+"="+data[key];
                        }
                        flag = false;
                    }
                }
                makePositon(" GET URL STR ", urlStr);
                const response = await fetch(urlStr, {
                    method: "GET", // 또는 'PUT'
                    headers: {
                        "Content-Type": "application/json",
                    },
                });

                const result = await response.json();
                makePositon(" GET URL RESULT ", JSON.stringify(result));
                console.log("성공:", result);
            } catch (error) {
                console.error("실패:", error);
            }
        }

        loadGetValue(basePath+"/get",payload);

```
url 을 구성하기 위한 부분등을 제외하면 거의 유사한 모습을 지니고 있습니다. 
그 결과는 아래와 같습니다. 

``` javascript
    ====== GET URL STR ======
    http://httpbin.org/get?key1=value1&key2=value2&key2=value3

    ====== GET URL RESULT ======
    {"args":{"key1":"value1","key2":["value2","value3"]},"headers":{"Accept":"*/*","Accept-Encoding":"gzip, deflate","Accept-Language":"ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7","Content-Type":"application/json","Host":"httpbin.org","Origin":"http://localhost:1313","Referer":"http://localhost:1313/","User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36","X-Amzn-Trace-Id":"Root=1-648e65e4-679248c80951827f25511060"},"origin":"xxx.xxx.xxx.xxx","url":"http://httpbin.org/get?key1=value1&key2=value2&key2=value3"}
```

post 방식은 다음과 같습니다. 
``` javascript

        const loadPostValue = async (url, data) => {
            try {
                const response = await fetch(url, {
                    method: "POST", // 또는 'PUT'
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify(data),
                });

                const result = await response.json();
                makePositon(" POST URL RESULT ", JSON.stringify(result));
                console.log("성공:", result);
            } catch (error) {
                console.error("실패:", error);
            }
        }

        loadPostValue(basePath+"/post", payload);
```
body 항목에 데이터를 구성하고 있는 것을 확인해 볼 수 있습니다. 

그 결과는 다음과 같습니다. 


``` javascript
====== POST URL RESULT ======
{"args":{},"data":"{\"key1\":\"value1\",\"key2\":[\"value2\",\"value3\"]}","files":{},"form":{},"headers":{"Accept":"*/*","Accept-Encoding":"gzip, deflate","Accept-Language":"ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7","Content-Length":"44","Content-Type":"application/json","Host":"httpbin.org","Origin":"http://localhost:1313","Referer":"http://localhost:1313/","User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36","X-Amzn-Trace-Id":"Root=1-648e65e3-4c1fe6647aa1419e05f26128"},"json":{"key1":"value1","key2":["value2","value3"]},"origin":"xxx.xxx.xxx.xxx","url":"http://httpbin.org/post"}
```

javascript 로 구성한 내용도 python 과 거의 유사하다고 보아도 좋을 것 같습니다. 
python 의 request 모듈은 개발자가 해 줘야 할 부분을 조금 쉽게 구성할 수 있도록 만들어 주고 있는 것이 약간의 차이입니다. 

python 의 모듈을 찾다보면, 일반적인 프로젝트에서 공통으로 구성해야할 모듈을 거의 완제품 형식의 제품으로 구성한 각종 모듈이 있어서, 
그들을 조합하여 사용할 수 있는 편리함이 있는것 같습니다.   
반면 그 원리를 몰라도 사용할 수 있다는 것은, 그 모듈이상의 깊이 있는 프로그램을 개발할 때에는 불편함으로 다가 올 수도 있을 것 같습니다.  
(물론 그런 개발이 흔한 일은 아니겠지만요 ... ) 

### Java, HttpClient, ObjectMapper
Java는 11 version 이후에 제공하는 HttpRequest, HttpClient, HttpResponse 가 있고, 그 이전 Version 에서는 HttpURLConnection, HttpsURLConnection 등 http, https 등을 별도로 관리한 클래스가 있었습니다.    
Apache 에서 open source 로 제공하는 library 가 있었으니, 그것을 사용하여도 괜찮지만, 현재 OpenJDK 19 에서는 ( 11 이후 ) HttpClient 등의 클래스를 API 차원에서 제공합니다.    
Web 에서 접근할 경우 HttpServletRequest, HttpServletResponse 등 특화된 모듈이 있고, Spring 등에서 그것을 감싼 편한 클래스와 메소드를 제공하기 때문에 직접 사용할 기회는 많지 않습니다.     
하지만, Application 등에서 직접 접속이 필요하거나 할때 유용히 사용할 수 있는 부분이고, 앞선 언어와 비교하기 편하기 때문에 11 Version 이후에 제공하는 API 를 사용하여 비교해 보겠습니다.  

JSON 의 경우 이 글에서는 ObjectMapper 를 사용합니다. 간단한 예제에서는 직접 구현해서 테스트 하는 것도 의미 있지만, 직접구현이 번거로운 측면도 있고, ObjectMapper 는 강력한 기능을 지닌 Library 라 정리차원에서도 의미가 있어 보입니다.    
java 에서도 gradle, maven 등을 통해 pip , node 등의 install 이 가능하지만, 이곳에서는 maven site 에서 dependency 를 확인하고 3개의 jar 파일을 직접 다운로드 받아 사용하고 있습니다.   
아래는 ObjectMapper 관련 jar 파일을 얻을 수 있는 site 입니다.  

#### [https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind/2.15.2](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind/2.15.2)
#### [https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-annotations/2.15.2](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-annotations/2.15.2)
#### [https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.15.2](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.15.2)

java 관련한 소스는 분리해서 이야기 하기 힘들어 전체 클래스를 소개하겠습니다.   
구현 방법은 여러 가지 이지만, python 을 정리하기 위한 비교로 java 를 설명하는 것이기 때문에 유사성과 차이만 보시면 좋을것 같습니다.   
첫번째 path 는 json 이 라인으로 구분되어 있을 경우의 예제라 추가해 보았습니다.    

``` java 
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpRequest.BodyPublishers;
import java.net.http.HttpResponse.BodyHandlers;
import java.time.Duration;
import java.net.URI;

import java.util.*;

import com.fasterxml.jackson.core.type.TypeReference;
import com.fasterxml.jackson.databind.ObjectMapper;

public class HttpTester {

    public static Map<String,Object> parseJson(String text) {
        ObjectMapper mapper = new ObjectMapper();
        Map<String,Object> result = null;
        if ( text == null || text.isEmpty() ) {
            return result;
        }
        System.out.println( text );
        try {
            result = mapper.readValue(text , new TypeReference<Map<String,Object>>(){});
        } catch(Exception ee) {
            ee.printStackTrace();
        } 
        System.out.println ( "RESULT : " + result);
        return result;
    }

    private static void makeJsonValues(String text) {
        System.out.println ( text );
        ObjectMapper mapper = new ObjectMapper();

        try {
            List<Map<String,Object>> result = new ArrayList<Map<String,Object>>();
            for ( String str : text.split("\n")) {
                Map<String, Object> data = mapper.readValue(
                        str,
                        new TypeReference<Map<String,Object>>(){});
                result.add(data);
            }
            System.out.println ( result.get(0) );
        } catch ( Exception ee ) {
            ee.printStackTrace();
        }
     }

    private static void executeHttpClientMode( String urlStr) throws Exception {
        HttpRequest req = HttpRequest.newBuilder()
            .uri( new URI(urlStr))
            .version(HttpClient.Version.HTTP_2)
            .GET()
            .timeout(Duration.ofSeconds(60L))
            .build();

        HttpClient.newHttpClient()
            .sendAsync(req,BodyHandlers.ofString())
            .thenApply(HttpResponse::body)
            .thenAccept(HttpTester::makeJsonValues)
            .join();
    }

    private static void loadAuthValue( String urlStr, String userName, String userPwd) throws Exception {
        String str = Base64.getEncoder().encodeToString((userName+":"+userPwd).getBytes());
        HttpRequest req = HttpRequest.newBuilder()
            .uri( new URI(urlStr))
            .version(HttpClient.Version.HTTP_2)
            .GET()
            .header("Authorization", "Basic " + str )
            .timeout(Duration.ofSeconds(60L))
            .build();

        HttpClient.newHttpClient()
            .sendAsync(req,BodyHandlers.ofString())
            .thenApply(HttpResponse::body)
            .thenAccept(HttpTester::parseJson)
            .join();
    }

    private static void loadGetValue (String urlStr, Map<String,Object> data) throws Exception {
        String transUrl = urlStr;
        String mark = "?";
        for ( String key : data.keySet() ) {
            Object obj = data.get(key);
            if ( obj instanceof ArrayList ) {
                ArrayList<String> oArray = (ArrayList)obj;
                for ( int i = 0; i < oArray.size(); i++ ) {
                    transUrl += mark + key + "=" + oArray.get(i);
                    mark = "&";
                }
            } else {
                transUrl += mark + key + "=" + obj.toString();
            }
            mark = "&";
        }

        System.out.println (String.format(" URL STR : %s", transUrl) );

        HttpRequest req = HttpRequest.newBuilder()
            .uri( new URI(transUrl))
            .version(HttpClient.Version.HTTP_2)
            .GET()
            .timeout(Duration.ofSeconds(60L))
            .build();

        HttpClient.newHttpClient()
            .sendAsync(req,BodyHandlers.ofString())
            .thenApply(HttpResponse::body)
            .thenAccept(HttpTester::parseJson)
            .join();
    }

    private static void loadPostValue (String urlStr, Map<String,Object> data) throws Exception {
        ObjectMapper mapper = new ObjectMapper();
        String dataParam = mapper.writeValueAsString(data);

        System.out.println (String.format(" TRANS PARAMS  : %s", dataParam) );

        HttpRequest req = HttpRequest.newBuilder()
            .uri( new URI(urlStr))
            .version(HttpClient.Version.HTTP_2)
            .POST(BodyPublishers.ofString(dataParam))
            .timeout(Duration.ofSeconds(60L))
            .build();

        HttpClient.newHttpClient()
            .sendAsync(req,BodyHandlers.ofString())
            .thenApply(HttpResponse::body)
            .thenAccept(HttpTester::parseJson)
            .join();
    }

    
    public static void main(String[] args) {
        String path = "https://resources.oreilly.com/examples/0636920023784/-/raw/master/pydata-book-master/ch02/usagov_bitly_data2012-03-16-1331923249.txt";

        String basePath = "https://httpbin.org";
        String authPath = "/basic-auth/user/pass";

        String payload = "{\"key1\" : \"value1\", \"key2\" : [\"value2\",\"value3\"]}";
        Map<String,Object> payMap = parseJson(payload);
        try {
            executeHttpClientMode(path);
            loadAuthValue(basePath + authPath,"user","pass");
            loadGetValue(basePath + "/get", payMap );
            loadPostValue(basePath+"/post", payMap);
        } catch (Exception ee) {
            ee.printStackTrace();
        }
    }
}
``` 
결과는 위의 python 이나, javascript 와 유사한 결과가 나오기 때문에 굳이 기술하지 않습니다.   
언어별 특징이 있어서 조금 다른 부분도 있지만, 상당히 유사한 형식을 띠고 있습니다.    
http(s) 프로토콜을 기준으로 구성하는 것이기 때문에 언어별 차이보다는 유사성이 많아 보입니다.    
언어를 비교하면서 조금씩 드는 생각은 Python 관련 모듈이 C, C++ 등으로 구성하여 실행 속도도 빠르게 유지하면서, 
업무( 프로토콜 ) 등을 잘 알고 있을 경우 사용하기 쉬운 완제품 같은 형태의 모듈로 제공되는 것 같은 느낌이 듭니다.    
다른 언어에서 제공하는 Library도 그런 기능을 하긴 하지만, Python 의 모듈이 좀더 완제품에 가까운것 같습니다.    
사용하기 편하고 좋다는 강력한 장점과, 제공하지 않는 기능이나, 새롭게 구성하여야 할 경우 변형이 만만치 않을 것 
같은 생각이 듭니다. 아마도 대부분의 경우 이런일은 발생하지 않겠지만요 ^^ 
