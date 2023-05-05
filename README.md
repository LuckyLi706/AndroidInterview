# 安卓面试
## Java

### 集合

+ ArrayList

  - 基于数组实现的

  - 查询用下标访问的速度比较快，但是插入和删除元素，会有移动元素的开销

  - [扩容机制](https://juejin.cn/post/7092645723623129118)：ArrayList扩容的关键方法grow(): 获取到ArrayList中elementData数组的内存空间长度 扩容至原来的1.5倍

+ LikedList

  - 基于双向链表实现的

  - 插入和删除速度比较快，但是查询元素需要遍历元素

  - 由于他的底层是用双向链表实现的，没有初始化大小，所以没有油扩容机制，就是一直在前面或者是后面新增就好

+ HashMap

  - [深入剖析HashMap的底层原理](https://juejin.cn/post/7188057359754166331)

  - 基于数组+链表（jdk1.7），数组+链表+红黑树（jdk1.8），链表长度大于8时转换为红黑树

  - HashMap采⽤Entry数组来存储key-value对，每⼀个键值对组成了⼀个Entry实体，Entry类实际上是⼀个单向的链表结 构，它具有Next指针，可以连接下⼀个Entry实体。 只是在JDK1.8中，链表⻓度⼤于8的时候，链表会转成红⿊树！

  - HashMap长度最开始默认是 `16`，当HashMap的元素个数大于长度*扩容因子，默认扩容因子loadFactor为0.75，第一次扩容16\*0.75=12，后面遇到扩容都会 `<< 1`，也就是说 长度 永远是 **2的幂次方** 。

  - 计算数组下标：（HashMap长度-1）* key的hashCode值，

  - 链表的目的就是减少hash碰撞。

+ CopyOnWriteArrayList（ArrarList的线程安全）

+ ConcurrentHashMap（HashMap的线程安全）

### 线程

+ synchronized 和 volatile 关键字（区别和原理）

  - volatile只能保证内存可见性，不能保证内存原子性，非阻塞

  - synchronized既可以保证内存可见性，又能保证内存原子性，阻塞

  - 如果写入变量值不依赖变量当前值，那么就可以用 volatile

  - [volatile和synchronized的区别，图文并茂](https://zhuanlan.zhihu.com/p/111229417)

  - synchronized底层原理：Java虚拟机是通过进入和退出Monitor对象来实现代码块同步和方法同步的，代码块同步使用的是`monitorenter`和 `monitorexit` 指令实现的，而方法同步是通过`Access flags`后面的标识来确定该方法是否为同步方法

+ ReentrantLock

+ ThreadLocal（结合Looper考察）

+ 线程同步方案

+ 线程池

### JVM

+ 类加载机制

  - 类加载阶段

    1. 加载

       加载是类加载过程中的一个阶段，这个阶段会在内存中生成一个代表这个类的java.lang.Class对象，作为方法区这个类的各种数据的入口。

    2. 验证 

       确保Class文件的字节流中包含的信息是否符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。

    3.  准备 

       准备阶段是正式为类变量分配内存并设置类变量的初始值阶段，即在方法区中分配这些变量所使用的内存空间。

    4.  解析

       解析阶段是指虚拟机将常量池中的符号引用替换为直接引用的过程。

    5.  初始化

       初始化阶段是执行类构造器<client>方法的过程。到了初始阶段，才开始真正执行类中定义的Java程序代码。

  - 类加载器

    1. 启动类加载器

       负责加载Java_HOME/lib目录中的类库，或通过-Xbootclasspath参数指定路径中被虚拟机认可的类库。

    2. 扩展类加载器

       负责加载 Java_HMOE/lib/ext 目录中的类库，或者通过java.ext.dirs系统变量加载指定路径中的类库。

    3. 应用程序类加载器

       负责加载用户路径(classpath)上的类库。

    4. 自定义加载器

       也可以通过继承java.lang.ClassLoader实现自定义的类加载器

  - 双亲委派模型

    1. 向上委派

       第一步： 将自定义加载器挂载到应用程序类加载器 

       第二步： 应用程序类加载器将请求委托给扩展类加载器 

       第三步： 扩展类加载器将请求委托给启动类加载器

    2. 向下委托

       第一步： 启动类在加载路径下查找并加载Class文件，如果没有找到就交给扩展类加载器加载 

       第二步：扩展类加载器在它的加载路径下查找并加载Class文件，如果还是没有找到，再交给应用程序类加载器加载 

       第三步：应用程序类加载器在加载路径下查找并加载Class文件，如果还是没有找到，就交给自定义加载器进行加载 

       第四步：自定义加载器在用户指定的位置进行查找并加载Class文件，如果还是没有找到，JVM抛出ClassNotFund异常。这时类加载失败，JVM也启动失败。

+ 内存模型

  方法区、内存堆、虚拟机栈（线程私有）、本地方法栈（线程私有）、程序计数器（线程私有）,

+ 垃圾回收机制（算法）

  - GC类型

    1. Minor GC：只回收新生代区域。
    2. Major GC：只回收老年代区域。只有CMS实现了Major GC，所以在老年代里，触发GC，除了CMS和G1之外的其他收集器，大多数触发的其实是 Full GC
    3. Full GC：回收整个堆区和方法区
    4. Mixed GC：回收整个新生代和部分老年代。G1收集器实现了这个类型

  - 内存区域

    - 新生代（Young generation）

      |              |                                                              |
      | ------------ | ------------------------------------------------------------ |
      | 触发的gc类型 | Minor GC                                                     |
      | 空间划分     | Eden Space+From Space+To Space                               |
      | 空间分配     | 8：1：1                                                      |
      | GC收集顺序   | 1. 绝大多数新创建的对象会存放在伊甸园空间（Eden）。          |
      |              | 2. 在伊甸园空间执行第 **1** 次GC（Minor GC）之后，存活的对象被移动到其中一个幸存者空间（Survivor）。 |
      |              | 3. 此后每次 Minor GC，都会将 Eden 和 使用中的Survivor 区域中存活的对象，一次性复制到另一块空闲中的Survivor区，然后直接清理 Eden 和 使用过的那块Survivor 空间。 |
      |              | 4. 从以上空间分配我们知道，Survivor区内存占比很小，当空闲中的Survivor空间不够存放活下来的对象时，这些对象会通过分配担保机制直接进入老年代。 |
      |              | 5. 在以上步骤中重复N次（N = MaxTenuringThreshold（年龄阀值设定，默认15））依然存活的对象，就会被移动到老年代。 |
      | 主要点       | 1. **两个幸存者空间，必须有一个是保持空的**                  |
      |              | 2. **新创建的对象，是保存在伊甸园空间的（Eden）。那些经历多次GC依然存活的对象会经由幸存者空间（Survivor）转存到老年代空间（Old generation）**。 |

    - 老年代（Old generation）

      对象在新生代周期中存活了下来的，会被拷贝到这里。通常情况下这个区域分配的空间要比新生代多。正是由于对象经历的GC次数越多越难回收，加上相对大的空间，发生在老年代的GC次数要比新生代少得多。这个区域触发的垃圾回收称之为：**Major GC** 或者 **Full GC**

  - 算法
    1. 复制算法：（年轻代）
    2. 标记清除算法（老年代）：先标记出要回收的对象，然后统一回收这些对象
    3. 标记整理算法（老年代）：先标记清除，再次扫描并往一端滑动存活对象。

+ 强弱软虚引用的使用场景

  1. 强引用

     只要某个对象有强引用与之关联，JVM则无法回收该对象，即使在内存不足的情况下，JVM宁愿抛出OutOfMemory错误，也不会回收这种对象

  2. 软引用

     软引用常常用来描述一些有用但是非必需的对象。对于软引用关联的对象，会在JVM内存不足时既OutOfMemory之前将这些对象列入回收范围，进行二次回收。如果这时回收还是没有足够的内存才会造成内存溢出异常。软引用一般用于网页的缓存图片的缓存等等比较耗时的操作，但是这些操作目前一般使用LruChche来实现，因此目前代码中很少见到SoftReference。

  3. 弱引用

     弱引用也是用来描述非必要对象的，但是它的轻度比软引用要弱一些，被弱引用关联的对象只能生存到下一次垃圾收集器回收之前，当[JVM](https://so.csdn.net/so/search?q=JVM&spm=1001.2101.3001.7020)进行gc操作时，无论当前内存是否足够，都会回收掉只被弱引用关联的对象。

  4. 虚引用也称之为幽灵引用，或者幻影引用，它是最弱的一种引用关系，一个对象是否有虚引用的存在，完全不会对其生存时间构成影响。也无法通过虚引用来取得一个对象的实例，为一个对象设置为虚引用的唯一目的是希望这个对象被回收器回收时能收到一个系统通知

### 设计模式

+ 单例模式

+ 建造者模式
+ 观察者模式
+ 责任链模式
+ 代理模式

## Kotlin

### run、apply、let、also、with的用法和区别

#### let

```kotlin
// 使用Java
if( mVar != null ){
    mVar.function1();
    mVar.function2();
    mVar.function3();
}

// 使用kotlin（无使用let函数）
mVar?.function1()
mVar?.function2()
mVar?.function3()

// 使用kotlin（使用let函数）
// 方便了统一判空的处理 & 确定了mVar变量的作用域
mVar?.let {
       it.function1()
       it.function2()
       it.function3()
       999    //返回值
}
```

#### also

类似let函数，但区别在于返回值：

```kotlin
// let函数
var result = mVar.let {
               it.function1()
               it.function2()
               it.function3()
               999
}
// 最终结果 = 返回999给变量result

// also函数
var result = mVar.also {
               it.function1()
               it.function2()
               it.function3()
               999
}
// 最终结果 = 返回一个mVar对象给变量result
```

#### with

调用同一个对象的多个方法 / 属性时，可以省去对象名重复，直接调用方法名 / 属性即可

```kotlin
// 此处要调用people的name 和 age属性
// kotlin
val people = People("carson", 25)
with(people) {
println("my name is $name, I am $age years old")
}

// Java
User peole = new People("carson", 25);
String var1 = "my name is " + peole.name + ", I am " + peole.age + " years old";
System.out.println(var1);
```

#### run

结合了let、with两个函数的作用，即：

1. 调用同一个对象的多个方法 / 属性时，可以省去对象名重复，直接调用方法名 / 属性即可
2. 定义一个变量在特定作用域内
3. 统一做判空处理

```kotlin
// 此处要调用people的name 和 age属性，且要判空
// kotlin
val people = People("carson", 25)
people?.run{
    println("my name is $name, I am $age years old")
}

// Java
User peole = new People("carson", 25);
String var1 = "my name is " + peole.name + ", I am " + peole.age + " years old";
System.out.println(var1);
```

#### apply

apply的常见情况是对象配置。

```kotlin
val adam = Person("Adam").apply {
    age = 32
    city = "London"        
}
```

### 协程



## Android

### 四大组件

### Activity

+ 启动模式

  - 标准模式

    默认的启动模式，每次启动，都启动一个new Instance。

  - 栈顶复用模式（singleTop）

    当task顶部有singleTop的Activity时，就复用；若没有就重新创建。

    场景：某些本应只展示一个的场景，比如一下子收到一堆推送消息，不能每个都弹出来吧。但凡是这样的都行。优酷的推荐视频，电商app推送一个活动。

  - 栈内复用模式（singleTask）

    顶上不是target Activity，移除target之上的，把自己变成top。

    场景：只能用在启动页了吧，首页，这种只允许有一个instance，如果有其他就移除掉它顶上的。从这个Activity进入的其他activity都抛弃掉了。用户要再操作一次

  - 新栈模式（singleInstance）

    开辟一个新栈来存放Activity

    场景：适合需要与程序分离开的页面。

+ Activity的渲染

+ 

### Service

+ 两种启动方式

### BroadcastReceiver

### View

#### 自定义View

#### 滑动冲突

### 开源库

#### Okhttp

1. [Okhttp原理](https://blog.csdn.net/JMW1407/article/details/122698278)
2. 拦截器
3. [连接复用](https://www.jianshu.com/p/2b79a65478ab)

#### Refroit

#### Glide

+ [Glide面试](https://blog.csdn.net/songzi1228/article/details/84426165)

#### Jetpack

### Handler

1. Handler工作原理

   在使用Handler之前必须要调用Looper.prepare()这句代码，这句代码的作用是将Looper与当前的线程进行绑定，在实例化Handler的时候，通过Looper.myLooper()获取Looper，然后再获得Looper中的MessageQueue。在子线程中调用Handler的sendMessage方法就是将Message放入MessageQueue中，然后调用Looper.loop()方法来从MessageQueue中取出Message，在取到Message的时候，执行 msg.target.dispatchMessage(msg); 这句代码，这句代码就是从当前的Message中取出Handler然后执行Handler的handleMessage方法。

2. Handler、Message、MessageQueue以及Looper之间的关系

   - Handler：负责发送和处理消息。
   - Message：用来携带需要的数据。
   - MessageQueue：消息队列，队列里面的内容就是Message。
   - Looper：消息轮巡器，负责不停的从MessageQueue中取Message。

3. [Handler面试总结](https://juejin.cn/post/7076314847637405710)

### 优化

+ 启动优化

  1. 启动白屏的问题

     在Activity启动前会展示一个名字叫StartingWindow的window，这个window的背景是取要启动Activity的Theme中配置的WindowBackground，Activity启动后，Activity的window就替换掉这个StartingWindow了。如果没有这个StartingWindow，那么点击后就会一段时间没有反应，给用户误解。这就是启动时展示白屏的原因。解决方法：替换第一个activity（通常是闪屏页）的Theme，把白色背景换成Logot图。

  2. 异步初始化

     异步处理Application的onCreate方法中进行较多的初始化操作

  3. 延迟初始化

     在 Application 和 Activity 中可能存在优先级不高的初始化任务，可以考虑把这些任务进行 **延迟初始化**。延迟初始化并不是减少了主线程耗时，而是让耗时操作让位、让资源给UI绘制，将耗时的操作延迟到UI加载完毕后。

+ 内存优化

  1. 集成LeakCanary检测内存泄漏
  2. 使用memory profile分析内存数据
  3. 避免在onDraw中创建对象，onDraw会被频繁调用，容易造成内存抖动。循环中创建大的对象，也是如此。
  4. 注册广播后，在生命周期结束时反注册
  5. 及时关闭流操作
  6. 避免 static 成员变量引用资源耗费过多实例
  7. 大图片优化
     - [BitmapRegionDecoder加载长图](https://blog.csdn.net/u011077027/article/details/92129708)
     - 尺寸压缩
     - 质量压缩（使用RGB-565代替ARGB-8888可以降低图片占用内存）

+ 绘制优化（布局）

  1. 使用ConstraintLayout,可以实现完全扁平化的布局，减少层级。

  2. RelativeLayout本身尽量不要嵌套使用。

  3. 嵌套的LinearLayout中，尽量不要使用weight，因为weight会重新测量两次。

  4. 推荐使用merge标签，可以减少一个层级。

  5. 使用ViewStub延迟加载。（本身没有大小，不占用空间，直到当调用 `inflate()` 方法或者可见性变为VISIBLE时，才会将指定的布局加载到父布局中。）

+ 网络优化

+ 存储优化

+ 自定义View优化

+ Apk体积优化

+ WebView优化

### 其他

+ 点击桌面图标的过程
  1. 点击桌面App图标，Launcher进程采用Binder IPC向system_server进程发起startActivity请求；
  2. system_server进程接收到请求后，向zygote进程发送创建进程的请求；
  3. Zygote进程fork出新的子进程，即App进程；
  4. App进程，通过Binder IPC向sytem_server进程发起attachApplication请求；
  5. system_server进程在收到请求后，进行一系列准备工作后，再通过binder IPC向App进程发送scheduleLaunchActivity请求；
  6. App进程的binder线程（ApplicationThread）在收到请求后，通过handler向主线程发送LAUNCH_ACTIVITY消息；
  7. 主线程在收到Message后，通过发射机制创建目标Activity，并回调Activity.onCreate()等方法。到此，App便正式启动，开始进入Activity生命周期，执行完onCreate/onStart/onResume方法
+ 

## Flutter

