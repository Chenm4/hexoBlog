---
title: JSP&三层架构
author: 朝生暮
avatar:
authorLink: zhaoshengmu.top
authorAbout: 一个好奇的人
authorDesc: 一个好奇的人
categories: 技术
date: 2022-08-04 22:16:01
comments: true
tags:
- web
- 悦读
keywords: 
description: Javaweb相关知识
photos: https://img-1313111331.cos.ap-nanjing.myqcloud.com/webImage/hexoWeb/202208061255251.jpg
---

# day26-JSP&三层架构

# 教学目标

- [ ] 能够说出el表达式的作用
- [ ] 能够使用el表达式获取javabean的属性
- [ ] 能够使用jstl标签库的if标签
- [ ] 能够使用jstl标签库的foreach标签
- [ ] **能够使用三层架构模式完成转账案例**
- [ ] 能够使用ThreadLocal
- [ ] 能够完成转账案例

# 第一章-EL表达式 

## 知识点-EL表达式概述

### 1.目标

+ 能够说出el表达式的作用

### 2.讲解

#### 2.1.什么是El表达式

​	域对象：

​				request：一次请求

​				session：一次会话

​				ServletContext：整个应用

​	API：

​			getAttribute(name);

​			 <%=getAttribute(name) %>

​			${name}

​	Expression Language:表达式语言,  jsp2.0之后内置在jsp里面

​	目的：为了使JSP写起来更加简单,  取值(**取的域对象里面存的值**)更加简单。(代替脚本 <%= %>)

#### 2.2.EL语法

​	${el表达式}

#### 2.3.EL表达式的用途 

​	1.获取数据. 获取的是**域(request,session,ServletContext)对象中存储的数据**

​	2.EL执行运算(算术运算及逻辑运算)

### 3.小结

1. EL概念：表达式语言，目的简化开发
2. EL语法：${el表达式}
3. 作用：
   1. 获取域对象中的数据
   2. 执行运算

## 知识点-El获取数据

### 1.目标

+ 能够使用el表达式获取域对象里面的数据(**先要把数据存到域对象里面**)

### 2.路径

+ 获取简单数据类型数据(基本类型,字符串)
+ 获取数组
+ 获取list
+ 获取Map
+ **获取bean**

### 3.讲解

#### 3.1获取简单数据类型数据

​	语法:${requestScope|sessionScope|applicationScope.属性名}; 

​	快捷写法:==${属性名},  属性名就是存在域对象里面的key==



```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--EL获取简单类型数据
        1.在域对象中存储数据
        2.使用EL表达式获取数据 ${key}
        注意：
            情况一： EL表达式获取不到数据返回""
            情况二：EL表达式获取数据时，会从作用域范围小的到范围大的【request-->session-->application】进行查找，找到就停止返回，找不到继续向下查找。
    --%>
    <%
        //request 、 session 、ServletContext
        //jsp内置对象：request、session、application
        //request是jsp的内置对象  jsp生来就有 可以不用创建 直接使用
        request.setAttribute("name","tom");
        session.setAttribute("age",18);
        //application就是ServletContext对象 只不过是在jsp中换了个马甲
        application.setAttribute("address","深圳");

        //在request作用域中在存储一个age
        request.setAttribute("age",20);

    %>

    <%--获取域对象中存储的数据--%>
    <h3>老方式：<%=request.getAttribute("name") %></h3>
    <h3>EL表达式：${requestScope.name}</h3>
    <h3>简化方式：${name}</h3>
    <hr>
    <h3>老方式：<%=session.getAttribute("age") %></h3>
    <h3>EL表达式：${sessionScope.age}</h3>
    <h3>简化方式：${age}</h3>
    <hr>
    <h3>老方式：<%=application.getAttribute("address") %></h3>
    <h3>EL表达式：${applicationScope.address}</h3>
    <h3>简化方式：${address}</h3>

    <%--情况一：当获取的数据，在域对象中不存在时使用EL表达式获取会出现什么情况？  EL表达式获取不到数据返回""--%>
    <hr>
    <h3>老方式：<%=request.getAttribute("sex") %></h3>
    <h3>EL表达式：${requestScope.sex}</h3>
    <%--情况二：当多个作用域中存储相同key的数据，EL表达式使用简化方式获取时，会从范围小的到范围大的【request-->session-->application】进行查找，找到就停止返回--%>
    <hr>
    <h3>EL表达式：${requestScope.age}</h3>
    <h3>EL表达式：${sessionScope.age}</h3>
    <h3>简化方式：${age}</h3>
</body>
</html>
```

#### 3.2获取数组

​	语法: ${key[下标]}    key就是域对象里面存的key

#### 3.3获取list

​	`语法:${list属性名[index]}或者${list属性名.get(index)};list属性名就是存入域对象里面的key`

#### 3.4获取Map

​	 `语法:${map属性名.键} 或 ${map属性名.get("键")} 或${map属性名[键],map属性名就是存入域对象里面的key } `

