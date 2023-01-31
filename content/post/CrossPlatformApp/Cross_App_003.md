---
title: "QT PYQT  SWING JFX Layout 기초 1"
date: 2023-01-26T19:54:05+09:00
draft: false
tags : ["CrossPlatform","Application","Language","JAVA", "QT","PYQT6","SWING","OpenJFX"]
topics : []
description : "각 언어별 Layout Class 중심 정리"
---

# Application Layout 
   
   GUI 프로그램을 구성할 때, 첫인상을 좌우 하는 것은 전체적인 Widet(Component) 의 배치 입니다.    
   이를 도와 주는 역할을 담당하는 것이 Layout 인데 언어마다 유사하지만 조금씩 다른 내용을 가지고 있습니다.    
   각 언어의 Layout 을 모두 하나씩 살펴보는 것은 너무 많기 때문에 이런 것들이 있다 정도로 정리하고자 합니다.   
   나중에 시간이 되면, 간단한 Toy Project를 구성하면서, 각 언어별 사용법을 정리해 보고자 합니다. 
   그래도 대부분 제공 하는 것이, 상하,좌우 센터에 Anchor 할 수 있는 Layout, 가로, 세로로 정렬할 수 있는 Layout, 
   Table 처럼 cell 로 정렬할 수 있는 Layout, 자유롭게 배치 할 수 있는 Layout 등이 있습니다.    각 언어별 제공하는 
   Layout class 들을 정리해 보겠습니다.      

