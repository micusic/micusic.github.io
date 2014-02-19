---
layout: post
title:  "Proxy In Java"
date:   2014-02-17 20:51:12
categories: tekni:k
---

#Proxy是什么
Proxy是一种设计模式, 定义为: 对其他对象提供一种代理以控制对这个对象的访问. 在某些情况下,一个对象不想或者不能直接引用另一个对象,而代理对象可以在客户端和目标对象之间起到中介的作用。代理模式的思想是为了提供额外的处理或者不同的操作而在实际对象与调用者之间插入一个代理对象。这些额外的操作通常需要与实际对象进行通信。
  		
###举个栗子
Clone 这个 repo 并 checkout 到 "856978d origin proxy." .

	git@github.com:micusic/ProxySampleJava.git
	
共4个文件:

1. Test.java: 包含main函数的客户代码;
2. Robot.java: Robot的接口, 规定了robot应有的接口;
3. Dora.java: 名叫Dora的机器人, 实现了robot接口;
4. DoraProxy.java: Dora的代理类;

他们的角色是: 

- 抽象角色：Robot, 声明真实对象和代理对象的共同接口;
- 真实角色：Dora, 代理角色所代表的真实对象,是我们最终要引用的对象;
- 代理角色：DoraProxy, 代理对象角色内部含有对真实对象的引用,从而可以操作真实对象; 同时代理对象提供与真实对象相同的接口, 以便在任何时刻都能代替真实对象;
- 客户代码: main函数, 直接调用代理角色的该方法;

分析一下, Dora 本身就实现了 Robot 接口, 可以执行 say( ) 的方法, 为什么需要一个代理呢?

- 从设计上不想让客户代码直接访问Dora类的时候;
- 客户代码不能够直接访问Dora类的时候;
- 在Dora的say( )的前后, 可以进行额外处理, 相当于对真实对象进行封装;

以上也就是代理的好处和作用, 那么不足和改进点呢?

- DoraProxy必须确切知道Dora, 知道代理谁, 并将其作为filed;
- Dora必须已经是真实存在的, 才能开发DoraProxy;
- 一个Dora对应一个DoraProxy, 代码膨胀;

为了解决以上问题, 可以使用动态代理.

#Dynamic Proxy
Checkout到 "c6f0355  dynamic proxy." .

3 处修改:

1. 删除原有代理DoraProxy.java;
2. 新增Handler.java, 实现了InvocationHandler接口的invoke方法, 就是最终Proxy调用的固定接口方法; 该函数第一个参数obj一般是指代理类,method是被代理的方法,say(),args为该方法的参数数组;
3. 修改客户代码main函数, 通过Proxy.newProxyInstance方法获取Proxy实例, 并强转为Robot, 再调用say( )方法;

他们的角色是:

- 抽象角色: Robot, 不变;
- 真实角色: Dora, 不变
- 代理角色: 替换为Handler, Proxy不管客户端对其的调用是怎么实现的,当客户端调用Proxy时,会调用Handler的invoke方法,真正实现功能的代码就必须在invoke方法中去调用;这个抽象方法在Proxy中动态实现;
- 客户代码: 获取代理实例并强转后, 调用代理的该方法;

总结一下:

动态代理其实就是java.lang.reflect.Proxy类动态地根据所指定的接口生成一个class,该class会继承Proxy类,并(宣称)实现所有指定的接口（在参数中传入的接口数组）; 然后再利用指定的classloader将该class加载进系统,最后生成这样一个类的对象,并初始化该对象的一些值,如invocationHandler,以及所有的接口对应的Method成员. 初始化之后将对象返回给调用的客户端。这样客户端拿到的就是一个实现你所有的接口的Proxy对象, 可以把它当作这些interface中的任何一个来用。实质上,这个Dynamic Proxy其实就是一个Proxy,它不会替你作实质性的工作,在生成它的实例时提供的handler会接管实际的工作. 

通过这种方式,被代理的对象(Dora)可以在运行时动态改变,需要控制的接口(Robot)可以在运行时改变,控制的方式(Handler)也可以动态改变,从而实现了非常灵活的动态代理关系。

---

_最后再看看大神的视野..._

_Checkout 到 "9c8a9b4 add outputs."._

_在main函数下面加了一些对proxy打印, 输出结果:_

	com.sun.proxy.$Proxy0 extends java.lang.reflect.Proxy implements com.TW.proxysample.Robot
	{
    	java.lang.reflect.Method m3;
    	java.lang.reflect.Method m1;
    	java.lang.reflect.Method m0;
    	java.lang.reflect.Method m2;

    	boolean equals(java.lang.Object);
    	java.lang.String toString();
    	int hashCode();
    	void say();
	}

" _很明显,Proxy.newProxyInstance方法会做如下几件事：_

_1,根据传入的第二个参数interfaces动态生成一个类,实现interfaces中的接口,该例中即BusinessProcessor接口的processBusiness方法。并且继承了Proxy类,重写了hashcode,toString,equals等三个方法。具体实现可参看 ProxyGenerator.generateProxyClass(...); 该例中生成了$Proxy0类_

_2,通过传入的第一个参数classloder将刚生成的类加载到jvm中。即将$Proxy0类load_

_3,利用第三个参数,调用$Proxy0的$Proxy0(InvocationHandler)构造函数 创建$Proxy0的对象,并且用interfaces参数遍历其所有接口的方法,并生成Method对象初始化对象的几个Method成员变量_

_4,将$Proxy0的实例返回给客户端。_

_现在好了。我们再看客户端怎么调就清楚了。_

_1,客户端拿到的是$Proxy0的实例对象,由于$Proxy0继承了BusinessProcessor,因此转化为BusinessProcessor没任何问题。_

_BusinessProcessor bp = (BusinessProcessor)Proxy.newProxyInstance(....);_

_2,bp.processBusiness()；_

_实际上调用的是$Proxy0.processBusiness();那么$Proxy0.processBusiness()的实现就是通过InvocationHandler去调用invoke方法啦!_ "