​	注意：当map的键比较特殊(eg："1"、"a.ba.c")时，此时使用点的方式会报错，可以选择使用[]的方式。

```jsp
<%@ page import="java.util.ArrayList" %>
<%@ page import="java.util.List" %>
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--
        EL获取数据特点：
            1.EL获取数据会进行自动类型转换
    --%>
    <%--1.在域对象中存储数据--%>
    <%
        /*1.1：域对象中存储数组*/
        String[] names = {"张三","李四","王五"};
        request.setAttribute("names",names);

        /*1.2：域对象中存储list集合*/
        List<String> list = new ArrayList<>();
        list.add("篮球");
        list.add("足球");
        list.add("混球");
        request.setAttribute("list",list);

        /*1.3：获取Map集合中的元素*/
        Map<String,Object> map = new HashMap<>();
        map.put("cn","中国");
        map.put("jp","小日本");
        map.put("usa","漂亮国");
        request.setAttribute("map",map);
    %>
    <%--2.使用EL表达式获取域对象中的数据--%>
    <%--2.1：获取数组中的第二个元素 语法：${key[下标]}--%>
    <h3>老方式：<%=((String[])request.getAttribute("names"))[1]%></h3>
    <h3>获取数组数据：${names[1]}</h3>
    <hr>
    <%--2.2：获取list集合中的第三个元素 语法：${key[下标]} 或 ${key.get(下标)}--%>
    <h3>获取list数据：${list[2]}</h3>
    <h3>获取list数据：${list.get(2)}</h3>
    <hr>
    <%--2.3:获取map集合中的数据 语法：${key["map的键"]} 或 ${key.get("map的键")} 或 ${key.map的键}--%>
    <h3>获取map集合中键cn对应值：${map["cn"]}</h3>
    <h3>获取map集合中键cn对应值：${map.get("cn")}</h3>
    <h3>获取map集合中键cn对应值：${map.cn}</h3>
</body>
</html>
```



#### 3.5 获取bean

​	==语法:${key.javabean属性}==

​	依赖getxxx()方法; eg: getPassword()---去掉get-->Password()----首字母小写--->password



```jsp
<%@ page import="java.util.ArrayList" %>
<%@ page import="java.util.List" %>
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %>
<%@ page import="com.itheima.bean.User" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--
        EL获取数据特点：
            1.EL获取数据会进行自动类型转换
    --%>
    <%--1.在域对象中存储数据--%>
    <%
       //使用域对象存储javabean对象数据
        User user = new User();
        user.setName("zs");
        user.setPwd("123");
        request.setAttribute("user",user);

        //java对象属性：就是去掉get/set之后的单词首字母小写 得到就是属性  getName-->Name-->name
    %>
    <%--2.使用EL表达式获取javabeaan数据 语法：${key.javabean属性名称}--%>
    <h3>获取user对象的name属性值：${user.name}</h3>
</body>
</html>
```

#### 3.6：获取特殊key的值

```html
<%@ page import="java.util.ArrayList" %>
<%@ page import="java.util.List" %>
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %>
<%@ page import="com.itheima.bean.User" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--
        EL获取数据特点：
            1.EL获取数据会进行自动类型转换
    --%>
    <%--1.在域对象中存储数据--%>
    <%
       request.setAttribute("a.b.c.d","特殊key a.b.c.d");

       Map<String,Object> map = new HashMap<>();
       map.put("cn","中国");
       map.put("small.jp","小日本");
       request.setAttribute("map",map);
    %>

    <%--2.使用EL表达式获取--%>
    <h3>${a.b.c.d}</h3>
    <hr>
    <h3>${["a.b.c.d"]}</h3>
    <hr>
    <h3>${requestScope["a.b.c.d"]}</h3>

    <hr>
    <h3>${map.small.jp}</h3>
    <h3>${map["small.jp"]}</h3>
</body>
</html>
```

### 4.小结

	1. EL获取域对象中存储的数据 语法：${key}
	2. 获取简单数据类型数据 ：==${key}==
	3. 获取数组元素：==${key[下标]}==
	4. 获取list集合元素：==${key[下标]} 或${key.get(下标)}==
	5. 获取map集合元素：==${key["map的键"]} 或 ${key.get("map的键")} 或 ${key.map的键}==
	6. 获取javabean属性值：==${key.javabean属性名称}==
	7. 注意：
	  	1. EL获取不到数据返回""
	  	2. EL获取会进行自动类型转换
	  	3. EL获取数据从范围小的到大的【request-->session-->application】进行查找，找到就停止。

## 知识点-EL执行运算

### 1.目标

+ 掌握EL执行运算

### 2.讲解

![img](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103310.jpg)

#### 2.1算术运算

​	+,-,*,/

> **+**:加号在EL表达式中代表加运算，不作拼接使用,如果是数字的字符串形式，则EL表达式会自动进行类型转换，然后再进行算术运算

#### 2.2关系运算

​	< >= <= != ==

#### 2.3逻辑运算

​	&& || !

#### 2.4非空判断【重点】

​	empty，

