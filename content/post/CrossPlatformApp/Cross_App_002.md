---
title: "QT PYQT  SWING JFX Program Event 기초"
date: 2023-01-20T19:53:43+09:00
draft: false
tags : ["CrossPlatform","Application","Language","JAVA", "QT","PYQT6","SWING","OpenJFX"]
topics : []
description : "언어별 Event 호출에 대한 기본정리"
---

# Application 에서 Event 란?
   우리가 사용하는 PC, Web, 휴대폰등 거의 모든 IT 기기 혹은 모듈은 사용자가 클릭, 변경, 선택, 이동 등 무엇인가를 했을 때 그 행동에 
   반응하는 시스템입니다.    
   뭔지 모를 그 행동을 사건 즉 Event 라고 생각해 보겠습니다.    Event 에는 Keyboard 관련한 부분, Mouse 관련한 부분, Touch(Touch Screen) 등에 관련한 부분 등이 있을 것 같습니다. 
   
   우리가 사용하는 컴퓨터를 키면, OS 가 로딩되면서 최종 우리가 보는 컴퓨터 첫화면이 나타납니다.    
   OS ( Operating System ) 은 끄기 전까지, 무엇인가 발생할 Event 를 무한 반복하면 기다립니다.    CPU 가 1초에 몇번(?) 움직이네 등으로 표현하는 그 시간을 끄기전까지 반복합니다.   

   그다음 우리가 구성한 Application 이 해당 OS 에서 동작하는 것이라면, 중간을 몇단계 거치더라도, 결국 CPU 에서 인식한 그 Event 를 Active 되어 있는 Application 에 전달해 주게 됩니다. 
   이제 부터 Applicaton 은 받아 들인 Event 로 누가 발생한 것인지, 어떤 Event 인지( Keyboard, Mouse, Touch ) 그 Event 의 세부 내용이 무엇인지 ( Keydown, up, Mouse move, Mouse button, ...  ) 를 확인하고, 
   약속된 묶음을 만들어 어떤 행동을 하도록 요청합니다.    이런 Event를  구성할 때 어떻게 구분하고, 나누는 지가 어떻게 호출하는 지가 개발 언어마다 약간의 차이를 가질 수 있지만, 큰 흐름은 유사한 것 같습니다.   

   개발하는 입장에서는 흐름은 그렇다고 해도, 각 개발 언어마다의 특징이 있으니 그것을 잘 알고, 다루는 것이 상당히 중요합니다.    
   앞으로 계속 공부하고, 정리해 나가야 할 영역이긴 한데, 전체적인 윤곽만 정리해 보려고 합니다.     
   Event 처리는 정확하게 이해하는게 프로그램 개발에서 중요한 영역이기 때문에 짧은 글에서 전체를 담을 수는 없을 것 같습니다.    대략적인 흐름이라도 정리하면 좋을것 같습니다. 
   

