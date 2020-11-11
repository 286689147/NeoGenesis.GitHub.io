# **Session（重点）**
**1，什么是Session？**

 - 服务器会对每一个用户（浏览器）创建一个Session对象
 - 一个Session独占一个浏览器，只要浏览器没关，这个Session就存在
 - 用户登录之后，整个网站都可以访问-->保存用户的信息；保存购物车的信息

**2，对Session的理解**

 - SessionID相当于一把钥匙，Session相当于一个房间，每一个用户的SessionID可以打开属于自己的Session房间
 - Cookie相当于服务端返回给你了一把钥匙加房间里的东西，全都给你了
 - ServletContext相当于再服务端之前增加了一个空间，所有用户都能访问到，作用域比Session更高

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201025230752524.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzODM3NTI3,size_16,color_FFFFFF,t_70#pic_center)

**3，Session中的方法**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201025200309487.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzODM3NTI3,size_16,color_FFFFFF,t_70#pic_center)


**4，Session和Cookie的区别**

 - Cookie是把用户的数据写给浏览器，由浏览器进行保存（可以保存多个）
 - Session是把用户的数据写到独占的Session，由服务端进行保存（保存重要信息，减少服务器资源的浪费）
 - Session对象由服务端创建

**5，使用场景**

 - 保存一个用户的登录信息
 - 保存购物车信息
 - 在整个网站中经常会使用的数据将其保存在Session中

**5，使用Session（不要忘记在web.xml添加servlet）**

```java
//Demo01
package com.tusirui.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.Date;

public class CookieDemo01 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");
        //在页面上创建输出对象
        PrintWriter out = resp.getWriter();
        //可能由多个Cookie，所以定义一个数组接收
        Cookie[] cookies = req.getCookies();
        if(cookies!=null){
            out.write("上一次访问时间是：");
            for (Cookie cookie : cookies) {
                //获取cookie的名字
                if(cookie.getName().equals("LastLoginTime")){
                    //获取Cookie中的值
                    long lastLoginTime = Long.parseLong(cookie.getValue());
                    Date date = new Date(lastLoginTime);
                    out.write(date.toLocaleString());
                }
            }
        }else{
            out.write("这是您第一次来本站");
        }
        //第一次来的时候创建一个Cookie并赋值
        Cookie cookie = new Cookie("LastLoginTime",System.currentTimeMillis()+"");
        //将Cookie传输给客户端
        resp.addCookie(cookie);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}

```

```java
//Demo2
package com.tusirui.servlet;

import com.tusirui.pojo.Person;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

public class SessionDemo02 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //解决乱码问题
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");
		//从session中得到Person类中的name属性并使用Person中的toString方法将其转化成字符串
        HttpSession session = req.getSession();
        Person person =(Person) session.getAttribute("name");
        System.out.println(person.toString());
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}

```

```java
//Demo3
package com.tusirui.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

public class SessionDemo03 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        HttpSession session = req.getSession();
        //手动移除Session
        session.removeAttribute("name");
        session.invalidate();
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}

```

```java
//Person类
package com.tusirui.pojo;

import java.util.Objects;

public class Person {
    private String name;
    private int age;

    public Person(String name,int age) {
        this.name = name;
        this.age=age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age &&
                Objects.equals(name, person.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}

```
**7，移除Session的两种方法：**

 1. （手动注销）在同一个web项目中自己创建一个Servlet，然后调用Session的removeAttribute()方法先移除Session中之间传入的值，然后再调用invalidate()方法来移除Session
 2. （自动过期）在web.xml文件中创建一个：

```html
<!--失效的时间单位为分钟-->
	<session-config>
		<session-timeout>15</session-timeout>
	</session-config>
```