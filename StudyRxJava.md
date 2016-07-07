#学习RxJava
#####现在android开发流行使用RxJava+Retrofit+mvp模式开始，所以我也在不断的学习RxJava中。在使用过程中，觉得RxJava的确很不错。解决了回调嵌套问题。特别是提供了很多操作符的使用。其实要想学习好RxJava主要就是先了解其实现思想，主要就是利用观察者来实现。然后就是要学习好RxJava的一系列操作符。

##一 利用RxJava实现EventBus 事件总线 RxBus
··· java

    public class RxBus {

	    private static volatile  RxBus defaultInstance;
	    private final  Subject bus;
	    private RxBus()
	    {
	       bus=new SerializedSubject<>(PublishSubject.create());
	    }
	
	    /**
	     *获取单例的RxBus
	     * @return
	     */
	    public  static  RxBus getDefaultInstance()
	    {
	        if(defaultInstance==null)
	        {
	                synchronized (RxBus.class)
	                {
	                    if (defaultInstance==null)
	                    {
	                        defaultInstance=new RxBus();
	                    }
	                }
	        }
	        return defaultInstance;
	    }
	
	    /**
	     * 发送事件
	     * @param o
	     */
	    public void post(Object o)
	    {
	        bus.onNext(o);
	    }
	
	    /**
	     * 获取事件
	     * @param eventType
	     * @param <T>
	     * @return
	     */
	    public  <T> Observable<T> toObservable(Class<T> eventType)
	    {
	        return bus.ofType(eventType);
	    }

    }
```

使用如下

··· java

     RxBus.getDefaultInstance().post("你好！");

     subscription=RxBus.getDefaultInstance().toObservable(String.class)
           .observeOn(AndroidSchedulers.mainThread())//指定在mian线程执行
           .subscribe(new Action1<String>() {
               @Override
               public void call(String s) {
                   showToast(s);
                   ULog.i(TAG, s);
               }
            }, new Action1<Throwable>() {
               @Override
               public void call(Throwable throwable) {
                   showToast(throwable.getMessage());
                   ULog.i(TAG, throwable.getMessage());
               }
           });


```

其中我在调用toObservable()方法后还调用了observOn()方法，指定在main线程运行，这个地方为什么要调用呢？因为RxBus的post()方法不确定是在哪个线程调用，当post()方法在main线程调用的时候toObservable()方法也在main线程执行。如果post()方法在子线程调用的时候toObservable()方法也就在子线执行。所以简单来说就是post()方法在什么线程调用，那么toObservable()方法也就在什么线程调用。在我的代码中因为我在toObservable()的subscribe()的Action的call()方法中有对ui的操作，所以我在这里指定了在main线程执行。