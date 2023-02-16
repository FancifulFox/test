# 问题集

* 问：在传入函数参数的时候，值传递，引用，和指针的使用时机
* set和list的排序算法sort在自定义排序的时候算法底层到底是什么样子的，什么时候用仿函数什么时候用bool
* 为什么map在用迭代器的时候不能用it<m.end()
* 文件的读入和写入，ifs.eof()咋用的，为什么在之前要先读入一个数据

# 有趣的知识点

* 构造函数是否可以写成私有的属性呢，是可以的，但是在外界不能调用这个对象里的私有函数，想要调用就只能用对象内部的成员，可是没有构造函数我们就不能创建这个对象，这里我们的就觉办法就是通过static或者是友元，static共有成员是独立于对象而存在的。

# 基础编程

(因为在学这个部分的时候我还没有用typora，所以这里就做不到很清楚的分类了，之后一直到类和对象之前都是比较零碎的知识点)

#### 生成随机数

rand(),生成范围想要多大就在后面%x,例如rand()%100就表示0到99的数不然默认为(0,1),但是这样写是伪随机，所以要想真随机应该加

```C++
#include<ctime>
srand((unsigned int)time(Null));//利用系统当前时间作为随机数种子，可以做到真随机
```

#### 函数的值传递

函数在调用的时候是把实参传递给函数的形参，在进行函数内部的操作之后，实参并不发生变化，变化的只有形参。

#### 函数的分文件编写

#### 指针所占的内存空间

在32位操作系统下，所有类型的指针所占的内存空间都是4位

#### const修饰指针

1.常量指针
const int* p = &a
指针指向的值不能改变，但是指针本身可以指向其他的地址，也就是说不能通过指针来操作改变a的值
2.指针常量
int* const p = &a
指针指向的值可以改变，但是指针不能改成指向其他地址，这样是禁止的。
3.两者结合，形式结合，结果的效果也结合



# 核心编程

## 杂碎知识点

### 程序的内存模型

1.代码区的特点是共享和只读
2.全局区中存放全局变量，静态变量（static),和常量
3.常量区包括const修饰的全局常量和字符串常量
4.堆区开辟的数据返回都是地址，要用指针进行接收
5.用堆开出的数组，用delete【】来删除，不是数组则单纯用delete即可

### 引用

引用的本质其实是一个常量指针，这也就说明了为什么引用不可以更改引用其他的变量。
引用必须初始化。
引用不可以接受字面值常量，也不可以接受临时变量如表达式的结果，也不可以接收不匹配的对象，函数的返回值如果不是引用类型，也算是一个临时变量，所以如果接收了就会报错，但是常量引用就可以绑定一个临时量而不出现错误，因为不会用引用去修改(正常的我们使用引用一般都是为了对原来的数据进行操作）。指向常量对象时也必须要用常量引用，原因和之前类似，也算是一个临时变量。
引用依然不能作为返回值返回局部变量。

### 函数重载

1.函数重载必须要在同一个作用域下（通常是全局作用域）
2.函数名称相同
3.函数参数不同或者个数不同或者顺序不同
*注意：不能用函数返回值作为函数重载的条件

## 类和对象

### 类的初识

类class和结构体struct的区别在于类之中的数据全部默认为private
而struct里面的数据全部默认为public。
class里面的数据分为public，protect，和private，都可以在类内进行访问，除了public都不可以在类外访问。
如果想从类外对类内的东西进行访问，只能用类内的public函数进行操作。
有时一个类太多内容可以新建一个.h和.cpp的文件，在前者里面写函数声明和成员变量声明和对一些头文件的添加，还要加一个#pragma once防止头文件重包含，在后者里面具体写对每一个函数的实现并包含头文件，函数名之前要写类名：：来表示在这个作用域里面。具体也可以重新看一下函数分文件编写，注意相同的地方和不同的地方。

### 构造函数和析构函数

#### 基本概念

构造函数形式 类名p（）{} 析构函数形式～类名p（）{}
都没有返回值，不用写void
只要创建对象就会执行构造函数，只要释放就会执行析构函数
拷贝构造函数的形式 类名（const 类名 &p){}
这两个函数都可以含参数并且调用，其中调用默认构造函数时不能用类名 p(),就是不能用括号，不然会认为是声明。
不要用拷贝函数来初始化匿名对象，匿名对象就是类名直接（）里面加上要传入的参数值，这样就相当于没有名字，可以用给当作赋值的右值，执行完立马回收，不能拷贝的原因是因为这样系统会将其当成一个声明（？没细搞这里），也就是不能直接类名(p)
==拷贝构造函数在用作值传递作为返回值或者作为参数的时候都会被调用==

#### 构造函数调用规则

* 如果用户定义有参构造函数，c++不在提供默认无参构造，但是会提供默认拷贝构造

* 如果用户定义拷贝构造函数，c++不会再提供其他构造函数

#### 深拷贝与浅拷贝

浅拷贝就是系统简单的赋值，一一对应，但是在运用到指针的时候就会出现一些bug，首先在类里开辟堆区用指针来接收，在析构函数里就要写delete函数将堆区释放，并且将指针指向空指针防止野指针出现，但是在复制拷贝的时候容易出错，因为析构函数会进行两次释放指针，所以深拷贝就是自己写一个拷贝构造函数，在里面int* p=new int(*p1),也就是重新开一个堆的空间，这样确保不会出问题。

#### 初始化列表

类名（构造函数）（int a,int b,int c）: m_a(a),m_b(b),m_c(c){}

类对象作为类成员，这个类成员先创造后释放。

#### 静态成员

###### 静态成员变量

