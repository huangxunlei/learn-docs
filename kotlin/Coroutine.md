协程

- 异步进行耗时操作

- GlobalScope 

  Global Scope 通常用于启动顶级协程，这个协程在整个应用程序中进行，不会被过早地取消。

  GlobalScope 创建的协程没有父协程，Global Scope 通常也不与任何生命周期组件绑定

- MainScope

- ViewModelScope