​		**1. 判断一个对象是否为null,为null返回true  **

​		**2. 判断集合长度是否为0,集合为null或长度为0返回true**  

​		3. 判断一个字符串是否为 null或""，如果是则返回true

​	not empty

​	语法: ${empyt 属性名};属性名 就是域对象里面的key值

```jsp
<%@ page import="java.util.ArrayList" %>
<%@ page import="java.util.List" %>
<%@ page import="java.util.Map" %>
<%@ page import="java.util.HashMap" %>
<%@ page import="com.itheima.bean.User" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--EL执行运算
        1.+：表示加运算，不表示拼接 如果+两边有一方无法转换为数字 则报错
        2.empty
            2.1：判断一个对象是否为null               是返回true
            2.2：判断一个字符串是否为null或""           是返回true
            2.3：判断一个list集合是否为null或长度为0  是返回true
       3.not empty:判断是否非null 非null 返回true

    --%>
    <h4>${3+4}</h4>
    <h4>${3+"4"}</h4>
    <%--<h4>${3+"4a"}</h4>--%>

    <%
        User user = new User();
        request.setAttribute("user",user);

        String str1 = null;
        String str2 = "";
        request.setAttribute("str1",str1);
        request.setAttribute("str2",str2);

        List list1 = null;
        List list2 = new ArrayList();
        request.setAttribute("list1",list1);
        request.setAttribute("list2",list2);
    %>
    <h4>user对象是否为null：${empty user}</h4>
    <h4>str1是否为null或""：${empty str1}</h4>
    <h4>str2是否为null或""：${empty str2}</h4>
    <h4>list1是否为null或长度为0：${empty list1}</h4>
    <h4>list2是否为null或长度为0：${empty list2}</h4>

    <h4>user对象是否非null：${not empty user}</h4>
</body>
</html>
```

### 3.小结

1. 注意: **+只能做加法运算,不能拼接字符串**	,如果加号两边有一方无法转换为数字，则报错
2. empty：判断是否为null 为null返回true
   1. 判断一个对象是否为null
   2. 判断字符串是否为null或""
   3. 判断list集合是否为null或长度为0
# 第二章-JSTL标签库  

## 知识点-JSTL标签库概述

### 1.目标

+ 掌握什么是JSTL标签库

### 2.讲解

JSTL和EL是黄金搭档：JSTL作逻辑处理，EL获取数据展示。

#### 2.1什么是JSTL标签库

​	JSTL（JSP Standard Tag Library，JSP标准标签库)是一个不断完善的开放源代码的JSP标签库，是由apache的jakarta小组来维护的。这个JSTL标签库没有集成到JSP的, 要使用的话, 需要导jar包.

#### 2.2JSTL标签库的作用

​	为了简化在jsp页面上操作数据;  eg:  **遍历数据 判断数据**等

#### 2.3JSTL标签库的类别

![img](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103312.jpg)



### 3.小结

1. JSTL：JSP标签标准标签库，用于逻辑处理，可以替代java小脚本。
2. 作用：遍历数据 判断数据
3. 使用：由于JSTL没有被集成到jsp中，所以使用时需要导入jar包





## 知识点-JSTL核心标签库

### 1.目标

+ 掌握if,foreach标签的使用

### 2.讲解

#### 2.1核心标签库使用步骤

1. 导入jar包 ![img](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103313.png)

2. 在JSP页面上导入核心标签库`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>`



#### 2.3if标签

![img](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103314.png)

- 语法

```
<c:if test="el表达式${..}" [var="给之前的表达式的结果起个名字"] [scope="将结果保存在那个域中 默认page"]>
</c:if>
```

- 实例

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%
        //域对象存储数据
        request.setAttribute("age",6);
    %>
    <%--
        if标签：用于判断  test属性值为true，则执行if标签体中的内容，为false，不执行
            test：设置判断条件 可以使用EL表达式进行判断 返回一个boolean类型的结果true|false
            var：声明一个变量 存储判断结果
            scope：将判断结果变量存入到一个作用域中  方便后期使用
    --%>
    <c:if test="${age >= 7}" var="flag" scope="request">
        <h4>可以上小学了</h4>
    </c:if>
    <c:if test="${age<7}">
        <h4>回家玩泥巴去吧</h4>
    </c:if>
    <h3>if判断结果：${requestScope.flag}</h3>
</body>
</html>
```

+ 小结

  + 语法 

  ```
  <c:if test="${} "></c:if>
  ```

  + 特点
    + 如果test里面的是true, if标签体里面的就会执行
    + 如果test里面的是false, if标签体里面的就不会执行
    + 没有else的

#### 2.4choose标签

- 实例 

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%
        //域对象存储数据
        request.setAttribute("age",15);
        //需求：大于6岁可以上小学 大于13岁可以上中学  大于18岁可以上大学  如果小于等于6岁回家玩泥巴
        //分析：使用多重if判断实现
        //实现：
    %>
    <%--c:choose表示的就是多重if选择结构  c:when表示的就是if判断  c:otherwise表示的就是else--%>
    <c:choose>
        <c:when test="${age>18}">
            上大学
        </c:when>
        <c:when test="${age>13}">
            上中学
        </c:when>
        <c:when test="${age>6}">
            上小学
        </c:when>
        <c:otherwise>
            回家玩泥巴
        </c:otherwise>
    </c:choose>
</body>
</html>
```

