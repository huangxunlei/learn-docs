

<img src="https://upload-images.jianshu.io/upload_images/2917250-76abb111b83fbb4d.png" width="500" height="200">

## 1、EventBus 简介

### 1.1 三个角色

- **定义事件Event**

  它可以是任意类型，EventBus会根据事件类型进行全局的通知。

- **准备事件订阅者 Subscriber**

  事件订阅者，在EventBus 3.0之前我们必须定义以onEvent开头的那几个方法，分别是`onEvent`、`onEventMainThread`、`onEventBackgroundThread`和`onEventAsync`，而在3.0之后事件处理的方法名可以随意取，不过需要加上注解`@subscribe`，并且指定线程模型，默认是`POSTING`。

- **订阅者同时需要在总线上注册和注销自己**

- **发送事件 Publisher**

  事件的发布者，可以在任意线程里发布事件。一般情况下，使用`EventBus.getDefault()`就可以得到一个EventBus对象，然后再调用`post(Object)`方法即可。
  
### 1.2 四种线程模型

  - **POSTING**：默认，表示事件处理函数的线程跟发布事件的线程在同一个线程。
  
  - **MAIN**：表示事件处理函数的线程在主线程(UI)线程，因此在这里不能进行耗时操作。
  
  - **BACKGROUND**：表示事件处理函数的线程在后台线程，因此不能进行UI操作。如果发布事件的线程是主线程(UI线程)，那么事件处理函数将会开启一个后台线程，如果果发布事件的线程是在后台线程，那么事件处理函数就使用该线程。
  
  - **ASYNC**：表示无论事件发布的线程是哪一个，事件处理函数始终会新建一个子线程运行，同样不能进行UI操作。

  

## 2、EventBus 使用

### 2.1 引入依赖

```groovy
implementation 'org.greenrobot:eventbus:3.1.1'
```

### 2.2 自定义事件类

```kotlin
open class MessageEvent(message: String)
```

### 2.3 发布事件

#### 2.3.1 注册事件

```kotlin
override fun onStart() {
   super.onStart()
   EventBus.getDefault().register(this)
 }
```

#### 2.3.2 解除注册

```kotlin
override fun onStop() {
    super.onStop()
    EventBus.getDefault().unregister(this)
}
```
#### 2.3.3 发送事件

```kotlin
EventBus.getDefault().post(MessageEvent("通知了"))
```
#### 2.3.4 处理事件

```kotlin
@Subscribe(threadMode = ThreadMode.MAIN)
fun messageEvent(msg: String) {

}
```

### 2.4 黏性事件

所谓的黏性事件，就是指发送了该事件之后再订阅者依然能够接收到的事件。使用黏性事件的时候有两个地方需要做些修改。一个是订阅事件的地方，这里我们在先打开的Activity中注册监听黏性事件：

```kotlin
@Subscribe(threadMode = ThreadMode.MAIN, sticky = true)
fun onGetStickyEvent(message: MessageEvent) {
    
}
```

另一个是发布事件的地方，这里我们在新的开的Activity中发布黏性事件。即调用EventBus的`postSticky`方法来发布事件：

```kotlin
EventBus.getDefault().postSticky(MessageEvent("粘性事件的通知"))
```

**使用场景**
 我们要把一个Event发送到一个还没有初始化的Activity/Fragment，即尚未订阅事件。那么如果只是简单的post一个事件，那么是无法收到的，这时候，你需要用到粘性事件,它可以帮你解决这类问题.