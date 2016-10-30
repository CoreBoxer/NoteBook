引用
	相当于起别名,引用不能单独存在
	指针类型的引用：
		格式：指向的数据类型 * & 指针别名 = 被引用的指针名
		先初始化一个被引用的指针：int *p = &a；
		然后引用它： 				int *&q = p；   
const常量：
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

函数参数默认值
	默认值最好定义在函数的声明中，在定义时使用默认值可能会报错；
	如 void fun(int i, int j = 5, int k = 10);
	void fun(int i, int j = 5; int k);错误，默认值只能右置

	无实参时使用默认值，有的时候则使用新值

函数重载(同名函数)
	同名函数getMax(int x, int y, int z);编译后会变成getMax_int_int_int
	getMax(double x, double y);编译后会变成getMax_double_double;

内联函数（展开）
	作用：编译时将函数体代码和实参代替函数调用语句，将省去执行时函数的调用出入栈等过程

	关键字：inline
	方法：inline int getMax(int x, int y, int z);
	注意：内联编译是建议性的，最终由编译器决定；
		逻辑简单，调用频繁的函数建议使用内联；
		递归函数无法使用内联方式

内存管理
	申请单位内存： 	int *p = new int;			delete p;
	申请块内存:		int *p = new int[100];		delete []p;
	赋初值			int *p = new int(10);	数组不可以

	申请后通过判断NULL来检查，删除后指针须赋NULL

对象实例化
	两种方式:堆\栈
	堆中实例化：
		ClassName *p = new ClassName();
		ClassName *p = new ClassName[10];delete []p;
		访问方式:
			单一：p->type = 0;
			数组：p[0]->type = 0;

	栈中实例化：
		ClassName ObjectName;
		ClassName ObjectName[10];
		访问方式：
			tv.type = 0;

String类型：
	#inlcude <string>
	初始化：
		string s1; //s1为空串
		string s2("ABC"); //用字符串面值初始化s2
		string s3(s2); //将s3初始化为s2的一个副本
		string s4(n,'c'); //将s4初始化为字符'c'的n个副本
	常用操作:
		s.empty()	若s为空串，则返回true，否则返回false
		s.size()	返回s中字符的个数
		s[n]		返回s中位置为n的字符，位置从0开始
		s1 + s2		将两个串连接成新串，返回将新生成的串
		s1 = s2		把s1的内容替换为s2的副本
		v1 == v2	判定相等，相等为ture，否则false
		v1 != v2	判定不等，不等为ture，否则false
	错误操作：
		string s = "hello" + "world"; //两个""不能直接连接
	字符串输入函数：
		getline(cin,PraName);
面向对象的思想：
	对象的所有数据调用都通过函数来完成
命名规范：
	身份_类型+名字
		如：string类型的数据成员name ——》 m_strName
			int类型的数据成员age ——》 m_iAge
	形参：下划线+名字
		如：name形参： _name
类内定义与内联函数:
	类内定义的成员函数会优先变为内联函数，但复杂成员函数仍然会为普通函数
类外定义：
	同文件类外定义
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
	分文件类外定义：
		用法：头文件内声明，源文件内定义（要包含该头文件）
		
