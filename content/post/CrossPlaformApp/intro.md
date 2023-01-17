---
title: "Cross Platform Applications"
date: 2023-01-17T20:34:10+09:00
draft: false
tags : ["CrossPlatform","Application","Language","JAVA", "PYQT6","SWING","OpenJFX"]
topics : []
description : "Cross Platform Application 들"
---

# Cross Platform Application 들

   프로그램 개발이 Web일 경우, Service 환경이 Linux 로 구성된 Server 일지라도, Client 환경이 Web Browser 로 접속하게 되기 때문에 
   Server 환경은 그렇게 중요한 개발 요건이 되지 않습니다.    .NET 등의 언어로 개발할 경우 Server 가 MS 계열이어야 하지만, Java 일 경우, 
   대부분 Linux 이겠지만, 그렇지 않아도 크게 영향을 받지는 않습니다.  ( 사실 성능면에서는 차이가 있겠지요 ... ^^ )   

   하지만, 개발해야 하는 내용이 Application 일 경우 조금 다른 양상을 보이게 됩니다.    
   Windows 사용자 만을 대상으로 한다면, C++, C#, QT 등을 떠올릴 것이지만, Linux 에서도 동작하는게 필요하다고 하면, QT, JAVA(SWING), JAVA(JFX), C++ 등 
   OS에 선택할 언어를 정해야 합니다.    

   개발하는 주체가 익숙한 언어에 따라 달라지긴 하겠지만, C++ 날코딩으로 구성하는 것 보다는, QT 를 선택할 확율이 조금 더 높고, 가끔 JAVA(SWING) 이나, JFX 를 
   선택할 수 있을 것 같습니다.    
   특히 QT 는 현재 많은 관심을 끌고 있는 Python 으로 감싼 PYQT 가 있어서 더 쉽게 접근할 수 있는것이 아닌가 하는 생각이 들었습니다.   

   PYQT, QT, JAVA(SWING), JAVA(JFX) 정도가 Cross Platform 에서 동작할 수 있는 선택지가 아닌가 합니다. ( 물론 C, C++ 로 구현이 불가능하다고 이야기 하는것은 아닙니다.  ^^ )

   ### 개인적인 개발과정 및 관심사  
   아주 예전에 C# WinForm 과 GDI++ 로 Application 을 구성해 본 경험이 있습니다.    모들의 일부는 C Library 를 사용하였으니, Win32 API 일부를 사용한 것일 겁니다.    
   Windows CE.NET 으로 간단한 모발 프로그램도 구성해 보았었지만, 시간이 흘러 얼마전 Unity Script 로 C# 소스를 보면서, 새록새록 새삼스러운 , 문법도 기억이 나질 않는
   아주 즐거운(?) 경험을 하였습니다.     그래도 Java와 C# 은 언어가 나올 때 부터 봐왔었는데요...  10년 넘게 보지 않았으니 어쩌면 당연한 것인지도 모르겠습니다.     
   C# 이 이럴진데, C 나 C++ 은 개인 공부로만 접하고, 프로젝트에서는 일부분만 사용해본 저로서는 처음 배우는 사람과 그리 다르지 않을 것 같습니다.    

   그나마, Java 는 최근까지 개발에 적용해 오고는 있지만, 개발환경에서 Version 을 함부로 올릴 수 없으니, 옛날 개발 환경에 머물러 있는 형편 입니다.    
   개발해야할 모듈이 Web외에 Linux 에서 동작해야 하는 환경이라, Swing 으로 개발한 경험은 조금 가지고 있습니다.    

   오래되어서 기억이 나진 않지만, 한번쯤 공부해본 언어를 나열하면, Java > Javascript > CSHELL > C# > ... R > C > C++ > PYTHON > PHP 정도가 아닌가 합니다.    
   (... 이하는 정말 기억이 별로 나지 않네요 ... ^^ )

   ### Cross Platform 언어 선택
   무엇인가 선택하는것을 것을 무척 어려워 하는 제 성격상, Linux 환경에서 개발해야하는 Cross Platform 언어를 모두 정리해 보는것은 어떨까 하는 생각이 들었습니다.   
   앞서 언급했듯, QT, JFX 가 있고, PYQT, SWING 등이 있으니 언어 계열로는 C++, JAVA 이고 거기에 PYTHON 이 포함된 것이니 모두 정리해 보면 어떨까 하는 생각 입니다.  
   일단 PYQT6, SWING, OpenJFX 를 기준으로 정리해 보겠습니다.    
   앞서 개인적인 관심사를 이야기한 이유는 혹시 제가 특정 언어를 더 많이 이야기 할 경우 다른 언어를 몰라서 그렇다는 이야기를 하고자 나열한 것입니다.  ^^
   정리하는 내용 역시 그럴지 모르겠습니다. ^^ 
   향후 여력이 되면, C#, C++ 로 Windows Application 도 정리를 해 보고 싶지만, 일단 뒤로 미뤄 두겠습니다. ^^   

