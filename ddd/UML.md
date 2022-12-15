## 什么是类图

统一建模语言（Unified Modeling Language，缩写UML）是非专利的第三代建模和规约语言。

UML是一种开放的方法，用于说明、可视化、构建和编写一个正在开发的、面向对象的、软件密集系统的制品的开放方法。

## 类图的元素

### 01 类

**类是具有相似结构、行为和关系的一组对象的描述符，是面向对象系统中最重要的构造块。**

**如下图所示，就表示一个类：**

![Animal.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3ba15fdf4ef945c78f35b5fcdbfe347e~tplv-k3u1fbpfcp-watermark.image?)



三个格子从上到下分别表示：

\-  类名称或者是接口名称（如果抽象类，那么名称是斜体）

\-  类的特征（一般是类的字段和属性可以为空）

\-  类的操作（一般是类的方法或者行为）

它们前边的符号有以下几类：

  \+ 代表 public 

  \- 代表 private

  \# 代表 protect

### 02 类的关系

 **除了类，类图中还有一个重要元素，即类之间的关系。**

 （1）**依赖关系**（用虚线表示+ 尖箭头表示）

  所谓依赖关系，就是构造这个类的时候，需要依赖其他的类。

 可以简单的理解，就是一个类A使用到了另一个类B，而这种使用关系是具有偶然性的、、临时性的、非常弱的，但是B类的变化会影响到A；比如某人要过河，需要借用一条船，此时人与船之间的关系就是依赖；

 表现在代码层面，为类B作为参数被类A在**某个method方法中使用**。

 如下图所示：



![1231.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fe46b24197334328ad35e453d0939d60~tplv-k3u1fbpfcp-watermark.image?)



metabolism 方法中需要 传入 Oxygen 和Water 两个类型的参数，所以 Aminal 类就和Oxygen和Water类建立了依赖关系。

#### （2）**继承、泛化关系**（用带空心三角形的实线表示）

继承关系也叫泛化关系，指的是一个类（称为子类、子接口）继承另外的一个类（称为父类、父接口）的功能，并可以增加它自己的新功能的能力，继承是类与类或者接口与接口之间最常见的关系。

*继承用实线空心箭头表示，由子类指向父类。*

如下图：Dog和Cat 都继承了Animal 都是 Animal的子类。



![extend.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/74fd6f61661c432eb54caba6753abe51~tplv-k3u1fbpfcp-watermark.image?)



#### （3）**实线关系**（用带空心三角形的虚线表示）

指的是一个class类实现interface接口（可以是多个）的功能；实现是类与接口之间最常见的关系；在Java中此类关系通过关键字implements明确标识，在iOS中我将其理解成代理的实现。

如下图所示：VipUser和SuperVipUser都实现了IUser,他们都有了walk功能



![implements.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c057e350cbfe4e74acef40bacbf9df76~tplv-k3u1fbpfcp-watermark.image?)

#### （4）关联关系（Association）

所谓关联关系，就是这个类有一个属性是其他类。

> 表现在代码层面，为被关联类B**以类属性的形式**出现在关联类A中，也可能是关联类A引用了一个类型为被关联类B的全局变量；
>

写一个Person类，他拥有一个宠物猫和宠物狗，他们之间的关系是关联。

![Association.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8225b00df6f24e99929c98533cf2139c~tplv-k3u1fbpfcp-watermark.image?)


#### （5）**聚合关系**（用带空心菱形的实线表示）



聚合是关联关系的一种特例，他体现的是整体与部分、拥有的关系，即has-a的关系，此时整体与部分之间是可分离的，他们可以具有各自的生命周期，部分可以属于多个整体对象，也可以为多个整体对象共享；比如计算机与CPU、公司与员工的关系等；表现在代码层面，和关联关系是一致的，只能从语义级别来区分；

特点： 部分对象的生命周期并不由整体对象来管理。也就是说，当整体对象已经不存在的时候，部分的对象还是可能继续存在的。比如，教室里包含了桌子，椅子，但是教室不存在，桌子椅子还可以单独存在。

![Aggregation.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1442cdccca3e4acebfd9e9df511a5146~tplv-k3u1fbpfcp-watermark.image?)

#### （6）**组合关系**（用带实心菱形的实线表示）

组合关系同样是关联关系的一种，是比聚合关系还要强的关系。他体现的是一种contains-a的关系，这种关系比聚合更强，也称为强聚合；他同样体现整体与部分间的关系，但此时整体与部分是不可分的，整体的生命周期结束也就意味着部分的生命周期结束；比如你和你的大脑；表现在代码层面，和关联关系是一致的，只能从语义级别来区分；

特点：在组合中，部分与整体生命期一致，部分与组合同时创建并同时消亡 。比如：鸟与翅膀的关系。

![Composition.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4ca3e730b0b74dbbbde6ab68d0311999~tplv-k3u1fbpfcp-watermark.image?)

### 03总结

类图中类与类之间的关系主要由：**继承**、**实现**、**依赖**、**关联**、**聚合**、**组合**这六大类型。表示方式如下图


![aaa.jpg](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e9e09d02756443a296b8a7b1e31d60b9~tplv-k3u1fbpfcp-watermark.image?)