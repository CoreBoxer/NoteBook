一切数据都是对象

可变数据类型：int string tuple
不可变数据类型：list tuple dict

元组 tuple	(1,2,3)
列表 list 	[1,2,3]

dir() 列表显示模块的所有函数
	 	内部为空时：查看当前已加载的命名空间中所有变量和函数

help() 显示模块和函数等的帮助信息

对原数据的所有操作结果都会创建新的变量，id不同

当string类型含有字母时，不能强制转换成为int类型

字符串处理
	len() : 获取字符串长度（一个中文为3个字节长）
			可以转换成Unicode码：1.在字符串前加u，如u"哈"
							 	 2.转换函数，b = a.decode("utf-8")
			注意计算长度时需要转化成Unicode
	字符串的子列（下标）
				a.[-1]代表a的最后一个字符
			a[x:y] a的第x个到第y个字符，不包含第y个字符
				如a="abcd", 则a[1:3]为'bc'
	字符串拼接
			字符串 + 可以把字符串连接
			也可以使用%s，%d等占位符添加
				如 print "my name %s mike" % "is"  输出my name is mike
				使用元组：print "my name %s mike, %d years old" % ("is",15)  
							输出my name is mike
			.join函数， 使用列表容纳变量
				如".".join(["e","f","g"])
	字符串拆除
			x.spilt(String s, int i)
			将x按其中的s分成i+1部分
			当i = -1时，按s全部分开

单引号 双引号 三引号
			单引号和双引号可以互相包含，但是单独存在时须用\转义，三引号可以忽略'和"的区别且不分次数