静态成员首先所有内容是共享的,任意一处都可以进行修改，在类内声明在内外进行赋值初始化，不赋值也行默认为0，但是必须要进行声明，不然报错，在类外的初始化可以不用写类名，但是要写数据类型。但这样的话就会变成全局变量，所以前面要加类名：：，这样表示作用域是在类之下的。对静态变量进行访问的时候可以有两种方式，一种是直接类名(对象名）.变量的形式，一种是作用域（类）写在前面然后加变量名，两种方式都可以进行访问。

###### 静态成员函数

静态成员函数是所有对象共享的，访问方式有两种，和上面变量的访问是一样的，**静态成员函数可以直接写在类里面**。要注意的是静态成员函数它只能访问静态变量，因为如果在静态成员函数里面访问非静态成员变量，在有多个对象的时候不知道访问是哪一个对象的变量。

### 4.3 C++对象模型和this指针

#### 4.3.1 成员函数和成员变量分开存储

空对象所占内存为1，为了防止不同的空对象占用同一个内存。含正常的数据类型的变量的对象所占内存和正常的一致，然后静态成员变量和成员函数都不属于类的对象，也就是在sizeof对象的时候，不会计算其内存。

#### 4.3.2 this指针概念

##### 1.解决名称冲突

当形参和成员变量同名的时候，可以用this->加变量名来表示被调用函数所属对象的成员变量，注意this本质是一个指针

##### 2.返回对象本身用this

只需在函数末尾加上return *this就可以了，因为this指向的是这个对象，所以想要返回这个对象就可以用这个方法，注意如果返回值类型写的是类名，则是调用拷贝构造函数导致开辟了一个新的对象，所以如果想要一值是这个对象进行累加应该用引用的方式，也就是**类名+&**表示返回值是引用，这样就能进行不断的迭代。

```C++
Person& PersonAddPerson(Person p)
	{
		this->age += p.age;
		//返回对象本身
		return *this;
	}
```

如上所示

#### 4.3.3 空指针访问成员函数

空指针也是可以进行对成员函数的访问，这是因为成员函数的存储不在类内，并不会有影响但是这个函数之中不能调用对象内的成员变量，因为函数调用成员变量时相当于在变量前面加上了this->，如果指针已经是空的了，则不能访问到这个地址，所以一般为了代码的健壮性，会在之前加上判断语句

```C++
if（this==Null）{
	return；
}
```

#### const修饰成员函数 

在成员函数后面加const，修饰的是this的指向，this本质就是一个指针常量，如果想修改成常量指针，要在函数后面加const。如果想在其中进行数值的修改，就要在成员属性声明的时候加上关键字mutable。  对象前面加上const代表的就是常对象，常对象只能调用常数函数，因为如果不是常数函数的话，其他的函数内部可以修改常对象某个成员变量的函数，这样就违背了。

### 4.4 友元

#### 4.4.1全局函数做友元

全局函数不能访问类之中的私有成员，如果想要可以访问，就要在类里面对这个全局函数进行一个声明，前面加上一个friend关键字。

#### 4.4.2 友元类

和全局函数一样，一个类也不能访问另一个类的私有成员，如果想要访问就要在这个类里面声明，在其之前加上关键字friend

注意我们可以在类内写声明，在内外进行函数的定义，只不过要在前面写上函数名之前写上他的作用域是在类里面。

```C++
class Building;
class goodGay
{
public:

	goodGay();
	void visit();

private:
	Building *building;
};


class Building
{
	//告诉编译器 goodGay类是Building类的好朋友，可以访问到Building类中私有内容
	friend class goodGay;

public:
	Building();

public:
	string m_SittingRoom; //客厅
private:
	string m_BedRoom;//卧室
};

Building::Building()
{
	this->m_SittingRoom = "客厅";
	this->m_BedRoom = "卧室";
}

goodGay::goodGay()
{
	building = new Building;
}

void goodGay::visit()
{
	cout << "好基友正在访问" << building->m_SittingRoom << endl;
	cout << "好基友正在访问" << building->m_BedRoom << endl;
}

void test01()
{
	goodGay gg;
	gg.visit();

}
```

#### 4.4.3 成员函数做友元

若想一个类内的成员函数可以访问另外一个内内的私有成员，可以将该函数在另外一个类里面进行声明，并且加上关键字friend，同时函数名之前也要加上作用域是原类。

### 4.5 运算符重载

运算符重载的目的==是为了让运算符有新的运算法则以适应不同的对象要求==

#### 4.5.1 加法运算符重载

加法运算符重载主要用于自定义的类之间的相加

```C++
Person operator+(const Person &p){
    Person temp;/创建临时变量
    temp.m_A=this->m_A+p.m_A;/因为这是在类的成员函数，所以在调用这个函数的时候，this指向两个加数中的一个
    return temp;
}
```

还有全局函数的写法，与上面不同的就是他要传入两个参数，其他没有什么差别。

加法运算重载符还可以进行函数的重载，以便不同的类型相加，比如类中的某一个数和一个常数相加。

注意的点：对于内置数据类型本身的运算符不能进行修改，其次最好不要在加法运算符重载函数里面写其他操作，不能乱用。

#### 4.5.2 左移运算符重载

（左移运算符本来以为是在一个数的次方乘里面）不知道为啥竟然是cout后面跟着的东西

cout << 后面只能输出的是固有内置类型，如果想直接输出一个类里面的东西需要自己写左移运算符重载。首先不能在类内用成员函数写重载函数，因为你不知道括号里面该写啥（其实这一块感觉老师讲的不是很好，差强人意吧），只能用全局函数写

```C++
ostream& operator<<(ostream& cout, Person& p)/因为希望在执行了这个重载函数之后还能跟在后面继续输出，所以返回值要是和
{						/cout一样的类型，因为ostream只能有一个这样的数据类型，所以要用引用，不然会创造出新的这样的类型
    cout<<p.m_A<<enl;
 	return cout;
}
```

如果访问的是私有成员变量，可以用之前的友元来解决。

#### 4.5.3 递增运算符重载

递增运算符重载也是对类里面的数据进行操作的一个方法，分为前置递增和后置递增。

前置递增代码如下

```C++
MyInteger& operator++(){/这里不用传入参数是因为这本身就是类内的成员函数,调用的时候可以直接访问类内的成员变量
    (this->)m_A++;
    return *this；/这里返回值是引用的原因是因为引用始终指向的是一个内存空间，如果换成返回值为类的话就是创造了新的对象，并没有对这个对象进行操作。
}
```

后置代码如下

```c++
MyInteger operator++(int){/这里要写一个int是为了防止函数重定义，int作为占位参数可以起到一个函数重载的作用
   MyInteger temp = *this;/和前置不一样，我们是先返回结果再进行递增，但如果直接返回函数就结束了，所以我们先用变量记住它原来的值
   (this->)p.m_A++;
   return temp;/这里返回值为类的原因是因为这里temp是临时变量，如果返回的是引用，这个内存会被释放，之后不能再进行访问。
}
```

#### 4.5.4 赋值运算符重载

创建一个对象的时候系统除了自动给你加三个构造函数之外还会给你加一个赋值运算符重载函数，赋值运算符重载的问题在于他是直接将对象的值进行赋值，也就是之前说的浅拷贝，这样会出先堆区重复释放的过程，所以我们要对被赋值的对象进行赋值运算符重载，进行深拷贝。

```C++
Person& operator=(Person &p1){/这里用Person& 的原因是因为为了可以连等
    /首先判断被赋值的对象是否已经有了堆区，如果有的话就进行释放
    if(m_A!=Null){
        delete p2.m_A;
        m_A=null;
	}
	m_A=new int(*p1.m_A);
    return *this;
}
```

(wuwuwu······有点小累，但要坚持)

#### 4.5.5 关系运算符重载

（哈哈，终于有个很简单的了）

关系运算符重载就是判断两个对象是否相等或者不相等

```C++
bool operator==(Person &p1){
    if(m_A==p1.m_A){
		return true;
    }
	return false;
}
```

#### 4.5.6 函数调用重载

函数调用重载就是对()进行重载，形式多样，返回值和参数都根据自己的需求编写，例如之后的STL语法里面就会用到很多，由于重载后使用的方式非常像函数的调用，因此称为仿函数，还要注意函数调用重载也可以用作匿名对象

下面偷懒就copy一份代码啦

```C++
class MyPrint
{
public:
	void operator()(string text)
	{
		cout << text << endl;
	}

};
void test01()
{
	//重载的（）操作符 也称为仿函数
	MyPrint myFunc;
	myFunc("hello world");
}
```

可以看出确实挺灵活的。

### 4.6 继承

#### 4.6.1 基本语法

继承的主要目的就是减少代码的重复性，当很多类都含有同一个类的代码时，就可以用继承的方法继承这个类里面的成员

语法主要是：class 子类 ： 继承方式  父类。子类也称为派生类，父类称为基类。

#### 4.6.2继承方式

继承方式主要有三种：公有继承，保护继承和私有继承。在父类中的私有成员用何种继承方式都不能继承下去。

公有继承除了私有成员，其他的和父类的一样。保护继承除了私有成员，父类的公有成员和保护成员都是保护成员。私有继承除了私有成员，父类的公有成员和保护成员都是私有成员。**后两者的表现形式都是在类内可以访问，类外不可以访问，但是如果想确切地知道到底是保护还是私有，可以再次进行一次继承，如果继承后的类里还是不可以访问那就是私有。**

#### 4.6.3 继承中的对象模型

父类中非静态成员都会被继承下去，虽然私有成员不可访问，但是占了内存，也就是在计算大小的时候是要算上私有成员的。

#### 4.6.4 构造和析构的顺序

继承中 先调用父类构造函数，再调用子类构造函数，析构顺序与构造相反

#### 4.6.5 继承同名成员处理方法

1. 子类对象可以直接访问到子类中同名成员
2. 子类对象加作用域可以访问到父类同名成员
3. 当子类与父类拥有同名的成员函数，子类会隐藏父类中同名成员函数，加作用域可以访问到父类中同名函数。**也就是说，如果父类中有函数重载，子类里面没有，则不能直接调用这个重载了的函数。**

#### 4.6.6 同名静态成员处理

同名静态成员处理方式和非静态处理方式一样，只不过有两种访问的方式（通过对象 和 通过类名）

```c++
cout << "通过类名访问： " << endl;
cout << "Son  下 m_A = " << Son::m_A << endl;
cout << "Base 下 m_A = " << Son::Base::m_A << endl;
```

注意这里的写法，第一个：：代表的是这是通过类名访问，第二个：：代表的是它是在Base的作用域下。这里还要注意的一个点就是静态变量的内存问题，和之前说的父类非静态变量都能继承一样，其实说的不太准确，静态的大家都可以访问，所以显示出来也是继承了的样子，只不过在内存上，是单独开辟出来的，在全局区。

#### 4.6.7 继承语法

一个子类可以同时继承两个甚至多个父类，语法为 class 子类：public 父类1，public 父类 2······

如果两个父类同时出现了相同的变量，则在调用的时候要注意在变量前说明作用域。其实在我们写项目的时候不建议一个类继承多个父类，因为通常不同的人写不同的类，如果变量弄重了，之后会非常麻烦。

#### 4.6.8 菱形继承

当两个派生类继承了同一个基类之后，如果又来了一个新的派生类继承了这两个派生类，就会出现二义性问题，可以加上作用域，但是会浪费资源，因为创造了两份资料。

解决这一问题的办法就是用虚继承，在public前面写上一个virtual，这时候内部会创建一个虚基类指针，指向虚基列表，虚基列表里面储存的是一个偏移量，加上这个偏移量指向的是这个派生类里面的数据。

### 4.7 多态

(不得不说，这核心编程真的有点恶心人啊啊啊啊，有点看不下去了，但好好调节一下还是继续加油🐷🐷🐷)

#### 4.7.1 多态的基本概念

多态大概的本质就是说多种形态，分为两类：静态多态和动态多态。静态多态一般就是指函数重载和运算符重载，复用函数名这样的，在函数编译的时候就已经确定了函数的地址；动态多态就是派生类和虚函数实现运行时多态，在运行的时候才确定函数的地址。

```C++
class Animal
{
public:
	//Speak函数就是虚函数
	//函数前面加上virtual关键字，变成虚函数，那么编译器在编译的时候就不能确定函数调用了。
	virtual void speak()
	{
		cout << "动物在说话" << endl;
	}
};

class Cat :public Animal
{
public:
	void speak()
	{
		cout << "小猫在说话" << endl;
	}
};

class Dog :public Animal
{
public:

	void speak()
	{
		cout << "小狗在说话" << endl;
	}

};
//我们希望传入什么对象，那么就调用什么对象的函数
//如果函数地址在编译阶段就能确定，那么静态联编
//如果函数地址在运行阶段才能确定，就是动态联编

void DoSpeak(Animal & animal)
{
	animal.speak();
}
//
//多态满足条件： 
//1、有继承关系
//2、子类重写父类中的虚函数
//多态使用：
//父类指针或引用指向子类对象

void test01()
{
	Cat cat;
	DoSpeak(cat);

	Dog dog;
	DoSpeak(dog);
}


int main() {

	test01();

	system("pause");

	return 0;
}
```

这里粘贴代码，主要是为了进行自己的对应的理解。==首先想实现动态多态，第一要有继承关系，第二是在子类里面要对父类的虚函数进行重写(函数返回值类型 函数名 参数列表 完全一致称为重写)，因为虚函数对应的是一个vfptr（virtual function pointer虚函数指针),指向的是一个vftable(虚函数表)，这里面存储的是被调用函数的地址，在继承的过程中，子类的虚函数表里面存储的是父类的虚函数表，如果想要调用子类的函数，就要重写这个函数，前面可以不用加virtual，重写这个函数之后它的虚函数表里面存储的就是自己的函数的地址，在调用的时候指向的就是自己的函数。==

然后就是多态的使用场合，一般都是父类的指针或者引用指向的是一个子类对象(这个操作是合理的)，这个时候我们的目的大多都是为了调用子类里面的函数，所以这个时候多态就显得格外重要。

#### 4.7.2 多态的好处

多态的结构清晰，可读性强，便于后期的维护和拓展，不用修改源码，很方便。方法就是如上父类里的成员函数变成虚函数，子类对父类的这个函数进行重写，实现的时候用父类的指针或者引用调用子类，引用之前已经展示了，指针的写法就是`父类 *对象名=new` 子类

#### 4.7.3 纯虚函数和抽象类

我们在4.7.1里面就可以发现，父类的虚函数里面的实现几乎没什么用，所以我们往往直接将他置成纯虚函数，纯虚函数的写法就是virtual 返回值类型（大多数时候为void) 函数名 = 0，只要有这么一个纯虚函数，则整个类就叫做抽象类，抽象类不可以实例化对象，而且子类里面必须要对父类的这个纯虚函数进行重写，否则这个子类也会成为抽象类，不可以实例化对象。所以通常我们使用多态的时候都要求子类对父类进行函数重写。

#### 4.7.4 虚析构和纯虚析构

(这一节的东西都不太能从底层逻辑上去理解清楚，所以这里只是先简单地说明一下具体的用法和原因)

我们在写多态的时候，如果在子类中开辟了堆区数据，一般是在析构函数中进行释放，但是父类指针指向子类的时候，父类指针释放是不会执行子类的析构函数，所以会导致子类的堆区内存空间泄露，解决方式：将父类中的析构函数改为**虚析构**或者**纯虚析构**，如果子类中没有堆区数据就可以不用。

虚析构和纯虚析构的相同之处在于都可以解决子类析构函数不被调用的问题，而且虚析构和纯虚析构都要有具体的函数实现(==这里就不是很懂==)，但是不同之处在于如果是纯虚析构函数的话就属于抽象类，不能实例化对象。纯虚析构函数的语法是内类声明，内外加上作用域进行函数实现。

## 文件的读写

### 5.1 文本文件

#### 5.1.1 文本文件的写

文件的写分为以下几步

1. 加上头文件<fstream>
2. 创建一个ofstream的类，可以起名叫ofs
3. 读写文件的语法就是ofs.open("文件名"，**读写方式**)；
4. 写数据ofs<<“写入的数据”；
5. ofs.close();

| 打开方式    | 解释                       |
| ----------- | -------------------------- |
| ios::in     | 为读文件而打开文件         |
| ios::out    | 为写文件而打开文件         |
| ios::ate    | 初始位置：文件尾           |
| ios::app    | 追加方式写文件             |
| ios::trunc  | 如果文件存在先删除，再创建 |
| ios::binary | 二进制方式                 |

**注意：** 文件打开方式可以配合使用，利用|操作符

**例如：**用二进制方式写文件 `ios::binary | ios:: out`

#### 5.1.2 文本文件的读

文件的读和文件的写类似，不同的地方在于类名要改成ifstream(其实两者都可以用fstream)，还多了一个语法，is_open来判断是否打开了文件。

以下是读文件的三种方式，还有一种逐个读字符的这里就忽略了。

```C++
//第一种方式
	char buf[1024] = { 0 };
	while (ifs >> buf)
	{
		cout << buf << endl;
	}

	//第二种
	char buf[1024] = { 0 };
	while (ifs.getline(buf,sizeof(buf)))
	{
		cout << buf << endl;
	}

	//第三种
	string buf;
	while (getline(ifs, buf))
	{
		cout << buf << endl;
	}
```

### 5.2 二进制文件

#### 5.2.1二进制文件的写

基本步骤一致，但是在打开方式的时候要加上iOS：：binary ,另外还要单独用write函数来进行写入

```c++
ofs.write((const char *)&p, sizeof(p));
```

这就是二进制文件写入的语法，调用write函数，然后强制将要写入的东西进行取地址操作，这里的“东西“最好使用char数组而不是用string，会出现一些不知名的错误，后面的参数就是这个数组的大小。

#### 5.2.2 二进制文件的读

基本步骤一致，也要打开iOS：：binary，要用read函数来进行读

```C++
ifs.read((char *)&p, sizeof(p));
```

注意这里的参数形式不一样，前面是char *,没有const

# 提高编程

## 1.模板

### 1.1 模板的概念

模板的作用就相当于网上的PPT模板，为了提高复用性，将大体相同但数据类型等不同的函数进行修改，提供一个模板函数。

### 1.2 函数模板

#### 1.2.1 函数模板的基本语法

函数模板的基本语法就是template<typename T>，typename 也可以换成class，这里的T是自己定义的，代表一个广泛的数据类型，不确定的，提前进行声明这样服务器就不会进行报错，之后在写函数的时候，将数据类型换成T。

例如在交换两个数的值的函数中，我们可以用T来写swap函数，在调用的时候可以直接写swap(a,b)，这样系统帮你自动判断数据类型，也可以写swap<int>(a,b)来指定数据类型，两种方式都可以。

```C++
template<typename T>
函数声明或定义
```

