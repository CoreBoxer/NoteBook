<font size="+3" color="red">

***类后加;***
**同样的访问限定符，如public等可以出现多次**
##引用
	相当于起别名,引用不能单独存在
	指针类型的引用：
		格式：指向的数据类型 * & 指针别名 = 被引用的指针名
		先初始化一个被引用的指针：int *p = &a；
		然后引用它： 				int *&q = p；

--- 

##const常量：
	const与指针：
		const int *p = NULL;与
		int const *p = NULL;完全等价

		const修饰前面的int类型时
			指针指向的对象的值不可改变,相当于只有读权限 
			如*p = 1;错误
		const修饰后面的指针名p时
			指针指向的对象不可改变     如*p = &b;错误
	const int x = 3; int *y = &x;错误，因为常量x有被修改的风险

	但const int x = 3; const int *y = &x;正确，因为指针在引用它是已经被限制为只读

	int x = 3; const int *y = &x;正确，因为此时y为只读权限

	整数时：
		int const &z = x;z = 10;错误，因为z变成了常量，只能赋初值

	函数中：
		const修饰形参时，则不能在函数中修改形参

	#define 与 const ：
		都是常量，但const检查语法错误，所以推荐

---

##函数参数默认值
	默认值最好定义在函数的声明中，在定义时使用默认值可能会报错；
	如 void fun(int i, int j = 5, int k = 10);
	void fun(int i, int j = 5; int k);错误，默认值只能右置

	无实参时使用默认值，有的时候则使用新值

##函数重载(同名函数)
	同名函数getMax(int x, int y, int z);编译后会变成getMax_int_int_int
	getMax(double x, double y);编译后会变成getMax_double_double;

##内联函数（展开）
	作用：编译时将函数体代码和实参代替函数调用语句，将省去执行时函数的调用出入栈等过程

	关键字：inline
	方法：inline int getMax(int x, int y, int z);
	注意：内联编译是建议性的，最终由编译器决定；
		逻辑简单，调用频繁的函数建议使用内联；
		递归函数无法使用内联方式

---

##内存管理
	申请单位内存： 	int *p = new int;			delete p;
	申请块内存:		int *p = new int[100];		delete []p;
	赋初值			int *p = new int(10);	数组不可以
###new与malloc的区别
**new会调用类的构造函数，而malloc仅仅是分配堆空间**
	申请后通过判断NULL来检查，删除后指针须赋NULL

##对象实例化
* 两种方式:堆\栈
	* 堆中实例化：
		ClassName *p = new ClassName();
		ClassName *p = new ClassName[10];delete []p;
		访问方式:
			单一：p->type = 0;
			数组：p[0]->type = 0;

	* 栈中实例化：
		ClassName ObjectName;
		ClassName ObjectName[10];
		访问方式：
			tv.type = 0;

---

##String类型：
	`#inlcude <string>`
* 初始化：
	* string s1; //s1为空串
	* string s2("ABC"); //用字符串面值初始化s2
	* string s3(s2); //将s3初始化为s2的一个副本
	* string s4(n,'c'); //将s4初始化为字符'c'的n个副本
* 常用操作:
	* s.empty()	若s为空串，则返回true，否则返回false
	* s.size()	返回s中字符的个数
	* s[n]		返回s中位置为n的字符，位置从0开始
	* s1 + s2		将两个串连接成新串，返回将新生成的串
	* s1 = s2		把s1的内容替换为s2的副本
	* v1 == v2	判定相等，相等为ture，否则false
	* v1 != v2	判定不等，不等为ture，否则false
	* 错误操作：
		`string s = "hello" + "world"; //两个""不能直接连接`
	* 字符串输入函数：
		`getline(cin,PraName);`

---
<font size="+1" color="purple">

##1.构造函数
* 构造函数与类同名
* 构造函数在对象实例化时被自动调用
* 构造函数没有返回值
* 构造函数可以有多个重载形式
* 实例化对象时仅有一个构造函数被调用
* 没有用户定义的构造函数时，编译器自动生成