#### 2.5foreach标签

![img](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103315.png)		

- 简单的使用:

```jsp
<%--forEach标签的简单使用
        需求：使用foreach标签遍历1-10 每个数字显示字题为红色
        forEach标签：用于遍历数据
            var：迭代变量 在foreach标签体中获取迭代变量数据 需要使用el表达式
            begin：开始 从第几个开始遍历
            end：结束  遍历到第几个结束
            step：步长 间隔
            varStatus：记录迭代变量的状态信息
--%>

<c:forEach var="i" begin="1" end="10" step="2" varStatus="status">
    <font color="red">${status.index} --> ${i}</font><br>
</c:forEach>
```



- 复杂的使用:

  ​			![img](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103316.png)



- c:forEach中的varStatus属性。

   	  指向一个字符串，该字符串引用一个对象。  map.put("vs",一个对象);

           	  这个对象记录着当前遍历的元素的一些信息：
     
             		   index:返回索引。从0开始
     
              		   count:返回计数。从1开始
     
             		    last:是否是最后一个元素
     
              		   first:是否是第一个元素	

```jsp
<%@ page import="java.util.ArrayList" %>
<%@ page import="com.itheima.bean.User" %>
<%@ page import="java.util.List" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--forEach标签的实际使用
        需求：查询操作，查询所有用户 在jsp页面显示
        分析：操作数据库，查询得到数据封装到list集合中 ，将数据保存到域对象中，在jsp页面就可以获取到展示
    --%>
    <%
        List<User> list = new ArrayList<>();
        User user1 = new User();
        user1.setName("zs");
        user1.setPwd("123");
        User user2 = new User();
        user2.setName("ls");
        user2.setPwd("456");
        User user3 = new User();
        user3.setName("ww");
        user3.setPwd("789");

        list.add(user1);
        list.add(user2);
        list.add(user3);

        request.setAttribute("list",list);
    %>
   <table border="1px" cellspacing="0" cellpadding="0" width="300px">
       <tr>
           <td>序号</td>
           <td>姓名</td>
           <td>密码</td>
       </tr>
       <%--forEach遍历：每遍历一次  输出一行用户信息记录显示在页面
            items：要遍历的数组/集合  使用el表达式获取
            var：声明迭代变量 接收数组|集合中的每一个元素
            varStatus：状态  status.index获取到索引下标
       --%>
       <c:forEach items="${list}" var="user" varStatus="status">
           <tr>
               <td>${status.index+1}</td>
               <td>${user.name}</td>
               <td>${user.pwd}</td>
           </tr>
       </c:forEach>

   </table>
</body>
</html>
```

### 3.小结

0. 准备

   1. 导入jar包

   2. 在要使用jstl标签库的jsp页面引入jstl标签库

      ```html
      <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
      ```

1. if标签

   ```html
   <c:if test="返回boolean类型数据的el表达式">
   
   </c:if>
   ```

2. choose标签

   ```html
   <c:choose>
   	<c:when test="判断条件1"></c:when>
       <c:when test="判断条件2"></c:when>
       ...
       <c:otherwise></c:otherwise>
   </c:choose>
   ```

3. foreach标签

   1. foreach简单使用

      ```html
      <c:forEach var="迭代变量" begin="开始" end="结束" step="步长" varStatus="迭代变量状态">
      	${迭代变量}
      </c:forEach>
      ```

   2. foreach实际使用

      ```html
      <c:forEach items="要遍历的集合 使用el表达式获取" var="迭代变量" varStatus="迭代变量状态">
      	${迭代变量}
      </c:forEach>
      ```

      

# 第三章-综合案例和开发模式

## 案例-完成转账的案例v1 ##

### 一.需求 ###

- 当单击提交按钮，付款方向收款方按照输入的金额转账。

  


![img](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103317.png)

### 二,分析

![image-20210729112956601](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103318.png) 

### 三,实现

#### 1.案例的准备工作

+ 数据库的准备

  ```
  create database day25;
  use day25;
  create table account(
      id int primary key auto_increment,
      name varchar(20),
      money double
  );
  
  insert into account values (null,'zs',1000);
  insert into account values (null,'ls',1000);
  insert into account values (null,'ww',1000);
  ```

+ 页面

  ```
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  <html>
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Insert title here</title>
  </head>
  
  <body>
  <form method="post" action="">
    <table border="1px" width="500px" align="center">
      <tr>
        <td>付款方</td>
        <td><input type="text" name="from"></td>
      </tr>
      <tr>
        <td>收款方</td>
        <td><input type="text" name="to"></td>
      </tr>
      <tr>
        <td>金额</td>
        <td><input type="text" name="money"></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit"></td>
      </tr>
    </table>
  </form>
  
  </body>
  </html>
  
  ```

