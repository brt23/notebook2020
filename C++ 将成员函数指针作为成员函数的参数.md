# C++ 将成员函数指针作为成员函数的参数

在c++11标准下，可以使用如下方式定义成员函数指针，并使用成员函数指针作为其他成员函数参数。

## 背景

想要在C++中实现一个简易的有限状态机。状态机使用类的方式实现，因为状态完全已知，为了方便将状态函数全部都实现为状态机类的成员函数。类的定义如下：

```C++
class DragStateMachine
{
public:
    // 定义的类状态函数指针
	typedef std::string(DragStateMachine::*StateFunc)(Leap::Hand& hand);

private:
	PaperAndIndexClick switch_;

private:
	std::string currentState_ = "start";

private:
    // 定义的类状态函数
	std::string startState(Leap::Hand& hand);
	//std::string draggingState(Leap::Hand& hand);
	std::string endState(Leap::Hand& hand);

private:
    // 想要把所有的状态函数都存放到该map数据结构中
    // 方便函数调用
	std::map<std::string, StateFunc> handle;
	void addState(std::string stateName, StateFunc);

public:
	void initState();
	void changeState(Leap::Hand& hand);
};
```

## 遇到的问题

最开始的时候，将类状态函数指针定义为：

```c++
// 未在指针之前加类名
typedef std::string(*StateFunc)(Leap::Hand& hand);
```



然后使用指针的时候出问题了，出问题的使用方法如下：

```c++
// 未在指针之前加类名
typedef std::string(*StateFunc)(Leap::Hand& hand);

// 这样使用指针就会报： error C2276: “&”: 绑定成员函数表达式上的非法操作 错误
void
DragStateMachine::initState()
{
	this->addState("start", &this->startState);
	this->addState("end", &this->endState);
}
```

然后将指针使用方法改为：

```c++
// 未在指针之前加类名
typedef std::string(*StateFunc)(Leap::Hand& hand);

// 这样使用指针一样会报错
void
DragStateMachine::initState()
{
	this->addState("start", &DragStateMachine::startState);
	this->addState("end", &DragStateMachine::endState);
}
```

然后在网络上查找解决方法，因为类函数会隐形传递this指针，所以将定义的类状态函数指针该变了一下，将定义个参数改为void*:

```c++
// 未在指针之前加类名，将函数第一参数改为void*
typedef std::string(*StateFunc)(void*, Leap::Hand& hand);

// 使用指针的时候，将函数指针强制转换为StateFunc
// 这样这里就不会报错了
void
DragStateMachine::initState()
{
	this->addState("start", (StateFunc)&DragStateMachine::startState);
	this->addState("end", (StateFunc)&DragStateMachine::endState);
}

// 但是使用指针解引用的时候就会报错，说不能给不是指针的变量解引用
void
DragStateMachine::changeState(Leap::Hand& hand)
{
	StateFunc func = this->handle[this->currentState_];
	std::string newStateName = (*func)(hand); // 这里报错
	this->currentState_ = newStateName;
}
```

## 解决方式

最后终于发现了能成功解决问题的方法：

```c++
// 定义的类状态函数指针
// 类函数指针定义的时候一定要加类名
typedef std::string(DragStateMachine::*StateFunc)(Leap::Hand& hand);

// 使用的时候要用(this->*func)的方式来调用
void
DragStateMachine::changeState(Leap::Hand& hand)
{
	StateFunc func = this->handle[this->currentState_];
	std::string newStateName = (this->*func)(hand); //使用这样的方式来调用就没有问题了
	this->currentState_ = newStateName;
}
```