###初始化列表：
`Student():m_strName("Lucy"),m_iAge(10){}`
传值型
`Student(string _name,int age):m_strName(_name),m_iAge(age){}`
**不能用=赋值，只能用括号**

* 初始化列表先于构造函数执行
* 初始化列表只能用于构造函数
* 可同时初始化多个数据成员
###重要性
**const成员常量只能通过初始化列表或构造函数默认值赋值，不能通过构造函数**
###拷贝构造函数
`Student(const Student &stu){}`
* 如果没有定义，则系统自动生成
* 以直接初始化（括号形式）或复制初始化（赋值形式）来实例化对象时自动调用
* 也可连接初始化列表
##析构函数
* 定义格式：~Classname();
* 可自动生成
* 在对象销毁时自动调用
* **没有返回值、没有参数、不能重载**
* **存在必要性**：
##对象的生命
**申请内存->初始化列表->构造函数->参与运算->析构函数->释放内存**

</font>
---

##面向对象的思想：
**对象的所有数据调用都通过函数来完成**
##命名规范：
* 身份_类型+名字
	如：string类型的数据成员name ——》 m_strName
			int类型的数据成员age ——》 m_iAge
* 形参：下划线+名字
	如：name形参： _name
##类内定义与内联函数:
	**类内定义的成员函数会优先变为内联函数，但复杂成员函数仍然会为普通函数**
##类外定义：
* 同文件类外定义
	如：
		class Car
	{
	public:
		void run();
		void stop();
	};

	void Car::run()
	{
		/*....*/
	}

	void Car::stop()
	{
		/*....*/
	}

* 分文件类外定义：
	用法：头文件内声明，源文件内定义（要包含该头文件）

---

###成员函数

**除析构函数外均可重载**

* 属性封装函数
* 一般功能函数**注意前一定加返回值 void等**
* 特殊功能函数
	* 构造函数
		* 拷贝构造函数
		* 默认构造函数
	* 析构函数
---

##类数组
**指针型：p指向第一个成员类首地址，p+1指向第二个成员类首地址……**
* 给成员赋值

`{
	Coordinate *p = new Coordinate[3];

	p->m.iX = 3;
	p[0].m.iY = 5;
}`

* **注意：当p++后，p[0]将指向原数组第2个元素**
* **要严格监视p指向的位置，防止使用或delete时产生内存错误**
* **(stu+1)会实质上给stu+1指向下一个地址**
	`(stu+1)->setName("abc");`
* 堆中类的析构函数会在delete时执行，而栈中类的析构函数会在return 0;后执行

* `delete p`与`delete []p`的区别
	delete p 时会只析构当前指向的类，而delete []p会析构数组中所有的类
* 实例化和销毁的顺序相反，实例化时先顺序实例化成员，最后实例化类
	而销毁时先销毁类，然后反序销毁成员
* 初始化数组类的各自成员函数，须配合构造函数
```
Coordinate coorArr[2]={{1,2},{3,4}};
```
---
##深拷贝与浅拷贝
**浅拷贝会将成员变量的值全部拷贝，包括指针类会指向相同的地址，相同地址使用和析构都会产生错误**
---

###this指针
* this指针是成员函数的一个隐含参数

###const高级应用
* 常成员参数
	* const可写在变量类型前面或后面
		`const int m_iX; 或 int const m_iX;`
* 常成员函数
	* 使用方法```void setX() const;```
		* 相当于```void setX(const Coordinate *this) const;```
		* **本质就是把对象指针this变成了常对象指针**
		* 所以常成员函数可以调用普通的数据成员，但是不能修改它的值
	* 常成员函数与同名的普通成员函数**互为重载**
		* 此时要想使用常成员函数须实例化 
		`const Coordinate coor[3];`
---
###对象的引用
**相当于为对象起别名，this指针，构造和析构还是只执行一次**

#继承
* 继承之前对象的某些特性
* 格式：
	`class Worker : public Person
	{
	public:
		void work();
		int m_iSalary;
	}`
* 三种继承方式
	* 公有继承：class A : public B
	* 私有继承：class A : private B
	* 保护继承：class A : protected B
