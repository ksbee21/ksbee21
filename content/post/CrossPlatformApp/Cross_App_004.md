---
title: "QT PYQT  SWING JFX Layout 기초 2"
date: 2023-02-20T18:26:18+09:00
draft: false
tags : ["CrossPlatform","Application","Language","JAVA", "QT","PYQT6","SWING","OpenJFX"]
topics : []
description : "초간단 계산기 각 언어로 구성하기기"
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
   PyQT 와 Swing, JFX 에서는 간단한 계산기를 구현해 보면서 Layout, 특히 Grid 유형의 Layout 을 확인해 보도록 하겠습니다.    
   구현 방법은 가급적 동일한 방법으로 구현하도록 구성하여 유사한 기능을 어떻게 만들 수 있는지에 중점을 두고 살펴 보겠습니다.     
   사실 API Level 에서 살펴볼 예정 이었으나, 시간이 좀 경과하다보니, 동일한 기능을 구현하면서 차이점과 공통점을 살펴 보는게 좋을 것 같아서 그 방향으로 정리해 보도록 하겠습니다. 

   ### UI 구성
   QGridLayout 을 활용하여 아주 단순하게 격자 Cell 형식으로 구성해 보았습니다.   소스에서 직관적으로 확인이 가능한 부분이라, 별다른 설명이 필요할 것 같진 않지만, 대략 labels 로 구현할 부분의 문자열을 구성합니다.    
   labels  = ["C","CE","DEL","/","7","8","9","X","4","5","6","-","1","2","3","+","+/-","0",".","="]    
   Member 로 사용할 self.textField = QLineEdit("0"),  self.label = QLabel("",self) 은 별도록 구성해 놓습니다.    
   self.layout = QGridLayout()  이미 구성해 놓은 QGridLayout 에 loop 를 돌며 Button 을 구성합니다.    
   그 결과 UI 는 아래와 같습니다.   
   
   ![/imgs/cross_004_01.png](/imgs/cross_004_01.png)

   ### Event 구성
   button 클릭을 시그널로 각 버튼마다 slots을 구성할 수 도 있습니다.   이 예제에서는 하나의 slot 을 구성한 후 해당 함수에서 넘겨온 객체를 기준으로 분기하는 방법을 사용하였습니다.   
   여러 방법중 하나를 정리하고자 한 것입니다.   계산기 같은 단순한 Action 은 굳이 이런 방식이 필요하지 않을 것 같지만, 몇개의 Group 으로 Action 을 분기하고, 해당 Action 에서 다시 분기되는 형태의 모듈에서는 
   Command Pattern 형식으로 한 곳에 모아 분기하는 것도 의미가 있기 때문에 이런 형식으로 구성해 보았습니다.    
   소스에서 주의 깊에 볼 부분은 slotFns = [self.execButtonClicked] 에서 함수 원형을 배열에 담고 있는 부분입니다.   필요하다면 항목에 맞춰 함수를 매핑할 수도 있을 것 같습니다.    
   두번째는 btn.clicked.connect(fn) 의 fn 이 위의 self.execButtonClicked 함수인데 이렇듯 매개 변수로 담아서 호출 할 수 있다는 부분 입니다.   PYQT 에서는 함수로 구성된 모든 것이 slot 이 될 수 있다고 합니다.   

   ### 전체 구현 소스
   ``` python
      import sys
      import math
      from decimal import *
      from CalculatorDTO import CalculationDTO
      from PyQt6.QtCore import (
         Qt,
         QSize
      )

      from PyQt6.QtWidgets import (
         QApplication, 
         QGridLayout,
         QLineEdit, 
         QLabel,
         QMainWindow, 
         QPushButton, 
         QWidget, 
      )


      class CalculatorUI(QMainWindow):
         def __init__(self):
            super().__init__()

            self.setWindowTitle("Calculator")
            self.layout = QGridLayout()
            self.initUI();

            self.calcDto = CalculationDTO()
            self.resetFlag = False

            widget = QWidget()
            widget.setLayout(self.layout)
            self.setCentralWidget(widget)

         def initUI(self):
            labels  = ["C","CE","DEL","/","7","8","9","X","4","5","6","-","1","2","3","+","+/-","0",".","="]
            slotFns = [self.execButtonClicked]
            self.textField = QLineEdit("0")
            self.textField.setReadOnly(True)
            self.label = QLabel("",self)
            qs01 = QSize(320, 40);
            qs02 = QSize(320, 60);
            self.label.setFixedSize(qs01);
            self.label.setAlignment(Qt.AlignmentFlag.AlignRight)
            self.textField.setFixedSize(qs02)
            self.textField.setAlignment(Qt.AlignmentFlag.AlignRight)
            self.textField.setReadOnly(True)
            self.layout.addWidget(self.label, 0, 0, 1, 4)
            self.layout.addWidget(self.textField, 1,0, 1, 4)

            for  i in range(len(labels)):
                  gx = (i % 4)
                  gy = (math.trunc(i / 4)+2)
                  #print("%s, %s" %(gx, gy))
                  self.layout.addWidget(self.createButtonUI(labels[i],slotFns[0]),gy,gx)


         def createButtonUI(self,txt,fn):
            btn = QPushButton(txt,self)
            btn.setBaseSize(80,60)
            btn.clicked.connect(fn)
            return btn

         def execButtonClicked(self):
            sender = self.sender()
            str = sender.text()
            if ( str == 'C' ):
                  self.cleanAll()
            elif ( str == 'CE'):
                  self.cleanCurrentField()
            elif ( str == 'DEL'):
                  self.removeLastValue()
            elif ( str == '/'):
                  self.divideValue()
            elif (str == 'X'):
                  self.multiplyValue()
            elif (str == '+'):
                  self.plusValue()
            elif(str == '-'):
                  self.minusValue()
            elif (str == '='):
                  self.calcResultValue()
            elif (str == '+/-'):
                  self.togglePlusMinusValue();
            elif(str == '.'):
                  self.setTextFieldValue(str)
            else :
                  self.setTextFieldValue(str)

         def plusValue(self):
            txtValue = self.textField.text()
            numValue = Decimal(txtValue)
            self.calcDto.setMarkValues("+", numValue)
            self.label.setText(self.calcDto.getDisplayStringValue())
            self.resetFlag = True

         def minusValue(self):
            txtValue = self.textField.text()
            numValue = Decimal(txtValue)
            self.calcDto.setMarkValues("-", numValue)
            self.label.setText(self.calcDto.getDisplayStringValue())
            self.resetFlag = True

         def multiplyValue(self):
            txtValue = self.textField.text()
            numValue = Decimal(txtValue)
            self.calcDto.setMarkValues("x", numValue)
            self.label.setText(self.calcDto.getDisplayStringValue())
            self.resetFlag = True

         def divideValue(self):
            txtValue = self.textField.text()
            numValue = Decimal(txtValue)
            self.calcDto.setMarkValues("/", numValue)
            self.label.setText(self.calcDto.getDisplayStringValue())
            self.resetFlag = True


         def calcResultValue(self):
            if ( self.resetFlag ):
                  return
            
            txtValue = self.textField.text()
            numValue = Decimal(txtValue)
            self.calcDto.setMarkValues("=", numValue)
            self.textField.setText(self.calcDto.getCalculationValueString())
            self.label.setText(self.calcDto.getDisplayStringValue())
            self.resetFlag = True

         def setTextFieldValue(self, str):
            if ( self.resetFlag ):
                  self.resetFlag = False
                  self.textField.setText(str)
                  return
            
            txtValue = self.textField.text()
            if ( txtValue == '0' and str != '.' ):
                  txtValue = str
            else :
                  txtValue += str
            self.textField.setText(txtValue)

         def removeLastValue(self):
            txtValue = self.textField.text()
            txtValue = txtValue[:-1]
            if not txtValue :
                  txtValue = '0'
            self.textField.setText(txtValue)

         def togglePlusMinusValue(self):
            txtValue = self.textField.text()
            if not txtValue :
                  txtValue = '0'

            numValue = Decimal(txtValue)
            numValue *= -1
            self.textField.setText(self.calcDto.tranlateNumberToString(numValue))

         def cleanCurrentField(self):
            self.textField.setText("0")
            self.resetFlag = False

         def cleanAll(self):
            self.textField.setText("0")
            self.label.setText("")
            self.calcDto.clearAll()
            self.resetFlag = False




         
      app = QApplication(sys.argv)

      window = CalculatorUI();
      window.show();

      app.exec()
   ``` 

   ``` python
      import math

      class CalculationDTO:

         def __init__(self):
            self.calculationValue       = 0.0
            self.currentValue           = 0.0
            self.displayStr             = ""
            self.currentMarkType        = -1
            self.calculationMarkType    = -1

         def translateMarkToType(self, markStr):
            if not markStr :
                  return -1
            markStr = markStr.strip().lower()
            if ( markStr == '+'):
                  return 1
            elif ( markStr == '-'):
                  return 2
            elif ( markStr == 'x'):
                  return 3
            elif ( markStr == '/'):
                  return 4
            elif ( markStr == '='):
                  return 5
            else :
                  return -1
            
         def setMarkValues(self, mark, numValue):
            self.calculationMarkType = self.currentMarkType
            if ( self.calculationMarkType == 1 ):
                  self.plusValue(numValue)
                  self.displayStr += self.tranlateNumberToString(numValue )            
            elif ( self.calculationMarkType == 2 ):
                  self.minusValue(numValue)
                  self.displayStr += self.tranlateNumberToString(numValue )            
            elif ( self.calculationMarkType == 3 ):
                  self.multiplyValue(numValue)
                  self.displayStr += self.tranlateNumberToString(numValue )            
            elif ( self.calculationMarkType == 4 ):
                  self.divideValue(numValue)
                  self.displayStr += self.tranlateNumberToString(numValue )            
            elif ( self.calculationMarkType == 5 ):
                  self.displayStr = self.tranlateNumberToString(self.calculationValue )             
            else :
                  self.calculationValue = numValue
                  self.displayStr = self.tranlateNumberToString(self.calculationValue )   

            self.currentMarkType = self.translateMarkToType(mark)
            if ( self.currentMarkType == 1 ):
                  self.displayStr = self.displayStr + " + "
            elif ( self.currentMarkType == 2 ):
                  self.displayStr += " - "
            elif ( self.currentMarkType == 3 ):
                  self.displayStr += " x "
            elif ( self.currentMarkType == 4 ):
                  self.displayStr += " / "            
            elif ( self.currentMarkType == 5 ):
                  self.displayStr += " = " + self.tranlateNumberToString(self.calculationValue) 
                  self.currentMarkType = -1
            else :
                  self.calculationValue = numValue
                  self.displayStr = self.tranlateNumberToString(self.calculationValue ) 



         def plusValue(self, numValue):
            self.calculationValue += numValue

         def minusValue(self, numValue):
            self.calculationValue -= numValue

         def multiplyValue(self, numValue):
            self.calculationValue *= numValue

         def divideValue(self, numValue):
            if ( numValue == 0.0 ):
                  self.calculationValue = 0
                  return;
         
            self.calculationValue /= numValue

         def tranlateNumberToString(self, numValue):
            if ( numValue == round(numValue)):
                  return "%d" % round(numValue)
            else:
                  return "%s" % numValue

         def getCalculationValueString(self):
            return self.tranlateNumberToString(self.calculationValue)
         
         def getDisplayStringValue(self):
            return self.displayStr
         
         def clearAll(self):
            self.calculationValue       = 0.0
            self.currentValue           = 0.0
            self.displayStr             = ""
            self.currentMarkType        = -1
            self.calculationMarkType    = -1
        
   ```

   
    


