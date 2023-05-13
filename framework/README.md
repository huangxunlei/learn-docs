Android Framework的学习记录



## 1、镜像源

镜像源：https://mirrors.tuna.tsinghua.edu.cn/

AOSP和Ubuntu的镜像源都可以使用

使用镜像源可以大大缩短我们下载资源文件的时间

## 2、Ubuntu的一些常规配置



### 2.1、Ubuntu安装搜狗输入法 

直接对应官网教程即可 [地址](https://shurufa.sogou.com/linux/guide)

### 2.2 Ubuntu制作快捷启动图标的方法

如果不设置快捷启动图标，每次打开都要进入..../android-studio/bin/下，特别麻烦

ubuntu 的所有的快捷启动图标都在/usr/share/applications/内

在终端输入以下命令：

```shell
sudo gedit /usr/share/applications/AndroidStudio.desktop  
```

  ```shell
      [Desktop Entry]  
      Name=Android Studio  
      Comment=android studio  
      Exec=/home/lcw/android_develop_tools/android-studio/bin/studio.sh  
      Icon=/home/lcw/android_develop_tools/android-studio/bin/studio.png  
      Terminal=false  
      Type=Application  
      Categories=Application  
  ```

保存、关闭后，会在/usr/share/applications中看到Android Studio的快捷方式，复制到桌面双击图标就可使用了

(需要注意的是，每一行必须紧靠左侧且每行最后面不能有空格，否则会失败)

创建完将其复制到桌面即可

对上面的命令中的几条稍作解释：

Exec代表应用程序的位置【视实际情况修改】

Icon代表应用程序图标的位置【视实际情况修改】

Terminal的值为false表示启动时不启动命令行窗口，值为true表示启动命令行窗口【建议为false】

Categories这里的内容决定创建出的起动器在应用程序菜单中的位置，按照上面的写法创建的起动器将出现在应用程序－Internet中，以此类推，如果想在应用程序－办公中创建起动器，上述最后一行应该写成：Categories=Application;Office;