##### JVM如何加载.class文件

通过classLoader将.class二进制数据流加载到内存中（可以来源于硬盘或者网络）

形成运行时数据区的数据结构

依托Execution Engine对命令进行解析

##### 类的加载方式

1. 使用new 进行隐式的加载 当我们new一个对象的时候会自动将对象对应的class文件加载到JVM中
2. 使用Class.forName(),loader.loadClass()的方式显示的加载，这种加载需要使用newInstance方法进行对象的实例化

##### LoadClass和forName的区别

1. loadClass得到的class还没有进行链接，所以不会执行类中的静态代码块，Spring中使用延迟加载基于这种方式，因为不用执行类中的静态代码块，速度会提高
2. forName的方式得到的class已经初始化了，会执行类中的静态代码块，jdbc驱动使用这种方式注册驱动

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/classLoader.png)



![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/pc.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/stack1.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/local.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/stackFrame.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/xms.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/stack%26frame.png)

 ![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/intern.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/reachable.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/tiaoyou.png) 

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/gull%20gxc.png)



![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/gc-1.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/gc-2.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/gc-3.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/gc-4.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/gc-5.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/gc-6.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/ref/strongref.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/ref/softref.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/ref/weakref.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/ref/phantomref.png)

![](https://keyon-edu.oss-cn-beijing.aliyuncs.com/jvm/ref/refconclude.png)