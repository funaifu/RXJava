# 深入理解RxJava

### RxJava简单使用

![RxJava简单使用](https://github.com/funaifu/RXJava/blob/master/image/RXJava%E7%AE%80%E5%8D%95%E4%BD%BF%E7%94%A8.jpg)

**********************************************

### RxJava模型

![RXJava流程](https://github.com/funaifu/RXJava/blob/master/image/RXJava%E6%B5%81%E7%A8%8B.jpg)

### RxJava源码分析

以图（1）RXJava简单的使用为例，我们一步一步慢慢的去分析RxJava的调用,以及RxJava是如何实现线程调度的<br>
<br>
1， Observable.create(new Observable.OnSubscribe<Object>()）返回一个Observable对象我们称之为observable_1<br>
    这样方便我们理解。observable_1持有一个Observable.OnSubscribe对象的引用我们称之为onSubscribe_1。create方法如下图：<br>
    
   ![](https://github.com/funaifu/RXJava/blob/master/image/Observable_create.jpg)
     
2， observable_1调用observeOn（AndroidSchedulers.mainThread()）方法，传入一个Scheduler对象，我们去查看一下observeOn方法源码如下图：
  
  ![](https://github.com/funaifu/RXJava/blob/master/image/Obeservable_observeOn.jpg)
  
  发现最终会调用lift()方法，并且传入一个新建的new OperatorObserveOn<T>(scheduler)对象，lift方法如下图：
  
   ![](https://github.com/funaifu/RXJava/blob/master/image/Observable_lift.jpg)
   
   lift方法最终会返回一个新创建的Observable对象，我们称之为observable_2,其持有一个OnSubscribe对象的引用我们称之为onSubscribe_2;
   
   
 3，observable_2调用subscribeOn(Schedulers.io())方法，传入一个Scheduler对象，subscribeOn方法源码如下图：
 
 
   ![](https://github.com/funaifu/RXJava/blob/master/image/Obeservable_subscribeOn.jpg)
   
   从源码中我们发现最终会调用nest().lift(new OperatorSubscribeOn<T>(scheduler))，那么我们先了解一下nest()方法的源码，如下图：
    
    
     ![](https://github.com/funaifu/RXJava/blob/master/image/nest.jpg)
     
      ![](https://github.com/funaifu/RXJava/blob/master/image/just.jpg)
      
   从上面两个图中，我们可以看到nest()方法最终会调用just方法，并且返回一个通过调用ScalarSynchronousObservable.create(value)方法创建的<br>
   一个ScalarSynchronousObservable对象，该对象持有observable_2对象的引用。nest()方法执行完成后返回一个ScalarSynchronousObservable<br>
   对象，然后由ScalarSynchronousObservable对象调用其lift(new OperatorSubscribeOn<T>(scheduler))方法，lift()方法最终会执行<br>
    new Observable<R>(new OnSubscribe<R>()）新建一个Observable对象我们称之为observable_3,其持有的OnSubscribe新建对象我们称之为<br>
    onSubscribe_3。
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
 
  