+ jar包
+ 工具类
+ 配置文件

#### 2.代码实现

+ UserServlet

```java
package com.itheima.web;

import com.itheima.utils.C3P0Utils;
import org.apache.commons.dbutils.QueryRunner;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.sql.SQLException;

@WebServlet("/transfer")
public class TransferServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        try {
            //0.中文乱码处理
            request.setCharacterEncoding("UTF-8");
            response.setContentType("text/html;charset=UTF-8");
            // 1.获取请求参数【付款方、收款方、金额】
            String from = request.getParameter("from"); //付款方
            String to = request.getParameter("to");     //收款方
            double money = Double.parseDouble(request.getParameter("money"));     //转账金额
            // 2.处理业务【判断付款方收款方是否存在 金额是否合理 假设一切OK】
            // 3.操作数据库【两个update语句 钱减少 钱增加】rows1 rows2
            QueryRunner queryRunner = new QueryRunner(C3P0Utils.getDataSource());
            String sql1 = "update account set money=money-? where name=?";
            int rows1 = queryRunner.update(sql1, money, from);
            String sql2 = "update account set money=money+? where name=?";
            int rows2 = queryRunner.update(sql2, money, to);
            // 4.根据处理结果进行响应
            if(rows1>0 && rows2>0){
                response.getWriter().print("转账成功！");
            }else{
                response.getWriter().print("转账失败！");
            }
        } catch (Exception e) {
            e.printStackTrace();
            response.getWriter().print("服务器异常！");
        }
    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request, response);
    }
}
```

### 四.小结

1. 转账功能：操作数据库 付款方钱减少 收款方钱增加
2. 编写TransferServlet
   1. 获取请求参数
   2. 处理业务
   3. 响应



## 知识点-开发模式  

### 1.目标

+ 理解模式二(MVC)和模式三(三层架构)

### 2.讲解

#### 2.1JSP的开发模式一【了解】

jsp+javabean：实现

 	javaBean:实体类。特点：私有化的属性、公共的getter setter方法、无参的构造。

<img src="https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103319.png" alt="image-20201209173731685" style="zoom:60%;" />

问题：

​	1.所有的代码都在jsp中  会存在大量代码冗余  不利于代码维护修改 

​    2.jsp已经属于上个时代的技术了 过时了 jsp执行效率非常低 jsp-->翻译成java代码-->编译class文件-->执行

#### 2.2JSP的开发模式二

​	JSP【html】 + Servlet + JavaBean 称为MVC的开发模式.

​	**MVC:开发模式**

​	M：model 模型 （javaBean：处理业务封装数据）

​	V：View 视图  （JSP|html：展示数据）

​	C：controller 控制器 （Servlet：接收请求进行分发，做为控制器）



![img](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103320.png)

#### 2.3模式三: 三层架构 



- 软件中分层：按照不同功能分为不同层，通常分为三层：表现层(web层)，业务层，持久(数据库)层。

  

  ![img](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103321.png)

- 不同层次包名的命名

| 分层                 | 包名(公司域名倒写)                     |
| -------------------- | -------------------------------------- |
| 表现层(web层)        | com.itheima.web        编写Servlet     |
| 业务层(service层)    | com.itheima.service   编写Service类    |
| 持久层(数据库访问层) | com.itheima.dao           编写Dao类    |
| JavaBean             | com.itheima.bean         封装数据      |
| 工具类               | com.itheima.utils           编写工具类 |

![image-20210814123710601](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103322.png)

- 分层的意义:
  1. 解耦：降低层与层之间的耦合性。 
  2. 可维护性：提高软件的可维护性，对现有的功能进行修改和更新时不会影响原有的功能。
  3. 可扩展性：提升软件的可扩展性，添加新的功能的时候不会影响到现有的功能。
  4. 可重用性：不同层之间进行功能调用时，相同的功能可以重复使用。

缺点：代码量增多，结构复杂，当新增功能时，需要web、service、dao分别写一次。

但是利大于弊！所以今后的开发全部使用三层架构进行代码编写。

好的代码：低耦合+高内聚

![img](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103323.png)



### 3.小结

1. 模式一：jsp+javabean
2. 模式二：MVC
   1. M：model  javabean 封装数据
   2. V：view     jsp|html    展示数据
   3. C：controller servlet  接收请求，调用业务处理，响应
3. 三层架构：
   1. 表现层【web】： com.itheima.web  编写Servlet类
      1. 获取请求参数
      2. 调用业务处理
      3. 响应
   2. 业务层【service】：com.itheima.service  编写Service类
      1. 处理业务
      2. 调用dao
   3. 持久层【dao】：com.itheima.dao   编写dao类	
      1. 操作数据库

## 案例-完成转账的案例v2 ##

### 一.需求 ###

- 使用三层架构改写转账案例

  


![img](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103317.png)

### 二,分析

![image-20210814143913354](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103324.png) 

### 三,实现

+ TransferServlet