# QT 

   ### QT Layout Class Link 
   [https://doc.qt.io/qt-6/layout.html](https://doc.qt.io/qt-6/layout.html)    
   QT 에서 사용하는 Layout 과 사용상의 주의점 등을 정리하여 보여 주고 있습니다.    
   GUI를 개발하다 보면, 기본 Layout 구조외에 별도록 구성해야 할 경우가 있는데, 그 경우 구현해야될 
   메소드와 구성하는 방법 등에 대해서도 설명하고 있는 페이지 입니다.    개발 과정에서 틈틈히 참조해야 할 부분인데 , 
   해당 페이지에서 소개하고 있는 기본 Layout Class 들은 아래와 같은 것이 있습니다.   
   #### QBoxLayout
   이름 그대로 정렬된 상태를 구성하기 위한 Layout 입니다. 
   enum	Direction { LeftToRight, RightToLeft, TopToBottom, BottomToTop } 을 제공하고 있습니다.  명명된 그대로라고 생각하면 좋을 것 같습니다. 
   #### QButtonGroup
   버튼을 묶어서 담고, 그 버튼간에 어떤것이 active 상태 인지등을 확인할 때 사용하는 Layout 이라고 합니다.   
   하나씩 테스트 해보진 않았지만, exclusive() const 함수가 bool 을 return 하고, setExclusive(bool) 메소드가 있습니다.   
   #### QFormLayout 
   API 에는 2개의 column form을 배치하기 위한 Layout 이라고 표현하고 있습니다.   
   메소드에는 몇개의 overloading 된 내용이 있는데 addRow(QWidget *label, QWidget *field)가 기본이 되는것 같습니다. QWidget 을 상속 받은 모든 형태가 
   가능하고, label, field 의 명명으로 볼때 기본형은 문자열, 입력Widget 가 될 수 있습니다.  addRow(const QString &labelText, QLayout *field) 함수로 제공합니다.    
   #### QGraphicsAnchor , QGraphicsAnchorLayout
   QGraphicsAnchorLayout class 는 QGraphicsAnchor class 를 상속 받았습니다. 
   예시로 되어 있는 내용을 보면 왼쪽 b 가 오른쪽 a 위치의 상대 위치를 의미 합니다. 
   ``` c++
    layout->addAnchor(b, Qt::AnchorLeft, a, Qt::AnchorRight);
    layout->addAnchor(b, Qt::AnchorTop, a, Qt::AnchorBottom);
   ```
   위와 같은 코드 일 때 b는 a 의 오론쪽, a 아래 위치하는 구성이 됩니다.      
   [https://doc.qt.io/qt-6/qgraphicsanchorlayout.html](https://doc.qt.io/qt-6/qgraphicsanchorlayout.html) 사이트 에서 자세히 확인가능합니다. 
   #### QGridLayout 
   말 그대로 Grid 에 배치할 수 있는 Layout 입니다. HTML table 같은것을 연상하셔도 될것 같습니다.   많이 사용하는 부분이니 예제에서 상세히 확인해 보겠습니다. 
   #### QGroupBox
   타이틀과 격자 틀을 제공하는 Layout 입니다.    묶음을 표현할 때 사용할 수 있습니다. 

   #### QHBoxLayout , QVBoxLayout 
   QBoxLayou 과 유사하나, 수평선으로 정렬 혹은 수직방향으로 정렬을 제공 하는 Layout 입니다. 

   #### QLayout, QLayoutItem
   Resize 등 전체 사이즈 변경에 대한 처리 등을 관리하는 Base Class 입니다.   
   API 문서에는 다음과 같이 기재되어 있습니다.  
   This is an abstract base class inherited by the concrete classes QBoxLayout, QGridLayout, QFormLayout, and QStackedLayout.   
   위에서 살펴본 대다수의 Layout class 가 QLayout 을 상속 받았고, 해당 클래스는 abstract class 입니다.   
   제공하는 enum type 으로 enum	SizeConstraint { SetDefaultConstraint, SetFixedSize, SetMinimumSize, SetMaximumSize, SetMinAndMaxSize, SetNoConstraint }  이 있습니다. 
   개발자가 직접 Layout 을 만들고자 할 때 해당 클래스를 상속 받아 구현하면 될 것 같습니다.  
   QLayoutItem 은 QLayout 의 부모 클래스 입니다. 

   #### QSizePolicy 
   Layout 관련 수평, 수직, resizing 에 관련한 속성 관련한 class 입니다.  
   #### QSpacerItem 
   Layout 안에서 공백 관련한 클래스 입니다.  
   #### QStackedLayout 
   말 그대로 Stack 에 올려 놓고, 그중 한개만 보여주기 위한 Layout 입니다.  
   addItem(QLayoutItem *item) override 의 메소드에서 보듯 Layout 객체를 Stack 에 담을 대상으로 합니다.  
   #### QStackedWidget 
   위와 유사하나, addWidget(QWidget *widget) 의 함수를 제공합니다. QWidget 이 담을 수 있는 대상 입니다.
   #### QWidgetItem 
   API 에서는 개발자가 사용할 일은 별로 없다고 하네요 ... ^^ QLayoutItem 을 상속 받고 있습니다. 

   ### QT 기본 Layout 예제 입니다.  
   [https://doc.qt.io/qt-6/examples-layouts.html](https://doc.qt.io/qt-6/examples-layouts.html)  
   해당 예제는 Qt Creator 에서 소스를 제공하고 있기 때문에  주요 부분만 확인해 보겠습니다. 
   ``` c++
      // Copyright (C) 2016 The Qt Company Ltd.
      // SPDX-License-Identifier: LicenseRef-Qt-Commercial OR BSD-3-Clause

      #ifndef DIALOG_H
      #define DIALOG_H

      #include <QDialog>

      QT_BEGIN_NAMESPACE
      class QAction;
      class QDialogButtonBox;
      class QGroupBox;
      class QLabel;
      class QLineEdit;
      class QMenu;
      class QMenuBar;
      class QPushButton;
      class QTextEdit;
      QT_END_NAMESPACE

      //! [0]
      class Dialog : public QDialog
      {
         Q_OBJECT

      public:
         Dialog();

      private:
         void createMenu();
         void createHorizontalGroupBox();
         void createGridGroupBox();
         void createFormGroupBox();

         enum { NumGridRows = 3, NumButtons = 4 };

         QMenuBar *menuBar;
         QGroupBox *horizontalGroupBox;
         QGroupBox *gridGroupBox;
         QGroupBox *formGroupBox;
         QTextEdit *smallEditor;
         QTextEdit *bigEditor;
         QLabel *labels[NumGridRows];
         QLineEdit *lineEdits[NumGridRows];
         QPushButton *buttons[NumButtons];
         QDialogButtonBox *buttonBox;

         QMenu *fileMenu;
         QAction *exitAction;
      };
      //! [0]

      #endif // DIALOG_H

   ```
   구현 부위의 각 함수를 보면 다음과 같습니다. 
   메뉴 구성 영역입니다.    &F 는 단축키 설정을 하고 있습니다.   exitAction 에서 signal 을  &QAction::triggered 의 QAction 으로 구성후 QDialog 에서 accept slot 으로 받아 
   Exit Action 이 수행되도록 구성하고 있습니다. 
   ``` c++ 
      //! [6]
      void Dialog::createMenu()
      {
         menuBar = new QMenuBar;

         fileMenu = new QMenu(tr("&File"), this);
         exitAction = fileMenu->addAction(tr("E&xit"));
         menuBar->addMenu(fileMenu);

         connect(exitAction, &QAction::triggered, this, &QDialog::accept);
      }
      //! [6]

   ```
   Group Box 와 QHBoxLayout 을 구성한후 layout 에 담고, GroupBox 에 담는 구조 입니다.  
   ``` c++

      //! [7]
      void Dialog::createHorizontalGroupBox()
      {
         horizontalGroupBox = new QGroupBox(tr("Horizontal layout"));
         QHBoxLayout *layout = new QHBoxLayout;

         for (int i = 0; i < NumButtons; ++i) {
            buttons[i] = new QPushButton(tr("Button %1").arg(i + 1));
            layout->addWidget(buttons[i]);
         }
         horizontalGroupBox->setLayout(layout);
      }
      //! [7]

   ```
   동일하게 Group Box 를 사용하는데 이번에는 QGridLayout 에 담는 예제 입니다. 
   QTextEdit 를 담는 메소드는 layout->addWidget(smallEditor, 0, 2, 4, 1); 입니다. 첫번째 객체, 두번째 row index, 세번째 column index 네번째 4는 row 4칸의 의미 입니다. 
   다섯번째 1은 column 1칸 입니다.     setColumnStretch 의 첫번째 인자는 column index 이고 두번째는 상대값입니다.   default 가 0이니 첫번째는 0, 두번째는 10, 세번째가 20 입니다.  
   상대적으로 마지막 cell 인 QTextEdit 가 가장 크게 표현됩니다. 
   ``` c++
      //! [8]
      void Dialog::createGridGroupBox()
      {
         gridGroupBox = new QGroupBox(tr("Grid layout"));
      //! [8]
         QGridLayout *layout = new QGridLayout;

      //! [9]
         for (int i = 0; i < NumGridRows; ++i) {
            labels[i] = new QLabel(tr("Line %1:").arg(i + 1));
            lineEdits[i] = new QLineEdit;
            layout->addWidget(labels[i], i + 1, 0);
            layout->addWidget(lineEdits[i], i + 1, 1);
         }

      //! [9] //! [10]
         smallEditor = new QTextEdit;
         smallEditor->setPlainText(tr("This widget takes up about two thirds of the "
                                       "grid layout."));
         layout->addWidget(smallEditor, 0, 2, 4, 1);
      //! [10]

      //! [11]
         layout->setColumnStretch(1, 10);
         layout->setColumnStretch(2, 20);
         gridGroupBox->setLayout(layout);
      }
      //! [11]
   ```
   위 사이트 내용중 일부를 조금 살펴 보았습니다.   QT 에서의 Lay out 은 약간씩 차이가 있어도 대략 위에 기재한 정도로 사용되고 있는 것 같습니다.    
   

# PYQT6

   ### PYQT6 Layout Class Link
   [https://www.riverbankcomputing.com/static/Docs/PyQt6/](https://www.riverbankcomputing.com/static/Docs/PyQt6/)   
   [https://www.riverbankcomputing.com/static/Docs/PyQt6/search.html?q=QLayout](https://www.riverbankcomputing.com/static/Docs/PyQt6/search.html?q=QLayout)   
   PYQT6를 구성한 riverbankcomputing 에서 제공하는 내용입니다. QLayout 으로 검색하면 해당 내용을 확인할 수 있습니다.    QT 의 class 들을 Python 으로 매핑해 놓았음을 설명하고 있습니다. 
   QT 에서 설명한 클래스 들을 활용하고 있기 때문에 별도로 정리하지는 않겠습니다.    
   탭을 클릭하면 선택한 항목이 출력되는 예제 입니다.   

   ``` python

      from PyQt6.QtWidgets import QWidget
      from PyQt6.QtGui import QPalette, QColor

      class Color(QWidget):
         def __init__(self, color):
            super(Color, self).__init__()
            self.setAutoFillBackground(True)
            palette = self.palette()
            palette.setColor(QPalette.ColorRole.Window, QColor(color))
            self.setPalette(palette)
   ```

   ``` python

      import sys

      from PyQt6.QtCore import Qt 
      from PyQt6.QtWidgets import (
         QApplication, 
         QHBoxLayout, 
         QLabel,
         QMainWindow, 
         QPushButton, 
         QStackedLayout,
         QVBoxLayout, 
         QWidget, 
      )

      from color import Color

      class MainWindow(QMainWindow):
         def __init__(self):
            super().__init__()

            self.setWindowTitle("My App")

            pagelayout = QVBoxLayout();
            button_layout = QHBoxLayout();
            self.stacklayout = QStackedLayout();

            pagelayout.addLayout(button_layout)
            pagelayout.addLayout(self.stacklayout)


            btn = QPushButton("red")
            btn.pressed.connect(self.activate_tab_1)
            button_layout.addWidget(btn)
            self.stacklayout.addWidget(Color("red"))

            btn = QPushButton("green")
            btn.pressed.connect(self.activate_tab_2)
            button_layout.addWidget(btn)
            self.stacklayout.addWidget(Color("green"))

            btn = QPushButton("blue")
            btn.pressed.connect(self.activate_tab_3)
            button_layout.addWidget(btn)
            self.stacklayout.addWidget(Color("blue"))

            widget = QWidget();
            widget.setLayout(pagelayout)
            self.setCentralWidget(widget)

         def activate_tab_1(self):
            self.stacklayout.setCurrentIndex(0)

         def activate_tab_2(self):
            self.stacklayout.setCurrentIndex(1)

         def activate_tab_3(self):
            self.stacklayout.setCurrentIndex(2)


      app = QApplication(sys.argv)

      window = MainWindow()
      window.show();

      app.exec()        

   ```


# SWING
   ### SWING Layout Class Link
   [https://docs.oracle.com/en/java/javase/17/docs/api/java.desktop/module-summary.html](https://docs.oracle.com/en/java/javase/17/docs/api/java.desktop/module-summary.html)
   Swing 은 Java 에서 GUI 를 구성하기 위해 제공한 두번째 framework 입니다.  java 1.2 version 에서 처음 제공하였고, 그 이전에는 AWT 라는 toolkit 처럼 제공하는 api 모음이 있었습니다.    
   그러다 보니 Class 구조가 AWT 와 SWING 에서 공유하는 부분이 있었고, class tree 구조가 약간 혼합되어 있는 느낌도 있었습니다.    
   API 는 참조로 확인해 보면 될 것 같고, 아래의 사이트에서 예제와, Layout 에 대한 설명을 하고 있습니다.   
   이번 글에서는 Layout 만 확인해 보도록 하겠습니다.  

   ### Layout Sample URL 
   [https://docs.oracle.com/javase/tutorial/uiswing/layout/visual.html](https://docs.oracle.com/javase/tutorial/uiswing/layout/visual.html)
   #### BorderLayout 
   Border Layout 은 동서남북, 그리고 중앙에 배치할 수 있는 Layout 입니다.   Center 는 나머지 전체 Size 를 담게 됩니다.    
   API 에서 더 상세히 위치 지정할 수 있는 항목을 정의하고 있습니다.    static final String 으로 위치를 지정할 수 있도록 제공하고 있습니다. 
   (AFTER_LAST_LINE, AFTER_LINE_ENDS, BEFORE_FIRST_LINE, BEFORE_LINE_BEGINS, CENTER, EAST, LINE_END, LINE_START, NORTH, PAGE_END, PAGE_START, SOUTH, WEST )    
   JFrame, JApplet, JDialog 등에서 ContentPane 의 기본 Layout 으로 사용되고 있습니다.  
   #### BoxLayout 
   수직 혹은 수평으로 배열할 수 있는 Layout 입니다.    
   (X_AXIS, Y_AXIS, LINE_AXIS, PAGE_AXIS ) 는 가로 혹은 세로로 지정된 내용으로 담겨진 객체들을 배열할 수 있습니다.
   #### CardLayout 
   QT 의 QStackedLayout 과 유사하게 여러개의 화면중 하나를 표현할 수 있는 Layout 입니다.  사용법은 조금 차이가 있지만, 거의 유사한 기능을 하는 Layout 입니다. 
   #### FlowLayout
   등록한 순서대로 출력하기 위한 Layout 입니다.    가로 방향으로 길이가 넘쳐나면 다음 아랫줄로 이동하여 표현합니다.  
   #### GridBagLayout 
   아래 GridLayout 보다 세부적인 조정이 더 가능한 Layout 입니다.   Cell별 크기 등, GridLayout 보다 강력하지만, 사용하기는 조금 더 어렵습니다. 
   #### GridLayout 
   격자 형태로 UI 를 구성하기 위한 Layout 입니다.  
   #### GroupLayout 
   세로 혹은 가로로 묶음이 필요한 영역을 구성하기 위한 Layout 입니다.   swing 에서는 박스와 title 등은 TitledBorder 클래스에서 지원하고 있습니다.  
   #### SpringLayout 
   다른 component 의 edge 에 영향을 받아서 정렬하는 Low level Layout 입니다.  간격등을 기준으로 구성할 때 편할 것 같습니다. 

   사이트에서 제공하는 간단한 예제 입니다. 
   아래의 예제에서 BorderLayout 사용을 어떻게 한다라는 것을 확인할 수 있습니다.   Swing 애서는 조금더 살펴 보아야 할 것이, 
   ``` java 
            //Schedule a job for the event dispatch thread:
            //creating and showing this application's GUI.
            javax.swing.SwingUtilities.invokeLater(new Runnable() {
                  public void run() {
                     createAndShowGUI();
                  }
            });
   ```
   의 구문 입니다. 향후 글이 이어지다 보면 다시 언급되겠지만, UI 가 Thread Safe 하지 않습니다.   말이 조금 어려운데요, GUI 를 어떤 순으로 그려야 할 경우, 각 Event 에서 
   변경을 호출하면 경우에 따라서 원하는 UI 변경을 확인하지 못할 수도 있습니다.   그것을 방지하고자, UI 갱신등을 관리하는 invokeLater 등의 메소드로 순차적인 갱신이 가능하도록 
   구성하고 있습니다.   Swing 이 1.2 version 으로 발표되고, 1.4 정도에 보완되고, 더이상 크게 진전 없이 JFX 로 API 구조가 변경된 것도 이런 원인이 있는 것은 아닌지 하는 지극히 
   개인적인 생각도 드는 부분입니다.  ^^^ 


   ``` java 

      /*
      * Copyright (c) 1995, 2008, Oracle and/or its affiliates. All rights reserved.
      *
      * Redistribution and use in source and binary forms, with or without
      * modification, are permitted provided that the following conditions
      * are met:
      *
      *   - Redistributions of source code must retain the above copyright
      *     notice, this list of conditions and the following disclaimer.
      *
      *   - Redistributions in binary form must reproduce the above copyright
      *     notice, this list of conditions and the following disclaimer in the
      *     documentation and/or other materials provided with the distribution.
      *
      *   - Neither the name of Oracle or the names of its
      *     contributors may be used to endorse or promote products derived
      *     from this software without specific prior written permission.
      *
      * THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS
      * IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO,
      * THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR
      * PURPOSE ARE DISCLAIMED.  IN NO EVENT SHALL THE COPYRIGHT OWNER OR
      * CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL,
      * EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
      * PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR
      * PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF
      * LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING
      * NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
      * SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
      */
      
      package layout;
      
      /*
      * BorderLayoutDemo.java
      *
      */
      import javax.swing.*;
      import java.awt.BorderLayout;
      import java.awt.Container;
      import java.awt.Dimension;
      
      public class BorderLayoutDemo {
         public static boolean RIGHT_TO_LEFT = false;
         
         public static void addComponentsToPane(Container pane) {
               
            if (!(pane.getLayout() instanceof BorderLayout)) {
                  pane.add(new JLabel("Container doesn't use BorderLayout!"));
                  return;
            }
               
            if (RIGHT_TO_LEFT) {
                  pane.setComponentOrientation(
                        java.awt.ComponentOrientation.RIGHT_TO_LEFT);
            }
               
            JButton button = new JButton("Button 1 (PAGE_START)");
            pane.add(button, BorderLayout.PAGE_START);
               
            //Make the center component big, since that's the
            //typical usage of BorderLayout.
            button = new JButton("Button 2 (CENTER)");
            button.setPreferredSize(new Dimension(200, 100));
            pane.add(button, BorderLayout.CENTER);
               
            button = new JButton("Button 3 (LINE_START)");
            pane.add(button, BorderLayout.LINE_START);
               
            button = new JButton("Long-Named Button 4 (PAGE_END)");
            pane.add(button, BorderLayout.PAGE_END);
               
            button = new JButton("5 (LINE_END)");
            pane.add(button, BorderLayout.LINE_END);
         }
         
         /**
         * Create the GUI and show it.  For thread safety,
         * this method should be invoked from the
         * event dispatch thread.
         */
         private static void createAndShowGUI() {
               
            //Create and set up the window.
            JFrame frame = new JFrame("BorderLayoutDemo");
            frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
            //Set up the content pane.
            addComponentsToPane(frame.getContentPane());
            //Use the content pane's default BorderLayout. No need for
            //setLayout(new BorderLayout());
            //Display the window.
            frame.pack();
            frame.setVisible(true);
         }
         
         public static void main(String[] args) {
            /* Use an appropriate Look and Feel */
            try {
                  //UIManager.setLookAndFeel("com.sun.java.swing.plaf.windows.WindowsLookAndFeel");
                  UIManager.setLookAndFeel("javax.swing.plaf.metal.MetalLookAndFeel");
            } catch (UnsupportedLookAndFeelException ex) {
                  ex.printStackTrace();
            } catch (IllegalAccessException ex) {
                  ex.printStackTrace();
            } catch (InstantiationException ex) {
                  ex.printStackTrace();
            } catch (ClassNotFoundException ex) {
                  ex.printStackTrace();
            }
            /* Turn off metal's use bold fonts */
            UIManager.put("swing.boldMetal", Boolean.FALSE);
               
            //Schedule a job for the event dispatch thread:
            //creating and showing this application's GUI.
            javax.swing.SwingUtilities.invokeLater(new Runnable() {
                  public void run() {
                     createAndShowGUI();
                  }
            });
         }
      }
   ```

# JFX 
   ### JFX Layout Class Link 
   [https://openjfx.io/javadoc/11/javafx.graphics/javafx/scene/layout/package-tree.html](https://openjfx.io/javadoc/11/javafx.graphics/javafx/scene/layout/package-tree.html)   

   #### AnchorPane 
   Top, Left, Bottom, Right  구석에 Layout을 배치할 수 있는 Layout 입니다.    
   아래 코드에서 보듯 사용하는 방법은 다음과 같습니다.     
   ``` java 
      /*
      * Creates an anchor pane using the provided grid and an HBox with buttons
      * 
      * @param grid Grid to anchor to the top of the anchor pane
      */
         private AnchorPane addAnchorPane(GridPane grid) {
      
               AnchorPane anchorpane = new AnchorPane();
               
               Button buttonSave = new Button("Save");
               Button buttonCancel = new Button("Cancel");
      
               HBox hb = new HBox();
               hb.setPadding(new Insets(0, 10, 10, 10));
               hb.setSpacing(10);
               hb.getChildren().addAll(buttonSave, buttonCancel);
      
               anchorpane.getChildren().addAll(grid,hb);
               // Anchor buttons to bottom right, anchor grid to top
               AnchorPane.setBottomAnchor(hb, 8.0);
               AnchorPane.setRightAnchor(hb, 5.0);
               AnchorPane.setTopAnchor(grid, 10.0);
      
               return anchorpane;
         }
   ``` 

   소스의 HBox 는 Bottom, Right 으로 구성되어 있으니, 오른쪽 하단에 위치하게 됩니다.  Grid 는 상단에 위치합니다.  



   #### BorderPane 
   Swing 의 BorderLayout 과 유사한 Layout 입니다.    Top, Left, Right, Bottom 에 둘수 있고, Center 는 나머지 전체를 차지 합니다. 
   예시된 소스에서 보듯 AnchorPane 이 Center 에 적용되어 있습니다.   대부분의 영역을 차지하게 될 것입니다.  

   ``` java
               BorderPane border = new BorderPane();
               
               HBox hbox = addHBox();
               border.setTop(hbox);
               border.setLeft(addVBox());
               
      // Add a stack to the HBox in the top region
               addStackPane(hbox);  
               
      // To see only the grid in the center, uncomment the following statement
      // comment out the setCenter() call farther down        
      //        border.setCenter(addGridPane());
               
      // Choose either a TilePane or FlowPane for right region and comment out the
      // one you aren't using        
               border.setRight(addFlowPane());
      //        border.setRight(addTilePane());
               
      // To see only the grid in the center, comment out the following statement
      // If both setCenter() calls are executed, the anchor pane from the second
      // call replaces the grid from the first call        
               border.setCenter(addAnchorPane(addGridPane()));

   ``` 

   #### FlowPane 
   Swing 의 FlowLayout 과 유사합니다.   적재되는 데로 표현되고, 넘치면 아래로 밀리는 구성 입니다.  

   아래의 소스에서 보면  flow.setPrefWrapLength(170); // preferred width allows for two columns 이런 구문이 있습니다. 
   이미지 사이즈가 width 81px 정도 이니 두개가 들어갈 수 있는 너비를 셋팅한 것입니다. 

   ``` java
      /*
      * Creates a horizontal flow pane with eight icons in four rows
      */
         private FlowPane addFlowPane() {
      
               FlowPane flow = new FlowPane();
               flow.setPadding(new Insets(5, 0, 5, 0));
               flow.setVgap(4);
               flow.setHgap(4);
               flow.setPrefWrapLength(170); // preferred width allows for two columns
               flow.setStyle("-fx-background-color: DAE6F3;");
      
               ImageView pages[] = new ImageView[8];
               for (int i=0; i<8; i++) {
                  pages[i] = new ImageView(
                           new Image(LayoutSample.class.getResourceAsStream(
                           "graphics/chart_"+(i+1)+".png")));
                  flow.getChildren().add(pages[i]);
               }
      
               return flow;
         }

   ```
   #### GridPane 
   격자 모양의 Layout 이라고 생각하면 될 것 같습니다.  어떻게 보면 HTML TABLE 구조와 유사한 구조라고 보아도 될것 같습니다. 
   아래는 예제 코드 입니다. 
   ``` java 

      /*
      * Creates a grid for the center region with four columns and three rows
      */
         private GridPane addGridPane() {
      
               GridPane grid = new GridPane();
               grid.setHgap(10);
               grid.setVgap(10);
               grid.setPadding(new Insets(0, 10, 0, 10));
      
               // Category in column 2, row 1
               Text category = new Text("Sales:");
               category.setFont(Font.font("Arial", FontWeight.BOLD, 20));
               grid.add(category, 1, 0); 
               
               // Title in column 3, row 1
               Text chartTitle = new Text("Current Year");
               chartTitle.setFont(Font.font("Arial", FontWeight.BOLD, 20));
               grid.add(chartTitle, 2, 0);
               
               // Subtitle in columns 2-3, row 2
               Text chartSubtitle = new Text("Goods and Services");
               grid.add(chartSubtitle, 1, 1, 2, 1);
               
               // House icon in column 1, rows 1-2
               ImageView imageHouse = new ImageView(
                           new Image(LayoutSample.class.getResourceAsStream("graphics/house.png")));
               grid.add(imageHouse, 0, 0, 1, 2);
      
               // Left label in column 1 (bottom), row 3
               Text goodsPercent = new Text("Goods\n80%");
               GridPane.setValignment(goodsPercent, VPos.BOTTOM);
               grid.add(goodsPercent, 0, 2);
               
               // Chart in columns 2-3, row 3
               ImageView imageChart = new ImageView(
                           new Image(LayoutSample.class.getResourceAsStream("graphics/piechart.png")));
               grid.add(imageChart, 1, 2, 2, 1);
               
               // Right label in column 4 (top), row 3
               Text servicesPercent = new Text("Services\n20%");
               GridPane.setValignment(servicesPercent, VPos.TOP);
               grid.add(servicesPercent, 3, 2);
               
      //        grid.setGridLinesVisible(true);
               return grid;
         }

   ```

   #### HBox , VBox 
   이름에서 보듯 가로 세로로 담을 수 있는 Layout 입니다.  

   ``` java 
      /*
      * Creates an HBox with two buttons for the top region
      */
         
         private HBox addHBox() {
      
               HBox hbox = new HBox();
               hbox.setPadding(new Insets(15, 12, 15, 12));
               hbox.setSpacing(10);   // Gap between nodes
               hbox.setStyle("-fx-background-color: #336699;");
      
               Button buttonCurrent = new Button("Current");
               buttonCurrent.setPrefSize(100, 20);
      
               Button buttonProjected = new Button("Projected");
               buttonProjected.setPrefSize(100, 20);
               
               hbox.getChildren().addAll(buttonCurrent, buttonProjected);
               
               return hbox;
         }
         
      /*
      * Creates a VBox with a list of links for the left region
      */
         private VBox addVBox() {
               
               VBox vbox = new VBox();
               vbox.setPadding(new Insets(10)); // Set all sides to 10
               vbox.setSpacing(8);              // Gap between nodes
      
               Text title = new Text("Data");
               title.setFont(Font.font("Arial", FontWeight.BOLD, 14));
               vbox.getChildren().add(title);
               
               Hyperlink options[] = new Hyperlink[] {
                  new Hyperlink("Sales"),
                  new Hyperlink("Marketing"),
                  new Hyperlink("Distribution"),
                  new Hyperlink("Costs")};
      
               for (int i=0; i<4; i++) {
                  // Add offset to left side to indent from title
                  VBox.setMargin(options[i], new Insets(0, 0, 0, 8));
                  vbox.getChildren().add(options[i]);
               }
               
               return vbox;
         }


   ```

   #### StackPane 
   Swing 의 CardLayout 과 유사합니다. QT 에서 제공하는 QStackedLayout 과 유사합니다.      
   언어는 달라도 사용자의 편의성을 보장하기 위해서 API 단에서 유사한 Layout 들을 제공하고 있는 것 같습니다.     

   ``` java 

      /*
      * Uses a stack pane to create a help icon and adds it to the right side of an HBox
      * 
      * @param hb HBox to add the stack to
      */
         private void addStackPane(HBox hb) {
      
               StackPane stack = new StackPane();
               Rectangle helpIcon = new Rectangle(30.0, 25.0);
               helpIcon.setFill(new LinearGradient(0,0,0,1, true, CycleMethod.NO_CYCLE,
                  new Stop[]{
                  new Stop(0,Color.web("#4977A3")),
                  new Stop(0.5, Color.web("#B0C6DA")),
                  new Stop(1,Color.web("#9CB6CF")),}));
               helpIcon.setStroke(Color.web("#D0E6FA"));
               helpIcon.setArcHeight(3.5);
               helpIcon.setArcWidth(3.5);
               
               Text helpText = new Text("?");
               helpText.setFont(Font.font("Verdana", FontWeight.BOLD, 18));
               helpText.setFill(Color.WHITE);
               helpText.setStroke(Color.web("#7080A0")); 
               
               stack.getChildren().addAll(helpIcon, helpText);
               stack.setAlignment(Pos.CENTER_RIGHT);
               // Add offset to right for question mark to compensate for RIGHT 
               // alignment of all nodes
               StackPane.setMargin(helpText, new Insets(0, 10, 0, 0));
               
               hb.getChildren().add(stack);
               HBox.setHgrow(stack, Priority.ALWAYS);
                     
         }

   ``` 

   #### TilePane 
   수평 수직으로 타일처럼 배치할 수 있는 Layout 입니다.  구성하는 전체의 Layout 크기에 따라 가변적으로 구성될 수 있습니다.  
   아래의 예에서  tile.setPrefColumns(2); 의 메소드 호출은 column 이 2개로 유지되면 좋겠다는 선언이고, 전체 크기가 그에 적당하면 2개의 Column 이 표기되나, 
   사이즈가 변경되면 적절한 크기에 맞게 변형됩니다.   

   ``` java 

      /*
      * Creates a horizontal (default) tile pane with eight icons in four rows
      */
         private TilePane addTilePane() {
               
               TilePane tile = new TilePane();
               tile.setPadding(new Insets(5, 0, 5, 0));
               tile.setVgap(4);
               tile.setHgap(4);
               tile.setPrefColumns(2);
               tile.setStyle("-fx-background-color: DAE6F3;");
      
               ImageView pages[] = new ImageView[8];
               for (int i=0; i<8; i++) {
                  pages[i] = new ImageView(
                           new Image(LayoutSample.class.getResourceAsStream(
                           "graphics/chart_"+(i+1)+".png")));
                  tile.getChildren().add(pages[i]);
               }
      
               return tile;
         }

   ```

   #### Layout Sample URL 
   [https://docs.oracle.com/javafx/2/layout/jfxpub-layout.htm](https://docs.oracle.com/javafx/2/layout/jfxpub-layout.htm)
   [https://docs.oracle.com/javafx/2/layout/builtin_layouts.htm](https://docs.oracle.com/javafx/2/layout/builtin_layouts.htm) 

   위에 사용하였던 소스 전체 입니다.    
   위 예제 사이트에서 다운로드 받아 사용할 수 있습니다.    출처를 밝히기 위해 전체를 게시 하였습니다.  

   ``` java

      /*
      * Copyright (c) 2012, 2013 Oracle and/or its affiliates.
      * All rights reserved. Use is subject to license terms.
      *
      * This file is available and licensed under the following license:
      *
      * Redistribution and use in source and binary forms, with or without
      * modification, are permitted provided that the following conditions
      * are met:
      *
      *  - Redistributions of source code must retain the above copyright
      *    notice, this list of conditions and the following disclaimer.
      *  - Redistributions in binary form must reproduce the above copyright
      *    notice, this list of conditions and the following disclaimer in
      *    the documentation and/or other materials provided with the distribution.
      *  - Neither the name of Oracle nor the names of its
      *    contributors may be used to endorse or promote products derived
      *    from this software without specific prior written permission.
      *
      * THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
      * "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
      * LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
      * A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
      * OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
      * SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
      * LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
      * DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
      * THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
      * (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
      * OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
      */

                                 

      //package layoutsample;


      import javafx.application.Application;
      import javafx.geometry.Insets;
      import javafx.geometry.Pos;
      import javafx.geometry.VPos;
      import javafx.scene.Scene;
      import javafx.scene.control.Button;
      import javafx.scene.control.Hyperlink;
      import javafx.scene.image.Image;
      import javafx.scene.image.ImageView;
      import javafx.scene.layout.AnchorPane;
      import javafx.scene.layout.BorderPane;
      import javafx.scene.layout.FlowPane;
      import javafx.scene.layout.GridPane;
      import javafx.scene.layout.HBox;
      import javafx.scene.layout.Priority;
      import javafx.scene.layout.StackPane;
      import javafx.scene.layout.TilePane;
      import javafx.scene.layout.VBox;
      import javafx.scene.paint.Color;
      import javafx.scene.paint.CycleMethod;
      import javafx.scene.paint.LinearGradient;
      import javafx.scene.paint.Stop;
      import javafx.scene.shape.Rectangle;
      import javafx.scene.text.Font;
      import javafx.scene.text.FontWeight;
      import javafx.scene.text.Text;
      import javafx.stage.Stage;
      
      
      /**
      * Sample application that shows examples of the different layout panes
      * provided by the JavaFX layout API.
      * The resulting UI is for demonstration purposes only and is not interactive.
      */
      
      
      public class LayoutSample extends Application {
         
      
         /**
            * @param args the command line arguments
            */
         public static void main(String[] args) {
               launch(LayoutSample.class, args);
         }
         
         @Override
         public void start(Stage stage) {
      
      
      // Use a border pane as the root for scene
               BorderPane border = new BorderPane();
               
               HBox hbox = addHBox();
               border.setTop(hbox);
               border.setLeft(addVBox());
               
      // Add a stack to the HBox in the top region
               addStackPane(hbox);  
               
      // To see only the grid in the center, uncomment the following statement
      // comment out the setCenter() call farther down        
      //        border.setCenter(addGridPane());
               
      // Choose either a TilePane or FlowPane for right region and comment out the
      // one you aren't using        
               border.setRight(addFlowPane());
      //        border.setRight(addTilePane());
               
      // To see only the grid in the center, comment out the following statement
      // If both setCenter() calls are executed, the anchor pane from the second
      // call replaces the grid from the first call        
               border.setCenter(addAnchorPane(addGridPane()));
      
               Scene scene = new Scene(border);
               stage.setScene(scene);
               stage.setTitle("Layout Sample");
               stage.show();
         }
      
      /*
      * Creates an HBox with two buttons for the top region
      */
         
         private HBox addHBox() {
      
               HBox hbox = new HBox();
               hbox.setPadding(new Insets(15, 12, 15, 12));
               hbox.setSpacing(10);   // Gap between nodes
               hbox.setStyle("-fx-background-color: #336699;");
      
               Button buttonCurrent = new Button("Current");
               buttonCurrent.setPrefSize(100, 20);
      
               Button buttonProjected = new Button("Projected");
               buttonProjected.setPrefSize(100, 20);
               
               hbox.getChildren().addAll(buttonCurrent, buttonProjected);
               
               return hbox;
         }
         
      /*
      * Creates a VBox with a list of links for the left region
      */
         private VBox addVBox() {
               
               VBox vbox = new VBox();
               vbox.setPadding(new Insets(10)); // Set all sides to 10
               vbox.setSpacing(8);              // Gap between nodes
      
               Text title = new Text("Data");
               title.setFont(Font.font("Arial", FontWeight.BOLD, 14));
               vbox.getChildren().add(title);
               
               Hyperlink options[] = new Hyperlink[] {
                  new Hyperlink("Sales"),
                  new Hyperlink("Marketing"),
                  new Hyperlink("Distribution"),
                  new Hyperlink("Costs")};
      
               for (int i=0; i<4; i++) {
                  // Add offset to left side to indent from title
                  VBox.setMargin(options[i], new Insets(0, 0, 0, 8));
                  vbox.getChildren().add(options[i]);
               }
               
               return vbox;
         }
      
      /*
      * Uses a stack pane to create a help icon and adds it to the right side of an HBox
      * 
      * @param hb HBox to add the stack to
      */
         private void addStackPane(HBox hb) {
      
               StackPane stack = new StackPane();
               Rectangle helpIcon = new Rectangle(30.0, 25.0);
               helpIcon.setFill(new LinearGradient(0,0,0,1, true, CycleMethod.NO_CYCLE,
                  new Stop[]{
                  new Stop(0,Color.web("#4977A3")),
                  new Stop(0.5, Color.web("#B0C6DA")),
                  new Stop(1,Color.web("#9CB6CF")),}));
               helpIcon.setStroke(Color.web("#D0E6FA"));
               helpIcon.setArcHeight(3.5);
               helpIcon.setArcWidth(3.5);
               
               Text helpText = new Text("?");
               helpText.setFont(Font.font("Verdana", FontWeight.BOLD, 18));
               helpText.setFill(Color.WHITE);
               helpText.setStroke(Color.web("#7080A0")); 
               
               stack.getChildren().addAll(helpIcon, helpText);
               stack.setAlignment(Pos.CENTER_RIGHT);
               // Add offset to right for question mark to compensate for RIGHT 
               // alignment of all nodes
               StackPane.setMargin(helpText, new Insets(0, 10, 0, 0));
               
               hb.getChildren().add(stack);
               HBox.setHgrow(stack, Priority.ALWAYS);
                     
         }
      
      /*
      * Creates a grid for the center region with four columns and three rows
      */
         private GridPane addGridPane() {
      
               GridPane grid = new GridPane();
               grid.setHgap(10);
               grid.setVgap(10);
               grid.setPadding(new Insets(0, 10, 0, 10));
      
               // Category in column 2, row 1
               Text category = new Text("Sales:");
               category.setFont(Font.font("Arial", FontWeight.BOLD, 20));
               grid.add(category, 1, 0); 
               
               // Title in column 3, row 1
               Text chartTitle = new Text("Current Year");
               chartTitle.setFont(Font.font("Arial", FontWeight.BOLD, 20));
               grid.add(chartTitle, 2, 0);
               
               // Subtitle in columns 2-3, row 2
               Text chartSubtitle = new Text("Goods and Services");
               grid.add(chartSubtitle, 1, 1, 2, 1);
               
               // House icon in column 1, rows 1-2
               ImageView imageHouse = new ImageView(
                           new Image(LayoutSample.class.getResourceAsStream("graphics/house.png")));
               grid.add(imageHouse, 0, 0, 1, 2);
      
               // Left label in column 1 (bottom), row 3
               Text goodsPercent = new Text("Goods\n80%");
               GridPane.setValignment(goodsPercent, VPos.BOTTOM);
               grid.add(goodsPercent, 0, 2);
               
               // Chart in columns 2-3, row 3
               ImageView imageChart = new ImageView(
                           new Image(LayoutSample.class.getResourceAsStream("graphics/piechart.png")));
               grid.add(imageChart, 1, 2, 2, 1);
               
               // Right label in column 4 (top), row 3
               Text servicesPercent = new Text("Services\n20%");
               GridPane.setValignment(servicesPercent, VPos.TOP);
               grid.add(servicesPercent, 3, 2);
               
      //        grid.setGridLinesVisible(true);
               return grid;
         }
      
      /*
      * Creates a horizontal flow pane with eight icons in four rows
      */
         private FlowPane addFlowPane() {
      
               FlowPane flow = new FlowPane();
               flow.setPadding(new Insets(5, 0, 5, 0));
               flow.setVgap(4);
               flow.setHgap(4);
               flow.setPrefWrapLength(170); // preferred width allows for two columns
               flow.setStyle("-fx-background-color: DAE6F3;");
      
               ImageView pages[] = new ImageView[8];
               for (int i=0; i<8; i++) {
                  pages[i] = new ImageView(
                           new Image(LayoutSample.class.getResourceAsStream(
                           "graphics/chart_"+(i+1)+".png")));
                  flow.getChildren().add(pages[i]);
               }
      
               return flow;
         }
         
      /*
      * Creates a horizontal (default) tile pane with eight icons in four rows
      */
         private TilePane addTilePane() {
               
               TilePane tile = new TilePane();
               tile.setPadding(new Insets(5, 0, 5, 0));
               tile.setVgap(4);
               tile.setHgap(4);
               tile.setPrefColumns(2);
               tile.setStyle("-fx-background-color: DAE6F3;");
      
               ImageView pages[] = new ImageView[8];
               for (int i=0; i<8; i++) {
                  pages[i] = new ImageView(
                           new Image(LayoutSample.class.getResourceAsStream(
                           "graphics/chart_"+(i+1)+".png")));
                  tile.getChildren().add(pages[i]);
               }
      
               return tile;
         }
      
      /*
      * Creates an anchor pane using the provided grid and an HBox with buttons
      * 
      * @param grid Grid to anchor to the top of the anchor pane
      */
         private AnchorPane addAnchorPane(GridPane grid) {
      
               AnchorPane anchorpane = new AnchorPane();
               
               Button buttonSave = new Button("Save");
               Button buttonCancel = new Button("Cancel");
      
               HBox hb = new HBox();
               hb.setPadding(new Insets(0, 10, 10, 10));
               hb.setSpacing(10);
               hb.getChildren().addAll(buttonSave, buttonCancel);
      
               anchorpane.getChildren().addAll(grid,hb);
               // Anchor buttons to bottom right, anchor grid to top
               AnchorPane.setBottomAnchor(hb, 8.0);
               AnchorPane.setRightAnchor(hb, 5.0);
               AnchorPane.setTopAnchor(grid, 10.0);
      
               return anchorpane;
         }
      }
   ```

