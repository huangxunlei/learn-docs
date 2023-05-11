### 1、电脑传文件到手机
adb push test.txt /sdcard/test
把当前目录下的test.tex文件传到手机 /sdcard/test目录中
### 2、手机文件传到电脑
adb pull /sdcard/test/test.txt D:/test
把/sdcard/test/目录下的test.txt文件传到 D盘的test目录中

### 3、无权限操作问题

当我们需要更改系统应用时,编译完系统的应用,会adb push 新生成的apk到/system/app下有时会提示文件目录是read-only,我们会adb remount 将system 分区重新挂载,但是有时会提示:remount failed: Operation not permitted

此问题解决的方法如下：

#### a.修改文件的读写权限

1. 确保电脑连接上设备了，命令如下：

   ```shell
   # adb devices
   # adb shell
   
   ```


2.进入shell后，是当前的user用户，需进入超级用户(有些设备中su命令被禁止，只能将手机root后才行)，

   命令如下

   ```shell
   su
   #设置system分区的挂载，命令如下
   mount -o rw,remount -t yaffs2 /dev/block/mtdblock3 /system
   #修改system分区的权限未可读可写，命令如下
   chmod 777 /system 
   ```

 退出，这时就有系统目录的读写权限了，可以执行adb remount或使用adb push 把文件push到系统目录中，或者删除系统文件

### b.root设备

1.在电脑终端，将设备root，但是有些设备是无法进行的：

```shell
adb root
```

2.进行remount，重新挂载分区：

```shell
adb remount
```



