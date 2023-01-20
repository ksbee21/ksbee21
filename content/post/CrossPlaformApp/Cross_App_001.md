---
title: "QT PYQT  SWING JFX Program 진입지점 "
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

