# MyBatis笔记之出现java.lang.ExceptionInInitializerError异常

**在运行Test类的时候很有可能会出现这个错误，接下来说几个解决方法**

![image-20201124211830290](C:\Users\28668\AppData\Roaming\Typora\typora-user-images\image-20201124211830290.png)

### **一：由于资源无法导入造成的异常**

#### 		先利用Maven的clean插件清除target目录，再查看此博客[Idea中resources资源导出失败的问题 | 涂斯睿 (lovezm.xyz)](https://lovezm.xyz/posts/IDEA中resources资源导出失败的问题/)后运行

### **二：由于xml文件中出现中文注释导致的异常**

#### 		清除xml中的中文注释

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
		PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
		"http://mybatis.org/dtd/mybatis-3-config.dtd">

<!--Configuration核心配置文件-->
<configuration>
	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC"/>
			<dataSource type="POOLED">
				<property name="driver" value="com.mysql.cj.jdbc.Driver"/>
				<property name="url" value="jdbc:mysql://localhost:3306/mybatis?userSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=UTC"/>
				<property name="username" value="root"/>
				<property name="password" value="tusirui"/>
			</dataSource>
		</environment>
	</environments>

<!--每一个mapper都需要在Mybatis核心配置文件中注册！-->
	<mappers>
		<mapper resource="com/tsr/dao/UserMapper.xml"/>
	</mappers>
</configuration>
```

#### 		删除如下两条注释即可

![image-20201124213149199](C:\Users\28668\AppData\Roaming\Typora\typora-user-images\image-20201124213149199.png)

![image-20201124213201755](C:\Users\28668\AppData\Roaming\Typora\typora-user-images\image-20201124213201755.png)