* 会分别继承至派生类的相应分类中去
* 
   基类				继承方式				派生类
public成员							  public成员
private成员        public继承  		  无法访问 
protected成员						  protected成员

##隐藏
父子含有同名成员函数或数据成员的时候，会隐藏从父类中继承来的同名函数，无法重载，但可已通过特殊方法来调用
* 子类：  Soldier.play();
* 父类：  Soldier.Person::play();
**在子类的成员函数中调用父类继承的变量，则Person::m_strNumber;

##Is a
使用派生类初始化基类，将会只赋值继承部分
virtual 关键字:
	**使用  Person *p = new Soldier;
	派生类来实例化基类的指针的时候，会先初始化基类、派生类，但只析构基类,造成内存泄露，在派生类析构函数前加virtual关键字，可以将其一并析构** 

##多继承
一个派生类由两个及以上的基类继承而来，具有其基类的特征
`class 农民工 : public 工人,public 农民`
构造函数：
	`ChildLabourer(string name, int age):Worker(name),Children(age)`
**如果不写继承类型，默认为private私有继承**

##虚继承
菱形继承：两个或多个中间级派生类又同时是一个基类的派生类，此时底层派生类
		 初始化方式：
		 		`class Worker :virtual public Person`
		 		`class Migrant :virtual public Person`
		 		并通过#ifndef的条件编译方式防止基类文件多次被声明
		 	**因为有虚继承，整个过程中共用基类Person会分别被实例化一次，析构一次**

#多态
**定义：相同对象收到不同消息、或不同对象收到相同消息时产生的不同动作**
* 静态多态（早绑定）：
	- 例如重载的程序，会在编译时就根据参数不同而采取不同的功能
* 动态多态（晚绑定）：
	- 通过在基类中多功能函数上添加virtual，使其可以调用不同派生类的相应同名函数
	- **virtual修饰基类析构函数，使其变成虚析构函数，则可以使用父类指针同时析构子类**
virtual 在函数中的使用限制
1. 只能修饰成员函数，不能修饰普通全局函数
2. 不能修饰静态成员函数，如 `virtual static int fun();`
3. 不能修饰内联函数，会造成内联关键字失效，变成虚函数
4. 不能修饰构造函数

##虚函数
类的大小即所含数据成员的大小，如果没有数据成员，会留1个字节来标示类
如果有虚函数（包含虚析构函数），会产生一个虚函数列表指针，代销变为4个字节
基类的虚函数指针会传递给派生类，但并不是指向一个地址
对象的指针位置指向虚函数表，如果不存在虚函数表，则指向第一个数据成员

通过基类对象实例化派生类
	Animal *p = new Dog;

##纯虚函数
**含有 纯虚函数 的类叫做 抽象类，在虚函数表的对应地址里填0**
写法：virtual int func() = 0;
**抽象类无法实例化对象，只有抽象类的子类把纯虚函数具体化后才能用它实例化**

##接口类
* 定义：仅含有纯虚函数的类叫做接口类
* 接口类 更多的表示 能力或协议
* 接口类没有构造函数和析构函数

##运行时类型识别（RTTI）
* typteid typteid(*obj).name() 可以识别对象的类型
	- type_id返回一个type_info对象的引用
	- 如果想通过基类指针获得派生类的数据类型，基类必须带有虚函数，否则只能获得定义时的	 数据类型
	- 只能获取对象的实际类型
	- 比对方法： if(typteid(*obj).name == typteid(Bird))
* 动态类型转换: Bird* bird = dynamic_cast<Bird*>(obj);
	- 注意事项：
		+ 只能用于类的指针和引用的转换，而不能是类的本身
		+ 要转换的类型须包含虚函数
		+ 转换成功：返回子类地址	失败：返回NULL
---
##异常处理
throw...catch
例如发生数组下标越界异常
`
if(string.size < index)
{
	throw string("Ivalid Index")
}

catch(string &val)
{
	cout << val << endl;
}
`
###多态中的应用
将异常 Exception 定义为一个接口类，里面放处理和打印的方法纯虚函数，当跑出子对象的时候，就可以用接口类来捕获处理
如 throw new SizeErr();
	catch(Exception &e)
	{
		e.xxx();
	}