```java
package com.itheima.web;

import com.itheima.service.TransferService;
import com.itheima.utils.C3P0Utils;
import org.apache.commons.dbutils.QueryRunner;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.sql.SQLException;

@WebServlet("/transfer")
public class TransferServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        try {
            //0.中文乱码处理
            request.setCharacterEncoding("UTF-8");
            response.setContentType("text/html;charset=UTF-8");
            //1.获取请求参数【付款方、收款方、金额】
            String from = request.getParameter("from"); //付款方
            String to = request.getParameter("to");     //收款方
            double money = Double.parseDouble(request.getParameter("money"));     //转账金额
            //2.调用业务处理
            TransferService transferService = new TransferService();
            boolean flag = transferService.transfer(from,to,money);
            //3.响应
            if(flag){
                response.getWriter().print("转账成功！");
            }else{
                response.getWriter().print("转账失败！");
            }
        } catch (Exception e) {
            e.printStackTrace();
            response.getWriter().print("服务器异常！");
        }
    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request, response);
    }
}
```

+ TransferService

```java
package com.itheima.service;

import com.itheima.dao.TransferDao;

import java.sql.SQLException;

public class TransferService {

    /**
     * 转账业务
     * @param from  付款方
     * @param to    收款方
     * @param money 转账金额
     * @return 转账是否成功  true|false
     */
    public boolean transfer(String from, String to, double money) throws SQLException {
        //1.处理业务【判断付款方 收款方是否存在 转账金额是否合理  假设一切正常】
        //2.调用dao
        TransferDao transferDao = new TransferDao();
        //2.1：付款方钱减少
        int rows1 = transferDao.reduceMoney(from,money);

        //2.2：收款方钱增加
        int rows2 = transferDao.addMoney(to,money);
        //3.根据dao处理结果判断转账是否成功 并返回
        if(rows1>0 && rows2>0){
            return true;
        }
        return false;
    }
}
```

+ TransferDao

```java
package com.itheima.dao;

import com.itheima.utils.C3P0Utils;
import org.apache.commons.dbutils.QueryRunner;

import java.sql.SQLException;

public class TransferDao {

    /**
     * 付款方钱减少
     * @param from
     * @param money
     * @return
     */
    public int reduceMoney(String from, double money) throws SQLException {
        //1.操作数据库
        QueryRunner queryRunner = new QueryRunner(C3P0Utils.getDataSource());
        String sql = "update account set money = money-? where name=?";
        return queryRunner.update(sql,money,from);
    }

    /**
     * 收款方钱增加
     * @param to
     * @param money
     * @return
     */
    public int addMoney(String to, double money) throws SQLException {
        //1.操作数据库
        QueryRunner queryRunner = new QueryRunner(C3P0Utils.getDataSource());
        String sql = "update account set money = money+? where name=?";
        return queryRunner.update(sql,money,to);
    }
}
```

### 四.小结

+ web层  com.itheima.web  XxxServlet
  + 获取请求参数
  + 调用业务处理
  + 响应
+ service  com.itheima.service XxxService
  + 处理业务
  + 调用dao
+ dao  com.itheima.dao  XxxDao
  + 操作数据库



## 案例-完成转账的案例v3 ##

### 一.需求 ###

- 当单击提交按钮，付款方向收款方安照输入的金额转账。 使用手动事务进行控制

  


![img](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103317.png)

### 二,分析


#### 1.DBUtils实现事务管理

| API                                                          | 说明                                    |
| ------------------------------------------------------------ | --------------------------------------- |
| QueryRunner()                                                | 创建QueryRunner对象. 手动提交事务时使用 |
| query(connection，String sql, Object[] params, ResultSetHandler<T> rsh) | 查询(需要传入Connection)                |
| update(connection，String sql, Object... params)             | 更新                                    |

#### 2.思路

![image-20191212153634611](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103325.png) 

### 三,实现

+ TransferService

```java
package com.itheima.service;

import com.itheima.dao.TransferDao;
import com.itheima.utils.C3P0Utils;

import java.sql.Connection;
import java.sql.SQLException;

public class TransferService {

    /**
     * 转账业务
     * @param from  付款方
     * @param to    收款方
     * @param money 转账金额
     * @return 转账是否成功  true|false
     * 需求：转账包含两个操作：一个钱减少，一个钱增加，只有当钱减少和钱增加两个操作都执行成功时，才表示转账成功，如果有一个失败，则转账失败，付款方和收款方的金额应该不变
     * 分析：要将转账的两个操作封装成为一个整体，使用事务  事务的特性：将多个操作作为一个整体执行，要成功，都成功，要失败，都失败
     * 事务的使用：
     *      1.开启事务          ：connection.setAutoCommit(false);
     *      2.执行操作          :钱减少、钱增加
     *      3.事务提交或事务回滚  ：true：connection.commit();     false|exception:connection.rollback();
     * 事务使用细节：执行操作所有的connection连接对象要是同一个，如果不是同一个，则不能进行事务控制
     * 事务使用的地方：
     *      事务使用在service层，将多个数据库操作作为一个整体执行。
     */
    public boolean transfer(String from, String to, double money) throws Exception {

        /********1.开启手动事务*******/
        Connection connection = C3P0Utils.getConnection();
        connection.setAutoCommit(false);


        try {
            //1.处理业务【判断付款方 收款方是否存在 转账金额是否合理  假设一切正常】
            //2.调用dao
            TransferDao transferDao = new TransferDao();
            //2.1：付款方钱减少
            int rows1 = transferDao.reduceMoney(connection,from,money);

            //人为制作异常 用于测试
            //int i = 1/0;

            //2.2：收款方钱增加
            int rows2 = transferDao.addMoney(connection,to,money);
            //3.根据dao处理结果判断转账是否成功 并返回
            if(rows1>0 && rows2>0){
                //转账成功 提交事务
                connection.commit();
                return true;
            }else{
                //转账失败 回滚事务
                connection.rollback();
                return false;
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
            //发生异常 回滚事务
            connection.rollback();
            return false;
        }
    }
}
```

