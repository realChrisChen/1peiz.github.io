---
layout: post
read_time: true
show_date: true
title:  设计模式之策略模式
date:   2022-04-03 13:54:00 -0600
description: 策略模式是怎么实现的呢?
img: posts/20220403/Techlead.jpg
tags: [Cplusplus,设计模式]
author: Chris Chen
mathjax: no
---

## 组件协作模式

策略模式属于组件协作模式中的一种, 组件协作模式中还有观察者模式和模板模式, 组件模式通过"晚期绑定"实现了现代软件设计中的框架与应用分离,从而实现二者的松耦合



## 策略模式提出的动机

* 如果一个对象中有很多复杂的算法,将算法编码到对象当中,不仅会导致对象异常复杂,还会导致对象加载不需要的算法导致内存的负担

* 如何实现算法与对象的分离,让对象在运行时动态的调用需要的算法呢?

  

## 策略模式的定义

将一系列的算法个个都进行封装(通过子类来进行扩展),从而能使得每个都能被替换(变化),从而使得该算法(变化)能独立于其客户端(稳定)而变化



## 具体的代码

```c++

#include <iostream>
#include <string>
#include <memory>
using namespace std;

//上下文类(定义税收时的具体上下文)
class Context
{

public:
    
    //这里本来想将m_context声明为私有,将
    //taxStrategy声明为它的友元类,但可惜
    //C++中的友元关系不能被继承,所以只好
    //公开了
    string m_context; 
    Context(string x = "Normal"):m_context(x){}
    

};

//税收类(抽象类)
class taxStrategy
{
    public:
    	//变化的
        virtual double Calculate(const Context& context) = 0;
        virtual ~taxStrategy(){}

};

//子类:中国税收
class taxCN:public taxStrategy
{
    public:
    	//变化的
        double Calculate(const Context& context)
        {
            cout<<"Standard: "<<context.m_context<<endl;
            cout<<"Tax 700 cny\n";
            return 700.0;
        }
    
};

//子类:美国税收
class taxUS:public taxStrategy
{
    public:
    	//变化的
        double Calculate(const Context& context)
        {
            cout<<"Standard: "<<context.m_context<<endl;
            cout<<"Tax 900 usd\n";
            return 900.0;
        }
};

//销售类(会调用税收类中的算法来进行收税)
class salesOrder
{
private:
    shared_ptr<taxStrategy> m_strategy;
public:
    //稳定的
    salesOrder(shared_ptr<taxStrategy> strategy)
    {
        //构造函数
        this->m_strategy = strategy;
    }
    

    //稳定的
    double calcualteTax()
    {
        //计算税收
        Context context;
        double val = m_strategy->Calculate(context);
        return val;
    }
    
    ~salesOrder()
    {
        
    }
};


//主程序入口
int main()
{
    /*
    strategy -> taxUS
    sale   ->  salesOrder - >taxUS 
    
    
    */
    //这里要考虑内存管理与释放,所有用了智能指针
	shared_ptr<taxStrategy> strategy(new taxUS());
	shared_ptr<salesOrder> sale(new salesOrder(strategy));
    cout<<sale->calcualteTax();
}

 

 
```

## 总结

* Strategy模式是用来解决变化的算法需求与客户对象的分离的.它在一定情况下可以替代if ... else ... 和 switch 的使用,从而维持了设计中的开放封闭原则.
* 如果Strategy对象没有实例变量,那么上下文可以共享一个Strategy对象,从而节省对象开销.