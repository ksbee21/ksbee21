---
title: "QT PYQT  SWING JFX Layout 기초 2"
date: 2023-01-29T18:26:18+09:00
draft: true
tags : ["CrossPlatform","Application","Language","JAVA", "QT","PYQT6","SWING","OpenJFX"]
topics : []
description : "각 언어별 예제 중심 정리"
---

# QT 

   ### QT 사이트 Layout 예제중에서 ...
   사이트에서 제공하는 예제중 GUI 를 구성할 때 많이 사용할 것 같은 2개의 예제를 확인해 보겠습니다. 

   #### Border Layout Sample 
   [https://doc.qt.io/qt-6/qtwidgets-layouts-borderlayout-example.html](https://doc.qt.io/qt-6/qtwidgets-layouts-borderlayout-example.html)    
   이 예제는 Qt Creator에서 소스를 확인할 수 있습니다. Swing, JFX 등에서 유사한 Layout 을 제공 하고 있고, QT 에서 사용자 Layout 을 직접 구성할 때 어떻게 만들어야 하는지를  
   설명하고 있기 때문에 QT 내부에서 어떤 작업이 진행되었는지 확인하기 좋은 예제 같습니다. QT 예서 예제로 제공하고 있는 소스라 QT 를 설치하면 확인해 볼 수 있는 소스지만,  정리를 위해 필요한 부분을 
   나열하고 살펴보도록 하겠습니다. 

   ``` c++
      // Copyright (C) 2016 The Qt Company Ltd.
      // SPDX-License-Identifier: LicenseRef-Qt-Commercial OR BSD-3-Clause

      #ifndef BORDERLAYOUT_H
      #define BORDERLAYOUT_H

      #include <QLayout>
      #include <QRect>

      class BorderLayout : public QLayout
      {
      public:
         enum Position { West, North, South, East, Center };

         explicit BorderLayout(QWidget *parent, const QMargins &margins = QMargins(), int spacing = -1);
         BorderLayout(int spacing = -1);
         ~BorderLayout();

         void addItem(QLayoutItem *item) override;
         void addWidget(QWidget *widget, Position position);
         Qt::Orientations expandingDirections() const override;
         bool hasHeightForWidth() const override;
         int count() const override;
         QLayoutItem *itemAt(int index) const override;
         QSize minimumSize() const override;
         void setGeometry(const QRect &rect) override;
         QSize sizeHint() const override;
         QLayoutItem *takeAt(int index) override;

         void add(QLayoutItem *item, Position position);

      private:
         struct ItemWrapper
         {
            ItemWrapper(QLayoutItem *i, Position p) {
                  item = i;
                  position = p;
            }

            QLayoutItem *item;
            Position position;
         };

         enum SizeType { MinimumSize, SizeHint };
         QSize calculateSize(SizeType sizeType) const;

         QList<ItemWrapper *> list;
      };

      #endif // BORDERLAYOUT_H

   ```

   ``` c++

      // Copyright (C) 2016 The Qt Company Ltd.
      // SPDX-License-Identifier: LicenseRef-Qt-Commercial OR BSD-3-Clause

      #include "borderlayout.h"

      BorderLayout::BorderLayout(QWidget *parent, const QMargins &margins, int spacing)
         : QLayout(parent)
      {
         setContentsMargins(margins);
         setSpacing(spacing);
      }

      BorderLayout::BorderLayout(int spacing)
      {
         setSpacing(spacing);
      }


      BorderLayout::~BorderLayout()
      {
         QLayoutItem *l;
         while ((l = takeAt(0)))
            delete l;
      }

      void BorderLayout::addItem(QLayoutItem *item)
      {
         add(item, West);
      }

      void BorderLayout::addWidget(QWidget *widget, Position position)
      {
         add(new QWidgetItem(widget), position);
      }

      Qt::Orientations BorderLayout::expandingDirections() const
      {
         return Qt::Horizontal | Qt::Vertical;
      }

      bool BorderLayout::hasHeightForWidth() const
      {
         return false;
      }

      int BorderLayout::count() const
      {
         return list.size();
      }

      QLayoutItem *BorderLayout::itemAt(int index) const
      {
         ItemWrapper *wrapper = list.value(index);
         return wrapper ? wrapper->item : nullptr;
      }

      QSize BorderLayout::minimumSize() const
      {
         return calculateSize(MinimumSize);
      }

      void BorderLayout::setGeometry(const QRect &rect)
      {
         ItemWrapper *center = nullptr;
         int eastWidth = 0;
         int westWidth = 0;
         int northHeight = 0;
         int southHeight = 0;
         int centerHeight = 0;
         int i;

         QLayout::setGeometry(rect);

         for (i = 0; i < list.size(); ++i) {
            ItemWrapper *wrapper = list.at(i);
            QLayoutItem *item = wrapper->item;
            Position position = wrapper->position;

            if (position == North) {
                  item->setGeometry(QRect(rect.x(), northHeight, rect.width(),
                                          item->sizeHint().height()));

                  northHeight += item->geometry().height() + spacing();
            } else if (position == South) {
                  item->setGeometry(QRect(item->geometry().x(),
                                          item->geometry().y(), rect.width(),
                                          item->sizeHint().height()));

                  southHeight += item->geometry().height() + spacing();

                  item->setGeometry(QRect(rect.x(),
                                    rect.y() + rect.height() - southHeight + spacing(),
                                    item->geometry().width(),
                                    item->geometry().height()));
            } else if (position == Center) {
                  center = wrapper;
            }
         }

         centerHeight = rect.height() - northHeight - southHeight;

         for (i = 0; i < list.size(); ++i) {
            ItemWrapper *wrapper = list.at(i);
            QLayoutItem *item = wrapper->item;
            Position position = wrapper->position;

            if (position == West) {
                  item->setGeometry(QRect(rect.x() + westWidth, northHeight,
                                          item->sizeHint().width(), centerHeight));

                  westWidth += item->geometry().width() + spacing();
            } else if (position == East) {
                  item->setGeometry(QRect(item->geometry().x(), item->geometry().y(),
                                          item->sizeHint().width(), centerHeight));

                  eastWidth += item->geometry().width() + spacing();

                  item->setGeometry(QRect(
                                    rect.x() + rect.width() - eastWidth + spacing(),
                                    northHeight, item->geometry().width(),
                                    item->geometry().height()));
            }
         }

         if (center)
            center->item->setGeometry(QRect(westWidth, northHeight,
                                             rect.width() - eastWidth - westWidth,
                                             centerHeight));
      }

      QSize BorderLayout::sizeHint() const
      {
         return calculateSize(SizeHint);
      }

      QLayoutItem *BorderLayout::takeAt(int index)
      {
         if (index >= 0 && index < list.size()) {
            ItemWrapper *layoutStruct = list.takeAt(index);
            return layoutStruct->item;
         }
         return nullptr;
      }

      void BorderLayout::add(QLayoutItem *item, Position position)
      {
         list.append(new ItemWrapper(item, position));
      }

      QSize BorderLayout::calculateSize(SizeType sizeType) const
      {
         QSize totalSize;

         for (int i = 0; i < list.size(); ++i) {
            ItemWrapper *wrapper = list.at(i);
            Position position = wrapper->position;
            QSize itemSize;

            if (sizeType == MinimumSize)
                  itemSize = wrapper->item->minimumSize();
            else // (sizeType == SizeHint)
                  itemSize = wrapper->item->sizeHint();

            if (position == North || position == South || position == Center)
                  totalSize.rheight() += itemSize.height();

            if (position == West || position == East || position == Center)
                  totalSize.rwidth() += itemSize.width();
         }
         return totalSize;
      }

   ``` 

   소스에서 Override 되어 있는 부분은 모두 QLayout 에서 정의된 부분이니, 해당 부분중 중요 메소드만 살펴보면 다음과 같습니다.  
   ``` c++ 
      QList<ItemWrapper *> list;  //   각 Widget 을 담는 구조체 그릇을 QList 에 담고 있습니다. 

      void setGeometry(const QRect &rect) override;   //  크기와 위치를 수동으로 조정하여 구성하는 역활을 담당합니다.  center 는 남은 전체 크기로 구성하고 있습니다. 
      QLayoutItem *takeAt(int index) override;        //  등록된 아이템을 인덱스를 통해 반환합니다.   QWidget 에서 등록한 객체를 메모리 해제하는 역활을 하듯 소멸자에서 delete 를 수동으로 구성합니다. 
      void add(QLayoutItem *item, Position position); //  item add
   ```
   간단히 주석으로 담은 것처럼, setGeometry 에서 center 영역은 다른 widget 의 크기를 계산후 남은 영역 전체를 차지하도록 구성하고 있습니다.  직접 크기를 계산하고 각 영역을 구성해 주는 방식입니다.    
   QT 에서 제공하는 Layout 역시 응용프로그램을 개발하는 개발자가 직접하진 않을 뿐, 이런 작업이 진행될 것입니다. 
   소멸자에서 등록한 모든 widgets 메모리 해제 작업을 진행합니다.
   ##### ( QT 에서 자기들이 구성한 Widget 은 직접 관리하지만, 개발자가 수동으로 구성한 항목은 직접 관리해야 할 것 같습니다. ) 

   #### QGridLayout - Calculor Example 
   [https://doc.qt.io/qt-6/qtwidgets-widgets-calculator-example.html](https://doc.qt.io/qt-6/qtwidgets-widgets-calculator-example.html)  
   이 소스 역시 Qt Creator 에 예제 Sample 로 제공하고 있습니다.     
   Layout 도 의미가 있지만, 객체를 연결할 때 Button 의 Event 와 행위가 발생한 후의 작업을 Signal 과 Slot 으로 구성해서 제공하는 예제 입니다.  

   
   ``` c++ 
      // Copyright (C) 2016 The Qt Company Ltd.
      // SPDX-License-Identifier: LicenseRef-Qt-Commercial OR BSD-3-Clause

      #ifndef BUTTON_H
      #define BUTTON_H

      #include <QToolButton>

      //! [0]
      class Button : public QToolButton
      {
         Q_OBJECT

      public:
         explicit Button(const QString &text, QWidget *parent = nullptr);
         QSize sizeHint() const override;
      };
      //! [0]

      #endif
   ``` 
   ``` c++ 
      // Copyright (C) 2016 The Qt Company Ltd.
      // SPDX-License-Identifier: LicenseRef-Qt-Commercial OR BSD-3-Clause

      #include "button.h"

      //! [0]
      Button::Button(const QString &text, QWidget *parent)
         : QToolButton(parent)
      {
         setSizePolicy(QSizePolicy::Expanding, QSizePolicy::Preferred);
         setText(text);
      }
      //! [0]

      //! [1]
      QSize Button::sizeHint() const
      //! [1] //! [2]
      {
         QSize size = QToolButton::sizeHint();
         size.rheight() += 20;
         size.rwidth() = qMax(size.width(), size.height());
         return size;
      }
      //! [2]
   ```
   button 클래스의 sizeHint 함수는 적절한 크기를 구성하기 위한 함수 입니다.   QT 에서 꾸밈을 구성할 때 사용할 수 있는 방법 같습니다.    

   계산을 위한 header 와 cpp 파일인데 소스가 QT 에서 제공하기 때문에 굳이 다시 나열하는 것이 의미는 없기 때문에 일부분만 추려서 기재하겠습니다.  
   계산기에 대한 기능 설명은 해당 사이트 에서 상세히 있습니다. 계산기의 기능보다는 Event 를 구성하는 부분과, QGridLayout 를 구성하는 부분을 중점적으로 살펴 보고자 합니다. 

   ``` c++

      // Copyright (C) 2016 The Qt Company Ltd.
      // SPDX-License-Identifier: LicenseRef-Qt-Commercial OR BSD-3-Clause

      #ifndef CALCULATOR_H
      #define CALCULATOR_H

      #include <QWidget>

      QT_BEGIN_NAMESPACE
      class QLineEdit;
      QT_END_NAMESPACE
      class Button;

      //! [0]
      class Calculator : public QWidget
      {
         Q_OBJECT

      public:
         Calculator(QWidget *parent = nullptr);

      private slots:
         void digitClicked();
         void unaryOperatorClicked();
         void additiveOperatorClicked();
         void multiplicativeOperatorClicked();
         void equalClicked();
         void pointClicked();
         void changeSignClicked();
         void backspaceClicked();
         void clear();
         void clearAll();
         void clearMemory();
         void readMemory();
         void setMemory();
         void addToMemory();
      //! [0]

      //! [1]
      private:
      //! [1] //! [2]
         Button *createButton(const QString &text, const char *member);
         void abortOperation();
         bool calculate(double rightOperand, const QString &pendingOperator);
      //! [2]

      //! [3]
         double sumInMemory;
      //! [3] //! [4]
         double sumSoFar;
      //! [4] //! [5]
         double factorSoFar;
      //! [5] //! [6]
         QString pendingAdditiveOperator;
      //! [6] //! [7]
         QString pendingMultiplicativeOperator;
      //! [7] //! [8]
         bool waitingForOperand;
      //! [8]

      //! [9]
         QLineEdit *display;
      //! [9] //! [10]

         enum { NumDigitButtons = 10 };
         Button *digitButtons[NumDigitButtons];
      };
      //! [10]

      #endif
   ```  
   header 파일에서 slots 으로 선언된 함수와 createButton 에서 QString 과 char* 형태로 매개변수를 선언했습니다.  
   SLOT() 에 해당하는 부분이 char * 형식으로 매칭 되고 있습니다.   

   ``` c++ 

      // Copyright (C) 2016 The Qt Company Ltd.
      // SPDX-License-Identifier: LicenseRef-Qt-Commercial OR BSD-3-Clause

      #include "calculator.h"
      #include "button.h"

      #include <QGridLayout>
      #include <QLineEdit>
      #include <QtMath>

      //! [0]
      Calculator::Calculator(QWidget *parent)
         : QWidget(parent), sumInMemory(0.0), sumSoFar(0.0)
         , factorSoFar(0.0), waitingForOperand(true)
      {
      //! [0]

      //! [1]
         display = new QLineEdit("0");
      //! [1] //! [2]
         display->setReadOnly(true);
         display->setAlignment(Qt::AlignRight);
         display->setMaxLength(15);

         QFont font = display->font();
         font.setPointSize(font.pointSize() + 8);
         display->setFont(font);
      //! [2]

      //! [4]
         for (int i = 0; i < NumDigitButtons; ++i)
            digitButtons[i] = createButton(QString::number(i), SLOT(digitClicked()));

         Button *pointButton = createButton(tr("."), SLOT(pointClicked()));
         Button *changeSignButton = createButton(tr("\302\261"), SLOT(changeSignClicked()));

         Button *backspaceButton = createButton(tr("Backspace"), SLOT(backspaceClicked()));
         Button *clearButton = createButton(tr("Clear"), SLOT(clear()));
         Button *clearAllButton = createButton(tr("Clear All"), SLOT(clearAll()));

         Button *clearMemoryButton = createButton(tr("MC"), SLOT(clearMemory()));
         Button *readMemoryButton = createButton(tr("MR"), SLOT(readMemory()));
         Button *setMemoryButton = createButton(tr("MS"), SLOT(setMemory()));
         Button *addToMemoryButton = createButton(tr("M+"), SLOT(addToMemory()));

         Button *divisionButton = createButton(tr("\303\267"), SLOT(multiplicativeOperatorClicked()));
         Button *timesButton = createButton(tr("\303\227"), SLOT(multiplicativeOperatorClicked()));
         Button *minusButton = createButton(tr("-"), SLOT(additiveOperatorClicked()));
         Button *plusButton = createButton(tr("+"), SLOT(additiveOperatorClicked()));

         Button *squareRootButton = createButton(tr("Sqrt"), SLOT(unaryOperatorClicked()));
         Button *powerButton = createButton(tr("x\302\262"), SLOT(unaryOperatorClicked()));
         Button *reciprocalButton = createButton(tr("1/x"), SLOT(unaryOperatorClicked()));
         Button *equalButton = createButton(tr("="), SLOT(equalClicked()));
      //! [4]

      //! [5]
         QGridLayout *mainLayout = new QGridLayout;
      //! [5] //! [6]
         mainLayout->setSizeConstraint(QLayout::SetFixedSize);
         mainLayout->addWidget(display, 0, 0, 1, 6);
         mainLayout->addWidget(backspaceButton, 1, 0, 1, 2);
         mainLayout->addWidget(clearButton, 1, 2, 1, 2);
         mainLayout->addWidget(clearAllButton, 1, 4, 1, 2);

         mainLayout->addWidget(clearMemoryButton, 2, 0);
         mainLayout->addWidget(readMemoryButton, 3, 0);
         mainLayout->addWidget(setMemoryButton, 4, 0);
         mainLayout->addWidget(addToMemoryButton, 5, 0);

         for (int i = 1; i < NumDigitButtons; ++i) {
            int row = ((9 - i) / 3) + 2;
            int column = ((i - 1) % 3) + 1;
            mainLayout->addWidget(digitButtons[i], row, column);
         }

         mainLayout->addWidget(digitButtons[0], 5, 1);
         mainLayout->addWidget(pointButton, 5, 2);
         mainLayout->addWidget(changeSignButton, 5, 3);

         mainLayout->addWidget(divisionButton, 2, 4);
         mainLayout->addWidget(timesButton, 3, 4);
         mainLayout->addWidget(minusButton, 4, 4);
         mainLayout->addWidget(plusButton, 5, 4);

         mainLayout->addWidget(squareRootButton, 2, 5);
         mainLayout->addWidget(powerButton, 3, 5);
         mainLayout->addWidget(reciprocalButton, 4, 5);
         mainLayout->addWidget(equalButton, 5, 5);
         setLayout(mainLayout);

         setWindowTitle(tr("Calculator"));
      }
      //! [6]

      //! [7]
      void Calculator::digitClicked()
      {
         Button *clickedButton = qobject_cast<Button *>(sender());
         int digitValue = clickedButton->text().toInt();
         if (display->text() == "0" && digitValue == 0.0)
            return;

         if (waitingForOperand) {
            display->clear();
            waitingForOperand = false;
         }
         display->setText(display->text() + QString::number(digitValue));
      }
      //! [7]

      // 이하생략 .... 

      //! [34]
      Button *Calculator::createButton(const QString &text, const char *member)
      {
         Button *button = new Button(text);
         connect(button, SIGNAL(clicked()), this, member);
         return button;
      }
      //! [34]

      // 이하생략 ...
   ```
   #### QT Event 예제
   for loop 구문을 제외하면 버튼을 구성하는 부분은 다음과 같이 호출하고 있습니다.    
   digitButtons[i] = createButton(QString::number(i), SLOT(digitClicked()));    
   QString 으로 button 에서 표현할 문자열과, SLOT( 함수 ) 로 char * 형식으로 SLOT 을 호출할 수 있도록 구성하고 있습니다.  
   createButton 함수에서 보면, 첫번째 인자가 button 을 만드는 표시 이름이고, 두번째 char * member 는 SLOT 입니다.   
   connect(button, SIGNAL(clicked()), this, member ) 의 의미는 sender 가 button 이고, button 이 clicked 되었을 때 현재 객체인 Calculator 의 member 인 
   slot 함수가 호출되는 구조로 되어 있습니다.    
   그중 한 함수인 slot 의  digitClicked 내용을 보면, Button *clickedButton = qobject_cast<Button *>(sender()); 을 통해 호출한 sender 가 무엇인지 sender() 라는 
   메소드로 확인후 형변환 하여 Button 객체로 받아 들이고 있습니다. ( 이미 Button 객체라는 것을 알고 있기 때문에 가능... )    
   ##### slot 함수에서는 sender() 라는 함수를 통해 보낸 객체 정보를 가져올 수 있으니, 필요하다면 Event 를 발생 시킨 객체에 포함된 다른 정보도 전달이 가능할 것 같습니다.            
   int digitValue = clickedButton->text().toInt(); 은 sender 객체의 문자열을 받아 int 로 변환하고 있는 구문 입니다.   
   표현하는 display 가 QLineEdit 객체이고, 해당 영역에 기존 문자열과 지금 확인한 문자열을 합(문자열 결합)하여 표현하는 로직이 들어가 있습니다.    


   #### QGridLayout 는 어떻게 사용할까요? 
   ``` c++
         QGridLayout *mainLayout = new QGridLayout;
      //! [5] //! [6]
         mainLayout->setSizeConstraint(QLayout::SetFixedSize);
         mainLayout->addWidget(display, 0, 0, 1, 6);
   ``` 
   먼저 3개의 구문만 확인해 보겠습니다.   
   생성, 그리고, 사이즈를 고정 사이즈로 하겠다는 선언이 있습니다.   그리고 사용법인데 mainLayout->addWidget(display, 0, 0, 1, 6); 라고 호출 하고 있습니다.    
   addWidget 에서 display widget 은 위치가 세로 0, 가로 0 번째에서 시작하고, 세로는 1칸 가로는 6칸을 사용한다고 선언하고 있습니다.    
   크기는 몰라도 대략 가장 윗줄에 텍스트 입력( readonly ) 형식의 widget 가로로 길게 공간을 차지하는 모습을 연상할 수 있습니다. 
   mainLayout->addWidget(clearMemoryButton, 2, 0);  이 내용은 clearMemoryButton widget 이 위에서 3번째( index 2 ) 그리고 해당 열에서 첫번째 행 ( 0 ) 에 놓일 것이고, 
   세로 한칸 가로 한칸을 차지한다는 의미 입니다.    그 아래 약간 for loop 는 3개 마다 다음행으로 넘어가야 하고, 그 시작이 2 부터 라는 표현이고, column 은 1부터 시작된다는 
   표현을 loop 안에서 하고 있습니다.    QGridLayout 은 cell 별로 시작 위치 차지하는 공간을 지정하여 구성할 수 있는 Layout 이기 때문입니다.    


   QT 에서는 사용자가 직접 구성하는 Layout을 통해 하나하나 위치를 계산하는 방법과, QT 에서 사용하는 Event 구성을 다시한번 확인해 보았습니다.    

# PYQT6

   ### Layout Sample URL 
   [https://www.pythonguis.com/tutorials/pyqt6-layouts/](https://www.pythonguis.com/tutorials/pyqt6-layouts/)    


# SWING
   ### SWING Layout Class Link
   [https://docs.oracle.com/en/java/javase/17/docs/api/java.desktop/module-summary.html](https://docs.oracle.com/en/java/javase/17/docs/api/java.desktop/module-summary.html)


   ### Layout Sample URL 
   [https://docs.oracle.com/javase/tutorial/uiswing/layout/visual.html](https://docs.oracle.com/javase/tutorial/uiswing/layout/visual.html)

   #### BorderLayout 

   #### BoxLayout 

   #### CardLayout 

   #### FlowLayout

   #### GridBagLayout 


   #### GridLayout 


   #### GroupLayout 

   #### SpringLayout 

# JFX 
   ### JFX Layout Class Link 
   [https://openjfx.io/javadoc/11/javafx.graphics/javafx/scene/layout/package-tree.html](https://openjfx.io/javadoc/11/javafx.graphics/javafx/scene/layout/package-tree.html)   

   #### AnchorPane 

   #### BorderPane 


   #### FlowPane 


   #### GridPane 

   #### HBox 

   #### StackPane 


   #### TilePane 


   #### VBox 


   #### Layout Sample URL 
   [https://docs.oracle.com/javafx/2/layout/jfxpub-layout.htm](https://docs.oracle.com/javafx/2/layout/jfxpub-layout.htm)
   [https://docs.oracle.com/javafx/2/layout/builtin_layouts.htm](https://docs.oracle.com/javafx/2/layout/builtin_layouts.htm) 