+ TransferDao

```java
package com.itheima.dao;

import com.itheima.utils.C3P0Utils;
import org.apache.commons.dbutils.QueryRunner;

import java.sql.Connection;
import java.sql.SQLException;

public class TransferDao {

    /**
     * 付款方钱减少
     * @param from
     * @param money
     * @return
     */
    public int reduceMoney(Connection connection, String from, double money) throws SQLException {
        //1.操作数据库
        //注意：多个操作使用同一个connection时，才能进行事务控制  所以此时创建QueryRunner对象时使用无参构造方法
        QueryRunner queryRunner = new QueryRunner();
        String sql = "update account set money = money-? where name=?";
        return queryRunner.update(connection,sql,money,from);
    }

    /**
     * 收款方钱增加
     * @param to
     * @param money
     * @return
     */
    public int addMoney(Connection connection, String to, double money) throws SQLException {
        //1.操作数据库
        QueryRunner queryRunner = new QueryRunner();
        String sql = "update account set money = money+? where name=?";
        return queryRunner.update(connection,sql,money,to);
    }
}
```

### 四.小结

1. 事务使用步骤
   1. 手动开启事务：connection.setAutoCommit(false);
   2. 执行操作：多个操作需要使用同一个连接对象connection
   3. 事务提交或事务回滚：connection.commit();     connection.rollback();
2. 事务使用位置：使用在Service层
3. 通过在Service层获取connection对象 传递到dao层进行使用操作数据库。

## 案例-完成转账的案例v4 ##

### 一.需求 ###

- 当单击提交按钮，付款方向收款方安照输入的金额转账。 使用事务进行控制

  


![img](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103317.png)

### 二,分析

#### 1.ThreadLocal

​	在“事务传递参数版”中，我们必须修改方法的参数个数，传递连接对象，才可以完成整个事务操作。如果不传递参数，是否可以完成？在JDK中给我们提供了一个工具类：ThreadLocal，此类可以在一个线程中共享数据。

​	java.lang.ThreadLocal,该类提供了线程局部 (thread-local) 变量，用于在当前线程中共享数据。ThreadLocal工具类**底层就是一个Map，key存放的当前线程，value存放需要共享的数据**

```java
//模拟ThreadLocal类 
public class ThreadLocal{
	private Map<Thread,Object> map = new HashMap<Thread,Object>();
	
	public void set(Connection conn){
		map.put(Thread.currentThread(),conn); //以当前线程对象作为key
	}
		
	public Object get(){
		return map.get(Thread.currentThread()); //只有当前线程才能取出value数据
	}
}
ThreadLocal:实现在当前线程下共享数据【范围小很多】  
类似于ServletContext在当前应用下共享数据【范围大一些】
结论：向ThreadLocal对象中添加的数据只能在当前线程下使用。
```

#### 2.思路