#模板
##友元函数
注意事项
* 友元关系不可传递
* 友元关系单向性
* 友元生命的形式和数量不受限制
* 友元只是封装的补充，会破坏封装性，属于定向暴露
##友元类
形式friend Match;需要在它之前声明Match防止编译出错 class Match;
---
##static 静态
静态变量或函数的存在不依赖于实例化，不在构造函数中实例化，可以单独进行
两种访问方法：
* 没定义对象时：Tank::getCount()
* 定义对象时  ：Tank.s_iCount

**静态的数据成员依赖于类，而普通的数据成员依赖于对象，所以不能再没有产生对象的情况下调用非静态的数据成员和成员函数**
静态成员函数没有this指针

##运算符重载
可赋予运算符新的功能，**本质就是函数的重载**
关键字：operator
###一元运算符重载
* 成员函数运算符重载：
	- Coordinate & operator-();
* 友元函数运算符重载：
	- friend Coordinate & operator-(Coordinate &);
* 前置运算符重载
	- Coordinate & operator++();		//++coor
	- 代码：
	{
		this->m_iX++;
		this->m_iY++;
		return *this;
	}
* 后置运算符重载
	- Coordinate operator++(int);	//coor++,没有引用
	- 代码：
	{
		Coordinate old(*this);
		this->m_iX++;
		this->m_iY++;
		return old;
	}
---
###二元运算符重载
* 成员函数运算符重载：
	- Coordinate operator+(const Coordinate &coor);
	- coor3 = coor1 + coor2 //coor1.operator + (coor2)
	- 代码：
	{
		Coordinate temp;
		temp.m_iX = this->m_iX + coor.m_iX;
		temp.m_iY = this->m_iY + coor.m_iY;
		return temp;
	}
	- 第一个参数不加是因为默认附带的this指针就可以
	- this指向的成员内变量与传入的coor内变量相加，赋给temp内变量并返回
* 友元函数运算符重载：
	- Coordinate operator+(const Coordinate &coor1,const Coordinate &coor2);
	- 代码：
	{
		Coordinate temp;
		temp.m_iX = coor1.m_iX + coor2.m_iX;
		temp.m_iY = coor1.m_iY + coor2.m_iY;
		return temp;
	}
	- coor1的成员内变量与coor2内变量相加，赋给temp内变量并返回
	- 加const是为了保护原变量不被破坏
输出运算符重载（因为this指针会占用1号形参，所以不能使用成员函数重载）
代码：
ostream & operator << (ostream &out,const Coordinate & coor)
{
	out << coor.m_iX << ',' << coor.m_iY;
	return out;
}

cout << coor; //operator(cout,coor);

而索引运算符[]必须采用成员函数进行重载，才能传入this指针
---
##模板
**模板与代码不能分离编译**
* 关键字：template typename class **不需要;**
  代码：
```
  		template <class T>
  		T max(T a, T b)
  		{
  			return (a > b) ? a : b;
  		}
```
   代码：
```
  		template <typename T>
  		void swap(T a, T b)
  		{
  			T temp;
  			temp = a;
  			a = b;
  			b = temp;

  		}

  		swap<int>(12,20);
```
* 变量作为模板参数
   代码：
```
  		template <int size>
  		void display()
  		{
  			cout << size << endl;
  		}

  		display<10>();
```
* 多参数模板
	代码：
```
  		template <typename T,typename S>
  		T max(T a,S b)
  		{
  			a++;
  			std::cout << s <<endl;
  			return (a > b) ? a : b;
  		}

  		max<int,string>(10,"dada");
```
**typename 和 class 可以混用
同名模板函数可以重载

##类模板
**在给类模板的函数进行类外定义的时候，每一个函数前都需要加一行template模板定义语句**
**在函数名后也需要加< >的语句**
代码：
```
	template <typename T,int Size,int Age>
	MyArray<T,Size,Age>::MyArray()
	{
		
	}

```
</font>




