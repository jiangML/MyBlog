#使用CrashHandler来捕获app的crash信息

###方法步骤如下

####一 、创建CrashHandler类实现Thread.UncaughtExceptionHandler该接口里面的所有方法。其中CrashHandler为单例类型

``` java

    mDefaultCrashHandler = Thread.getDefaultUncaughtExceptionHandler();

    public void init(Context context) {
        mDefaultCrashHandler = Thread.getDefaultUncaughtExceptionHandler();
        Thread.setDefaultUncaughtExceptionHandler(this);
        mContext = context.getApplicationContext();
    }
  
    @Override
    public void uncaughtException(Thread thread, Throwable ex) {
     
       //1 thread 是发生异常的线程
       //2 ex是异常信息
       //3 在这里可以实现把异常信息写入到sd卡 也可以发送到服务器
      
        //如果系统提供了默认的异常处理器，则交给系统去结束我们的程序，否则就由我们自己结束自己
        if (mDefaultCrashHandler != null) {
            mDefaultCrashHandler.uncaughtException(thread, ex);
        } else {
            Process.killProcess(Process.myPid());//退出程序 在此处理相关事情
        }
    }

```



#### 二、 自定义BaseApplication继承Application类
#### 三、 在自定义的Application类中初始化CrashHandler类<br>

```java

	  public class BaseApplication extends Application {
	    @Override
	    public void onCreate() {
	        super.onCreate();
	        CrashHandler crashHandler = CrashHandler.getInstance();
	        crashHandler.init(this);
	    } 
}

```
