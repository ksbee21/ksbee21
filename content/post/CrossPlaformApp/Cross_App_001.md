---
title: "QT, PYQT, SWING, JFX - Entry Point"
date: 2023-01-19T18:17:47+09:00
draft: false
tags : ["CrossPlatform","Application","Language","JAVA", "QT","PYQT6","SWING","OpenJFX"]
topics : []
description : "Cross Platform Application 개발에 필요한 기초사항 정리 "
---

# GUI Program 시작
   
   앞선 예제에서 Window 창을 뛰우는 간단한 예제를 QT, PYQT, SWING, JFX 로 살펴 보았습니다.    
   핵심적인 모듈만 띄어서 살펴 보면 다음과 같은 코드 들 입니다.  

   ``` c++
    # QT6 
    QGuiApplicatin app(arac, argv);
    ..
    return app.exec();

    # PyQT6
    app = QApplication(sys.argv)
    ..
    app.exec();

    # swing 
    JFrame jf = new JFrame("Hello World!!");
    jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    ...
    jf.setVisible(true);

    # JFX 
    extends Application ( Applicaiton 상속 )
    ...
    launch();
    stage.show();
   ```

   ### Java Swing 에서 JFrame 은 Top Level Container 라고 이야기 합니다.     
   이 의미는 다른 클래스 없이 GUI Program 을 구성할 때 독립적으로 화면을 구성할 수 있는 Class 라는 의미 입니다.    
   조금씩 확인해 보겠지만, 다른 종류의 GUI Widets 들은 독립적으로 화면을 구성할 수 없습니다. ( Swing 에서는 JFrame 외에 몇개 더 있습니다. ) 
   jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); 이 라인이 표현하고 있는 부분이 무엇일까요?     
   다른 소스에서는 자동으로 구성되기 때문에 표기가 되지 않고 있지만, 해당 GUI 가 종료 될 때 해야 할 일을 정의 하고 있습니다.    
   대략 의미하는 바는 GUI 를 종료 시키고, 그를 감싸고 있는 JVM 환경에 쓰인 자원을 시스템에 반납한다 정도로 해석 할 수 있을 것 같습니다.    
   현재 소스에서는 감춰져 있지만, GUI 프로그램은 내부적으로 시스템 자원으로 부터 Key, Mouse 등의 User Input Event 를 확인하기 위해서 무한히 반복하며 Event 를 기다리는 
   프로그램 입니다.  그렇기 때문에 그런 역활을 하는 클래스를 한정해 놓고 관리하는것인 아닌가 합니다. Swing 에서는 대표적인 것이 JFrame 입니다. 

   ### JFX Application Class 는 Application 시작 Class 입니다. 
   JFX API 에서는 Application Class 에 대한 설명을 대략 다음과 같이 설명하고 있습니다. 
   1. JavaFX runtime 을 시작 합니다. 
   2. Application Class 를 생성(메모리에 로딩)합니다. 
   3. init() 메소드를 호출하여 구성한 내용을 실행 합니다. 
   4. start( Stage ) 를 호출하여 UI 구성 내용을 실행 합니다. 
   5. Application 이 종료 할 때 까지 Event 를 반복하여 기다립니다.
   6. 종료 이벤트를 받으면 자원을 종료하고, stop() 메소드를 호출합니다. 
   
   중간에 Stage Class 는 Swing 의 JFrame 처럼 Top Level Container 입니다.    
   JFX 에서 조금 자세히 정리한 것은 Swing 의 JFrame 이 하는 것처럼 보인 일을 분리하여 Application 과 Stage 라는 두개로 구성되어 있다는 것을 확인하기 위함입니다.    
   QT 등의 모듈에서도 QApplication, QWidget() 의 관계와 유사하지 않을까 해서 입니다.   

   ### QT6 QApplication : QGuiApplication : QCoreApplication 은 상속 구조의 클래스 입니다.  
   QCoreApplicatioin 은 he QCoreApplication class provides an event loop for Qt applications without UI. More... 라고 설명하고 있습니다.    
   간단히 정리하면 UI 없이 System 으로 부터 Event 를 가져와서 처리하는 역활을 수행하는 클래스라고 해석해도 될 것 같습니다.    
   그렇기 때문에 해당 클래스를 상속 받은 QApplication 이나, QGuiApplication 은 동일한 역활을 수행한다고 생각해도 될것 같습니다.  
   QGuiApplication, QApplication 은 모두 GUI 를 구현하기 위한 모듈인데 QApplicaiton 은 QWidget 를 활용할 수 있다는 것이 약간(?) 의 
   차이 입니다.    QML( Markup Language )로 UI Design 을 구성할 경우 QGuiApplication 을 사용할 수 있을것 같습니다.   
   개인적으로는 QML 로 구성하는 것이 더 매력적으로 보이나, PYQT6 등의 예제에서 QApplication을 더 쉽게 찾을 수 있어 일단 QWidget 중심으로 정리할 예정 입니다. 

   QT 는 한가지 더 확인할 내용이 있습니다.    소스 Compile 과 구성을 위해 cmake file 을 활용할지, qmake file을 활용할 지 입니다.    
   QT Creator 에서 프로젝트를 구성할 때 선택 할 수 있습니다. 일단, 예제가 많은 qmake 를 기준으로 정리해 가겠습니다.    
   c 관련 모듈은 작은 단위라면 gcc, g++ 등으로 compile 하고 linking 을 직접 수행할 수도 있겠지만, 모듈이 커짐에 따라 makefile 로 관리하게 되는데 
   이를 쉽게(?) 사용할 수 있도록 해주는 것이 cmake, qmake 라고 생각해도 될 것 같습니다.    java 의 ant 가 makefile 역활을 수행한다고 이해해도 
   크게 문제가 되지는 않을것 같습니다.      

   ### PYQT6 은 QT6 을 감싼 프로그램 입니다.   
   PYQT6 의 앞선 예제는 Visual Studio Code 에서 Text 를 작성한 후 실행하면 GUI 화면이 출력됩니다.    
   QT6 으로 구성한다면, Creator 같은 도구를 사용해서 qmake 로 구성된 .pro 파일, hearder 파일, cpp 파일을 만들고, 
   build 한 후 run 해야 간단한 화면이 나타납니다.    
   그 모든 과정을 개발자의 관여 없이 진행해 준다는게 엄청난 편리함이라고 생각합니다.    
   내부에서 동작하는 것을 알 필요 없이, 하고자 하는 목적에 집중할 수 있게 해준다는 것은 개발자로써 정말 편리한 부분입니다.    
   하지만 드물긴 하겠지만, System Level 에서 처리해야할 Job 들이 많을 때 성능을 향상 시키는 부분등에서 QT 의 내용을 확인해야 할 경우가 
   있을 것 같습니다.      
   그래도 대부분의 경우 단점보다는 장점이 많지 않을까 생각합니다. ^^    