![image-20191212155332778](https://cdn.statically.io/gh/Chenm4/typoraImg@main/202208061103326.png)

### 三,代码

+ ConnectionManager

```java
package com.itheima.utils;

import java.sql.Connection;

public class ConnectionManager {
    //使用ThreadLocal工具类在同一个线程中共享数据
    private static ThreadLocal<Connection> t = new ThreadLocal<>();

    //获取Connection对象
    public static Connection getConnection() throws Exception {
        Connection connection = t.get();
        if(connection==null){
             connection = C3P0Utils.getConnection();
             t.set(connection);
        }
        return connection;
    }
}
```

+ UserService

```java
package com.itheima.service;

import com.itheima.dao.TransferDao;
import com.itheima.utils.C3P0Utils;
import com.itheima.utils.ConnectionManager;

import java.sql.Connection;
import java.sql.SQLException;

public class TransferService {

    /**
     * 转账业务
     * @param from  付款方
     * @param to    收款方
     * @param money 转账金额
     * @return 转账是否成功  true|false
	*/
    public boolean transfer(String from, String to, double money) throws Exception {

        /********1.手动开启事务*******/
        Connection connection = ConnectionManager.getConnection();
        connection.setAutoCommit(false);

        try {
            //1.处理业务【判断付款方 收款方是否存在 转账金额是否合理  假设一切正常】
            //2.调用dao
            TransferDao transferDao = new TransferDao();
            //2.1：付款方钱减少
            int rows1 = transferDao.reduceMoney(from,money);

            //人为制作异常 用于测试
            //int i = 1/0;

            //2.2：收款方钱增加
            int rows2 = transferDao.addMoney(to,money);
            //3.根据dao处理结果判断转账是否成功 并返回
            if(rows1>0 && rows2>0){
                //转账成功 提交事务
                connection.commit();
                return true;
            }else{
                //转账失败 回滚事务
                connection.rollback();
                return false;
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
            //发生异常 回滚事务
            connection.rollback();
            return false;
        }

    }
}
```

+ UserDao

```java
package com.itheima.dao;

import com.itheima.utils.C3P0Utils;
import com.itheima.utils.ConnectionManager;
import org.apache.commons.dbutils.QueryRunner;

import java.sql.Connection;
import java.sql.SQLException;

public class TransferDao {

    /**
     * 付款方钱减少
     * @param from
     * @param money
     * @return
     */
    public int reduceMoney(String from, double money) throws Exception {
        //1.操作数据库
        //注意：多个操作使用同一个connection时，才能进行事务控制  所以此时创建QueryRunner对象时使用无参构造方法
        QueryRunner queryRunner = new QueryRunner();
        String sql = "update account set money = money-? where name=?";
        return queryRunner.update(ConnectionManager.getConnection(),sql,money,from);
    }

    /**
     * 收款方钱增加
     * @param to
     * @param money
     * @return
     */
    public int addMoney(String to, double money) throws Exception {
        //1.操作数据库
        QueryRunner queryRunner = new QueryRunner();
        String sql = "update account set money = money+? where name=?";
        return queryRunner.update(ConnectionManager.getConnection(),sql,money,to);
    }
}
```



### 四.小结

1. ThreadLocal：java中一个工具类，用于在同一线程中共享数据。
2. 使用ThreadLocal存储Connection对象在同一线程中共享，这样Service和dao层获取Connection连接对象，就直接从ThreadLocal中拿，得到的就是同一个Connection对象，就可以实现事务控制。



# 本章小结

1. EL表达式

   1. 概念：用于在jsp页面中获取域对象中存储的数据 目的：简化jsp页面数据获取 语法：${EL表达式}

   2. 获取域对象中存储的数据

      1. 简单类型数据获取 ：${key}
      2. 数组类型数据获取：${key[下标]}
      3. list集合数据获取：${key[下标]} 或${key.get(下标)}
      4. map集合数据获取：${key["map的key"]} 或${key.get("map的key")} 或者 ${key.map的key}
      5. javabean属性值获取：${key.javabean属性名称}

      注意：

      ​	1.EL获取数据 从作用域范围小的到大的进行查找，找到就停止，找不到返回""

      ​	2.EL获取数据会进行自动类型转换

      ​	3.EL获取key中包含特殊字符的数据 

      ​				${requestScope["key"]}

      ​				${key["map的key"]}

   3. 执行运算

      1. +：在这里表示加运算，不进行字符串拼接，如果+号两边有一方无法转为数字类型，则报错
      2. empty：
         1. 判断对象是否为null 为null返回true
         2. 判断String为null或者""  如果是返回true
         3. 判断list集合为null或长度为0 如果是返回true

2. JSTL标签库

   1. 使用步骤

      1. 添加jar包到项目

      2. 在要使用jstl标签的jsp页面引入jstl标签库

         <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

      3. if标签的使用

         如果test属性值为true，则执行if标签体中的内容

         ```html
         <c:if test="el表达式 返回boolean类型的结果"></c:if>
         ```

      4. foreach标签的使用

         ```html
         <c:forEach items="要遍历的集合|数组" var=“迭代变量”> 
         ${迭代变量}
         </c:forEach>
         ```

3. 开发模式

   1. MVC开发模式
      - m：model 模型 javabean  封装数据
      - v：view     视图  jsp            展示数据
      - c：controller 控制器 Servlet  接收请求，调用业务处理，给出响应
   2. 三层架构
      1. web层  com.itheima.web  xxxServlet
         - 获取请求参数
         - 调用业务处理
         - 响应
      2. service层  com.itheima.service  xxxService
         - 处理业务
         - 调用dao
      3. dao层  com.itheima.dao     xxxDao
         - 操作数据库

4. 转账案例

   1. 传统方式实现转账案例
   2. 三层架构实现转账案例
   3. 手动事务控制实现转账
   4. 使用ThreadLocal统一管理connection对象  实现事务控制转账

5. 练习：

   1. ==传统方式实现转账案例==
   2. ==三层架构实现转账案例==
   3. 手动事务控制实现转账
   4. 使用ThreadLocal
   5. JSTL中if和forEach标签的使用
   6. 使用EL表达式获取数据







​		
