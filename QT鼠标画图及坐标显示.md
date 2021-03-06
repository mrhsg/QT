![Image 程序运行界面](https://github.com/mrhsg/images/blob/master/qt鼠标画图.PNG)

/*
     widget.h
*/

 #ifndef WIDGET_H
 #define WIDGET_H

 #include <QWidget>
 #include <QPoint>
 #include <vector>
 #include <QLabel>
 #include <QCursor>

using namespace std;

typedef struct myLine{
    QPoint startPnt;
    QPoint endPnt;
}myLine;

class Widget : public QWidget
{
    Q_OBJECT

public:
    Widget(QWidget *parent = 0);
    ~Widget();

    void paintEvent(QPaintEvent *p);
    void mousePressEvent(QMouseEvent *e);
    void mouseMoveEvent(QMouseEvent *e);
    void mouseReleaseEvent(QMouseEvent *e);

    QPoint startPnt;
    QPoint endPnt;
    bool isPressed;

    QLabel *label;
    //QCursor cursor;

    vector<myLine*> lines;
};

 #endif // WIDGET_H


/*
     widget.cpp
*/

 #include "widget.h"
 #include <QString>
 #include <QMessageBox>
 #include <QPainter>
 #include <QPen>
 #include <QMouseEvent>


Widget::Widget(QWidget *parent)
    : QWidget(parent)
{
    setMinimumSize(240,120);
    setMaximumSize(1024,840);
    this->setMouseTracking(true);
    this->isPressed = false;

    label=new QLabel(this);
    label->setGeometry(0,0,200,50);
}

Widget::~Widget()
{

}

void Widget::paintEvent(QPaintEvent *p)
{
    QPainter painter(this);
    QPen pen;

    pen.setColor(Qt::darkCyan);
    pen.setWidth(5);
    painter.setPen(pen);

    for(int i=0;i<lines.size();i++)
    {
        myLine *pLine = lines[i];
        painter.drawLine(pLine->startPnt,pLine->endPnt);
    }
}

void Widget::mousePressEvent(QMouseEvent *e)
{
    setCursor(Qt::PointingHandCursor);
    startPnt=e->pos();
    endPnt=e->pos();
    isPressed=true;
}

void Widget::mouseMoveEvent(QMouseEvent *e)
{
    if(isPressed)
    {
        endPnt=e->pos();

        myLine* line=new myLine;
        line->startPnt=startPnt;
        line->endPnt=endPnt;
        lines.push_back(line);

        update();
        startPnt=endPnt;
    }
    QPoint m = e->globalPos();
    QPoint n = this->pos();
    label->setText(QString("(点数:%1,position:%2,%3)").arg(lines.size()).arg(m.x()-n.x()).arg(m.y()-n.y()));

    if(lines.size()>2000)
    {
        QMessageBox::warning(0,tr("窗口"),"真的退出吗？",QMessageBox::Yes|QMessageBox::No);
        lines.clear();

    }
}

void Widget::mouseReleaseEvent(QMouseEvent *e)
{
    setCursor(Qt::ArrowCursor);
    this->isPressed=false;
}


