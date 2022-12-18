---
title: "Hugo Blog로 시작하기"
date: 2022-12-18T12:07:36+09:00
draft: false
description: "Hugo 를 선택한 이유 ..."
summary : "Hugo Blog 설치를 위한 간단한 내용"
tags: ["Common","Language","Go"]
topics: ["Installation"]
---

# Hugo or Jekyll
   
## 개발 배경

   아주 예전에 Blog 를 사용하기 위해서 Naver, google 등의 사이트에서 제공하는
   모듈을 이용하여 시작해 본적이 있습니다.    
   디자인은 신경쓰지 않는다고 하면서도 진행하다보면 휑한 화면과 정돈되지 못한 사이트가
   눈에 거슬리는 것은 어쩔 수 없었습니다. 
   
   시간이 흘러 지금에 이르러 보면 , 수학 수식 부터 전문적인 내용을 다루는 사이트 임에도
   깨끗이 정돈된 내용을 인터넷에서 많이 찾아 볼 수 있었습니다. 
   
   그런 사이트에 대한 기술문서를 찾아 보다 보니 처음 접합것이 Jekyll 이었습니다. 

## Jekyll 사용하려다 보니 ...

   Web Design 이 익숙하지 않지만, 개발과정에서 css 를 어느정도는 알아야 하기 때문에 개발 관점에서는
   어느정도 알고 있다고 생각 했습니다. 
   Jekyll 을 사용하면서 디자인을 구성하기 위해 Themes 를 사용하려다 보면, Jekyll 의 Version 에 따라 사용방법이
   차이가 나고, 마음에 드는 Thema 를 구해서 확인해 보면 Version 문제 등으로 설정을 다시 학습해서 해야 하는 상황이 
   반복되다 보니, 이럴 바에는 가장 최신 내용으로 적용해 보자는 생각이 들었습니다.    
   그렇게 찾다 보니 Hugo 라는 것이 더 최신 기술로 등장한 것을 확인 할 수 있었습니다. 

   Hugo 의 Go 도 낯설지만, Jekyll 의 ruby 도 잘 모르기는 마찬가지라, 선택에 중요한 부분은 아니었고,    
   속도가 차이가 난다고 하지만, static site ( html ) 로 구성된 후의 속도는 별 의미가 없기 때문에 순전히 
   Version 에 대한 호환성 지원이 가장 큰 선택 이유가 되었습니다.    
   ( 아마도 Jekyll 이 오래 되었고, 과거부터 현재까지 구성된 내용이 많다 보니, 제가 정리하는데 애를 먹어서 그런듯 합니다. ^^ )

## Github Page 에서 사용

   GitHub Page 에서 사용하는 것이기 때문에 git 에 관련한 부분은 잠시 접어 두고, Hugo 를 중심으로 구성을 해보면 아래와 같이 
   설치가 필요합니다. ( Windows 기준 )

   ### Hugo 설치
   URL : https://gohugo.io/  
   해당 사이트 에서는 chocolatey 로 설치하는 방법이 나와 있습니다. 
   ``` shell
   > choco install hugo-extended
   ```

   chocolatey 는 아래의 URL 에서 설치방법을 확인 할 수 있습니다.    
   URL : https://chocolatey.org/install, https://gohugo.io/installation/windows/

   다만 Hugo 는 Go 란 언어를 사용하기 때문에 Hugo 를 설치하기전 Go와 Git 이 설치되어 있어야 합니다. 
   
   #### Go 설치
   URL : https://go.dev/learn/ , https://go.dev/doc/install
   
   설치가 모두 정상적으로 되었다면  ( Windows Command 창을 닫은 후 다시 열어 확인 )

   ``` go
   > go version 
   ```
   위 내용으로 설치를 확인할 수 있습니다.    

   ```go
   package main
   import "fmt"

   func main() {
	   fmt.Println("Hello, World!!!")
   }
   ```

   go run .    

   결과는 Hello, World!!!

   #### Windows 설치 기준 정리 ( Git 은 설치되어 있음 가정 )

   1. Go 설치
   2. Chocolatey (Windows) 설치
   3. Hugo 설치

   URL : https://gohugo.io/getting-started/installing

   choco install hugo -confirm 
   choco install hugo-extended -confirm   

   둘중의 하나로 설치하면 되지만, 간혹 Themes 가 extended 를 사용하는 경우가 있기 때문에 사용하고자 하는 항목에 따라
   설치하면 될 것 같습니다. 





