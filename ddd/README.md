## 领域驱动设计(DDD)概念

#### DDD 是什么呢？

DDD 即Domain Driven Design, 翻译成中文的话就是领域驱动设计，首先我们应该理解这里的领域是什么意思？假设公司内部正在开发一套电商平台，而电商平台中包含了库存，订单，商品等核心业务。这些核心业务逻辑其实呈现的就是电商平台领域。通俗的理解就是一套体系的业务知识即代表了一个域。好比在线教育平台，它需要有一套体系的业务，包括招生，线上教学，课程等内容。我们将这些业务抽象出领域模型，而这些领域模型表达了产品经理所阐释的业务需求，我们反复地用这些领域模型与产品经理进程讨论沟通最终确定初步的领域模型指导代码设计开发。

### Bounded Context 目录结构

- application

  - commandservice
  - queryservice

  包含查询服务和命令服务这两大类的**应用服务**

- domain （领域对象）

  - model
    - aggregate
    - entity
    - valueobject
  - event
  - repository
  - command
  - query

  包含界限上下文中所有的领域模型对象和领域事件对象

- infrastructure

  - repository
    - mapper
    - factory
  - messaging

  包含资源库具体实现类、消息通讯工具等**基础设施**类组成

- interfaces

  - rest
    - assembler
    - dto

- integration

  - acl
  - event

```kotlin


```





```
|——— project-service  #项目服务
|   |—— build #编译后的文件目录
|       |——classes #java经过javac编译后的class文件
|       |——libs #bootjar后的jar包文件
|   |——
|——   src #源码文件目录
|—— 
```



|      |      |      |
| ---- | ---- | ---- |
|      |      |      |
|      |      |      |
|      |      |      |



