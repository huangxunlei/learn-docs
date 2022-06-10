### mycat 配置



####  1.新增mycat  账号

```xml
<username="mycatread">  //mycat用户名
 
 <propertyname="password">mycat</property> //mycat密码
 
 <propertyname="schemas">mydb</property>  //mycat虚拟数据库名
 
 <propertyname="readOnly">true</property>  //只读
 
 </user>
 
 
 
 <username="mycat">
 
 <propertyname="password">mycat</property>
 
 <propertyname="schemas">mydb</property>
 
 </user>
```