可以发现，函数的声明或者定义必须紧跟其下，否则就出错。

#### 1.2.2 函数模板的注意事项

1. 上面说的，template必须在你每一个要写的泛型函数前面都要写一个
2. 自动判定的时候必须要数据类型一样
3. 这个通用数据类型T必须在使用时有确定的指代，如果没有的话可以直接用指定法在前面随便加一个数据类型例如<int>

#### 1.2.3 普通函数和函数模板的区别

普通函数和函数模板的区别在于普通函数可以进行自动类型转换(隐式类型转化)，而函数模板的自动类型推导则不能发生隐式类型转换，但是函数模板的显示指定类型的方式可以指定数据类型进行转换，也就是自己确定通用类型T

#### 1.2.4 普通函数和函数模板的调用规则

1. 函数模板和普通模板同时可以调用的时候优先调用普通模板。
2. 如果想调用函数模板可以使用空模板参数列表强制调用函数模板。
3. 函数模板可以发生函数重载
4. 如果函数模板和普通模板同时都可以实现，但是普通模板要进行自动类型转化，编译器会优先调用函数模板。

**总结**：如果使用了函数模板就最好不要使用普通模板了，防止产生二义性。

#### 1.2.5 模板的局限性和解决办法

模板的局限性在于有些时候他的T不能包含各种类型，比如自定义的类，还有数组类型等一些。

解决办法有两种，一种是对函数里面的运算符进行重载，还有一种方法就是单独多写一条函数模板，就相当于是一个补充，通过具体化的操作使函数模板通用化。

```c++
//具体化，显示具体化的原型和定意思以template<>开头，并通过名称来指出类型
//具体化优先于常规模板
template<> bool myCompare(Person &p1, Person &p2)
```

- 学习模板并不是为了写模板，而是在STL能够运用系统提供的模板(standard template library)

### 1.3 类模板

#### 1.3.1 类模板的基本语法

类模板和函数模板很像，一样的语法之后紧跟着的由函数换成类就可以了，由于类内可能会含有多个参数，所以在typename之后可以加逗号，多定义几个通用类型。

```C++
//类似于以下代码
template <typename &p1,typename &p2>
类名
```

同时，在之后的显示指定类型中也要写相对应数量的数据类型名称。

#### 1.3.2 类模板和函数模板的区别

- 类模板使用只能用显示指定类型方式
- 类模板中的模板参数列表可以有默认参数，有了参数之后在后续的显示指定类型的方式的时候可以不用加上对应的数据类型

#### 1.3.3 类模板中成员函数的创建时机

类模板中的成员函数只要没有调用都不会进行创建，普通的类中的成员函数则是一开始就进行创建。因为编译器不知道到底T是哪个类型，也就不知道去调用哪个成员函数。

#### 1.3.4 类模板对象作函数参数

1. 指定传入的类型 — 直接显示对象的数据类型：==就是直接在函数参数中用引用的方式，将正常函数参数的类型变成类模板对象的类型==经过测试，发现其实这里传入的不一定要引用，传入一个值也可以，但可能引用有他独特的优势把。
2. 参数模板化 — 将对象中的参数变为模板进行传递：就是函数模板和类模板结合使用，使用较少，分别传入类中的数据类型
3. 整个类模板化 — 将这个对象类型 模板化进行传递：同上，不过传入的是整个类

**这里还有一个小用法就是typeid(p).name，这个可以检测通用类型的类型是什么**

```C++
//1、指定传入的类型
void printPerson1(Person<string, int> &p) 
{
	p.showPerson();
}
void test01()
{
	Person <string, int >p("孙悟空", 100);
	printPerson1(p);
}

//2、参数模板化
template <class T1, class T2>
void printPerson2(Person<T1, T2>&p)
{
	p.showPerson();
	cout << "T1的类型为： " << typeid(T1).name() << endl;
	cout << "T2的类型为： " << typeid(T2).name() << endl;
}
void test02()
{
	Person <string, int >p("猪八戒", 90);
	printPerson2(p);
}

//3、整个类模板化
template<class T>
void printPerson3(T & p)
{
	cout << "T的类型为： " << typeid(T).name() << endl;
	p.showPerson();

}
void test03()
{
	Person <string, int >p("唐僧", 30);
	printPerson3(p);
}
```

通常我们只用第一种方式，后面两种方式都很麻烦。

#### 1.3.5 类模板与继承

父类是模板的时候子类要是继承父类得定义父类中数据的类型，比如

```C++ 
template <class T>
class Base{
public：
    T m_A;
}
class Son:public Base<char>{}
//这样才能明确确定父类中这个通用数据类型的空间大小，才能开辟相应大小的空间
```

如果对父类的数据类型不确定的话，也可以将子类写成一个类模板

```C++
template <class T1,class T2>
class Son:public Base<T2>{
public:
    Son(T1 a,T2 b){
        m_B=a;
        m_A=b;//这么写也是错的，因为不知道这里调用的是父类还是子类的，前面应该加一个this->
        this->m_A=b;
}
    T1 m_B;
}
void test01(){
    Son p1<int ,char>(10,a)；//这么写是错的，正确的写法应该是显示指定的类型在对象前面
    Son <int ,char >p2(10,a);
}
```

#### 1.3.6 类模板构造函数和成员函数的类外实现

构造函数和成员函数的类外实现方法都是类似的，具体实现方法如下

```C++
template<class T1,class T2>//1.首先第一步是用template语法对通用类型进行声明
void Person<T1,T2>：：函数名(){T1 name,}//首先这里要加Person作类名表示作用域，其次在：：的前面加上指定类型
```

#### 1.3.7 类模板的分文件编写

