# Handler 工作的机制原理

子线程创建Handler 会报错，必须在线创建Looper



Handler 负责发送消息

Loooper 负责接收消息，并且将消息发送给Handler

<img src="../images/Handler机制.bmp " width="300" hegiht="320" align=center />



### 在主线程中发送消息到子线程中

- 在子线程创建Handler

  ```java
  class MyThread extends Thread{
   @Override
    public  void run (){
     Looper.prepare();
     handler=new  Handler(){
       public void handlerMessage(Message msg){
       
       }
    };
     Looper.loop();
     super.run();
    }
  }
  ```

  #### 子线程中发送消息到主线程
  
  