# UI Button 추가
   
   간단한 Button 하나를 추가해 보겠습니다.    
   각 프로그램마다 조금씩 차이게 있겠지만, 구조는 최대한 단순하게 추가하겠습니다.   얼마나 유사한지에 맞춰서 정리해 보겠습니다.    

   ### QT6
   ``` c++
        #include <QApplication>
        #include <QMainWindow>
        #include <QPushButton>

        int main(int argc, char *argv[])
        {
            QApplication a(argc, argv);
            QMainWindow window;
            window.setWindowTitle("Hello World!!!");

            //QPushButton *button = new QPushButton("Click ...", &window);
            //button->show();

            QPushButton *button = new QPushButton("Click ...");
            window.setCentralWidget(button);

            window.setFixedSize(QSize(320, 240));
            window.show();
            return a.exec();
        }

   ```
   QT 사이트에서는 위의 주석으로 마킹된 예제를 제시합니다.    부모 객체의 주소를 자식 객체에서 참조하는 방식으로 
   생성하고 있습니다.   이 경우 new 로 생성된 child 는 부모가 소멸할 때 같이 파괴되는 것으로 설명하고 있습니다.  
   원문은 ( The button is now a child of the window and will be deleted when the window is destroyed. )    
   QMainWindow 는 QWidget 를 상속 받은 class 입니다.   setCentralWidget 은 전체 크기로 담으라는 메소드 입니다.    
   QPushButton 은 QWidget 를 상속 받은 QAbstractButton 을 상속 받았습니다.   결국 QWidget 이죠 .... 

   ### PYQT6 
   ``` python 

        import sys

        from PyQt6.QtCore import QSize, Qt
        from PyQt6.QtWidgets import QApplication, QMainWindow, QPushButton


        class MainWindow(QMainWindow):
            def __init__(self):
                super().__init__()
                self.setWindowTitle("Hello World!!!")
                button = QPushButton("Click ..")
                self.setFixedSize(QSize(320, 240))
                self.setCentralWidget(button)

        app = QApplication(sys.argv)

        window = MainWindow()
        window.show()

        app.exec()

   ```

   위의 내용과 동일한 내용 입니다.  
   사실 PYQT6 참조 사이트의 내용을 위 QT 로 구성한 것이니까요 ...  
   Python 에서는 문법 구조를 확인하면 좋을 것 같습니다.  
   from 에서 선택할 대상그룹(Namespace, Package), import 에서 구체적인 class 를 가져오고 있습니다.  
   class 에서 () 로 상속을 표현하고 있습니다. 

   ### Swing 
   ``` java

        import java.awt.*;
        import javax.swing.*;

        public class App {

            private static void createAndShowGUI() {
                JFrame jf = new JFrame("Hello World!!!");
                jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

                JButton button = new JButton("Click ..");
                jf.getContentPane().add(button);

                jf.setSize(new Dimension(320,240));        
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
   QT 에서 상속 구조를 이야기 하였으니, 조금 이른 감이 있지만, 같이 언급해 보겠습니다. 
   JFrame 은 Object, Compoment, Container, Window, Frame , JFrame 의 순으로 부모 Class 가 있습니다.  
   JButton 은 Object, Component, Container, JComponent, AbstractButton, JButton 순입니다.   
   상위의 Container 까지 동일하고, 그 후 JComponet 의 하위 입니다.    
   AbstractButton 은 QT 와도 유사합니다.   ( 두 언어의 설계자가, 추상클래스를 상위로 둔 이유가 있겠지요 ... ) 

   설계자 마다 조금씩 다르지만, 유사한 점이 많다는 것은 유용한 것을 찾는 과정이 비슷하기 때문이 아닐까 합니다. ^^ 


   ### JFX 

   ``` java 
        import javafx.application.Application;
        import javafx.stage.Stage;
        import javafx.scene.Scene;
        import javafx.scene.control.Button;

        public class AppFX extends Application {

            @Override
            public void start(Stage stage) {
                Button button = new Button("Click ..");
                Scene scene = new Scene(button, 320, 240);
                
                stage.setScene(scene);
                stage.setTitle("Hello World!!!");
                stage.show();
            }

            public static void main(String[] args) {
                launch();
            }
        }

   ```

   JFX 에서도 Button 은 ButtonBase 를 상속 받습니다. 
   Application 은 QT 의 QApplication 과 유사한 측면이 있습니다.   UI 와 별도로 Event loop 를 담당하는 Class 입니다.  
   그 다음 구문은 상당히 유사해서 다음 정리할 때 다시 확인해 보겠습니다.    

   지금까지 구성한 소스를 실행하면 왼쪽 부터 QT, PYQT, SWING, JFX 의 UI 입니다.  

   ![QT](/imgs/qt_001.png) ![PYTHON](/imgs/py_001.png) ![SWING](/imgs/swing_001.png) ![JFX](/imgs/jfx_001.png)

# 참조한 사이트 입니다.  

   이곳 저곳 자료를 찾아서 정리하기 때문에 더 많은 사이트에서 정보를 취합하였지만, 대표적인 사이트만 나열하겠습니다. 

   QT 사이트 입니다. [https://doc.qt.io/qtcreator/creator-getting-started.html](https://doc.qt.io/qtcreator/creator-getting-started.html)   
   API 사이트 입니다 [https://doc-snapshots.qt.io/qt6-dev/](https://doc-snapshots.qt.io/qt6-dev/)    

   PYQT6 사이트 입니다. [https://www.riverbankcomputing.com/static/Docs/PyQt6/](https://www.riverbankcomputing.com/static/Docs/PyQt6/)   
   PYQT6 Tutorial 사이트 입니다. [https://www.pythonguis.com/pyqt6-tutorial/](https://www.pythonguis.com/pyqt6-tutorial/)    

   JAVA SWING Tutorial 사이트 입니다. [https://docs.oracle.com/javase/tutorial/uiswing/index.html](https://docs.oracle.com/javase/tutorial/uiswing/index.html)    

   JFX Tutorial 사이트 입니다. [https://docs.oracle.com/javafx/2/get_started/jfxpub-get_started.htm](https://docs.oracle.com/javafx/2/get_started/jfxpub-get_started.htm)   