# QT6 Event 
   ### QT6 Event 설명 Link 
   [https://doc.qt.io/qt-6/signalsandslots.html](https://doc.qt.io/qt-6/signalsandslots.html)  
   
   QT 에서 사용하는 Event 는 Signal 과 Slot 입니다. 
   Signal 은 이벤트가 발생할 때 던지는 역활을 하게 되는데, QT 의 widgets 들은 미리 정의된 signal 이 있고, 사용자가 widget 의 subclass 로 구성해서 자신만의 sinal을 만들 수 있다고 설명하고 있습니다.   
   QObject 을 상속받은 Class 에서는 Signal 과 Slot 을 구성해서 서로 데이터를 주고 받을 수 있는, Event 를 처리할 수 있다고 설명하고 있습니다.    
   가장 기본적인 호출 원형은 QObject 에서 정의한 아래의 구문 입니다.  QObject 가 모든 Qt objects 의 base 가 되는 class 입니다.  

   ``` c++

    [static]QMetaObject::Connection QObject::connect(const QObject *sender, const char *signal, const QObject *receiver, const char *method, Qt::ConnectionType type = Qt::AutoConnection)

    //  connect(senderObject, signal, receiverObject, slotMethod, QT::ConnectionType)
   ```
   ConnectionType은 기능보다는 수행 방법의 문제 이므로 default 값이 있다 정도로 생각하면 될 것 같습니다.    전달하지 않으면, QT::AutoConnetion 으로 정의 될 것 같습니다.   

   앞서 했던 예제를 조금 변경해서 PushButton 의 Event 구현 예제를 살펴 보겠습니다.    
   QT 공식 문서의 예제를 아주 약간만 수정한 소스 입니다.     
   사이트는 [https://doc-snapshots.qt.io/qt6-6.4/qpushbutton.html](https://doc-snapshots.qt.io/qt6-6.4/qpushbutton.html) 입니다. 
   QAbstractButton 을 상속 받고 있는데 slot 과 singnal 이 미리 정의 되어 있습니다.   여기서 사용하는 released() 는 singnal 로 정의 되어 있습니다.    

   Header 파일 입니다.  
   ``` c++
        #ifndef WIDGET_H
        #define WIDGET_H

        #include <QMainWindow>
        #include <QPushButton>



        class Widget : public QMainWindow
        {
            Q_OBJECT

        public:
            explicit Widget(QWidget *parent = nullptr);
            ~Widget();
        private slots:
            void handleButton();
        private:
            QPushButton *m_button;
        };
        #endif // WIDGET_H   
   ``` 
   header file 에서 보면 slots 을 설정하고, 함수 원형을 선언하고 있습니다. void handleButton();    
   이렇듯 클래스를 구성할 때 QObjec 를 상속받은 객체라면 자신만의 slot 을 구성할 수도 있을 것 같습니다.   


   구현 파일 입니다. (cpp)
   ``` c++
        #include "widget.h"

        Widget::Widget(QWidget *parent)
            : QMainWindow(parent)
        {
            m_button = new QPushButton("Click..", this);
            m_button->setGeometry(QRect(QPoint(10,10), QSize(200, 50)));

            connect(m_button, &QPushButton::released, this, &Widget::handleButton);
        }

        void Widget::handleButton() {
            m_button->setText("Example Clicked ...");
            m_button->resize(200,100);
        }

        Widget::~Widget()
        {
        }
   ```
   QMainWindow 를 상속 받고, 버튼을 만들고, 생성자에서 버튼의 이미 정의된 signal 인 released ( 아마도 button 객체가 클릭이 발생한 후 ) 받는 객체는 자신인 Widget 인 QMainWindow 이고, 받아서 처리할 내용은  
   header 에서 정의한 void handleButton() 함수를 실행 시키는 것으로 진행되게 될 것 같습니다.   
   클릭한 후에 버튼 Text 가 변경되고, 크기가 변경되는 구조 입니다.     
   모양을 변경하는 함수와 자료형은 언어마다 조금씩 차이가 있는데 QT 에서는 QRect, QPoint, QSize , resize 함수 setGeometry 함수 등이 사용되고 있습니다.     
   대부분의 언어에서 이름만 들어도 대략 짐작되는 행동을 유추할 수 있도록 명명하기 때문에 유추해서 해석하면 대부분 맞는 경우가 많은것 같습니다. ^^

   QT 는 Event 의 전달 및 수신을 객체끼리 받을 수 있도록 구성해 놓았기 때문에 어떤면에서는 사용하기에 따라 장점이 될 수 있지 않을까 하는 생각이 들었습니다.    

   

   ``` javascript
        QT       += core gui

        greaterThan(QT_MAJOR_VERSION, 4): QT += widgets

        CONFIG += c++17

        # You can make your code fail to compile if it uses deprecated APIs.
        # In order to do so, uncomment the following line.
        #DEFINES += QT_DISABLE_DEPRECATED_BEFORE=0x060000    # disables all the APIs deprecated before Qt 6.0.0

        SOURCES += \
            main.cpp \
            widget.cpp

        HEADERS += \
            widget.h

        # Default rules for deployment.
        qnx: target.path = /tmp/$${TARGET}/bin
        else: unix:!android: target.path = /opt/$${TARGET}/bin
        !isEmpty(target.path): INSTALLS += target

   ```
   QT Creator 에서 qmake 를 선택하였을 때 자동 생성해 주는 파일입니다.   Api 문서 에는 qmake일때 cmake 일때 설정하는 방법을 안내해 
   주고 있으니, 해당 부분을 참조해서 추가하여 makefile 을 구성하면 될 것 같습니다.    



# PYQT6 Event
   앞서 간단히 QT6 에서 사용하는 Event 인 Signal 과 Slot 을 살펴보았습니다.   
   PyQT6 에서는 어떻게 사용할 수 있을 까요?    

   ### PYQT6 Event 설명 Link
   [https://www.riverbankcomputing.com/static/Docs/PyQt6/signals_slots.html](https://www.riverbankcomputing.com/static/Docs/PyQt6/signals_slots.html)   
   PYQT6 에서 사용하는 Signal, Slot, 생성하는 방법 등에 대한 설명이 있습니다.    조금 방대하니 이런것이 있다라고 하고, 향후 하나씩 확인해 봐야 할 것 같습니다. 

   [https://www.pythonguis.com/tutorials/pyqt6-signals-slots-events/](https://www.pythonguis.com/tutorials/pyqt6-signals-slots-events/)
   예시로 PYQT6 에서 사용하는 방법을 제시하고 있습니다.    모든 예제를 확인할 수 없으니, 위 QT 의 예제 처럼 아주 간단한 호출을 구성해 보도록 하겠습니다.  

   ``` python 

        import sys

        from PyQt6.QtCore import QSize, QPoint, QRect, Qt
        from PyQt6.QtWidgets import QApplication, QMainWindow, QPushButton


        class MainWindow(QMainWindow):
            def __init__(self):
                super().__init__()
                self.setWindowTitle("Hello World!!!")
                self.button = QPushButton("Click ..",self)
                self.button.setGeometry(QRect(QPoint(10,10), QSize(200, 50)))

                self.button.released.connect(self.handleButton);
                self.setFixedSize(QSize(320, 240))
            
            def handleButton(self):
                self.button.setText("Example Clicked ...")
                self.button.resize(200, 100)

        app = QApplication(sys.argv)

        window = MainWindow()
        window.show()

        app.exec()

   ``` 
   앞서의 예와 동일한 일을 수행하는 소스 입니다.    
   눈여겨 볼 부분은 Python 에서 별도의 slot 을 선언하지 않고, 함수를 연결하여 호출하고 있는 부분입니다.   
   내부에서 접근하기 위해서 self.button 으로 생성하는 부분도 확인이 필요할 것 같습니다.  
   pySlot, pySignal, emit 등으로 호출하는 방식은 향후 조금씩 정리해야 할 것 같습니다.    


# SWING Event
   ### SWING Event 설명 Link 
   [https://docs.oracle.com/javase/tutorial/uiswing/events/intro.html](https://docs.oracle.com/javase/tutorial/uiswing/events/intro.html)
   [https://docs.oracle.com/javase/tutorial/uiswing/events/generalrules.html](https://docs.oracle.com/javase/tutorial/uiswing/events/generalrules.html)
   Swing 에서의 Event 기본 흐름을 설명하고 있는 링크 입니다.    
   Swing 에서는 JFX 와 유사하게 Event Source 의 개념을 사용하고 있습니다. 발생한 원천 정도로 이해하면 좋을 것 같습니다.   
   조금 다른 것은 해당 Event 를 Listener 를 구성해서 받아 들인 Event 에 따라 행동을 하도록 구성하고 있습니다.    주로 Low Level Event 를 어떻게 다룰 수 있는지에 
   초점을 맞춰서 설명하고 있습니다.   Swing 에서는 UI Thread 와 Event Thread 간의 동기화 문제가 있는데 이는 향후 조금씩 정리해 가도록 하겠습니다.  
   아래는 위와 유사한 일을 하는 소스 입니다.   

   ``` java

        import java.awt.*;
        import java.awt.event.*;
        import javax.swing.*;

        public class App {

            private static void createAndShowGUI() {
                JFrame jf = new JFrame("Hello World!!!");
                jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

                JButton button = new JButton("Click ..");
                button.setBounds(10,10,200,50);
                button.addActionListener( new ActionListener() {
                    @Override
                    public void actionPerformed(ActionEvent e) {
                        button.setText("Example Click..");
                        button.setSize( new Dimension(200, 100));
                    }
                });
                JPanel jp = new JPanel(null);
                jp.add(button);
                jf.getContentPane().add(jp);

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
   위와 동일한 행동을 하는 모듈 입니다.   다만, 위치를 수동으로 지정하기 위해서는 LayoutManager 가 없어야 하기 때문에 JPanel 을 추가 하고, 해당 객체에 Layout 을 null 로 초기화 하여, 
   수동으로 위치를 배치 하고 있습니다.     
   그 외의 구조는 Event 를 받아 들이기 위한 addActionListner 를 구성하고 해당 인터페이스의 메소드를 override 하여 text 를 변경하고, size 를 변경하는 일을 하고 있습니다.   
   소스에서 보듯 Swing 에서는 EventListener 를 Event Type 에 따라 잘 구성하는 것이 중요하기 때문에 해당 영역을 자세히 살펴보는 것이 중요할 것 같습니다.    

# JFX Event 
   
   ### JFX Event 설명 Link 
   [https://docs.oracle.com/javase/8/javafx/events-tutorial/events.htm](https://docs.oracle.com/javase/8/javafx/events-tutorial/events.htm)  
   [https://docs.oracle.com/javase/8/javafx/events-tutorial/processing.htm#CEGJAAFD](https://docs.oracle.com/javase/8/javafx/events-tutorial/processing.htm#CEGJAAFD) 
   첫번째 링크는 JFX 에서 사용하는 Event 설명에 대한 리스트를 제공하고 있습니다.   두번째 링크는 그중 Event 흐름에 대한 설명을 하고 있습니다.    
   대략, 사용자의 행위를 Event 로 받아 들이는 Event Class 를 기준으로 내장된 Event 가 있고, 그 Event 객체를 이용해서 새롭게 구성할 수도 있다라는 정도의 이야기 입니다.    
   크게 구성되는 항목이 EventType ( Key 인지 Mouse 인지등 ... ), Source ( 시작되는 지점, 그렇지만 Event Chain 에서 Source는 변경됨 ), Target ( EventTarget Interface 를 구현한 객체 대부분의 Node ) 으로 
   설명하고 있습니다.    정리해야할 내용이 많지만, 대략 EventType 은 Event 종류, Source 는 시작되는 곳 혹은 객체, Target 은 Event 를 받을 수 있는 객체 , 이런 정도로 정리해 볼 수 있을 것 같습니다.    
   Event 의 Captuing , Bubbling 등은 Web 에서 사용하는 개념과 유사한 부분인데, 향후 정리하도록 하겠습니다.    
   간단히 앞서와 같은 동일한 역활을 하는 예제 입니다.    

   ``` java 

        import javafx.application.Application;
        import javafx.scene.Scene;
        import javafx.stage.Stage;
        import javafx.scene.control.Button;

        import javafx.scene.*;
        import javafx.scene.paint.*;
        import javafx.scene.shape.*;
        import javafx.scene.control.*;
        import javafx.scene.layout.*;
        import javafx.event.ActionEvent;
        import javafx.event.EventHandler;

        public class AppFX extends Application {

            @Override
            public void start(Stage stage) {
                Button button = new Button("Click ..");
                button.setLayoutX(10);
                button.setLayoutY(10);
                button.setPrefSize(200,50);
                //button.setMaxSize(100,50);
                //button.setMinSize(100,50);

                /*
                button.setOnAction( new EventHandler<ActionEvent>() {
                    @Override
                    public void handle(ActionEvent e) {
                        button.setPrefSize(100,100);
                        button.setText("Example Click...");
                    }
                });
                */
                
                button.setOnAction( (ActionEvent) -> {
                    button.setPrefSize(200,100);
                    button.setText("Example Click...");
                });
                

                Group root = new Group();
                Scene s = new Scene(root, 320, 240);
                
                root.getChildren().add(button)    ;
                
                stage.setScene(s);
                stage.setTitle("Hello World!!!");
                stage.show();
            }

            public static void main(String[] args) {
                launch();
            }
        }

   ``` 
   소스에서 주석으로 마킹되어 있는 부분은 전통적인 프로그램과, Lamda 식으로 표현한 부분을 비교해 보기 위한 용도 입니다. 
   어떤면에서는 전통적인 방법을 사용하고 있는 방식이 클래스와 메소드를 명시하고 있기 때문에 가독성 측면에서는 더 좋을 수도 있습니다.    
   Stream 같은 객체에서는 자원의 효율적인 관리 등의 목적으로도 Lamda 식을 사용하기도 합니다.  코드의 간결성도 Lamda식을 사용하는 좋은 효과중 하나입니다.  
   개인의 선택 사항 같습니다. ^^    

   Scene 에 직접 add 되면 full size 가 되기 때문에 Group UI Class 를 추가해서 위치를 수동으로 조정하도록 하고 있습니다.   
   button 에 EventHandler 를 등록해 Button 이 클릭될 때 ActionEvent 가 발생하도록 구성하고 있으며, 그 메소드가 setOnAction 입니다.    
   setOnAction 을 api 에서 찾아 보면,  BaseButton 에서 정의 되어 있으며, 내용은 (The button's action, which is invoked whenever the button is fired. This may be due to the user clicking on the button with the mouse, or by a touch event, or by a key press, or if the developer programmatically invokes the fire() method.) 라고 표현하고 있습니다.     
   대략 사용자의 마우스클릭, touch , 자판(keyboard) 눌림 혹은, 다른 곳에서 fire() 메소드를 호출함으로써 시작된다는 이야기를 하고 있습니다.    
   Event는 프로그램을 개발할 때 정말 중요한 부분이기 때문에 각 언어별로 정확한 사용방법을 정리하는 것이 중요합니다.    
   지금은 아주 간단한 Event 의 기본 흐름만 간단히 살펴 보았습니다.  향후 다른 부분들을 정리해 가면서, 조금씩 Event 에 대한 내용을 더 확인해 가도록 하겠습니다.     



