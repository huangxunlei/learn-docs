- JakeWharton
- findView / setListener



APT 编译时生成代码



### 注解

- 普通注解

  - @Override
  - @Deprecated

- 元注解

  @Document

  @Target

  @Retention 注解的生命周期

  @Inherited  可继承的

- 自定义注解

反射

- 判断任意一个对象所属的类
- 构造任意一个类的对象
- 判断任意一个类所具有的成员变量和方法（通过反射甚至可以调用private方法）
- 调用任意一个对象的方法

java注解编译器



butterKnife 的工作原理

1、编译的时候扫描注解，并最相应的处理，生成java代码，生成java代码是调用