类模板的分文件编写和正常的函数分文件编写是不一样的（这里花了我好长时间去理解他的底层逻辑），这里先复制一个链接[(6条消息) C++中类模板分文件编写出现问题的原因（涉及函数定义作用、编译过程等）_对的时间点的博客-CSDN博客_c++分文件写程序要注意那些](https://blog.csdn.net/qq_41929943/article/details/103004083?ops_request_misc=%7B%22request%5Fid%22%3A%22166606181716782390523076%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=166606181716782390523076&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-3-103004083-null-null.142^v58^new_blog_pos_by_title,201^v3^control&utm_term=类模板的分文件编写&spm=1018.2226.3001.4187)

好了，下面我用自己的话来说一下，在正常的函数的分文件编写之中，首先所有的源码中的头文件都会被替换成头文件里面的代码，这是在预处理的时候发生的，然后如果源码中调用了一个没有定义的函数，编译器会认为这个函数的定义在其他的文件中，同时把这个函数当成一个特殊符号，然后将几个cpp文件通过连接器放到一起，然后对这个特殊符号进行处理。正常的函数分文件编写中编译器在编译的时候就已经进行了函数创建，所以在一个文件中进行声明就能在另一个文件中找到他的定义然后进行使用，但是类模板的话不一样，类模板的创建必须要声明，定义以及他的模板的参数列表三个缺一不可，现在一个cpp文件中对这个类模板进行声明，按理说要调用其中的成员函数就要去找他的定义，但是发现在定义类模板的文件中缺少参数列表，参数列表跟在使用这个函数的后面的括号里面，也就是我想要使用这个函数就得调用他的定义，我只有调用了他的定义我才能传参，但我这个不传参这个类模板就没有创建，所以就调用不了，所以有这么一个矛盾。

解决办法就是可以直接调用.cpp文件，还有一种解决办法就是头文件和源文件写在一起，调用的时候写.hpp文件就可以了。

#### 1.3.8 类模板与友元

类模板中全局函数配合友元进行类内的实现，直接在前面加上一个friend就行；如果进行类外实现，首先要在内部的声明时加上一个空模板列表，加在括号前面，其次实现要写在这个类之前，并且在实现的前面还要声明一下这个类，提前让编译器知道，所以说这里很麻烦，所以尽量用前者。

#### 1.3.9 类模板案例：数组类封装

我真的是服了，总是有些小地方搞得不对，最后总算是搞完了，踩的坑其实不多，在这里记录一下

1. 自己写的类模板数组要重载[]号，因为不是真正的数组，这个数组类中的数组是它的组成部分，而不能直接将其当成数组。
2. 如果是自己写的类，要补上一个无参构造函数，因为在数组类用new开辟空间的时候用到的都是无参构造函数。

## 2.STL初识

### 2.1 STL的诞生

- 长久以来，软件界一直希望建立一种可重复利用的东西
- C++的**面向对象**和**泛型编程**思想，目的就是**复用性的提升**
- 大多情况下，数据结构和算法都未能有一套标准,导致被迫从事大量重复工作
- 为了建立数据结构和算法的一套标准,诞生了**STL**

### 2.2 STL基本概念

- STL(Standard Template Library,**标准模板库**)
- STL 从广义上分为: **容器(container) 算法(algorithm) 迭代器(iterator)**
- **容器**和**算法**之间通过**迭代器**进行无缝连接。
- STL 几乎所有的代码都采用了模板类或者模板函数

### 2.3 STL六大组件

STL大体分为六大组件，分别是:**容器、算法、迭代器、仿函数、适配器（配接器）、空间配置器**

1. 容器：各种数据结构，如vector、list、deque、set、map等,用来存放数据。
2. 算法：各种常用的算法，如sort、find、copy、for_each等
3. 迭代器：扮演了容器与算法之间的胶合剂。
4. 仿函数：行为类似函数，可作为算法的某种策略。
5. 适配器：一种用来修饰容器或者仿函数或迭代器接口的东西。
6. 空间配置器：负责空间的配置与管理。

### 2.4 STL中容器、算法、迭代器

**容器：**置物之所也

STL**容器**就是将运用**最广泛的一些数据结构**实现出来

常用的数据结构：数组, 链表,树, 栈, 队列, 集合, 映射表 等

这些容器分为**序列式容器**和**关联式容器**两种:

 **序列式容器**:强调值的排序，序列式容器中的每个元素均有固定的位置。
​ **关联式容器**:二叉树结构，各元素之间没有严格的物理上的顺序关系

**算法：**问题之解法也

有限的步骤，解决逻辑或数学上的问题，这一门学科我们叫做算法(Algorithms)

算法分为:**质变算法**和**非质变算法**。

质变算法：是指运算过程中会更改区间内的元素的内容。例如拷贝，替换，删除等等

非质变算法：是指运算过程中不会更改区间内的元素内容，例如查找、计数、遍历、寻找极值等等

**迭代器：**容器和算法之间粘合剂

提供一种方法，使之能够依序寻访某个容器所含的各个元素，而又无需暴露该容器的内部表示方式。

每个容器都有自己专属的迭代器

迭代器使用非常类似于指针，初学阶段我们可以先理解迭代器为指针

迭代器种类：

| 种类           | 功能                                                     | 支持运算                               |
| -------------- | -------------------------------------------------------- | -------------------------------------- |
| 输入迭代器     | 对数据的只读访问                                         | 只读，支持++、==、！=                  |
| 输出迭代器     | 对数据的只写访问                                         | 只写，支持++                           |
| 前向迭代器     | 读写操作，并能向前推进迭代器                             | 读写，支持++、==、！=                  |
| 双向迭代器     | 读写操作，并能向前和向后操作                             | 读写，支持++、–，                      |
| 随机访问迭代器 | 读写操作，可以以跳跃的方式访问任意数据，功能最强的迭代器 | 读写，支持++、–、[n]、-n、<、<=、>、>= |

常用的容器中迭代器种类为双向迭代器，和随机访问迭代器

### 2.5 容器算法迭代器初识

首先如果要使用容器和算法都要包含相对的头文件，算法的是Alogrithms,容器一般都是相对应的文件名。

这里用vector做一个例子，首先和之前我们写的数组类差不多，在调用的时候后面的尖括号里面都要写上对应的数据类型，然后通过迭代器得到数据，例如

```C++
vector<int>::iterator it=v.begin();//这段代码的效果就是通过迭代器得到其实数据的指针
```

==这里有一个技巧就是*it得到的数据类型和尖括号了里面的数据类型是一样的==，v.begin()指向容器的第一个，而v.end()指向的是数组容器最后一个的后面一个，所以遍历的方法就可以用这两个分别当开始和结束。还有一种方法是用系统内置算法for_each来遍历，用法是传入三个参数，一个是开始指针，一个是末尾指针，还有一个是执行的函数。

容器内还可以嵌套容器，类似于

```C++
vector<vector<int>>::iterator it=v.begin();
```

## 3.常用STL容器

### 3.1 string容器

#### 3.1.1 string基本概念

- string是C++风格的字符串，而string本质上是一个类

**string和char \* 区别：**

- char * 是一个指针
- string是一个类，类内部封装了char**，*管理这个字符串，是一个char*型的容器。

**特点：**

string 类内部封装了很多成员方法

例如：查找find，拷贝copy，删除delete 替换replace，插入insert

string管理char*所分配的内存，不用担心复制越界和取值越界等，由类内部进行负责

#### 3.1.2 string的构造函数

构造函数原型：

- `string();` //创建一个空的字符串 例如: string str;
  `string(const char* s);` //使用字符串s初始化

- `string(const string& str);` //使用一个string对象初始化另一个string对象

- `string(int n, char c);` //使用n个字符c初始化

- ```C++
  string s1; //创建空字符串，调用无参构造函数
  	cout << "str1 = " << s1 << endl;
  
  	const char* str = "hello world";
  	string s2(str); //把c_string转换成了string
  
  	cout << "str2 = " << s2 << endl;
  
  	string s3(s2); //调用拷贝构造函数
  	cout << "str3 = " << s3 << endl;
  
  	string s4(10, 'a');
  	cout << "str3 = " << s3 << endl;
  ```

  

#### 3.1.3 string的赋值

- 给string字符串进行赋值

赋值的函数原型：

- `string& operator=(const char* s);` //char*类型字符串 赋值给当前的字符串

- `string& operator=(const string &s);` //把字符串s赋给当前的字符串

- `string& operator=(char c);` //字符赋值给当前的字符串

- `string& assign(const char *s);` //把字符串s赋给当前的字符串

- `string& assign(const char *s, int n);` //把字符串s的前n个字符赋给当前的字符串

- `string& assign(const string &s);` //把字符串s赋给当前字符串

- `string& assign(int n, char c);` //用n个字符c赋给当前字符串

  ```C++
  string str1;
  	str1 = "hello world";
  	cout << "str1 = " << str1 << endl;
  
  	string str2;
  	str2 = str1;
  	cout << "str2 = " << str2 << endl;
  
  	string str3;
  	str3 = 'a';
  	cout << "str3 = " << str3 << endl;
  
  	string str4;
  	str4.assign("hello c++");
  	cout << "str4 = " << str4 << endl;
  
  	string str5;
  	str5.assign("hello c++",5);
  	cout << "str5 = " << str5 << endl;
  
  
  	string str6;
  	str6.assign(str5);
  	cout << "str6 = " << str6 << endl;
  
  	string str7;
  	str7.assign(5, 'x');
  	cout << "str7 = " << str7 << endl;
  ```

  

#### 3.1.4 string字符串的拼接

- `string& operator+=(const char* str);` //重载+=操作符

- `string& operator+=(const char c);` //重载+=操作符

- `string& operator+=(const string& str);` //重载+=操作符

- `string& append(const char *s); `//把字符串s连接到当前字符串结尾

- `string& append(const char *s, int n);` //把字符串s的前n个字符连接到当前字符串结尾

- `string& append(const string &s);` //同operator+=(const string& str)

- `string& append(const string &s, int pos, int n);`//字符串s中从pos开始的n个字符连接到字符串结尾

- ```C++
  string str1 = "我";
  	str1 += "爱玩游戏";
  	cout << "str1 = " << str1 << endl;
  
  	str1 += ':';
  	cout << "str1 = " << str1 << endl;
  
  	string str2 = "LOL DNF";
  	str1 += str2;
  	cout << "str1 = " << str1 << endl;
  
  	string str3 = "I";
  	str3.append(" love ");
  	str3.append("game abcde", 4);
  	//str3.append(str2);
  	str3.append(str2, 4, 3); // 从下标4位置开始 ，截取3个字符，拼接到字符串末尾
  	cout << "str3 = " << str3 << endl;
  ```

#### 3.1.5 string查找和替换

**功能描述：**

- 查找：查找指定字符串是否存在
- 替换：在指定的位置替换字符串

**函数原型：**

- `int find(const string& str, int pos = 0) const;` //查找str第一次出现位置,从pos开始查找
- `int find(const char* s, int pos = 0) const; `//查找s第一次出现位置,从pos开始查找
- `int find(const char* s, int pos, int n) const; `//从pos位置查找s的前n个字符第一次位置
- `int find(const char c, int pos = 0) const; `//查找字符c第一次出现位置
- `int rfind(const string& str, int pos = npos) const;` //查找str最后一次位置,从pos开始查找
- `int rfind(const char* s, int pos = npos) const;` //查找s最后一次出现位置,从pos开始查找
- `int rfind(const char* s, int pos, int n) const;` //从pos查找s的前n个字符最后一次位置
- `int rfind(const char c, int pos = 0) const; `//查找字符c最后一次出现位置
- `string& replace(int pos, int n, const string& str); `//替换从pos开始n个字符为字符串str
- `string& replace(int pos, int n,const char* s); `//替换从pos开始的n个字符为字符串s

**总结**：

- find查找是从左往后，rfind从右往左
- find找到字符串后返回查找的第一个字符位置，找不到返回-1
- replace在替换时，要指定从哪个位置起，多少个字符，替换成什么样的字符，==并且替换的时候是多大就是多大，不是按照被替换的数据大小来分的。==

#### 3.1.5 string字符串的比较

**比较方式：**

- 字符串比较是按字符的ASCII码进行对比

= 返回 0

\> 返回 1

< 返回 -1

**函数原型：**

- `int compare(const string &s) const; `//与字符串s比较
- `int compare(const char *s) const;` //与字符串s比较

一般字符串的比较只是用来判断两个字符串是否相等，因为比较谁大谁小大部分时候没有什么意义，比如中文汉字的ascll码我们都不好判断大小，所以一般的运用就是判断是否相等。

#### 3.1.6 string字符穿存取

string中单个字符存取方式有两种

- `char& operator[](int n); `//通过[]方式取字符
- `char& at(int n); `//通过at方法获取字符

#### 3.1.6 string字符串的插入和删除

- `string& insert(int pos, const char* s); `//插入字符串
- `string& insert(int pos, const string& str); `//插入字符串
- `string& insert(int pos, int n, char c);` //在指定位置插入n个字符c
- `string& erase(int pos, int n = npos//其实也就是int npos);` //删除从Pos开始的n个字符

如果没有指定的话删除和插入的起始下标都是0。

#### 3.1.7string子串

- `string substr(int pos = 0, int n = npos) const;` //返回由pos开始的n个字符组成的字符串
- 一般我们可以灵活运用，先用find函数找到某个位置，再提取之前的数据。

### 3.2 vector容器

#### 3.2.1 vector基本概念

**功能：**

- vector数据结构和**数组非常相似**，也称为**单端数组**

**vector与普通数组区别：**

- 不同之处在于数组是静态空间，而vector可以**动态扩展**

**动态扩展：**

- 并不是在原空间之后续接新空间，而是找更大的内存空间，然后将原数据拷贝新空间，释放原空间

[![说明: 2015-11-10_151152](https://pic.imgdb.cn/item/61dd2d832ab3f51d9176c412.jpg)](https://pic.imgdb.cn/item/61dd2d832ab3f51d9176c412.jpg)

- vector容器的迭代器是支持随机访问的迭代器

#### 3.2.2 vector构造函数

**功能描述：**

- 创建vector容器

**函数原型：**

- `vector<T> v; `//采用模板实现类实现，默认构造函数
- `vector<T> v(v.begin(), v.end()); `//将v[begin(), end())区间中的元素拷贝给本身。
- `vector<T> v(n, elem);` //构造函数将n个elem拷贝给本身。
- `vector<T> v(const vector &vec);` //拷贝构造函数。

#### 3.2.3 vector赋值操作

**功能描述：**

- 给vector容器进行赋值

**函数原型：**

- `vector& operator=(const vector &vec);`//重载等号操作符

- `assign(beg, end);` //将[beg, end)区间中的数据拷贝赋值给本身。

- `assign(n, elem);` //将n个elem拷贝赋值给本身。

- ```C++
  	vector<int> v1; //无参构造
  	for (int i = 0; i < 10; i++)
  	{
  		v1.push_back(i);
  	}
  	printVector(v1);
  
  	vector<int>v2;
  	v2 = v1;
  	printVector(v2);
  
  	vector<int>v3;
  	v3.assign(v1.begin(), v1.end());
  	printVector(v3);
  
  	vector<int>v4;
  	v4.assign(10, 100);
  	printVector(v4);
  ```

#### 3.2.4 vector容量和大小

**功能描述：**

- 对vector容器的容量和大小操作

**函数原型：**

- `empty(); `//判断容器是否为空

- `capacity();` //容器的容量

- `size();` //返回容器中元素的个数

- `resize(int num);` //重新指定容器的长度为num，若容器变长，则以默认值填充新位置。

   //如果容器变短，则末尾超出容器长度的元素被删除。

- `resize(int num, elem);` //重新指定容器的长度为num，若容器变长，则以elem值填充新位置。

   //如果容器变短，则末尾超出容器长度的元素被删除

  ```C++
  vector<int> v1;
  	for (int i = 0; i < 10; i++)
  	{
  		v1.push_back(i);
  	}
  	printVector(v1);
  	if (v1.empty())//返回为真则为空
  	{
  		cout << "v1为空" << endl;
  	}
  	else
  	{
  		cout << "v1不为空" << endl;
  		cout << "v1的容量 = " << v1.capacity() << endl;
  		cout << "v1的大小 = " << v1.size() << endl;
  	}
  
  	//resize 重新指定大小 ，若指定的更大，默认用0填充新位置，可以利用重载版本替换默认填充
  	v1.resize(15,10);
  	printVector(v1);
  
  	//resize 重新指定大小 ，若指定的更小，超出部分元素被删除
  	v1.resize(5);
  	printVector(v1);
  ```

  ==这里的容量在重新设置大小之后如果size变小则不会变，如果变大则改变。==

#### 3.2.5 vector插入和删除

**功能描述：**

- 对vector容器进行插入、删除操作

**函数原型：**

- `push_back(ele);` //尾部插入元素ele

- `pop_back();` //删除最后一个元素

- `insert(const_iterator pos, ele);` //迭代器指向位置pos插入元素ele

- `insert(const_iterator pos, int count,ele);`//迭代器指向位置pos插入count个元素ele

- `erase(const_iterator pos);` //删除迭代器指向的元素

- `erase(const_iterator start, const_iterator end);`//删除迭代器从start到end之间的元素

- `clear();` //删除容器中所有元素

- ```C++
  vector<int> v1;
  	//尾插
  	v1.push_back(10);
  	v1.push_back(20);
  	v1.push_back(30);
  	v1.push_back(40);
  	v1.push_back(50);
  	printVector(v1);
  	//尾删
  	v1.pop_back();
  	printVector(v1);
  	//插入
  	v1.insert(v1.begin(), 100);
  	printVector(v1);
  
  	v1.insert(v1.begin(), 2, 1000);
  	printVector(v1);
  
  	//删除
  	v1.erase(v1.begin());
  	printVector(v1);
  
  	//清空
  	v1.erase(v1.begin(), v1.end());
  	v1.clear();
  	printVector(v1);
  ```

  注意vector里面要是想指定某一元素必须要使用迭代器，具体到某一个可以迭代器加上下标。

#### 3.2.6 vector数据存取

**功能描述：**

- 对vector中的数据的存取操作

**函数原型：**

- `at(int idx); `//返回索引idx所指的数据

- `operator[]; `//返回索引idx所指的数据

- `front(); `//返回容器中第一个数据元素

- `back();` //返回容器中最后一个数据元素

- ```C++
  vector<int>v1;
  	for (int i = 0; i < 10; i++)
  	{
  		v1.push_back(i);
  	}
  
  	for (int i = 0; i < v1.size(); i++)
  	{
  		cout << v1[i] << " ";
  	}
  	cout << endl;
  
  	for (int i = 0; i < v1.size(); i++)
  	{
  		cout << v1.at(i) << " ";
  	}
  	cout << endl;
  
  	cout << "v1的第一个元素为： " << v1.front() << endl;
  	cout << "v1的最后一个元素为： " << v1.back() << endl;
  ```

#### 3.2.7 vector互换容器

**功能描述：**

- 实现两个容器内元素进行互换

**函数原型：**

- `swap(vec);` // 将vec与本身的元素互换

**示例：**

```c++
void printVector(vector<int>& v) {

	for (vector<int>::iterator it = v.begin(); it != v.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;
}

void test01()
{
	vector<int>v1;
	for (int i = 0; i < 10; i++)
	{
		v1.push_back(i);
	}
	printVector(v1);

	vector<int>v2;
	for (int i = 10; i > 0; i--)
	{
		v2.push_back(i);
	}
	printVector(v2);

	//互换容器
	cout << "互换后" << endl;
	v1.swap(v2);
	printVector(v1);
	printVector(v2);
}

void test02()
{
	vector<int> v;
	for (int i = 0; i < 100000; i++) {
		v.push_back(i);
	}

	cout << "v的容量为：" << v.capacity() << endl;
	cout << "v的大小为：" << v.size() << endl;

	v.resize(3);

	cout << "v的容量为：" << v.capacity() << endl;
	cout << "v的大小为：" << v.size() << endl;

	//收缩内存
	vector<int>(v).swap(v); //这里的操作相当于用v的大小来创建一个新的匿名对象使其的容量等于V的大小并进行交换就能缩小其容量，而被换过去的新的匿名对象在本行结束之后就会进行释放。

	cout << "v的容量为：" << v.capacity() << endl;
	cout << "v的大小为：" << v.size() << endl;
}

int main() {

	test01();

	test02();

	system("pause");

	return 0;
}
```

总结：swap可以使两个容器互换，可以达到实用的收缩内存效果

#### 3.2.8 vector预留空间

**功能描述：**

- 减少vector在动态扩展容量时的扩展次数

**函数原型：**

- `reserve(int len);`//容器预留len个元素长度，预留位置不初始化，元素不可访问。

**示例：**

```C++
C++
#include <vector>

void test01()
{
	vector<int> v;

	//预留空间
	v.reserve(100000);

	int num = 0;
	int* p = NULL;
	for (int i = 0; i < 100000; i++) {
		v.push_back(i);
		if (p != &v[0]) {
			p = &v[0];
			num++;
		}
	}

	cout << "num:" << num << endl;
}

int main() {

	test01();
    
	system("pause");

	return 0;
}
```

总结：如果数据量较大，可以一开始利用reserve预留空间

### 3.3 deque容器

#### 3.3.1 deque容器基本概念

**功能：**

- 双端数组，可以对头端进行插入删除操作

**deque与vector区别：**

- vector对于头部的插入删除效率低，数据量越大，效率越低
- deque相对而言，对头部的插入删除速度回比vector快
- vector访问元素时的速度会比deque快,这和两者内部实现有关

[![说明: 2015-11-19_204101](https://pic.imgdb.cn/item/61dd2e922ab3f51d91779a78.jpg)](https://pic.imgdb.cn/item/61dd2e922ab3f51d91779a78.jpg)

deque内部工作原理:

deque内部有个**中控器**，维护每段缓冲区中的内容，缓冲区中存放真实数据

中控器维护的是每个缓冲区的地址，使得使用deque时像一片连续的内存空间

[![clip_image002-1547547896341](https://pic.imgdb.cn/item/61dd2eb22ab3f51d9177b488.jpg)](https://pic.imgdb.cn/item/61dd2eb22ab3f51d9177b488.jpg)

- deque容器的迭代器也是支持随机访问的

#### 3.3.2 deque构造函数

**功能描述：**

- deque容器构造

**函数原型：**

- `deque<T>` deqT; //默认构造形式
- `deque(beg, end);` //构造函数将[beg, end)区间中的元素拷贝给本身。
- `deque(n, elem);` //构造函数将n个elem拷贝给本身。
- `deque(const deque &deq);` //拷贝构造函数

**示例：**

```C++
C++
#include <deque>

void printDeque(const deque<int>& d) 
{
	for (deque<int>::const_iterator it = d.begin(); it != d.end(); it++) {
		cout << *it << " ";

	}
	cout << endl;
}
//deque构造
void test01() {

	deque<int> d1; //无参构造函数
	for (int i = 0; i < 10; i++)
	{
		d1.push_back(i);
	}
	printDeque(d1);
	deque<int> d2(d1.begin(),d1.end());
	printDeque(d2);

	deque<int>d3(10,100);
	printDeque(d3);

	deque<int>d4 = d3;
	printDeque(d4);
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

**总结：**deque容器和vector容器的构造方式几乎一致，灵活使用即可

#### 3.3.3 deque赋值操作

**功能描述：**

- 给deque容器进行赋值

**函数原型：**

- `deque& operator=(const deque &deq); `//重载等号操作符
- `assign(beg, end);` //将[beg, end)区间中的数据拷贝赋值给本身。
- `assign(n, elem);` //将n个elem拷贝赋值给本身。

**示例：**

```C++
C++
#include <deque>

void printDeque(const deque<int>& d) 
{
	for (deque<int>::const_iterator it = d.begin(); it != d.end(); it++) {
		cout << *it << " ";

	}
	cout << endl;
}
//赋值操作
void test01()
{
	deque<int> d1;
	for (int i = 0; i < 10; i++)
	{
		d1.push_back(i);
	}
	printDeque(d1);

	deque<int>d2;
	d2 = d1;
	printDeque(d2);

	deque<int>d3;
	d3.assign(d1.begin(), d1.end());
	printDeque(d3);

	deque<int>d4;
	d4.assign(10, 100);
	printDeque(d4);

}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：deque赋值操作也与vector相同，需熟练掌握

#### 3.3.4 deque大小操作

**功能描述：**

- 对deque容器的大小进行操作

**函数原型：**

- `deque.empty();` //判断容器是否为空

- `deque.size();` //返回容器中元素的个数

- `deque.resize(num);` //重新指定容器的长度为num,若容器变长，则以默认值填充新位置。

   //如果容器变短，则末尾超出容器长度的元素被删除。

- `deque.resize(num, elem);` //重新指定容器的长度为num,若容器变长，则以elem值填充新位置。

   //如果容器变短，则末尾超出容器长度的元素被删除。

**示例：**

```C++
C++
#include <deque>

void printDeque(const deque<int>& d) 
{
	for (deque<int>::const_iterator it = d.begin(); it != d.end(); it++) {
		cout << *it << " ";

	}
	cout << endl;
}

//大小操作
void test01()
{
	deque<int> d1;
	for (int i = 0; i < 10; i++)
	{
		d1.push_back(i);
	}
	printDeque(d1);

	//判断容器是否为空
	if (d1.empty()) {
		cout << "d1为空!" << endl;
	}
	else {
		cout << "d1不为空!" << endl;
		//统计大小
		cout << "d1的大小为：" << d1.size() << endl;
	}

	//重新指定大小
	d1.resize(15, 1);
	printDeque(d1);

	d1.resize(5);
	printDeque(d1);
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- deque没有容量的概念
- 判断是否为空 — empty
- 返回元素个数 — size
- 重新指定个数 — resize

#### 3.3.5 deque 插入和删除

**功能描述：**

- 向deque容器中插入和删除数据

**函数原型：**

两端插入操作：

- `push_back(elem);` //在容器尾部添加一个数据
- `push_front(elem);` //在容器头部插入一个数据
- `pop_back();` //删除容器最后一个数据
- `pop_front();` //删除容器第一个数据

指定位置操作：

- `insert(pos,elem);` //在pos位置插入一个elem元素的拷贝，返回新数据的位置。
- `insert(pos,n,elem);` //在pos位置插入n个elem数据，无返回值。
- `insert(pos,beg,end);` //在pos位置插入[beg,end)区间的数据，无返回值。
- `clear();` //清空容器的所有数据
- `erase(beg,end);` //删除[beg,end)区间的数据，返回下一个数据的位置。
- `erase(pos);` //删除pos位置的数据，返回下一个数据的位置。

**示例：**

```C++
C++
#include <deque>

void printDeque(const deque<int>& d) 
{
	for (deque<int>::const_iterator it = d.begin(); it != d.end(); it++) {
		cout << *it << " ";

	}
	cout << endl;
}
//两端操作
void test01()
{
	deque<int> d;
	//尾插
	d.push_back(10);
	d.push_back(20);
	//头插
	d.push_front(100);
	d.push_front(200);

	printDeque(d);

	//尾删
	d.pop_back();
	//头删
	d.pop_front();
	printDeque(d);
}

//插入
void test02()
{
	deque<int> d;
	d.push_back(10);
	d.push_back(20);
	d.push_front(100);
	d.push_front(200);
	printDeque(d);

	d.insert(d.begin(), 1000);
	printDeque(d);

	d.insert(d.begin(), 2,10000);
	printDeque(d);

	deque<int>d2;
	d2.push_back(1);
	d2.push_back(2);
	d2.push_back(3);

	d.insert(d.begin(), d2.begin(), d2.end());
	printDeque(d);

}

//删除
void test03()
{
	deque<int> d;
	d.push_back(10);
	d.push_back(20);
	d.push_front(100);
	d.push_front(200);
	printDeque(d);

	d.erase(d.begin());
	printDeque(d);

	d.erase(d.begin(), d.end());
	d.clear();
	printDeque(d);
}

int main() {

	//test01();

	//test02();

    test03();
    
	system("pause");

	return 0;
}
```

总结：

- 插入和删除提供的位置是迭代器！
- 尾插 — push_back
- 尾删 — pop_back
- 头插 — push_front
- 头删 — pop_front

#### 3.3.6 deque 数据存取

**功能描述：**

- 对deque 中的数据的存取操作

**函数原型：**

- `at(int idx); `//返回索引idx所指的数据
- `operator[]; `//返回索引idx所指的数据
- `front(); `//返回容器中第一个数据元素
- `back();` //返回容器中最后一个数据元素

**示例：**

```C++
C++
#include <deque>

void printDeque(const deque<int>& d) 
{
	for (deque<int>::const_iterator it = d.begin(); it != d.end(); it++) {
		cout << *it << " ";

	}
	cout << endl;
}

//数据存取
void test01()
{

	deque<int> d;
	d.push_back(10);
	d.push_back(20);
	d.push_front(100);
	d.push_front(200);

	for (int i = 0; i < d.size(); i++) {
		cout << d[i] << " ";
	}
	cout << endl;


	for (int i = 0; i < d.size(); i++) {
		cout << d.at(i) << " ";
	}
	cout << endl;

	cout << "front:" << d.front() << endl;

	cout << "back:" << d.back() << endl;

}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- 除了用迭代器获取deque容器中元素，[ ]和at也可以
- front返回容器第一个元素
- back返回容器最后一个元素

#### 3.3.7 deque 排序

**功能描述：**

- 利用算法实现对deque容器进行排序

**算法：**

- `sort(iterator beg, iterator end)` //对beg和end区间内元素进行排序

**示例：**

```C++
C++
#include <deque>
#include <algorithm>

void printDeque(const deque<int>& d) 
{
	for (deque<int>::const_iterator it = d.begin(); it != d.end(); it++) {
		cout << *it << " ";

	}
	cout << endl;
}

void test01()
{

	deque<int> d;
	d.push_back(10);
	d.push_back(20);
	d.push_front(100);
	d.push_front(200);

	printDeque(d);
	sort(d.begin(), d.end());
	printDeque(d);

}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：sort算法非常实用，使用时包含头文件 algorithm即可

### 3.4 评委打分案例

具体见自己写的代码，确实该多敲敲代码，这样真的很有成就感

### 3.5 stack容器

#### 3.5.1 stack 基本概念

**概念：\**stack是一种\**先进后出**(First In Last Out,FILO)的数据结构，它只有一个出口

[![说明: 2015-11-15_195707](https://pic.imgdb.cn/item/61dd2fc12ab3f51d91787ac4.jpg)](https://pic.imgdb.cn/item/61dd2fc12ab3f51d91787ac4.jpg)

栈中只有顶端的元素才可以被外界使用，因此栈不允许有遍历行为

栈中进入数据称为 — **入栈** `push`

栈中弹出数据称为 — **出栈** `pop`

#### 3.5.2 stack 常用接口

功能描述：栈容器常用的对外接口

构造函数：

- `stack<T> stk;` //stack采用模板类实现， stack对象的默认构造形式
- `stack(const stack &stk);` //拷贝构造函数

赋值操作：

- `stack& operator=(const stack &stk);` //重载等号操作符

数据存取：

- `push(elem);` //向栈顶添加元素
- `pop();` //从栈顶移除第一个元素
- `top(); `//返回栈顶元素

大小操作：

- `empty();` //判断堆栈是否为空
- `size(); `//返回栈的大小

**示例：**

```C++
C++
#include <stack>

//栈容器常用接口
void test01()
{
	//创建栈容器 栈容器必须符合先进后出
	stack<int> s;

	//向栈中添加元素，叫做 压栈 入栈
	s.push(10);
	s.push(20);
	s.push(30);

	while (!s.empty()) {
		//输出栈顶元素
		cout << "栈顶元素为： " << s.top() << endl;
		//弹出栈顶元素
		s.pop();
	}
	cout << "栈的大小为：" << s.size() << endl;

}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- 入栈 — push
- 出栈 — pop
- 返回栈顶 — top
- 判断栈是否为空 — empty
- 返回栈大小 — size

### 3.6 queue 容器

#### 3.6.1 queue 基本概念

**概念：\**Queue是一种\**先进先出**(First In First Out,FIFO)的数据结构，它有两个出口

[![说明: 2015-11-15_214429](https://pic.imgdb.cn/item/61dd30032ab3f51d9178b890.jpg)](https://pic.imgdb.cn/item/61dd30032ab3f51d9178b890.jpg)

队列容器允许从一端新增元素，从另一端移除元素

队列中只有队头和队尾才可以被外界使用，因此队列不允许有遍历行为

队列中进数据称为 — **入队** `push`

队列中出数据称为 — **出队** `pop`

#### 3.6.2 queue 常用接口

功能描述：栈容器常用的对外接口

构造函数：

- `queue<T> que;` //queue采用模板类实现，queue对象的默认构造形式
- `queue(const queue &que);` //拷贝构造函数

赋值操作：

- `queue& operator=(const queue &que);` //重载等号操作符

数据存取：

- `push(elem);` //往队尾添加元素
- `pop();` //从队头移除第一个元素
- `back();` //返回最后一个元素
- `front(); `//返回第一个元素

大小操作：

- `empty();` //判断堆栈是否为空
- `size(); `//返回栈的大小

**示例：**

```C++
C++

#include <queue>
#include <string>
class Person
{
public:
	Person(string name, int age)
	{
		this->m_Name = name;
		this->m_Age = age;
	}

	string m_Name;
	int m_Age;
};

void test01() {

	//创建队列
	queue<Person> q;

	//准备数据
	Person p1("唐僧", 30);
	Person p2("孙悟空", 1000);
	Person p3("猪八戒", 900);
	Person p4("沙僧", 800);

	//向队列中添加元素  入队操作
	q.push(p1);
	q.push(p2);
	q.push(p3);
	q.push(p4);

	//队列不提供迭代器，更不支持随机访问	
	while (!q.empty()) {
		//输出队头元素
		cout << "队头元素-- 姓名： " << q.front().m_Name 
              << " 年龄： "<< q.front().m_Age << endl;
        
		cout << "队尾元素-- 姓名： " << q.back().m_Name  
              << " 年龄： " << q.back().m_Age << endl;
        
		cout << endl;
		//弹出队头元素
		q.pop();
	}

	cout << "队列大小为：" << q.size() << endl;
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- 入队 — push
- 出队 — pop
- 返回队头元素 — front
- 返回队尾元素 — back
- 判断队是否为空 — empty
- 返回队列大小 — size

### 3.7 list容器

#### 3.7.1 list基本概念

**功能：**将数据进行链式存储

**链表**（list）是一种物理存储单元上非连续的存储结构，数据元素的逻辑顺序是通过链表中的指针链接实现的

链表的组成：链表由一系列**结点**组成

结点的组成：一个是存储数据元素的**数据域**，另一个是存储下一个结点地址的**指针域**

STL中的链表是一个双向循环链表

[![说明: 2015-11-15_225145](https://pic.imgdb.cn/item/61dd31062ab3f51d917986eb.jpg)](https://pic.imgdb.cn/item/61dd31062ab3f51d917986eb.jpg)

由于链表的存储方式并不是连续的内存空间，因此链表list中的迭代器只支持前移和后移，属于**双向迭代器**

list的优点：

- 采用动态存储分配，不会造成内存浪费和溢出
- 链表执行插入和删除操作十分方便，修改指针即可，不需要移动大量元素

list的缺点：

- 链表灵活，但是空间(指针域) 和 时间（遍历）额外耗费较大

List有一个重要的性质，插入操作和删除操作都不会造成原有list迭代器的失效，这在vector是不成立的。

总结：STL中**List和vector是两个最常被使用的容器**，各有优缺点

#### 3.7.2 list构造函数

**功能描述：**

- 创建list容器

**函数原型：**

- `list<T> lst;` //list采用采用模板类实现,对象的默认构造形式：
- `list(beg,end);` //构造函数将[beg, end)区间中的元素拷贝给本身。
- `list(n,elem);` //构造函数将n个elem拷贝给本身。
- `list(const list &lst);` //拷贝构造函数。

**示例：**

```C++
C++

#include <list>

void printList(const list<int>& L) {

	for (list<int>::const_iterator it = L.begin(); it != L.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;
}

void test01()
{
	list<int>L1;
	L1.push_back(10);
	L1.push_back(20);
	L1.push_back(30);
	L1.push_back(40);

	printList(L1);

	list<int>L2(L1.begin(),L1.end());
	printList(L2);

	list<int>L3(L2);
	printList(L3);

	list<int>L4(10, 1000);
	printList(L4);
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：list构造方式同其他几个STL常用容器，熟练掌握即可

#### 3.7.3 list 赋值和交换

**功能描述：**

- 给list容器进行赋值，以及交换list容器

**函数原型：**

- `assign(beg, end);` //将[beg, end)区间中的数据拷贝赋值给本身。
- `assign(n, elem);` //将n个elem拷贝赋值给本身。
- `list& operator=(const list &lst);` //重载等号操作符
- `swap(lst);` //将lst与本身的元素互换。

**示例：**

```C++
C++

#include <list>

void printList(const list<int>& L) {

	for (list<int>::const_iterator it = L.begin(); it != L.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;
}

//赋值和交换
void test01()
{
	list<int>L1;
	L1.push_back(10);
	L1.push_back(20);
	L1.push_back(30);
	L1.push_back(40);
	printList(L1);

	//赋值
	list<int>L2;
	L2 = L1;
	printList(L2);

	list<int>L3;
	L3.assign(L2.begin(), L2.end());
	printList(L3);

	list<int>L4;
	L4.assign(10, 100);
	printList(L4);

}

//交换
void test02()
{

	list<int>L1;
	L1.push_back(10);
	L1.push_back(20);
	L1.push_back(30);
	L1.push_back(40);

	list<int>L2;
	L2.assign(10, 100);

	cout << "交换前： " << endl;
	printList(L1);
	printList(L2);

	cout << endl;

	L1.swap(L2);

	cout << "交换后： " << endl;
	printList(L1);
	printList(L2);

}

int main() {

	//test01();

	test02();

	system("pause");

	return 0;
}
```

总结：list赋值和交换操作能够灵活运用即可

#### 3.7.4 list 大小操作

**功能描述：**

- 对list容器的大小进行操作

**函数原型：**

- `size(); `//返回容器中元素的个数

- `empty(); `//判断容器是否为空

- `resize(num);` //重新指定容器的长度为num，若容器变长，则以默认值填充新位置。

   //如果容器变短，则末尾超出容器长度的元素被删除。

- `resize(num, elem); `//重新指定容器的长度为num，若容器变长，则以elem值填充新位置。

-  //如果容器变短，则末尾超出容器长度的元素被删除。

- **示例：**

```C++
C++

#include <list>

void printList(const list<int>& L) {

	for (list<int>::const_iterator it = L.begin(); it != L.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;
}

//大小操作
void test01()
{
	list<int>L1;
	L1.push_back(10);
	L1.push_back(20);
	L1.push_back(30);
	L1.push_back(40);

	if (L1.empty())
	{
		cout << "L1为空" << endl;
	}
	else
	{
		cout << "L1不为空" << endl;
		cout << "L1的大小为： " << L1.size() << endl;
	}

	//重新指定大小
	L1.resize(10);
	printList(L1);

	L1.resize(2);
	printList(L1);
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- 判断是否为空 — empty
- 返回元素个数 — size
- 重新指定个数 — resize

#### 3.7.5 list 插入和删除

**功能描述：**

- 对list容器进行数据的插入和删除

**函数原型：**

- push_back(elem);//在容器尾部加入一个元素
- pop_back();//删除容器中最后一个元素
- push_front(elem);//在容器开头插入一个元素
- pop_front();//从容器开头移除第一个元素
- insert(pos,elem);//在pos位置插elem元素的拷贝，返回新数据的位置。
- insert(pos,n,elem);//在pos位置插入n个elem数据，无返回值。
- insert(pos,beg,end);//在pos位置插入[beg,end)区间的数据，无返回值。
- clear();//移除容器的所有数据
- erase(beg,end);//删除[beg,end)区间的数据，返回下一个数据的位置。
- erase(pos);//删除pos位置的数据，返回下一个数据的位置。
- remove(elem);//删除容器中所有与elem值匹配的元素。

**示例：**

```C++
C++

#include <list>

void printList(const list<int>& L) {

	for (list<int>::const_iterator it = L.begin(); it != L.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;
}

//插入和删除
void test01()
{
	list<int> L;
	//尾插
	L.push_back(10);
	L.push_back(20);
	L.push_back(30);
	//头插
	L.push_front(100);
	L.push_front(200);
	L.push_front(300);

	printList(L);

	//尾删
	L.pop_back();
	printList(L);

	//头删
	L.pop_front();
	printList(L);

	//插入
	list<int>::iterator it = L.begin();
	L.insert(++it, 1000);
	printList(L);

	//删除
	it = L.begin();
	L.erase(++it);
	printList(L);

	//移除
	L.push_back(10000);
	L.push_back(10000);
	L.push_back(10000);
	printList(L);
	L.remove(10000);
	printList(L);
    
    //清空
	L.clear();
	printList(L);
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- 尾插 — push_back
- 尾删 — pop_back
- 头插 — push_front
- 头删 — pop_front
- 插入 — insert
- 删除 — erase
- 移除 — remove
- 清空 — clear

#### 3.7.6 list 数据存取

**功能描述：**

- 对list容器中数据进行存取

**函数原型：**

- `front();` //返回第一个元素。
- `back();` //返回最后一个元素。

**示例：**

```C++
C++

#include <list>

//数据存取
void test01()
{
	list<int>L1;
	L1.push_back(10);
	L1.push_back(20);
	L1.push_back(30);
	L1.push_back(40);

	
	//cout << L1.at(0) << endl;//错误 不支持at访问数据
	//cout << L1[0] << endl; //错误  不支持[]方式访问数据
	cout << "第一个元素为： " << L1.front() << endl;
	cout << "最后一个元素为： " << L1.back() << endl;

	//list容器的迭代器是双向迭代器，不支持随机访问
	list<int>::iterator it = L1.begin();
	//it = it + 1;//错误，不可以跳跃访问，即使是+1
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- list容器中不可以通过[]或者at方式访问数据
- 返回第一个元素 — front
- 返回最后一个元素 — back

#### 3.7.7 list 反转和排序

**功能描述：**

- 将容器中的元素反转，以及将容器中的数据进行排序

**函数原型：**

- `reverse();` //反转链表
- `sort();` //链表排序

**示例：**

```C++
C++

void printList(const list<int>& L) {

	for (list<int>::const_iterator it = L.begin(); it != L.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;
}

bool myCompare(int val1 , int val2)
{
	return val1 > val2;
}

//反转和排序
void test01()
{
	list<int> L;
	L.push_back(90);
	L.push_back(30);
	L.push_back(20);
	L.push_back(70);
	printList(L);

	//反转容器的元素
	L.reverse();
	printList(L);

	//排序
	L.sort(); //默认的排序规则 从小到大
	printList(L);

	L.sort(myCompare); //指定规则，从大到小
	printList(L);
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- 反转 — reverse
- 排序 — sort （成员函数）

#### 3.7.8 排序案例

案例描述：将Person自定义数据类型进行排序，Person中属性有姓名、年龄、身高

排序规则：按照年龄进行升序，如果年龄相同按照身高进行降序

**示例：**

```C++
C++

#include <list>
#include <string>
class Person {
public:
	Person(string name, int age , int height) {
		m_Name = name;
		m_Age = age;
		m_Height = height;
	}

public:
	string m_Name;  //姓名
	int m_Age;      //年龄
	int m_Height;   //身高
};


bool ComparePerson(Person& p1, Person& p2) {

	if (p1.m_Age == p2.m_Age) {
		return p1.m_Height  > p2.m_Height;
	}
	else
	{
		return  p1.m_Age < p2.m_Age;
	}

}

void test01() {

	list<Person> L;

	Person p1("刘备", 35 , 175);
	Person p2("曹操", 45 , 180);
	Person p3("孙权", 40 , 170);
	Person p4("赵云", 25 , 190);
	Person p5("张飞", 35 , 160);
	Person p6("关羽", 35 , 200);

	L.push_back(p1);
	L.push_back(p2);
	L.push_back(p3);
	L.push_back(p4);
	L.push_back(p5);
	L.push_back(p6);

	for (list<Person>::iterator it = L.begin(); it != L.end(); it++) {
		cout << "姓名： " << it->m_Name << " 年龄： " << it->m_Age 
              << " 身高： " << it->m_Height << endl;
	}

	cout << "---------------------------------" << endl;
	L.sort(ComparePerson); //排序

	for (list<Person>::iterator it = L.begin(); it != L.end(); it++) {
		cout << "姓名： " << it->m_Name << " 年龄： " << it->m_Age 
              << " 身高： " << it->m_Height << endl;
	}
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- ==对于自定义数据类型，必须要指定排序规则，否则编译器不知道如何进行排序==
- 高级排序只是在排序规则上再进行一次逻辑规则制定，并不复杂

### 3.8 set/ multiset 容器

#### 3.8.1 set基本概念

**简介：**

- 所有元素都会在插入时自动被排序

**本质：**

- set/multiset属于**关联式容器**，底层结构是用**二叉树**实现。

**set和multiset区别**：

- set不允许容器中有重复的元素
- multiset允许容器中有重复的元素

#### 3.8.2 set构造和赋值

功能描述：创建set容器以及赋值

构造：

- `set<T> st;` //默认构造函数：
- `set(const set &st);` //拷贝构造函数

赋值：

- `set& operator=(const set &st);` //重载等号操作符

**示例：**

```C++
C++

#include <set>

void printSet(set<int> & s)
{
	for (set<int>::iterator it = s.begin(); it != s.end(); it++)
	{
		cout << *it << " ";
	}
	cout << endl;
}

//构造和赋值
void test01()
{
	set<int> s1;

	s1.insert(10);
	s1.insert(30);
	s1.insert(20);
	s1.insert(40);
	printSet(s1);

	//拷贝构造
	set<int>s2(s1);
	printSet(s2);

	//赋值
	set<int>s3;
	s3 = s2;
	printSet(s3);
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- set容器插入数据时用insert
- set容器插入数据的数据会自动排序

#### 3.8.3 set大小和交换

**功能描述：**

- 统计set容器大小以及交换set容器

**函数原型：**

- `size();` //返回容器中元素的数目
- `empty();` //判断容器是否为空
- `swap(st);` //交换两个集合容器

**示例：**

```C++
C++

#include <set>

void printSet(set<int> & s)
{
	for (set<int>::iterator it = s.begin(); it != s.end(); it++)
	{
		cout << *it << " ";
	}
	cout << endl;
}

//大小
void test01()
{

	set<int> s1;
	
	s1.insert(10);
	s1.insert(30);
	s1.insert(20);
	s1.insert(40);

	if (s1.empty())
	{
		cout << "s1为空" << endl;
	}
	else
	{
		cout << "s1不为空" << endl;
		cout << "s1的大小为： " << s1.size() << endl;
	}

}

//交换
void test02()
{
	set<int> s1;

	s1.insert(10);
	s1.insert(30);
	s1.insert(20);
	s1.insert(40);

	set<int> s2;

	s2.insert(100);
	s2.insert(300);
	s2.insert(200);
	s2.insert(400);

	cout << "交换前" << endl;
	printSet(s1);
	printSet(s2);
	cout << endl;

	cout << "交换后" << endl;
	s1.swap(s2);
	printSet(s1);
	printSet(s2);
}

int main() {

	//test01();

	test02();

	system("pause");

	return 0;
}
```

总结：

- 统计大小 — size
- 判断是否为空 — empty
- 交换容器 — swap

#### 3.8.4 set插入和删除

**功能描述：**

- set容器进行插入数据和删除数据

**函数原型：**

- `insert(elem);` //在容器中插入元素。==注意这里的begin()是经过排序之后的第一个，而不是按插入的顺序来判断的==
- `clear();` //清除所有元素
- `erase(pos);` //删除pos迭代器所指的元素，返回下一个元素的迭代器。
- `erase(beg, end);` //删除区间[beg,end)的所有元素 ，返回下一个元素的迭代器。
- `erase(elem);` //删除容器中值为elem的元素。

**示例：**

```C++
C++

#include <set>

void printSet(set<int> & s)
{
	for (set<int>::iterator it = s.begin(); it != s.end(); it++)
	{
		cout << *it << " ";
	}
	cout << endl;
}

//插入和删除
void test01()
{
	set<int> s1;
	//插入
	s1.insert(10);
	s1.insert(30);
	s1.insert(20);
	s1.insert(40);
	printSet(s1);

	//删除
	s1.erase(s1.begin());
	printSet(s1);

	s1.erase(30);
	printSet(s1);

	//清空
	//s1.erase(s1.begin(), s1.end());
	s1.clear();
	printSet(s1);
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- 插入 — insert
- 删除 — erase
- 清空 — clear

#### 3.8.5 set查找和统计

**功能描述：**

- 对set容器进行查找数据以及统计数据

**函数原型：**

- `find(key);` //查找key是否存在,若存在，返回该键的元素的迭代器；若不存在，返回set.end();==判断的时候就判断这个迭代器是否等于end()就好了==
- `count(key);` //统计key的元素个数==只会有0和1==

**示例：**

```C++
C++

#include <set>

//查找和统计
void test01()
{
	set<int> s1;
	//插入
	s1.insert(10);
	s1.insert(30);
	s1.insert(20);
	s1.insert(40);
	
	//查找
	set<int>::iterator pos = s1.find(30);

	if (pos != s1.end())
	{
		cout << "找到了元素 ： " << *pos << endl;
	}
	else
	{
		cout << "未找到元素" << endl;
	}

	//统计
	int num = s1.count(30);
	cout << "num = " << num << endl;
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- 查找 — find （返回的是迭代器）
- 统计 — count （对于set，结果为0或者1）

#### 3.8.6 set和multiset区别

**学习目标：**

- 掌握set和multiset的区别

**区别：**

- set不可以插入重复数据，而multiset可以
- set插入数据的同时会返回插入结果，表示插入是否成功
- multiset不会检测数据，因此可以插入重复数据

**示例：**

```C++
C++

#include <set>

//set和multiset区别
void test01()
{
	set<int> s;
	pair<set<int>::iterator, bool>  ret = s.insert(10);
	if (ret.second) {
		cout << "第一次插入成功!" << endl;
	}
	else {
		cout << "第一次插入失败!" << endl;
	}

	ret = s.insert(10);
	if (ret.second) {
		cout << "第二次插入成功!" << endl;
	}
	else {
		cout << "第二次插入失败!" << endl;
	}
    
	//multiset
	multiset<int> ms;
	ms.insert(10);
	ms.insert(10);

	for (multiset<int>::iterator it = ms.begin(); it != ms.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- 如果不允许插入重复数据可以利用set
- 如果需要插入重复数据利用multiset

#### 3.8.7 pair对组创建

**功能描述：**

- 成对出现的数据，利用对组可以返回两个数据

**两种创建方式：**

- `pair<type, type> p ( value1, value2 );`
- `pair<type, type> p = make_pair( value1, value2 );`

**示例：**

```C++
C++

#include <string>

//对组创建
void test01()
{
	pair<string, int> p(string("Tom"), 20);
	cout << "姓名： " <<  p.first << " 年龄： " << p.second << endl;

	pair<string, int> p2 = make_pair("Jerry", 10);
	cout << "姓名： " << p2.first << " 年龄： " << p2.second << endl;
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

两种方式都可以创建对组，记住一种即可

#### 3.8.8 set容器排序

学习目标：

- set容器默认排序规则为从小到大，掌握如何改变排序规则

主要技术点：

- 利用仿函数，可以改变排序规则

**示例一** set存放内置数据类型

```C++
C++

#include <set>

class MyCompare 
{
public:
	bool operator()(int v1, int v2) {
		return v1 > v2;
	}
};
void test01() 
{    
	set<int> s1;
	s1.insert(10);
	s1.insert(40);
	s1.insert(20);
	s1.insert(30);
	s1.insert(50);

	//默认从小到大
	for (set<int>::iterator it = s1.begin(); it != s1.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;

	//指定排序规则
	set<int,MyCompare> s2;
	s2.insert(10);
	s2.insert(40);
	s2.insert(20);
	s2.insert(30);
	s2.insert(50);

	for (set<int, MyCompare>::iterator it = s2.begin(); it != s2.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：利用仿函数可以指定set容器的排序规则

**示例二** set存放自定义数据类型

```C++
C++

#include <set>
#include <string>

class Person
{
public:
	Person(string name, int age)
	{
		this->m_Name = name;
		this->m_Age = age;
	}

	string m_Name;
	int m_Age;

};
class comparePerson
{
public:
	bool operator()(const Person& p1, const Person &p2)
	{
		//按照年龄进行排序  降序
		return p1.m_Age > p2.m_Age;
	}
};

void test01()
{
	set<Person, comparePerson> s;

	Person p1("刘备", 23);
	Person p2("关羽", 27);
	Person p3("张飞", 25);
	Person p4("赵云", 21);

	s.insert(p1);
	s.insert(p2);
	s.insert(p3);
	s.insert(p4);

	for (set<Person, comparePerson>::iterator it = s.begin(); it != s.end(); it++)
	{
		cout << "姓名： " << it->m_Name << " 年龄： " << it->m_Age << endl;
	}
}
int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

对于自定义数据类型，set必须指定排序规则才可以插入数据

### 3.9 map/ multimap容器

#### 3.9.1 map基本概念

**简介：**

- map中所有元素都是pair
- pair中第一个元素为key（键值），起到索引作用，第二个元素为value（实值）
- 所有元素都会根据元素的键值自动排序

**本质：**

- map/multimap属于**关联式容器**，底层结构是用二叉树实现。

**优点：**

- 可以根据key值快速找到value值

map和multimap**区别**：

- map不允许容器中有重复key值元素
- multimap允许容器中有重复key值元素

#### 3.9.2 map构造和赋值

**功能描述：**

- 对map容器进行构造和赋值操作

**函数原型：**

**构造：**

- `map<T1, T2> mp;` //map默认构造函数:
- `map(const map &mp);` //拷贝构造函数

**赋值：**

- `map& operator=(const map &mp);` //重载等号操作符

**示例：**

```C++
C++

#include <map>

void printMap(map<int,int>&m)
{
	for (map<int, int>::iterator it = m.begin(); it != m.end(); it++)
	{
		cout << "key = " << it->first << " value = " << it->second << endl;
	}
	cout << endl;
}

void test01()
{
	map<int,int>m; //默认构造
	m.insert(pair<int, int>(1, 10));
	m.insert(pair<int, int>(2, 20));
	m.insert(pair<int, int>(3, 30));
	printMap(m);

	map<int, int>m2(m); //拷贝构造
	printMap(m2);

	map<int, int>m3;
	m3 = m2; //赋值
	printMap(m3);
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：map中所有元素都是成对出现，插入数据时候要使用对组

#### 3.9.3 map大小和交换

**功能描述：**

- 统计map容器大小以及交换map容器

函数原型：

- `size();` //返回容器中元素的数目
- `empty();` //判断容器是否为空
- `swap(st);` //交换两个集合容器==两个容器输出之间会自动加一个换行==

**示例：**

```C++
C++

#include <map>

void printMap(map<int,int>&m)
{
	for (map<int, int>::iterator it = m.begin(); it != m.end(); it++)
	{
		cout << "key = " << it->first << " value = " << it->second << endl;
	}
	cout << endl;
}

void test01()
{
	map<int, int>m;
	m.insert(pair<int, int>(1, 10));
	m.insert(pair<int, int>(2, 20));
	m.insert(pair<int, int>(3, 30));

	if (m.empty())
	{
		cout << "m为空" << endl;
	}
	else
	{
		cout << "m不为空" << endl;
		cout << "m的大小为： " << m.size() << endl;
	}
}


//交换
void test02()
{
	map<int, int>m;
	m.insert(pair<int, int>(1, 10));
	m.insert(pair<int, int>(2, 20));
	m.insert(pair<int, int>(3, 30));

	map<int, int>m2;
	m2.insert(pair<int, int>(4, 100));
	m2.insert(pair<int, int>(5, 200));
	m2.insert(pair<int, int>(6, 300));

	cout << "交换前" << endl;
	printMap(m);
	printMap(m2);

	cout << "交换后" << endl;
	m.swap(m2);
	printMap(m);
	printMap(m2);
}

int main() {

	test01();

	test02();

	system("pause");

	return 0;
}
```

总结：

- 统计大小 — size
- 判断是否为空 — empty
- 交换容器 — swap

#### 3.9.4 map插入和删除

**功能描述：**

- map容器进行插入数据和删除数据

**函数原型：**

- `insert(elem);` //在容器中插入元素。
- `clear();` //清除所有元素
- `erase(pos);` //删除pos迭代器所指的元素，返回下一个元素的迭代器。==按照key来进行删除==
- `erase(beg, end);` //删除区间[beg,end)的所有元素 ，返回下一个元素的迭代器。
- `erase(key);` //删除容器中值为key的元素。

**示例：**

```C++
C++

#include <map>

void printMap(map<int,int>&m)
{
	for (map<int, int>::iterator it = m.begin(); it != m.end(); it++)
	{
		cout << "key = " << it->first << " value = " << it->second << endl;
	}
	cout << endl;
}

void test01()
{
	//插入
	map<int, int> m;
	//第一种插入方式
	m.insert(pair<int, int>(1, 10));
	//第二种插入方式
	m.insert(make_pair(2, 20));
	//第三种插入方式
	m.insert(map<int, int>::value_type(3, 30));
	//第四种插入方式
	m[4] = 40; 
	printMap(m);

	//删除
	m.erase(m.begin());
	printMap(m);

	m.erase(3);
	printMap(m);

	//清空
	m.erase(m.begin(),m.end());
	m.clear();
	printMap(m);
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- map插入方式很多，记住其一即可

- 插入 — insert
- 删除 — erase
- 清空 — clear

#### 3.9.5 map查找和统计

**功能描述：**

- 对map容器进行查找数据以及统计数据

**函数原型：**

- `find(key);` //查找key是否存在,若存在，返回该键的元素的迭代器；若不存在，返回set.end();
- `count(key);` //统计key的元素个数

**示例：**

```C++
C++

#include <map>

//查找和统计
void test01()
{
	map<int, int>m; 
	m.insert(pair<int, int>(1, 10));
	m.insert(pair<int, int>(2, 20));
	m.insert(pair<int, int>(3, 30));

	//查找
	map<int, int>::iterator pos = m.find(3);

	if (pos != m.end())
	{
		cout << "找到了元素 key = " << (*pos).first << " value = " << (*pos).second << endl;
	}
	else
	{
		cout << "未找到元素" << endl;
	}

	//统计
	int num = m.count(3);
	cout << "num = " << num << endl;
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- 查找 — find （返回的是迭代器）
- 统计 — count （对于map，结果为0或者1）

#### 3.9.6 map容器排序

**学习目标：**

- map容器默认排序规则为 按照key值进行 从小到大排序，掌握如何改变排序规则

**主要技术点:**

- 利用仿函数，可以改变排序规则

**示例：**

```C++
C++

#include <map>

class MyCompare {
public:
	bool operator()(int v1, int v2) {
		return v1 > v2;
	}
};

void test01() 
{
	//默认从小到大排序
	//利用仿函数实现从大到小排序
	map<int, int, MyCompare> m;

	m.insert(make_pair(1, 10));
	m.insert(make_pair(2, 20));
	m.insert(make_pair(3, 30));
	m.insert(make_pair(4, 40));
	m.insert(make_pair(5, 50));

	for (map<int, int, MyCompare>::iterator it = m.begin(); it != m.end(); it++) {
		cout << "key:" << it->first << " value:" << it->second << endl;
	}
}
int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：

- 利用仿函数可以指定map容器的排序规则
- 对于自定义数据类型，map必须要指定排序规则,同set容器

## 4 STL- 函数对象

### 4.1 函数对象

#### 4.1.1 函数对象概念

**概念：**

- 重载**函数调用操作符**的类，其对象常称为**函数对象**
- **函数对象**使用重载的()时，行为类似函数调用，也叫**仿函数**

**本质：**

函数对象(仿函数)是一个**类**，不是一个函数

#### 4.1.2 函数对象使用

**特点：**

- 函数对象在使用时，可以像普通函数那样调用, 可以有参数，可以有返回值
- 函数对象超出普通函数的概念，函数对象可以有自己的状态
- 函数对象可以作为参数传递

**示例:**

```C++
C++

#include <string>

//1、函数对象在使用时，可以像普通函数那样调用, 可以有参数，可以有返回值
class MyAdd
{
public :
	int operator()(int v1,int v2)
	{
		return v1 + v2;
	}
};

void test01()
{
	MyAdd myAdd;
	cout << myAdd(10, 10) << endl;
}

//2、函数对象可以有自己的状态
class MyPrint
{
public:
	MyPrint()
	{
		count = 0;
	}
	void operator()(string test)
	{
		cout << test << endl;
		count++; //统计使用次数
	}

	int count; //内部自己的状态
};
void test02()
{
	MyPrint myPrint;
	myPrint("hello world");
	myPrint("hello world");
	myPrint("hello world");
	cout << "myPrint调用次数为： " << myPrint.count << endl;
}

//3、函数对象可以作为参数传递
void doPrint(MyPrint &mp , string test)
{
	mp(test);
}

void test03()
{
	MyPrint myPrint;
	doPrint(myPrint, "Hello C++");
}

int main() {

	//test01();
	//test02();
	test03();

	system("pause");

	return 0;
}
```

总结：

- 仿函数写法非常灵活，可以作为参数进行传递。

### 4.2 谓词

#### 4.2.1 谓词概念

**概念：**

- 返回bool类型的仿函数称为**谓词**
- 如果operator()接受一个参数，那么叫做一元谓词
- 如果operator()接受两个参数，那么叫做二元谓词

#### 4.2.2 一元谓词

**示例：**

```C++
C++

#include <vector>
#include <algorithm>

//1.一元谓词
struct GreaterFive{
	bool operator()(int val) {
		return val > 5;
	}
};

void test01() {

	vector<int> v;
	for (int i = 0; i < 10; i++)
	{
		v.push_back(i);
	}

	vector<int>::iterator it = find_if(v.begin(), v.end(), GreaterFive());
	if (it == v.end()) {
		cout << "没找到!" << endl;
	}
	else {
		cout << "找到:" << *it << endl;
	}

}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：参数只有一个的谓词，称为一元谓词

#### 4.2.3 二元谓词

**示例：**

```C++
C++

#include <vector>
#include <algorithm>
//二元谓词
class MyCompare
{
public:
	bool operator()(int num1, int num2)
	{
		return num1 > num2;
	}
};

void test01()
{
	vector<int> v;
	v.push_back(10);
	v.push_back(40);
	v.push_back(20);
	v.push_back(30);
	v.push_back(50);

	//默认从小到大
	sort(v.begin(), v.end());
	for (vector<int>::iterator it = v.begin(); it != v.end(); it++)
	{
		cout << *it << " ";
	}
	cout << endl;
	cout << "----------------------------" << endl;

	//使用函数对象改变算法策略，排序从大到小
	sort(v.begin(), v.end(), MyCompare());
	for (vector<int>::iterator it = v.begin(); it != v.end(); it++)
	{
		cout << *it << " ";
	}
	cout << endl;
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：参数只有两个的谓词，称为二元谓词

### 4.3 内建函数对象

#### 4.3.1 内建函数对象意义

**概念：**

- STL内建了一些函数对象

**分类:**

- 算术仿函数
- 关系仿函数
- 逻辑仿函数

**用法：**

- 这些仿函数所产生的对象，用法和一般函数完全相同
- 使用内建函数对象，需要引入头文件 `#include<functional>`

#### 4.3.2 算术仿函数

**功能描述：**

- 实现四则运算
- 其中negate是一元运算，其他都是二元运算

**仿函数原型：**

- `template<class T> T plus<T>` //加法仿函数
- `template<class T> T minus<T>` //减法仿函数
- `template<class T> T multiplies<T>` //乘法仿函数
- `template<class T> T divides<T>` //除法仿函数
- `template<class T> T modulus<T>` //取模仿函数
- `template<class T> T negate<T>` //取反仿函数

**示例：**

```C++
C++

#include <functional>
//negate
void test01()
{
	negate<int> n;
	cout << n(50) << endl;
}

//plus
void test02()
{
	plus<int> p;
	cout << p(10, 20) << endl;
}

int main() {

	test01();
	test02();

	system("pause");

	return 0;
}
```

总结：使用内建函数对象时，需要引入头文件 `#include <functional>`

#### 4.3.3 关系仿函数

**功能描述：**

- 实现关系对比

**仿函数原型：**

- `template<class T> bool equal_to<T>` //等于
- `template<class T> bool not_equal_to<T>` //不等于
- `template<class T> bool greater<T>` //大于
- `template<class T> bool greater_equal<T>` //大于等于
- `template<class T> bool less<T>` //小于
- `template<class T> bool less_equal<T>` //小于等于

**示例：**

```C++
C++

#include <functional>
#include <vector>
#include <algorithm>

class MyCompare
{
public:
	bool operator()(int v1,int v2)
	{
		return v1 > v2;
	}
};
void test01()
{
	vector<int> v;

	v.push_back(10);
	v.push_back(30);
	v.push_back(50);
	v.push_back(40);
	v.push_back(20);

	for (vector<int>::iterator it = v.begin(); it != v.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;

	//自己实现仿函数
	//sort(v.begin(), v.end(), MyCompare());
	//STL内建仿函数  大于仿函数
	sort(v.begin(), v.end(), greater<int>());

	for (vector<int>::iterator it = v.begin(); it != v.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：关系仿函数中最常用的就是greater<>大于

#### 4.3.4 逻辑仿函数

**功能描述：**

- 实现逻辑运算

**函数原型：**

- `template<class T> bool logical_and<T>` //逻辑与
- `template<class T> bool logical_or<T>` //逻辑或
- `template<class T> bool logical_not<T>` //逻辑非

**示例：**

```C++
C++

#include <vector>
#include <functional>
#include <algorithm>
void test01()
{
	vector<bool> v;
	v.push_back(true);
	v.push_back(false);
	v.push_back(true);
	v.push_back(false);

	for (vector<bool>::iterator it = v.begin();it!= v.end();it++)
	{
		cout << *it << " ";
	}
	cout << endl;

	//逻辑非  将v容器搬运到v2中，并执行逻辑非运算
	vector<bool> v2;
	v2.resize(v.size());
	transform(v.begin(), v.end(),  v2.begin(), logical_not<bool>());
	for (vector<bool>::iterator it = v2.begin(); it != v2.end(); it++)
	{
		cout << *it << " ";
	}
	cout << endl;
}

int main() {

	test01();

	system("pause");

	return 0;
}
```

总结：逻辑仿函数实际应用较少，了解即可