# SWING
   ### Layout Sample URL 
   [https://docs.oracle.com/javase/tutorial/uiswing/layout/visual.html](https://docs.oracle.com/javase/tutorial/uiswing/layout/visual.html)

   PyQT 에서 구현한 방법과 완전히 동일한 방식으로 구성해 보았습니다.   
   언어는 다르지만, 구성하는 원리나 접근 방식은 거의 동일한 부분이기 때문에 차이점 보다 유사점에 초점을 맞추어 정리해 보겠습니다. 

   ### UI 구성
   GridBagLayout 은 조금 복잡한 Layout 이지만, 활용여부에 따라 다양한 모습을 연출할 수 있습니다.   여기에서는 정말 간단한 Cell 구조로 구성하였습니다.    
   PyQT 와 마찬가지로 문자열 배열을 이용해서 Loop 를 돌면서 구성합니다. Member 도 JLabel, JTextField 로 이름은 달라도 QT 의 Widget 과 거의 동일한 기능을 가지고 있습니다. 
   그 결과 UI 는 아래와 같습니다.   
   
   ![/imgs/cross_004_02.png](/imgs/cross_004_02.png)

   ### Event 구성
   Java 에서는 함수 포인터 등의 개념이 없기 때문에 매칭을 하는 방법이 PyQT 와는 다를 수 밖에 없습니다.    Button 에는 ActionListener 를 매핑할 수 있고, 해당 Event 에서는 발생시킨 
   원천 소스를 가져올 수 있기 때문에 PyQT 와 유사하게 발생한 Button 의 text 를 가져올 수 있게 됩니다.    Event 를 매칭하는 방법은 여러 가지가 가능한데, 별도의 Class 를 구성하여 처리하는 
   방법을 사용하였습니다.   
   ICalcUI 라는 Interface 를 구성해서, Swing, JFX 모두 사용할 수 있도록 구성해 보았습니다. 

   ### 전체 구현 소스
   #### UI 
   ``` java 
      import java.awt.*;
      import javax.swing.*;

      import java.awt.Dimension;

      public class CalculationUI implements ICalcUI {

         private JLabel label            = null;
         private JTextField textField    = null;
         private CalculationDTO calcDto  = null;
         private boolean resetFlag       = false;

         private CalculationUI() {
            initResource();
         }

         private void initResource() {
            int width = 70;
            int height = 60;

            label = createJLabelUI("test",new Dimension(width*4,height-20));
            label.setHorizontalAlignment(SwingUtilities.RIGHT);

            textField = new JTextField();
            textField.setPreferredSize( new Dimension(width*4, height));
            textField.setHorizontalAlignment(SwingUtilities.RIGHT);
            textField.setEditable(false);

            calcDto = new CalculationDTO();
         }

         public JPanel createGridBackJPanel() {
            JPanel panel = new JPanel(new GridBagLayout());

            //panel.setPreferredSize(new Dimension(500,500));

            CalculationAction listener = new CalculationAction(this);

            String[] labels = {"C","CE","DEL","/","7","8","9","X","4","5","6","-","1","2","3","+","+/-","0",".","="};

            Dimension btnDim = new Dimension(70,50);

            GridBagConstraints gc = new GridBagConstraints();
            gc.gridx = 0;
            gc.gridy = 0;
            gc.gridwidth = 4;

            panel.add(label, gc);
            gc.gridy = 1;
            panel.add(textField, gc);        

            gc.gridwidth = 1;
            for ( int i = 0; i < labels.length; i++ ) {
                  int gx = i%4;
                  int gy = i/4 + 2;
                  gc.gridx = gx;
                  gc.gridy = gy;
                  JButton btn = createButtonUI(labels[i], btnDim);
                  btn.addActionListener(listener);
                  panel.add(btn,gc);
            }
            return panel;
         }

         private JLabel createJLabelUI(String text, Dimension dim) {
            JLabel label = new JLabel(text);
            label.setPreferredSize( dim);
            return label;
         }

         private JButton createButtonUI(String text, Dimension dim) {
            JButton btn = new JButton(text);
            btn.setPreferredSize(dim);
            return btn;
         }

         public void plusValue() {
            String txtValue = this.textField.getText();
            double numValue = Double.valueOf(txtValue);
            this.calcDto.setMarkValues("+", numValue);
            this.label.setText(this.calcDto.getDisplayStringValue());
            this.resetFlag = true;
         }

         public void minusValue() {
            String txtValue = this.textField.getText();
            double numValue = Double.valueOf(txtValue);
            this.calcDto.setMarkValues("-", numValue);
            this.label.setText(this.calcDto.getDisplayStringValue());
            this.resetFlag = true;
         }

         public void multiplyValue() {
            String txtValue = this.textField.getText();
            double numValue = Double.valueOf(txtValue);
            this.calcDto.setMarkValues("x", numValue);
            this.label.setText(this.calcDto.getDisplayStringValue());
            this.resetFlag = true;
         }

         public void divideValue() {
            String txtValue = this.textField.getText();
            double numValue = Double.valueOf(txtValue);
            this.calcDto.setMarkValues("/", numValue);
            this.label.setText(this.calcDto.getDisplayStringValue());
            this.resetFlag = true;
         }


         public void calcResultValue() {
            if ( this.resetFlag )
                  return;
         
            String txtValue = this.textField.getText();
            double numValue = Double.valueOf(txtValue);
            this.calcDto.setMarkValues("=", numValue);
            this.textField.setText(this.calcDto.getCalculationValueString());
            this.label.setText(this.calcDto.getDisplayStringValue());
            this.resetFlag = true;
         }

         public void setTextFieldValue(String str) {
            if ( this.resetFlag ) {
                  this.resetFlag = false;
                  this.textField.setText(str);
                  return;
            }
         
            String txtValue = this.textField.getText();
            if ( txtValue.equals("0") && !str.equals(".")  ) {
                  txtValue = str;
            } else {
                  txtValue += str;
            }
            this.textField.setText(txtValue);
         }

         public void removeLastValue() {
            String txtValue = this.textField.getText();
            if ( txtValue == null ) {
                  this.textField.setText("0");
                  return;
            }
            int len = txtValue.length();
            if ( len <= 1 ) {
                  this.textField.setText("0");
                  return;
            }

            txtValue = txtValue.substring(0,len-1);
            this.textField.setText(txtValue);
         }

         public void togglePlusMinusValue() {
            String txtValue = this.textField.getText();
            if ( txtValue == null ) {
                  txtValue = "0";
            }

            double numValue = Double.valueOf(txtValue);
            numValue *= -1.0;
            this.textField.setText(this.calcDto.tranlateNumberToString(numValue));
         }

         public void cleanCurrentField() {
            this.textField.setText("0");
            this.resetFlag = false;
         }

         public void cleanAll() {
            this.textField.setText("0");
            this.label.setText("");
            this.calcDto.clearAll();
            this.resetFlag = false;  
         }  

         private static void createAndShowGUI() {
            JFrame frame = new JFrame("Calculator");
            frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
            CalculationUI uiDto = new CalculationUI();
            frame.getContentPane().add(uiDto.createGridBackJPanel());
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
   #### INTERFACE 
   ``` java
      public interface ICalcUI {
         public void plusValue();
         public void minusValue();
         public void multiplyValue();
         public void divideValue();
         public void calcResultValue();
         public void setTextFieldValue(String str);
         public void removeLastValue();
         public void togglePlusMinusValue();
         public void cleanCurrentField();
         public void cleanAll();
      }

   ```
   #### 업무 데이터 클래스
   ``` java
      public class CalculationDTO {
         private double calculationValue         = 0.0;
         private double currentValue             = 0.0;    
         private double beforeValue              = 0.0;
         private String displayStr               = "";
         private CALC_TYPES calculationMarkType  = null;
         private CALC_TYPES currentMarkType      = null;    

         public enum CALC_TYPES {
            PLUS(1, "+"), 
            MINUS(2,"-"),
            MUTIPLY(3,"x"),
            DIVIDE(4,"/"),
            CALC_RESULT(5, "="),
            NOT_YET(-1,"not_yet");


            private final int value;
            private final String mark;
            private CALC_TYPES(int value, String mark) {
                  this.value = value;
                  this.mark = mark;
            }

            public String getMark() {
                  return this.mark;
            }

            public int getValue() {
                  return this.value;
            }
         };

         private CALC_TYPES translateMarkToType(String markStr) {
            if ( markStr == null )
                  return CALC_TYPES.NOT_YET;
            markStr = markStr.trim().toLowerCase();
            if ( markStr.length() == 0 )
                  return CALC_TYPES.NOT_YET;

            if ( markStr == "+")
                  return CALC_TYPES.PLUS;
            else if ( markStr == "-")
                  return CALC_TYPES.MINUS;
            else if ( markStr == "x")
                  return CALC_TYPES.MUTIPLY;
            else if ( markStr == "/")
                  return CALC_TYPES.DIVIDE;
            else if ( markStr == "=")
                  return CALC_TYPES.CALC_RESULT;
            else 
                  return CALC_TYPES.NOT_YET;
         }


         public void setMarkValues(String mark, double numValue) {
            this.calculationMarkType = this.currentMarkType;
            if ( this.calculationMarkType == CALC_TYPES.PLUS ) {
                  this.plusValue(numValue);
                  this.displayStr += this.tranlateNumberToString(numValue);
            } else if ( this.calculationMarkType == CALC_TYPES.MINUS ) {
                  this.minusValue(numValue);
                  this.displayStr += this.tranlateNumberToString(numValue );

            } else if ( this.calculationMarkType == CALC_TYPES.MUTIPLY ) {
                  this.multiplyValue(numValue);
                  this.displayStr += this.tranlateNumberToString(numValue );           
            } else if ( this.calculationMarkType == CALC_TYPES.DIVIDE ) {
                  this.divideValue(numValue);
                  this.displayStr += this.tranlateNumberToString(numValue );
            } else if ( this.calculationMarkType == CALC_TYPES.CALC_RESULT ) {
                  this.displayStr = this.tranlateNumberToString(this.calculationValue)             ;
            } else { 
                  this.calculationValue = numValue;
                  this.displayStr = this.tranlateNumberToString(this.calculationValue) ;
            }

            this.currentMarkType = this.translateMarkToType(mark);
            if ( this.currentMarkType == CALC_TYPES.PLUS )
                  this.displayStr = this.displayStr + " + ";
            else if ( this.currentMarkType == CALC_TYPES.MINUS  )
                  this.displayStr += " - ";
            else if ( this.currentMarkType ==  CALC_TYPES.MUTIPLY )
                  this.displayStr += " x ";
            else if ( this.currentMarkType == CALC_TYPES.DIVIDE )
                  this.displayStr += " / ";            
            else if ( this.currentMarkType == CALC_TYPES.CALC_RESULT ) {
                  this.displayStr += " = " + this.tranlateNumberToString(this.calculationValue) ;
                  this.currentMarkType = CALC_TYPES.NOT_YET;
            } else {
                  this.calculationValue = numValue;
                  this.displayStr = this.tranlateNumberToString(this.calculationValue ) ;
            }
         }

         public void plusValue(double numValue) {
            this.calculationValue += numValue;
         }

         public void minusValue(double numValue) {
            this.calculationValue -= numValue;
         }

         public void multiplyValue(double numValue) {
            this.calculationValue *= numValue;
         }

         public void divideValue(double numValue) {
            if ( numValue == 0.0 ) {
                  this.calculationValue = 0.0;
                  return;
            }
            this.calculationValue /= numValue;
         }

         public String tranlateNumberToString(double numValue) {
            if ( numValue == (long)(numValue))
                  return Long.toString((long)numValue);
            else
                  return Double.toString(numValue);
         }

         public String getCalculationValueString() {
            return this.tranlateNumberToString(this.calculationValue);
         }
         
         public String getDisplayStringValue() {
            return this.displayStr;
         }
         
         public void clearAll() {
            this.calculationValue       = 0.0;
            this.currentValue           = 0.0;
            this.displayStr             = "";
            this.currentMarkType        = CALC_TYPES.NOT_YET;
            this.calculationMarkType    = CALC_TYPES.NOT_YET;
         }

      }

   ``` 
   #### Event Action Class
   ``` java
      import java.awt.event.*;
      import javax.swing.*;

      public class CalculationAction implements ActionListener  {

         private ICalcUI calcUI;

         public CalculationAction(ICalcUI uiDto) {
            super();
            this.calcUI = uiDto;
         }
         @Override
         public void actionPerformed(ActionEvent e) {
            if ( e.getSource() instanceof JButton ) {
                  String str = ((JButton)e.getSource()).getText();

                  if ( str == "C" ) { 
                     calcUI.cleanAll();
                  } else if ( str == "CE") { 
                     calcUI.cleanCurrentField();
                  } else if ( str == "DEL") { 
                     calcUI.removeLastValue();
                  } else if ( str == "/") { 
                     calcUI.divideValue();
                  } else if (str == "X") { 
                     calcUI.multiplyValue();
                  } else if (str == "+") { 
                     calcUI.plusValue();
                  } else if (str == "-") { 
                     calcUI.minusValue();
                  } else if (str == "=") { 
                     calcUI.calcResultValue();
                  } else if (str == "+/-") { 
                     calcUI.togglePlusMinusValue();
                  } else if (str == ".") { 
                     calcUI.setTextFieldValue(str);
                  } else  { 
                     calcUI.setTextFieldValue(str);
                  }
            }
         }
      }

   ``` 


# JFX 
   #### Layout Sample URL 
   [https://docs.oracle.com/javafx/2/layout/jfxpub-layout.htm](https://docs.oracle.com/javafx/2/layout/jfxpub-layout.htm)
   [https://docs.oracle.com/javafx/2/layout/builtin_layouts.htm](https://docs.oracle.com/javafx/2/layout/builtin_layouts.htm) 

   JFX 는 Swing 과 같은 Java 이기 때문에 공통적으로 ICalcUI 와  CalculationDTO 는 공통적으로 사용이 가능합니다.    
   ICalcUI interface 를 활용하여 업무로직을 동일하게 구성하였습니다.    

   ### UI 구성
   GridPane 은 앞서의 QGridLayout 과 유사합니다.   물론 Swing 의 Layout 과도 유사합니다.    
   Label, TextField 등도 앞서 제공했던 다른 언어의 UI 와 기능면에서 거의 동일합니다.  아래는 구성된 UI 입니다. 
   
   ![/imgs/cross_004_03.png](/imgs/cross_004_03.png)

   ### Event 구성
   Swing 과 거의 동일합니다.   약간의 차이는 EventHandler 에 Generic 구성이 가능하고, 조금 Modern(?) 한 방식의 프로그래밍이 가능하도록 구성된 정도 인것 같습니다. 
   물론 깊게 들어가면 차이가 있겠지만, 언어 구성해서 전달하는 방식은 위 언어들이 다른 것 같지만, 그리 큰 차이를 보이지 않는 것 같습니다. 

   ### 전체 구현 소스
   앞서 동일한 소스는 생략하겠습니다. 
   #### UI 
   ``` java 
      import javafx.application.Application;
      import javafx.geometry.Pos;
      import javafx.scene.Scene;
      import javafx.scene.control.Label;
      import javafx.scene.control.TextField;
      import javafx.scene.control.Button;
      import javafx.scene.layout.GridPane;
      import javafx.stage.Stage;
      

      public class JFXCalculationUI extends Application implements ICalcUI {

         private Label label                     = null;
         private TextField textField             = null;
         private CalculationDTO calcDto          = null;
         private boolean resetFlag               = false;

         private void initUI(double width, double height) {

            label = createLabelUI("test",width*4,height-20);
            label.setAlignment(Pos.CENTER_RIGHT);

            textField = new TextField("0");
            textField.setPrefSize(width*4, height);
            textField.setAlignment(Pos.CENTER_RIGHT);
            textField.setEditable(false);

            calcDto = new CalculationDTO();
         }

         private Label createLabelUI(String text, double width, double height) {
            Label label = new Label(text);
            label.setPrefSize(width,height);
            return label;
         }

         private Button createButtonUI(String text, double width, double height) {
            Button btn = new Button(text);
            btn.setPrefSize(width, height);
            return btn;
         }

         public void plusValue() {
            String txtValue = this.textField.getText();
            double numValue = Double.valueOf(txtValue);
            this.calcDto.setMarkValues("+", numValue);
            this.label.setText(this.calcDto.getDisplayStringValue());
            this.resetFlag = true;
         }

         public void minusValue() {
            String txtValue = this.textField.getText();
            double numValue = Double.valueOf(txtValue);
            this.calcDto.setMarkValues("-", numValue);
            this.label.setText(this.calcDto.getDisplayStringValue());
            this.resetFlag = true;
         }

         public void multiplyValue() {
            String txtValue = this.textField.getText();
            double numValue = Double.valueOf(txtValue);
            this.calcDto.setMarkValues("x", numValue);
            this.label.setText(this.calcDto.getDisplayStringValue());
            this.resetFlag = true;
         }

         public void divideValue() {
            String txtValue = this.textField.getText();
            double numValue = Double.valueOf(txtValue);
            this.calcDto.setMarkValues("/", numValue);
            this.label.setText(this.calcDto.getDisplayStringValue());
            this.resetFlag = true;
         }


         public void calcResultValue() {
            if ( this.resetFlag )
                  return;
         
            String txtValue = this.textField.getText();
            double numValue = Double.valueOf(txtValue);
            this.calcDto.setMarkValues("=", numValue);
            this.textField.setText(this.calcDto.getCalculationValueString());
            this.label.setText(this.calcDto.getDisplayStringValue());
            this.resetFlag = true;
         }

         public void setTextFieldValue(String str) {
            if ( this.resetFlag ) {
                  this.resetFlag = false;
                  this.textField.setText(str);
                  return;
            }
         
            String txtValue = this.textField.getText();

            if ( txtValue.equals("0") && !str.equals(".")  ) {
                  txtValue = str;
            } else {
                  txtValue += str;
            }
            this.textField.setText(txtValue);
         }

         public void removeLastValue() {
            String txtValue = this.textField.getText();
            if ( txtValue == null ) {
                  this.textField.setText("0");
                  return;
            }
            int len = txtValue.length();
            if ( len <= 1 ) {
                  this.textField.setText("0");
                  return;
            }

            txtValue = txtValue.substring(0,len-1);
            this.textField.setText(txtValue);
         }

         public void togglePlusMinusValue() {
            String txtValue = this.textField.getText();
            if ( txtValue == null ) {
                  txtValue = "0";
            }

            double numValue = Double.valueOf(txtValue);
            numValue *= -1.0;
            this.textField.setText(this.calcDto.tranlateNumberToString(numValue));
         }

         public void cleanCurrentField() {
            this.textField.setText("0");
            this.resetFlag = false;
         }

         public void cleanAll() {
            this.textField.setText("0");
            this.label.setText("");
            this.calcDto.clearAll();
            this.resetFlag = false;  
         }  



         @Override
         public void start(Stage stage) {

            double width = 70;
            double height = 60;

            initUI(width,height);

            GridPane grid = new GridPane();
            int mgx = 0; 
            int mgy = 0;
            int colSpan = 4;
            int rowSpan = 1;
            grid.add(this.label,mgx,mgy,colSpan, rowSpan);
            ++mgy;
            grid.add(this.textField,mgx,mgy,colSpan, rowSpan);
            ++mgy;

            String[] labels = {"C","CE","DEL","/","7","8","9","X","4","5","6","-","1","2","3","+","+/-","0",".","="};

            JFXActionHandler handler = new JFXActionHandler(this);

            for ( int i = 0; i < labels.length; i++ ) {
                  int gx = i%4;
                  int gy = i/4 + 2;
                  Button btn = createButtonUI(labels[i], width,height);
                  btn.setOnAction(handler);
                  grid.add(btn,gx,gy);
            }

            Scene scene = new Scene(grid);
            stage.setScene(scene);
            stage.setTitle("JFX Calculator");
            stage.show();
         }


         public static void main(String[] args) {
            launch(JFXCalculationUI.class, args);
         }
      }
   ```

   #### Event Handler 
   ``` java

      import javafx.event.ActionEvent;
      import javafx.event.EventHandler;
      import javafx.scene.control.Button;

      public class JFXActionHandler implements EventHandler<ActionEvent> {
         private ICalcUI calcUI;
         public JFXActionHandler(ICalcUI uiDto) {
            super();
            calcUI = uiDto;
         }

         @Override
         public void handle(ActionEvent e) {
            if ( e.getSource() instanceof Button ) {
                  String str = ((Button)e.getSource()).getText();

                  if ( str == "C" ) { 
                     calcUI.cleanAll();
                  } else if ( str == "CE") { 
                     calcUI.cleanCurrentField();
                  } else if ( str == "DEL") { 
                     calcUI.removeLastValue();
                  } else if ( str == "/") { 
                     calcUI.divideValue();
                  } else if (str == "X") { 
                     calcUI.multiplyValue();
                  } else if (str == "+") { 
                     calcUI.plusValue();
                  } else if (str == "-") { 
                     calcUI.minusValue();
                  } else if (str == "=") { 
                     calcUI.calcResultValue();
                  } else if (str == "+/-") { 
                     calcUI.togglePlusMinusValue();
                  } else if (str == ".") { 
                     calcUI.setTextFieldValue(str);
                  } else  { 
                     calcUI.setTextFieldValue(str);
                  }
            }
         }
         
      }
   ```