# PYQT6 
   PY QT 에 대해 검색을 하다보면, PYQT6 에 대해서는 언급된 내용이 별로 없습니다.  자료가 별로 없다는 것은 공부하는 입장에서는 별로 달가운 상황은 아닙니다.   
   하지만, 출시가 2021년도에 되었고, 현재 시점에서 1년 이상 지났으니, 새롭게 공부하는 입장에서 PyQT5 로 진행할 이유는 없을 것 같습니다.   
   공식 사이트에서 제공하는 Tutorial 을 중심으로 살펴볼 예정 이기 때문에 설명한 데로 진행해 보겠습니다.     

   ### Install  
   Python 이 설치 되어 있어야 합니다.   자료가 많으므로 생략 하겠습니다.    PyQT6을 설치하기 위해서 저는 Python 을 현재 시점 최신 버전으로 upgrade 하였습니다.  
   python --version 으로 확인하면, 3.11.0 입니다.   

   공식적인 사이트에서 windows install 관련 문서는 [https://www.pythonguis.com/installation/install-pyqt6-windows/](https://www.pythonguis.com/installation/install-pyqt6-windows/) 에서 확인 하실 수 있습니다. 
   저의 경우 cmd 가 관리자 권한이 아닐 경우 파일 접근이 안된다는 에러가 있었습니다.   
   설치 명령어는 
   ``` python 
   pip3 install pyqt6
   ``` 
   입니다. 

   설치가 정상적으로 되었다면, python 이후 import PyQt6 이 정상적으로 진행되면 정상 설치가 된것으로 봐도 좋을 것 같습니다. 
   Guide 문서에는 Qt Creator 나 Qt Designer 는 Qt Site 에서 받으라고 안내하고 있습니다.    
   Python 없이 Qt 로만 개발을 진행한다면, 위의 두개의 툴을 받아서 설치후 진행하는 것이 좋겠지만, PyQT의 메뉴얼를 보면서 진행하는 부분은 꼭 그렇게 하지 않아도 좋을 것 같습니다.  
   먼저 메뉴얼 데로 진행하다가 필요할 때 설치 하도록 하겠습니다.    
   한가지 먼저 확인할 내용은 PyQT 문서데로 진행하는 것은 License 가 GPL 입니다.    

   ### 첫번째 Application 
   사실 공식 문서의 첫번째는 Hello world! 인데 이 문서에서는 Application 을 뛰우는 것으로 종료 합니다. 

   ``` python 
    from PyQt6.QtWidgets import QApplication, QWidget

    import sys

    app = QApplication(sys.argv)

    window = QWidget()
    window.show()

    app.exec();
   ```
   python 은 뛰어쓰기가 문법이기 때문에 뛰어쓰기에 유의 해야 합니다.
   app.py 로 저장 되어 있다면, python app.py 를 호출 하시면 빈 화면을 보실 수 있을 것 같습니다.   

# Java Swing     

   ### Java Install 
   java jdk 를 설치하면 swing package 가 설치 되기 때문에 별도의 설치 과정이 필요 없습니다.   
   java swing jfx 는 모두 소스를 변형해서 사용하지 않을 경우, 상용 모듈을 개발하여도 license 는 무료 입니다. 
   jdk 설치에 관한 문서는 넘쳐 나기 때문에 별도로 기술 하지는 않겠습니다.  
   swing 은 8 이상이면 문제 없지만, 현재 제가 설치한 version 은 openjdk 1.7 LTS version 입니다. 

   ### 첫번째 Application 
   ``` java 

        import java.awt.*;
        import javax.swing.*;

        public class App {

            private static void createAndShowGUI() {
                JFrame jf = new JFrame("Hello World!!!");
                jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

                jf.setSize(new Dimension(500,500));        
                jf.setVisible(true);
            }

            public static void main(String[] args) {
                javax.swing.SwingUtilities.invokeLater(new Runnable() {
                    public void run() {
                        createAndShowGUI();
                    }
                });
            }
        }


   ```

   javac App.java
   java App 라고 실행 하면 빈 화면이 나타납니다.  

# JAVA JFX 
   java jfx 는 별도의 모듈을 설치하여야만 동작합니다.    
   처음은 어떤 툴도 사용하지 않고, text editor 기능이 있는 곳에서 수동으로 하는 것으로 시작하겠습니다. 

   ### Install 
   다음의 사이트에서 모듈을 받을 수 있습니다. 저는 SDK Version 을 받아서 설치하였습니다. 
   [https://gluonhq.com/products/javafx/](https://gluonhq.com/products/javafx/) 에서 처음 시작하는 모듈 확인이 가능합니다. 
   [https://openjfx.io/openjfx-docs/#install-javafx](https://openjfx.io/openjfx-docs/#install-javafx) 사이트에서 최신 Version 을 다운 받아 zip을 풀면 됩니다. 

   ### 첫번째 Application


   ``` java

        import javafx.application.Application;
        import javafx.scene.Scene;
        import javafx.scene.control.Label;
        import javafx.scene.layout.StackPane;
        import javafx.stage.Stage;

        public class HelloFX extends Application {

            @Override
            public void start(Stage stage) {
                String javaVersion = System.getProperty("java.version");
                String javafxVersion = System.getProperty("javafx.version");
                Label l = new Label("Hello, JavaFX " + javafxVersion + ", running on Java " + javaVersion + ".");
                Scene scene = new Scene(new StackPane(l), 640, 480);
                stage.setScene(scene);
                stage.show();
            }

            public static void main(String[] args) {
                launch();
            }

        }
   ```    

   실행 방법이 사이트에 잘 설명되어 있지만, 툴없이 진행한다면, 모듈 패스와 추가 클래스를 잘 확인하고 진행 하셔야 합니다. 
   윈도우 환경 설명 입니다. 

   ``` java 
    set PATH_TO_FX="path\to\javafx-sdk-19\lib"

    javac --module-path %PATH_TO_FX% --add-modules javafx.controls HelloFX.java

    java --module-path %PATH_TO_FX% --add-modules javafx.controls HelloFX

   ``` 

   뭔가 비슷하면서 조금 차이가 있네요 ..  
   다음 글에서 하나씩 정리해 보도록 하겠습니다.    







   




