#Lab4死锁
##1.死锁停在第几次的截图
###(1)
![](http://i.imgur.com/E3nt7G0.png)
###(2)
![](http://i.imgur.com/vSK5wZS.png)
###如上图，第一次死锁是在第9次，第二次死锁是在第72次
##2. 产生死锁的4个必要条件
###2.1.互斥条件：一个资源每次只能被一个进程使用
classA里的methodA、last方法和classB里的methodB、last都有synchronized关键词修饰。
所以在同一时刻最多只有一个线程执行methodA或methodB或last；

###2.2.请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放
当一个线程访问object的一个synchronized同步代码块或同步方法时，其他线程对object中所有其它synchronized同步代码块或同步方法的访问将被阻塞。

###2.3.不剥夺条件:进程已获得的资源，在末使用完之前，不能强行剥夺
题目所给的代码并没有给出抢占的机制，自然不能强行剥夺。

###2.4.循环等待条件:若干进程之间形成一种头尾相接的循环等待资源关系
这次死锁出现的条件之一就是b.methodB(a)和a.methodA(b)这两个语句碰巧同时执行，这样b在等a，a在等b，循环等待。

##3.对上述程序产生死锁的解释
在给定的程序中，线程t开始运行时会触发运行run()中的语句，即b.methodB(a)，然后在等待count减到0之后运行a.methodA(b)，这两个语句显然是互斥的,当b.methodB(a)和a.methodA(b)这两个语句碰巧同时执行，这样b在等a，a在等b，循环等待，就形成了死锁。