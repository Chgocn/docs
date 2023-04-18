> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/vmoEiUw1UpIPfViwEcpxhQ)

_1_

序言

Hello，我是小木箱，欢迎来到小木箱成长营业务架构系列教程，今天分享的内容是业务架构 · 基础篇 · Jetpack 四件套。  

2017 年，Google 发布了 Android Architecture Components，包括 Room、LiveData、ViewModel 和 Paging 等组件，旨在帮助开发者更轻松地实现 MVVM 架构。

2018 年，Google 在 I/O 大会上推出的一套 Android 开发组件库，旨在帮助开发者更轻松、更高效地构建 Android 应用。

随着时间的推移，Android Jetpack 不断地更新和增加新的组件，使得 Android 应用的开发更加高效、稳定和可维护。

今天的主题主要分为三个维度。第一个维度是 4W2H 分析 Jetpack，第二个维度是 Jetpack 四件套。第三个维度是总结与展望。

其中，4W2H 分析 Jetpack 主要针对 Jetpack 提出了 6 个高价值问题。

其中，Jetpack 四件套列举了 LifeCycle、LiveData、ViewModel 和 DataBing 四种常见的 Jetpack 工具包。

![](https://mmbiz.qpic.cn/mmbiz_png/dlQfNRHyZWRzU86EBX5xOuvYHsTr39C1KVAZecOLqic0Xnr6pf5VZoa7K0WH5IpbavDRTYh00UjiaU98HUdLibO4g/640?wx_fmt=png)

如果学完业务架构系列教程，那么任何人都能完整构建一套适合企业业务背景的架构设计。

_2_

4W2H 分析 Jetpack

**2.1 What: Jetpack 是什么?**
--------------------------

![](https://mmbiz.qpic.cn/mmbiz_png/dlQfNRHyZWRzU86EBX5xOuvYHsTr39C1qpLgBIFNNUqHvc15OianoVEct6uDTTrmApTApcHiaubVQZpuZFrc9yeg/640?wx_fmt=png)

Android Jetpack 是一组 Android 软件组件、工具和指南，它们可以帮助开发者构建高质量、稳定的 Android 应用程序。Jetpack 中包含多个库，它们旨在解决 Android 应用程序开发中的常见问题，并提供一致的 API 和开发体验。

Jetpack 中包含的库包括：

1. **ViewModel**：帮助管理 UI 组件的生命周期并存储和管理 UI 相关的数据。

  

2. **LiveDat****a**：提供了响应式编程的功能，可以让数据在数据源发生变化时自动更新 UI。

  

3. **Room**：提供了一个抽象层，可以让开发者方便地访问和管理 SQLite 数据库。

  

4.**Navigation**：提供了一种简单、一致的方式来处理应用程序的导航。

  

5. **WorkManager**：提供了一种简单、可靠的方式来管理后台任务。除此之外，Jetpack 还包括了诸如 Paging、Data Binding、Preferences、Security 等库，这些库都旨在简化开发过程并提高应用程序的性能和可靠性。

**2.2 Where: 什么场景下使用 Jetpack?**
-------------------------------

Jetpack 适用于开发各种类型的 Android 应用程序，包括单页面应用程序、多页面应用程序、后台任务应用程序等。下面是一些适合使用 Jetpack 的场景：

1. **构建大型应用程序**：Jetpack 提供了一些库，如 ViewModel、LiveData 和 Navigation，可以帮助开发者更好地管理应用程序的生命周期、状态和导航，使得构建大型应用程序更加容易。

2. **处理后台任****务**：Jetpack 中的 WorkManager 库提供了一种简单、可靠的方式来管理后台任务，如数据同步、推送通知、文件上传等。

3. **数据库访问**：Jetpack 中的 Room 库提供了一个抽象层，可以让开发者方便地访问和管理 SQLite 数据库，使得数据存储和访问更加容易。

4. **响应式编程**：Jetpack 中的 LiveData 和 Data Binding 库提供了响应式编程的功能，可以让数据在数据源发生变化时自动更新 UI，提高应用程序的性能和可靠性。

5. **代码重用**：Jetpack 中的各种库都旨在解决 Android 应用程序开发中的常见问题，并提供一致的 API 和开发体验，使得代码重用更加容易。

**2.3 Why: 为什么使用 Jetpack?**
---------------------------

以下是使用 Jetpack 的一些好处：

1. **更好的代码组织和可维护性**：Jetpack 提供了一组库，这些库旨在解决 Android 应用程序开发中的常见问题，如生命周期管理、数据存储、后台任务处理等。使用 Jetpack 可以使代码更加模块化，易于维护。

  

2. **更好的代码可读性**：Jetpack 提供了一致的 API 和开发体验，可以使代码更加易于理解和阅读。

  

3. **更好的开发效率**：Jetpack 提供了一些库和工具，如 Navigation 和 Data Binding，可以帮助开发者更快地开发 Android 应用程序，提高开发效率。

  

4. **更好的性能和可靠性**：Jetpack 中的一些库，如 LiveData 和 WorkManager，提供了响应式编程和后台任务处理的功能，可以提高应用程序的性能和可靠性。

  

5. **更好的向后兼容性**：Jetpack 中的一些库，如 ViewModel 和 LiveData，提供了对 Android 不同版本的向后兼容性支持，可以使开发者更容易地编写适用于不同版本的 Android 系统的应用程序。综上所述，Jetpack 可以帮助开发者更好地组织代码、提高开发效率、提高应用程序的性能和可靠性，并提供了对不同版本的 Android 系统的向后兼容性支持。

  

---

**2.4 Who: 什么样的团队该使用 Jetpack?**
-------------------------------

Jetpack 适用于各种规模的 Android 开发团队，特别是那些希望提高应用程序质量、开发效率和可维护性的团队。以下是一些团队适合使用 Jetpack 的场景：

1. **大型团队**：Jetpack 提供了一致的 API 和开发体验，可以使大型团队更容易协作开发，提高团队的开发效率和代码质量。

  

2. **跨职能团队**：Jetpack 提供了一些库和工具，如 Navigation 和 Data Binding，可以帮助开发者更快地开发 Android 应用程序，使得跨职能团队之间更容易协作。

  

3. **需要提高应用程序性能和可靠性的团队**：Jetpack 中的一些库，如 LiveData 和 WorkManager，提供了响应式编程和后台任务处理的功能，可以提高应用程序的性能和可靠性。

  

4. **需要提高代码可维护性的团队**：Jetpack 提供了一些库，如 ViewModel 和 Room，可以帮助开发者更好地管理应用程序的状态和数据，使得代码更易于维护。

  

5. **需要保持向后兼容性的团队**：Jetpack 中的一些库，如 ViewModel 和 LiveData，提供了对 Android 不同版本的向后兼容性支持，可以使开发者更容易地编写适用于不同版本的 Android 系统的应用程序。   综上所述，Jetpack 适合各种规模和类型的 Android 开发团队，特别是那些希望提高应用程序质量、开发效率和可维护性的团队。

  

**2.5 How: 怎样使用 Jetpack?**
--------------------------

以下是使用 Jetpack 的一般步骤：

**添加 Jetpack 库**：Jetpack 库可以通过在 build.gradle 文件中添加依赖项的方式进行添加。例如，添加 Lifecycle 库的依赖项：

```
  dependencies {
      def lifecycle_version = "2.3.1"// ViewModel
      implementation "androidx.lifecycle:lifecycle-viewmodel:$lifecycle_version"// LiveData
      implementation "androidx.lifecycle:lifecycle-livedata:$lifecycle_version"// Lifecycle
      implementation "androidx.lifecycle:lifecycle-runtime:$lifecycle_version"
  }


```

**使用 Jetpack 库**：在添加 Jetpack 库后，就可以在应用程序中使用 Jetpack 库提供的功能了。例如，使用 ViewModel 库创建一个 ViewModel 类：

```
   import androidx.lifecycle.ViewModel

   class MyViewModel : ViewModel() {
       // Add ViewModel logic here
   }


```

**结合 Jetpack 组件使用**：Jetpack 库提供的组件可以结合使用，以提高应用程序的开发效率和可维护性。例如，使用 ViewModel 库和 LiveData 库实现一个响应式的用户界面：

```
    class MyActivity : AppCompatActivity() {

        private lateinit var viewModel: MyViewModel

        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_my)

            // Get a reference to the ViewModel
            viewModel = ViewModelProvider(this).get(MyViewModel::class.java)

            // Observe a LiveData object in the ViewModel
            viewModel.someData.observe(this, Observer {
                // Update the UI with the new data
            })
        }
    }


```

以上是使用 Jetpack 的一般步骤。需要根据具体的 Jetpack 库和应用程序需求进行相应的配置和代码实现。

**2.6 How Much: 使用 Jetpack 业务价值**
---------------------------------

使用 Jetpack 可以带来以下业务价值：

1. **提****高开发效率**：Jetpack 提供了一些开发工具和库，例如 ViewModel、LiveData 和 Room，可以减少重复的编写代码，简化开发流程，并提高开发效率。

  

2. **提高应用程序质量**：Jetpack 提供了一致的 API 和开发体验，可以减少由于人为因素引起的代码错误，提高应用程序的质量。

  

3. **提高应用程序性能**：Jetpack 中的一些库，例如 Lifecycle 和 WorkManager，提供了响应式编程和后台任务处理的功能，可以提高应用程序的性能和响应速度。

  

4. **简化应用程序架构**：Jetpack 提供了一些组件和库，例如 ViewModel 和 Data Binding，可以帮助开发者更好地管理应用程序的状态和数据，并简化应用程序的架构。

  

5. **支持向后兼容性**：Jetpack 中的一些库，例如 ViewModel 和 LiveData，提供了对 Android 不同版本的向后兼容性支持，可以使开发者更容易地编写适用于不同版本的 Android 系统的应用程序。

综上所述，使用 Jetpack 可以带来多种业务价值，可以提高应用程序的质量、性能和开发效率，同时简化应用程序架构和支持向后兼容性，可以使应用程序更易于维护和升级。

_3_

Jetpack 四件套

**3.1 LifeCycle**
-----------------

![](https://mmbiz.qpic.cn/mmbiz_png/dlQfNRHyZWRzU86EBX5xOuvYHsTr39C14mMEV66a3hny6oLrZYZFxuEf8TgFjLsvdnjwYuCnlkTCAT6QwicJk6w/640?wx_fmt=png)

### 3.1.1 LifeCycle 基础定义

Android Jetpack Lifecycle 是 Android Jetpack 组件库中的一部分，Lifecycle 是基于 Android Framework 中的 Lifecycle 概念而构建的。

Lifecycle 提供了一种轻松管理组件（如 Activity 和 Fragment）生命周期的方式，同时也支持自定义组件的生命周期。

Jetpack Lifecycle 提供了一组类和接口，使得开发者可以在组件的生命周期各个阶段执行相应的操作。

这些类和接口包括：

*   **LifecycleOwner**
    
    : 拥有生命周期的对象，通常是 Activity 和 Fragment。
    
*   **LifecycleObserver**
    
    : 监听组件的生命周期事件的观察者对象。
    
*   **Lifecycle**
    
    : 组件的生命周期，包括 CREATED、STARTED、RESUMED、PAUSED、STOPPED、DESTROYED 等状态。
    
*   **LiveData**
    
    : 一个可观察的数据容器，可以在组件生命周期的不同阶段更新数据。
    

使用 Jetpack Lifecycle，可以更容易地避免内存泄漏和其他生命周期相关的问题。

例如，可以在组件被销毁时自动释放资源、取消网络请求等操作。

此外，Jetpack Lifecycle 还提供了一种方式来创建自定义的生命周期状态，以更好地满足 App 的需求。

总之，Jetpack Lifecycle 是 Android Jetpack 组件库中的一个重要组件，可以帮助开发者更轻松地管理组件的生命周期，从而提高 App 的质量和性能。

3.1.2 LifeCycle 基础使用

在 App 的主 Activity 中实现一个简单的计时器，当 Activity 处于前台时，计时器会不断递增，当 Activity 被销毁时，计时器将停止。

具体实现步骤如下：

1. 在 gradle 文件中添加 Jetpack 组件库的依赖。

```
dependencies {
    implementation "androidx.lifecycle:lifecycle-extensions:2.4.0"
}


```

  

2. 创建一个名为 Timer 的 Java 类，并实现 LifeCycleObserver 接口。

  

```
public class Timer implements LifecycleObserver {
    private Handler handler;
    private int seconds = 0;

    @OnLifecycleEvent(Lifecycle.Event.ON_START)public void startTimer() {
        handler = new Handler();
        handler.post(new Runnable() {
            @Overridepublic void run() {
                Log.d("Timer", "Seconds: " + seconds);
                seconds++;
                handler.postDelayed(this, 1000);
            }
        });
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_STOP)public void stopTimer() {
        handler.removeCallbacksAndMessages(null);
        handler = null;
    }
}


```

  

3. 在 MainActivity 中添加 LifecycleOwner，并在 onCreate 方法中添加 Observer。

  

```
public class MainActivity extends AppCompatActivity {

    @Overrideprotected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 获取LifecycleOwner对象LifecycleOwner lifecycleOwner = this;

        // 将Timer实例添加为Observer
        getLifecycle().addObserver(new Timer());

        // ...
    }

    // ...
}


```

这样，当 Activity 处于前台时，Timer 实例中的 startTimer 方法会被调用，计时器会开始递增；

当 Activity 被销毁时，Timer 实例中的 stopTimer 方法会被调用，计时器会停止。

这个例子展示了如何使用 Jetpack LifeCycle 组件来管理 App 组件的生命周期。

当 App 中存在需要在组件生命周期不同阶段执行的操作时，使用 LifeCycle 可以更方便地实现这些操作，同时避免了一些常见的生命周期问题。

### 3.1.3 LifeCycle 优势劣势

#### 优势

1. 管理生命周期方便

使用 LifeCycle 组件可以更方便地管理 App 组件的生命周期，避免了一些常见的生命周期问题，如内存泄漏和空指针异常等。

2. 模块化编程

使用 LifeCycle 组件可以将 App 的业务逻辑分解为模块化的组件，每个组件负责管理自己的生命周期，便于代码复用和维护。

3. 规范化编程

使用 LifeCycle 组件可以规范化 App 的开发，使代码更易于阅读、理解和维护。

4. 支持多个组件

LifeCycle 组件支持多个组件进行生命周期管理，可以轻松地在多个组件之间共享状态和数据。

#### 劣势

1. **需要继承 LifecycleObserver**：在实现 LifeCycle 功能的时候，需要继承 LifecycleObserver 接口，这会导致代码的继承关系稍微有点复杂。

  

2. **需要添加注释**：在使用 LifeCycle 组件的时候，需要添加一些注释来指示方法是在什么时候被调用，否则可能会出现一些难以诊断的问题。

### 3.1.4 LifeCycle 应用场景

Jetpack LifeCycle 组件的实际开发应用场景包括：

1. **Activity 和 Fragment 生命周期管理**：使用 LifeCycle 组件可以更方便地管理 Activity 和 Fragment 的生命周期，避免了一些常见的生命周期问题，如内存泄漏和空指针异常等。

  

2. **后台服务管理**：使用 LifeCycle 组件可以更方便地管理后台服务的生命周期，可以在 App 退出后自动停止后台服务，避免了一些不必要的资源浪费。

  

3. **数据库连接管理**：使用 LifeCycle 组件可以更方便地管理数据库连接的生命周期，可以在 App 退出时自动关闭数据库连接，避免了一些不必要的资源浪费。

  

4. **网络请求管理**：使用 LifeCycle 组件可以更方便地管理网络请求的生命周期，可以在 Activity 或 Fragment 销毁时自动取消网络请求，避免了一些不必要的网络请求。

  

5. **视图控制器管理**：使用 LifeCycle 组件可以更方便地管理视图控制器的生命周期，可以在 Activity 或 Fragment 销毁时自动清除视图控制器的状态，避免了一些不必要的状态保存和恢复操作。

### 3.1.5 LifeCycle 原理分析

#### 类图

LifecycleOwner 表示拥有生命周期的组件，比如 Activity 和 Fragment。

Lifecycle 表示组件的生命周期，LifecycleObserver 表示一个组件的生命周期观察者。

LifecycleRegistry 是 Lifecycle 接口的一个实现类，它维护了一个生命周期状态机，用于记录组件的生命周期状态和生命周期事件。

LifecycleRegistry 提供了一系列方法，用于管理组件的生命周期状态和生命周期事件。当组件的生命周期事件发生变化时，LifecycleRegistry 会自动更新状态机，并通知所有的 LifecycleObserver 观察者对象，以便它们可以相应地更新自己的状态。

LifecycleOwner 可以通过 getLifecycle() 方法获取到一个 Lifecycle 对象，然后将自己的生命周期观察者对象添加到 Lifecycle 对象中，从而实现对组件生命周期的监听。

当组件的生命周期事件发生变化时，Lifecycle 会自动通知所有的生命周期观察者对象，以便它们可以相应地更新自己的状态。

![](https://mmbiz.qpic.cn/mmbiz_png/dlQfNRHyZWRzU86EBX5xOuvYHsTr39C1icpYONHL46YzticUmR2rU6F3abEZlnv3QKj9RB2lkNJTILJq3Dv0CYJQ/640?wx_fmt=png)

源码

Lifecycle 库的核心是 Lifecycle 接口和 LifecycleObserver 接口。

Lifecycle 接口定义了一组方法，用于将 LifecycleOwner 与 LifecycleObserver 进行关联。

```
public abstract class Lifecycle {
    //添加观察者
    @MainThread
    public abstract void addObserver(@NonNull LifecycleObserver observer);
    //移除观察者
    @MainThread
    public abstract void removeObserver(@NonNull LifecycleObserver observer);
    //获取当前状态
    public abstract State getCurrentState();

//生命周期事件，对应Activity生命周期方法
    public enum Event {
        ON_CREATE,
        ON_START,
        ON_RESUME,
        ON_PAUSE,
        ON_STOP,
        ON_DESTROY,
        ON_ANY  //可以响应任意一个事件
    }

    //生命周期状态. （Event是进入这种状态的事件）
    public enum State {
        DESTROYED,
        INITIALIZED,
        CREATED,
        STARTED,
        RESUMED;

        //判断至少是某一状态
        public boolean isAtLeast(@NonNull State state) {
            return compareTo(state) >= 0;
        }
    }


```

LifecycleObserver 接口定义了一组回调方法，用于接收 LifecycleOwner 的生命周期事件。

在 Lifecycle 库的实现中，Lifecycle 接口有两个重要的实现类，分别是 LifecycleRegistry 和 LifecycleOwner。

LifecycleRegistry 实现了 Lifecycle 接口，并提供了一组方法，用于管理 LifecycleOwner 的生命周期状态。

LifecycleOwner 是一个接口，用于标识拥有生命周期状态的对象，通常是 Activity 或 Fragment。

```
//androidx.activity.ComponentActivity，这里忽略了一些其他代码，我们只看Lifecycle相关
public class ComponentActivity extends androidx.core.app.ComponentActivity implements LifecycleOwner{
    ...

    private final LifecycleRegistry mLifecycleRegistry = new LifecycleRegistry(this);
    ...
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mSavedStateRegistryController.performRestore(savedInstanceState);
        ReportFragment.injectIfNeededIn(this); //使用ReportFragment分发生命周期事件
        if (mContentLayoutId != 0) {
            setContentView(mContentLayoutId);
        }
    }
    @CallSuper
    @Override
    protected void onSaveInstanceState(@NonNull Bundle outState) {
        Lifecycle lifecycle = getLifecycle();
        if (lifecycle instanceof LifecycleRegistry) {
            ((LifecycleRegistry) lifecycle).setCurrentState(Lifecycle.State.CREATED);
        }
        super.onSaveInstanceState(outState);
        mSavedStateRegistryController.performSave(outState);
    }

    @NonNull
    @Override
    public Lifecycle getLifecycle() {
        return mLifecycleRegistry;
    }
}


```

在 LifecycleRegistry 中，有一个名为 mObserverMap 的成员变量，用于存储 LifecycleObserver 对象和其关联的 EventObserver 对象。

当 LifecycleOwner 的生命周期状态更改时，LifecycleRegistry 会自动调用 mObserverMap 中与之相关联的 EventObserver 对象的相应方法，以便它们可以执行适当的操作。

```
//LifecycleRegistry.java
   //系统自定义的保存Observer的map，可在遍历中增删
    private FastSafeIterableMap<LifecycleObserver, ObserverWithState> mObserverMap = new FastSafeIterableMap<>();

    public void handleLifecycleEvent(@NonNull Lifecycle.Event event) {
        State next = getStateAfter(event);//获取event发生之后的将要处于的状态
        moveToState(next);//移动到这个状态
    }

    private void moveToState(State next) {
        if (mState == next) {
            return;//如果和当前状态一致，不处理
        }
        mState = next; //赋值新状态
        if (mHandlingEvent || mAddingObserverCounter != 0) {
            mNewEventOccurred = true;
            return;
        }
        mHandlingEvent = true;
        sync(); //把生命周期状态同步给所有观察者
        mHandlingEvent = false;
    }

        private void sync() {
        LifecycleOwner lifecycleOwner = mLifecycleOwner.get();
        if (lifecycleOwner == null) {
            throw new IllegalStateException("LifecycleOwner of this LifecycleRegistry is already"
                    + "garbage collected. It is too late to change lifecycle state.");
        }
        while (!isSynced()) {  //isSynced()意思是 所有观察者都同步完了
            mNewEventOccurred = false;
            //mObserverMap就是 在activity中添加observer后 用于存放observer的map
            if (mState.compareTo(mObserverMap.eldest().getValue().mState) < 0) {
                backwardPass(lifecycleOwner);
            }
            Entry<LifecycleObserver, ObserverWithState> newest = mObserverMap.newest();
            if (!mNewEventOccurred && newest != null
                    && mState.compareTo(newest.getValue().mState) > 0) {
                forwardPass(lifecycleOwner);
            }
        }
        mNewEventOccurred = false;
    }
    ...

     static State getStateAfter(Event event) {
        switch (event) {
            case ON_CREATE:
            case ON_STOP:
                return CREATED;
            case ON_START:
            case ON_PAUSE:
                return STARTED;
            case ON_RESUME:
                return RESUMED;
            case ON_DESTROY:
                return DESTROYED;
            case ON_ANY:
                break;
        }
        throw new IllegalArgumentException("Unexpected event value " + event);
    }


```

LifecycleRegistry 还提供了一组方法，如 handleLifecycleEvent()、getCurrentState()、addObserver()、removeObserver() 等，用于管理组件的生命周期状态和 LifecycleObserver 对象。

在 Lifecycle 库的实现中，还有一些其他的类和接口，如 GenericLifecycleObserver、FullLifecycleObserver、LifecycleEvent、EventObserver 等，它们都是用于管理和处理组件生命周期事件的。

### 3.1.6 LifeCycle 注意事项

##### 3.1.6.1 不要在 onCreate() 方法中使用 Lifecycle 组件

Lifecycle 组件在 onCreate() 方法中尚未初始化完成，因此在该方法中使用它们可能会导致崩溃或不可预测的行为。建议在 onStart() 方法中使用 Lifecycle 组件。

##### 3.1.6.2 不要手动调用 onDestroy() 方法

手动调用 onDestroy() 方法会破坏 Lifecycle 组件的生命周期，从而导致应用程序行为异常。Lifecycle 组件应该由系统自动管理，应该避免手动干预。

##### 3.1.6.3 避免在 Fragment 中使用多个 LifecycleOwner

Fragment 自身就是一个 LifecycleOwner，因此不应该在 Fragment 中创建其他的 LifecycleOwner。这样会导致多个 LifecycleOwner 之间的状态不同步，从而导致应用程序出现问题。

**3.2 LiveData**
----------------

![](https://mmbiz.qpic.cn/mmbiz_png/dlQfNRHyZWRzU86EBX5xOuvYHsTr39C1kUq4micj6SBghLkq7qu8ge9jHQoA69sSYET49mC6T48OBwr621rfvmQ/640?wx_fmt=png)

### 3.2.1 LiveData 基础定义

Android Jetpack LiveData 是一种用于管理应用程序界面和数据交互的组件。

LiveData 是一种可观察的数据持有者，用于在应用程序组件（如 Activity、Fragment 和 Service）之间共享数据，并在数据发生更改时通知观察者。

LiveData 可以确保 UI 与数据的同步更新，避免了一些常见的错误，如内存泄漏和 UI 组件无法正确更新的问题。

LiveData 具有生命周期感知功能，可以自动感知应用程序组件的生命周期，并在组件处于活动状态时更新 UI，而在组件处于非活动状态时停止更新，从而有效地减少了资源消耗。

LiveData 还提供了线程安全的访问数据的机制，避免了多线程并发访问的问题。

### 3.2.2 LiveData 基础使用

如何 TextView 控件的显示内容呢?

首先，在 XML 布局文件中添加一个 TextView 控件：

```
<TextView
    android:id="@+id/text_view"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" />


```

然后，在 Activity 或 Fragment 中创建一个 LiveData 对象，用于更新 TextView 的显示内容：

```
public class MyActivity extends AppCompatActivity {

    private LiveData<String> mLiveData;
    private TextView mTextView;

    @Overrideprotected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mTextView = findViewById(R.id.text_view);

        mLiveData = new MutableLiveData<>();
        mLiveData.observe(this, new Observer<String>() {
            @Overridepublic void onChanged(String s) {
                mTextView.setText(s);
            }
        });
    }
}


```

在上述代码中，我们创建了一个 LiveData 对象，并将其与一个 TextView 控件关联。当 LiveData 对象中的值发生变化时，我们使用 Observer 来监听这个变化，然后更新 TextView 的显示内容。

最后，我们可以在代码中通过 setValue() 或 postValue() 方法来更新 LiveData 对象的值，从而更新 TextView 的显示内容。例如：

```
((MutableLiveData<String>) mLiveData)
.setValue("Hello, world!");


```

以上就是一个简单的 LiveData 案例，用于更新 TextView 控件的显示内容。当 LiveData 中的值发生变化时，TextView 控件会自动更新显示内容。

### 3.2.3 LiveData 优势劣势

#### 优势

1. 生命周期感知：LiveData 可以感知组件（如 Activity 或 Fragment）的生命周期，从而避免了由于 UI 组件的生命周期变化而引发的空指针异常和内存泄漏等问题。

  

2. 数据更新通知：LiveData 可以在数据发生变化时自动通知所有观察者更新数据，从而实现数据的实时更新和响应。

  

3. 数据一致性：LiveData 可以确保在配置更改时保持数据的一致性，避免了数据丢失和重复加载等问题。

  

4. 线程安全：LiveData 可以确保在主线程中更新 UI 界面，并支持在工作线程中进行异步操作，从而避免了多线程数据竞争问题。

  

5. 与 ViewModel 结合使用：LiveData 可以与 ViewModel 结合使用，实现数据与 UI 界面的分离，从而提高了代码的可维护性和可测试性。

#### 劣势

1. 适用场景有限：LiveData 适用于数据变化频繁、需要实时更新的场景，对于数据变化较少的场景，使用 LiveData 可能会增加代码复杂性。

  

2. API 限制：LiveData 是 Android Jetpack 组件，只能在支持 Jetpack 的 Android 版本上使用，对于一些较老的 Android 版本可能需要使用其他技术方案。

### 3.2.4 LiveData 应用场景

LiveData 的应用场景包括但不限于以下几个方面：

1. **数据库操作**：LiveData 可以与 Room 持久化库结合使用，当数据库中的数据发生变化时，LiveData 会自动通知 UI 组件进行更新。

  

2. **网络请求**：LiveData 可以与 Retrofit 网络请求库结合使用，当网络请求的结果返回时，LiveData 会自动通知 UI 组件进行更新。

  

3. **数据共享**：LiveData 可以在不同的组件之间共享数据，例如，当一个 Activity 和一个 Fragment 需要共享数据时，可以将 LiveData 对象设置为一个公共的数据持有者。

  

4. **资源释放**：LiveData 可以在 UI 组件不再处于活动状态时自动释放资源，避免出现内存泄漏等问题。

  

5. **代码简洁**：LiveData 可以减少代码复杂度，通过数据观察者的方式，避免手动编写繁琐的数据更新代码。

### 3.2.5 LiveData 原理分析

LiveData 是一种可以感知生命周期的数据持有者，它可以让数据更新时通知 UI 界面进行更新，同时也能够避免因为生命周期问题带来的内存泄漏。

#### 类图

#### LiveData 的简化类图：

![](https://mmbiz.qpic.cn/mmbiz_png/dlQfNRHyZWRzU86EBX5xOuvYHsTr39C1zhNwzeFJD0LWRfkvv9ype63mBPwrUJdBaYC4rWdZxT6kLafPe0xr5w/640?wx_fmt=png)

在上面的类图中，Observer 是 LiveData 的观察者接口，LiveData 是可观察数据的持有者类。LiveData 具有生命周期感知能力，可以根据其生命周期状态自动管理数据的订阅和取消订阅。MutableLiveData 是 LiveData 的可变子类，允许更新 LiveData 持有的数据。LiveData 的 observe() 方法用于注册观察者，setValue() 和 postValue() 方法用于更新 LiveData 数据。

#### 源码

LiveData 的核心代码在 androidx.lifecycle.LiveData 类中，下面对 LiveData 的源码进行简要分析：

##### 1. LiveData 的基本结构

LiveData 类是一个抽象类，它有一个泛型类型 T，表示 LiveData 中存储的数据类型。LiveData 类内部维护了一个数据源 (mData) 和一个观察者列表(mObservers)，当 LiveData 中的数据发生改变时，会通知所有注册的观察者进行 UI 更新。

```
public abstract class LiveData<T> {
    private static final Object NOT_SET = new Object();
    private Object mData;
    private boolean mDispatchingValue;
    private int mActiveCount;
    private volatile Object mPendingData = NOT_SET;
    private volatile Object mVersion = new Object();
    private final MainThreadExecutor mMainThreadExecutor = new MainThreadExecutor();
    private final SafeIterableMap<Observer<T>, ObserverWrapper> mObservers = new SafeIterableMap<>();

    // ...
}


```

##### 2. 观察者注册

LiveData 中的观察者是通过 observe() 方法进行注册的，这个方法接受一个 LifecycleOwner 对象和一个 Observer 对象。LifecycleOwner 是一个具有生命周期的对象，当 LifecycleOwner 的生命周期结束时，LiveData 会自动解注册所有与该 LifecycleOwner 相关的观察者，避免内存泄漏。

```
public void observe(@NonNull LifecycleOwner owner, @NonNull Observer<? super T> observer) {
    if (owner.getLifecycle().getCurrentState() == DESTROYED) {
        // ignorereturn;
    }
    // ...LifecycleBoundObserver wrapper = new LifecycleBoundObserver(owner, observer);
    // ...
}


```

##### 3. 观察者解注册

解注册则是通过 removeObserver() 方法进行的，该方法接受一个 Observer 对象，用于从观察者列表中删除相应的观察者。

```
public void removeObserver(@NonNull Observer<? super T> observer) {
    assertMainThread("removeObserver");
    // ...mObservers.remove(observerWrapper);
    // ...
}


```

##### 4. 数据更新和通知观察者  

LiveData 中的数据更新是通过 setValue() 和 postValue() 方法进行的。setValue() 方法是在主线程中进行调用的，它会直接更新 LiveData 中的数据并通知所有的观察者进行 UI 更新；而 postValue() 方法是在异步线程中进行调用的，它会将要更新的数据封装成 PendingPost 对象，并提交给主线程的 Handler 进行处理。

```
protected void setValue(T value) {
    assertMainThread("setValue");
    mVersion++;
    mData = value;
    dispatchingValue(null);
}

protected void postValue(T value) {
    if (Looper.myLooper() != Looper.getMainLooper()) {
        // ...
        return;
    }
    mPendingData = value;
    if (mPendingData == NOT_SET) {
        // ignore
        return;
    }
    // ...
    mMainThreadExecutor.execute(mPostValueRunnable);
}


```

当 LiveData 中的数据发生更新时，LiveData 会通知所有的观察者进行 UI 更新。LiveData 使用了模板方法。

设计模式中的观察者模式，它将数据源和观察者进行了解耦。LiveData 类中的 dispatchingValue() 方法就是通知观察者进行 UI 更新的核心方法。

```
private void dispatchingValue(@Nullable LifecycleBoundObserver initiator) {
    if (mDispatchingValue) {
        mDispatchInvalidated = true;
        return;
    }
    mDispatchingValue = true;
    do {
        mDispatchInvalidated = false;
        if (initiator != null) {
            considerNotify(initiator);
            initiator = null;
        } else {
            Iterator<Map.Entry<Observer<T>, ObserverWrapper>> iterator = mObservers.iteratorWithAdditions();
            while (iterator.hasNext()) {
                considerNotify(iterator.next().getValue());
                if (mDispatchInvalidated) {
                    break;
                }
            }
        }
    } while (mDispatchInvalidated);
    mDispatchingValue = false;
}


```

dispatchingValue() 方法中使用了一个迭代器遍历所有的观察者，然后调用 considerNotify() 方法进行 UI 更新。

```
private void considerNotify(ObserverWrapper observer) {
    if (!observer.mActive) {
        return;
    }
    if (!observer.shouldBeActive()) {
        observer.activeStateChanged(false);
        return;
    }
    if (observer.mLastVersion >= mVersion) {
        return;
    }
    observer.mLastVersion = mVersion;
    observer.mObserver.onChanged((T) mData);
}


```

considerNotify() 方法首先判断观察者是否处于活跃状态，如果不是则直接返回；接着判断观察者是否应该处于活跃状态，如果不是则调用 activeStateChanged() 方法将观察者状态更新为非活跃状态；最后判断数据版本号是否发生变化，如果发生变化则调用观察者的 onChanged() 方法进行 UI 更新。

##### 5. 其他方法

除了以上核心方法之外，LiveData 还提供了其他方法，例如 getValue() 方法用于获取 LiveData 中存储的数据；hasActiveObservers() 方法用于判断是否存在处于活跃状态的观察者等等。

```
public T getValue() {
    Object data = mData;
    if (data != NOT_SET) {
        return (T) data;
    }
    return null;
}

public boolean hasActiveObservers() {
    return mActiveCount > 0;
}


```

以上是对 LiveData 源码的简要分析，LiveData 使用了观察者模式，可以感知生命周期并进行 UI 更新，避免了因为生命周期问题带来的内存泄漏。

### 3.2.6 LiveData 注意事项

#### 数据倒灌

要解决 LiveData 数据倒灌问题，可以使用以下方法：

**使用 MediatorLiveData 代替 LiveData**：MediatorLiveData 可以作为一个中间层，将多个 LiveData 对象的数据源合并，从而避免数据倒灌问题。在 UI 组件的生命周期结束时，可以调用 MediatorLiveData 的 removeSource() 方法，将 LiveData 的数据源从 MediatorLiveData 中移除。

**在 ViewModel 中使用 LiveData**：将 LiveData 对象作为 ViewModel 中的成员变量，并在 ViewModel 中进行数据更新和观察，可以避免 LiveData 数据倒灌问题。

**在 UI 组件中使用自定义 Observer**：可以在自定义 Observer 中进行生命周期判断，当 UI 组件的生命周期已经结束时，不再更新 UI 界面。

下面是使用自定义 Observer 解决 LiveData 数据倒灌问题的示例代码：

```
    public class MyObserver<T> implements Observer<T> {

        private boolean mIsStarted = false;
        private Observer<T> mObserver;

        public MyObserver(Observer<T> observer) {
            mObserver = observer;
        }

        public void start() {
            mIsStarted = true;
        }

        public void stop() {
            mIsStarted = false;
        }

        @Overridepublic void onChanged(T t) {
            if (mIsStarted) {
                mObserver.onChanged(t);
            }
        }
    }


```

在 UI 组件中使用自定义 Observer 时，需要在 UI 组件的生命周期开始和结束时，调用 MyObserver 的 start() 和 stop() 方法，从而控制数据更新的时机，避免 LiveData 数据倒灌问题。

```
    public class MyActivity extends AppCompatActivity {

        private LiveData<Integer> mLiveData;
        private MyObserver<Integer> mObserver;

        @Overrideprotected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mLiveData = new MutableLiveData<>();
            mObserver = new MyObserver<>(integer -> {
                // 更新UI界面
            });
            mLiveData.observe(this, mObserver);
        }

        @Overrideprotected void onStart() {
            super.onStart();
            mObserver.start();
        }

        @Overrideprotected void onStop() {
            super.onStop();
            mObserver.stop();
        }
    }


```

以上是解决 LiveData 数据倒灌问题的一些方法，也可以通过 hook 修改 livedata 源码 observer.mLastVersion 的值，使得 if (observer.mLastVersion>= mVersion) 成立，就不会导致没有注册观察者，还能接收到消息。

![](https://mmbiz.qpic.cn/mmbiz_png/dlQfNRHyZWRzU86EBX5xOuvYHsTr39C1CtYLXv7wjCKVLoCBZS32Bqs9TdwQtu4diblk9yER397NCsoFKHfeeHg/640?wx_fmt=png)setValue 不起效

调用 LiveData 的 setValue() 方法时，如果 LiveData 的观察者处于激活状态，那么 LiveData 会将最新的数据推送给观察者，并在观察者的回调方法中更新 UI 界面。但是，如果 LiveData 的观察者处于非激活状态，那么 LiveData 不会将数据推送给观察者，也不会更新 UI 界面。因此，如果调用 setValue() 方法后，UI 界面没有发生更新，可能是因为 LiveData 的观察者处于非激活状态。

LiveData 的观察者处于非激活状态的原因可能有以下几种：

1. **观察者没有与 LiveData 建立连接**：在调用 LiveData 的 observe() 方法时，需要将观察者与 LiveData 建立连接，只有建立连接后，LiveData 才能将数据推送给观察者。如果没有建立连接，那么即使调用了 setValue() 方法，也无法更新 UI 界面。

  

2. **观察者的生命周期已经结束**：LiveData 的观察者必须在其生命周期的活动状态中才能接收到数据更新。如果观察者的生命周期已经结束，那么即使调用了 setValue() 方法，也无法更新 UI 界面。

  

3. **观察者处于非激活状态**：LiveData 的观察者在其生命周期的激活状态中才能接收到数据更新。如果观察者处于非激活状态，那么即使调用了 setValue() 方法，也无法更新 UI 界面。

因此，如果调用 setValue() 方法后，UI 界面没有发生更新，可以检查观察者的连接状态和生命周期状态，确保 LiveData 的观察者处于激活状态，并且已经与 LiveData 建立连接。如果仍然无法解决问题，可以考虑使用 postValue() 方法，该方法可以在 UI 线程空闲时更新 LiveData 的数据，并在观察者处于激活状态时通知观察者进行 UI 更新。

#### 内存泄漏

LiveData 的观察者（Observer）默认是弱引用，但是如果观察者没有及时取消观察，可能会导致内存泄漏。

#### 多次观察

如果一个 LiveData 对象被多个观察者同时观察，那么每个观察者都会收到相同的数据更新，可能会导致 UI 界面多次更新，造成性能问题。

#### 生命周期不一致

LiveData 是与生命周期相关联的，如果 UI 组件的生命周期结束了，但是 LiveData 仍在发送数据更新，那么就会引发异常。

#### 线程安全

LiveData 默认在主线程进行数据更新，如果需要在后台线程进行数据更新，就需要使用 LiveData 的 postValue() 方法，而不是 setValue() 方法。

#### 避免重复观察

如果在一个 UI 组件中多次观察同一个 LiveData 对象，可能会导致重复观察，造成性能问题和数据不一致问题。因此，可以使用 ViewModel 中的 getLiveData() 方法，保证同一个 LiveData 对象只被观察一次。

### 3.2.7 EventBus vs Otto vs RxJava vs LiveData

![](https://mmbiz.qpic.cn/mmbiz_png/dlQfNRHyZWRzU86EBX5xOuvYHsTr39C1VLcnsHGmHyPcH7TEmS0R7ZIj9Yv4G67MdW570TicDgQmlC060c9AKJw/640?wx_fmt=png)

**3.3 ViewModel**
-----------------

![](https://mmbiz.qpic.cn/mmbiz_png/dlQfNRHyZWRzU86EBX5xOuvYHsTr39C1dG4E1AOu6xA39B3LMuqEtFplWYvicX6wIic3o2rlwGicduqDf0Auvlg2w/640?wx_fmt=png)  

**3.3.1 ViewModel 基础定义**

Android Jetpack ViewModel 是一种用于管理 UI 相关数据的组件，它可以在屏幕旋转等配置更改时保存数据并重新创建 Activity 或 Fragment。ViewModel 是一个以生命周期感知的方式来保存和管理 UI 相关数据的类。

ViewModel 是一个专门用于存储和管理与 UI 相关的数据的类，它们可以在 Activity 或 Fragment 重新创建时保持数据的完整性，并且可以避免因为 Activity 或 Fragment 生命周期的变化而导致的数据丢失。ViewModel 还提供了一种在 Activity 和 Fragment 之间共享数据的机制。

在 ViewModel 中存储的数据是不受 Activity 或 Fragment 生命周期的影响的，这意味着在旋转屏幕或者配置更改时，ViewModel 中的数据将不会被清除，可以保持完整性，从而可以在 Activity 或 Fragment 重新创建时继续使用。

ViewModel 通常与 LiveData 或 RxJava 等响应式编程库一起使用，以实现数据的实时更新和响应。

### 3.3.2 ViewModel 基础使用

如何在 ViewModel 中保存和管理数据，并在 Activity 或 Fragment 重新创建时保持数据的完整性。

#### 1. 创建一个名为 MyViewModel 的 ViewModel 类

```
public class MyViewModel extends ViewModel {

    private MutableLiveData<Integer> count = new MutableLiveData<>();

    public void setCount(int count) {
        this.count.setValue(count);
    }

    public LiveData<Integer> getCount() {
        return count;
    }
}


```

在这个 ViewModel 类中，我们创建了一个 MutableLiveData 类型的 count 变量，并在 setCount() 方法中设置它的值，同时在 getCount() 方法中将其作为 LiveData 返回，以实现数据的实时更新和响应。

#### 2. 在 Activity 或 Fragment 中使用 ViewModel

```
public class MyActivity extends AppCompatActivity {

    private MyViewModel viewModel;

    @Overrideprotected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        viewModel = ViewModelProviders.of(this).get(MyViewModel.class);
        viewModel.getCount().observe(this, count -> {
            // 更新 UI
        });
    }

    public void onButtonClicked(View view) {
        int count = viewModel.getCount().getValue() + 1;
        viewModel.setCount(count);
    }
}


```

在这个 Activity 中，我们使用 ViewModelProviders.of() 方法获取 MyViewModel 的实例，并将其与该 Activity 绑定。

然后，我们可以使用 getCount() 方法获取 count 变量的值，并在 setCount() 方法中设置其值。

通过调用 getCount().observe() 方法，我们可以在 LiveData 数据变化时更新 UI。

这个案例演示了如何使用 Android Jetpack ViewModel 来管理 UI 相关的数据，并在 Activity 或 Fragment 重新创建时保持数据的完整性。

使用 ViewModel 可以帮助我们更好地管理和保持与 UI 相关的数据，并提高应用程序的稳定性和可靠性。

### 3.3.3 ViewModel 优势劣势

#### 优势

1. **保持数据的完整性**：ViewModel 可以在屏幕旋转等配置更改时保存数据并重新创建 Activity 或 Fragment，从而保持数据的完整性，避免因为生命周期变化而导致的数据丢失。

  

2. **简化代码**：使用 ViewModel 可以将 UI 相关的数据与 UI 控件分离，避免了在 Activity 或 Fragment 中处理数据逻辑的繁琐代码，使代码更加清晰、简洁。

  

3. **支持数据共享**：ViewModel 不会随着 Activity 的屏幕旋转而销毁，减少了维护状态的代码成本（数据的存储和读取、序列化和反序列化）；

  

4. **支持响应式编程**：ViewModel 可以与 LiveData 或 RxJava 等响应式编程库一起使用，实现数据的实时更新和响应。

#### 劣势

1.  #### 
    
    **不适合处理长时间运行的任务**：ViewModel 主要用于管理 UI 相关的数据，如果需要处理长时间运行的任务，需要在 ViewModel 中使用异步任务或者单独使用 Service。
    

  

2. **数据持久化需要额外处理**：ViewModel 中保存的数据只是暂时性的，如果需要长期保存数据，需要将数据保存到数据库或者 SharedPreferences 中。

  

3. **需要了解生命周期**：ViewModel 是以生命周期感知的方式来保存和管理数据的，因此需要了解 Activity 或 Fragment 的生命周期以便正确使用 ViewModel。

  

3.3.4 ViewModel 应用场景  

下面是一些实际开发过程中可能使用 ViewModel 的使用场景：

1. **数据持久化**：如果需要在应用程序的不同页面之间共享数据，并且希望在 Activity 或 Fragment 重新创建时保持数据的完整性，可以使用 ViewModel 来保存和管理数据。

  

2. **处理屏幕旋转等配置更改**：当应用程序的屏幕旋转或其他配置更改时，Activity 或 Fragment 可能会被销毁并重新创建，此时可以使用 ViewModel 来保存和恢复数据，避免因为生命周期变化而导致的数据丢失。

  

3. **分离 UI 与数据**：将 UI 相关的数据与 UI 控件分离，避免在 Activity 或 Fragment 中处理数据逻辑的繁琐代码，使代码更加清晰、简洁。

  

4. **实现响应式编程**：ViewModel 可以与 LiveData 或 RxJava 等响应式编程库一起使用，实现数据的实时更新和响应，从而提高应用程序的性能和用户体验。

  

5. **避免内存泄漏**：将数据存储在 ViewModel 中可以避免由于对 Activity 或 Fragment 的引用而导致的内存泄漏，从而提高了应用程序的稳定性和可靠性。

### 3.3.5 ViewModel 原理分析

#### 类图

*   ViewModel 类是一个抽象类，包含了一个
    
    onCleared()
    
    方法，该方法会在 ViewModel 不再被使用时被调用，用于释放资源和清除状态。
    
*   AndroidViewModel 是 ViewModel 的一个子类，它包含一个 Application 对象，用于在 ViewModel 中访问应用程序的上下文。
    
*   ViewModelProvider 是一个帮助类，用于获取 ViewModel 实例。
    
*   V
    
    ‍‍
    
    iewModelProvide
    
    r.Factory
    
    是一个接口，用于创建 ViewModel 实例。
    

![](https://mmbiz.qpic.cn/mmbiz_png/dlQfNRHyZWRzU86EBX5xOuvYHsTr39C1plf2FibeuEEE2qVEEtIPKlLBMwOwxq9t0tRYvVcIg7wYbOrdN1Mia2kQ/640?wx_fmt=png)

#### 原理

Jetpack ViewModel 是一个用于管理 UI 组件生命周期的组件，它的设计目的是为了在 Activity 或 Fragment 重建时能够保留数据，避免因为重建导致的数据丢失或重复加载。在本文中，我们将会对 Jetpack ViewModel 的源码进行分析，深入了解其实现原理。

Jetpack ViewModel 源码位于 androidx.lifecycle.ViewModel 包中，它包含了两个类：ViewModel 和 ViewModelProvider。

ViewModel 类的主要作用是定义一个用于存储 UI 组件数据的容器，并在 UI 组件生命周期变化时进行管理。具体来说，ViewModel 类继承了 Android 的 ViewModel 类，并添加了一些额外的方法，用于实现以下功能：  

1. 缓存 UI 组件数据

  

2. 在 UI 组件销毁后清理数据

  

3. 在 UI 组件重建时恢复数据

ViewModelProvider 类的主要作用是创建 ViewModel 实例，并为其提供一个唯一的 key，用于在 Activity 或 Fragment 重建时找回已经存在的 ViewModel 实例。ViewModelProvider 类的实现方式比较简单，主要是通过一个 HashMap 来存储 ViewModel 实例，并提供了一些方法，用于创建和查找 ViewModel 实例。

下面是 ViewModel 类的源码分析：

```
open class ViewModel : ViewModelStoreOwner {
    private val mViewModelStore = ViewModelStore()

    @CallSuperoverride fun onCleared() {
        mViewModelStore.clear()
    }

    fun getViewModelStore(): ViewModelStore {
        return mViewModelStore
    }
}


```

ViewModel 类实现了 ViewModelStoreOwner 接口，并在内部维护了一个 ViewModelStore 对象，用于存储 UI 组件数据。在 ViewModel 被销毁时，会调用

onCleared() 方法来清空 ViewModelStore 中的数据。getViewModelStore() 方法用于返回 ViewModelStore 对象。

ViewModelStoreOwner 接口的实现代码如下：

```
interface ViewModelStoreOwner {
    fun getViewModelStore(): ViewModelStore
}


```

ViewModelProvider 类的源码分析如下：

```
class ViewModelProvider private constructor(
        private val mFactory: Factory,
        private val mViewModelStore: ViewModelStore
) {
    // 缓存 ViewModel 实例的 HashMapprivate val mViewModels = HashMap<String, ViewModel>()

    companion object {
        private val sCache = HashMap<String, ViewModelProvider>()

        /**
         * 返回当前 Activity 或 Fragment 的 ViewModelProvider 实例
         */@MainThreadfun of(owner: ViewModelStoreOwner): ViewModelProvider {
            return of(owner, FactoryHolder.DEFAULT_FACTORY)
        }

        /**
         * 返回当前 Activity 或 Fragment 的 ViewModelProvider 实例，可指定 Factory
         */@MainThreadfun of(owner: ViewModelStoreOwner, factory: Factory): ViewModelProvider {
            val store = owner.getViewModelStore()

            // 先从缓存中查找 ViewModelProvider 实例var viewModelProvider = sCache[store.mKey]
            if (viewModelProvider == null) {
                viewModelProvider = ViewModelProvider(factory, store)
                sCache[store.mKey] = viewModelProvider
            }

            return viewModelProvider
        }
    }

    /**
     * 返回指定 key 的 ViewModel 实例，如果不存在则通过 Factory
     */

fun <T : ViewModel?> get(key: String, modelClass: Class<T>): T {
    var viewModel = mViewModels[key]
    if (modelClass.isInstance(viewModel)) {
        @Suppress("UNCHECKED_CAST")
        return viewModel as T
    }

    // 创建新的 ViewModel 实例，并将其添加到缓存中
    viewModel = mFactory.create(modelClass)
    mViewModels[key] = viewModel

    return viewModel
}

/**
 * Factory 接口，用于创建 ViewModel 实例
 */
interface Factory {
    fun <T : ViewModel?> create(modelClass: Class<T>): T
}

/**
 * FactoryHolder 类，用于提供默认的 Factory 实现
 */
private object FactoryHolder {
    val DEFAULT_FACTORY: Factory = object : Factory {
        override fun <T : ViewModel?> create(modelClass: Class<T>): T {
            try {
                return modelClass.newInstance()
            } catch (e: IllegalAccessException) {
                throw RuntimeException(e)
            } catch (e: InstantiationException) {
                throw RuntimeException(e)
            }
        }
    }
 }
}


```

ViewModelProvider 类包含了一个 HashMap，用于缓存 ViewModel 实例。

它的 of() 方法用于创建 ViewModelProvider 实例，并通过 ViewModelStore 对象来区分不同的 Activity 或 Fragment.get() 方法用于返回指定 key 的 ViewModel 实例，并在缓存中查找是否存在对应的实例。如果缓存中不存在该实例，则调用 Factory 接口来创建一个新的 ViewModel 实例，并将其添加到缓存中。

Factory 接口提供了一个 create() 方法，用于创建 ViewModel 实例。FactoryHolder 类用于提供默认的 Factory 实现，它使用 Java 的反射机制来创建 ViewModel 实例。最后，需要注意的是，ViewModelStore 和 ViewModelProvider 都是线程安全的，可以在多个线程中同时使用。

这意味着在多线程环境下，可以使用 ViewModel 来缓存和共享数据，从而减少数据的重复加载和提升应用程序的性能。

### 3.3.6 ViewModel 注意事项

#### 使用了错误的 ViewModel：

在某些情况下，可能会需要多个 ViewModel 来管理不同的 UI 数据。如果使用了错误的 ViewModel 来管理数据，可能会导致数据丢失或逻辑错误。因此，在使用 ViewModel 时应该清楚每个 ViewModel 的作用，避免出现混淆。

#### ViewModel 与生命周期的关系

ViewModel 的生命周期不同于 Activity 和 Fragment，它是被系统缓存的，因此可能会出现数据被清除的情况。在使用 ViewModel 时应该注意它的生命周期，及时保存数据并恢复数据。

#### 使用无效的上下文

ViewModel 需要一个有效的上下文来创建实例，如果使用无效的上下文可能会导致 ViewModel 创建失败。因此，在使用 ViewModel 时应该注意上下文的有效性，避免出现创建失败的情况。

#### 使用了错误的作用域

ViewModel 的作用域应该与需要管理的 UI 组件的生命周期相同，如果使用了错误的作用域，可能会导致数据被清除或者生命周期不一致。因此，在使用 ViewModel 时应该选择正确的作用域，避免出现问题。

**3.4 DataBinding**
-------------------

![](https://mmbiz.qpic.cn/mmbiz_png/dlQfNRHyZWRzU86EBX5xOuvYHsTr39C1icy7ISzpRocPv2JXAib6hj16nBz6pCfSpk74TUBb3ozSOmqgoYJBC0yA/640?wx_fmt=png)

### 3.4.1 DataBinding 基础定义

DataBinding 提供了一种声明性的方式将布局文件中的 UI 组件和应用程序的数据模型绑定在一起。

通过 DataBinding，开发者可以将 UI 组件的值绑定到数据模型中的属性，使得在更新 UI 时不需要手动更新每个组件的值。

DataBinding 库通过生成一个绑定类来实现 UI 和数据模型之间的绑定。

这个绑定类是在编译时自动生成的，它使用了数据模型和 UI 组件之间的绑定表达式，以便在运行时执行数据绑定。

### 3.4.2 DataBinding 基础使用

#### 技术需求

使用 DataBinding 实现一个简单的计数器功能。在每次更新计数器的值时，DataBinding 会自动更新 TextView 的值，使得开发者不需要手动更新 UI 组件的值，可以更加专注于业务逻辑的实现。

#### XML

```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <data>
        <variable
            
            type="Integer" />
    </data>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:id="@+id/tvCounter"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{counter.toString()}" />

        <Button
            android:id="@+id/btnIncrease"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="+" />

        <Button
            android:id="@+id/btnDecrease"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="-" />
    </LinearLayout>
</layout>


```

#### Code

创建 DataBinding 实例，并将 activity_main.xml 布局文件与 DataBinding 实例绑定。然后，将计数器的值赋给 DataBinding 实例的 counter 变量，并为增加和减少按钮设置点击事件。在点击事件中，更新计数器的值，并将新的值赋给 DataBinding 实例的 counter 变量。最后，调用 DataBinding 实例的 executePendingBindings() 方法，将 UI 组件的值更新到最新的值。

```
class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding

    private var counter = 0

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // 创建DataBinding实例并与布局文件绑定
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)

        // 设置初始值
        binding.counter = counter

        // 为按钮设置点击事件
        binding.btnIncrease.setOnClickListener {
            counter++
            binding.counter = counter
            binding.executePendingBindings()
        }

        binding.btnDecrease.setOnClickListener {
            counter--
            binding.counter = counter
            binding.executePendingBindings()
        }
    }
}


```

### 3.4.3 DataBinding 优势劣势

#### 优势

1. 简化代码使用 DataBinding 可以减少 findViewById() 方法的调用，使得代码更加简洁和易读。开发者可以通过 DataBinding 直接访问布局文件中的 UI 组件，而不需要手动查找和设置 UI 组件的属性。

2. 双向绑定 DataBinding 支持双向绑定，可以将 UI 组件的值绑定到数据模型中的属性，也可以将数据模型的属性绑定到 UI 组件的值。这种双向绑定可以帮助开发者更加方便地实现 UI 和数据模型之间的同步。

3. 减少错误 DataBinding 可以减少代码中的错误，因为使用 DataBinding 可以消除某些手动编写的代码。例如，DataBinding 可以自动生成一些代码，帮助开发者绑定 UI 组件和数据模型，减少手动编写代码的机会。

4. 性能优化 DataBinding 可以带来一些性能优化，例如通过生成绑定类来减少代码的执行时间。DataBinding 还支持懒加载，可以在需要时才加载数据模型和 UI 组件。

#### 劣势

1. 兼容性问题 DataBinding 库需要使用较新的 Android Gradle 插件和 Android SDK 版本，这可能导致一些兼容性问题。如果开发者使用较老的 Android 版本，可能需要进行一些兼容性处理。

2. 构建时间增加使用 DataBinding 可能会增加应用程序的构建时间，因为 DataBinding 库需要生成绑定类。在较大的应用程序中，这可能会导致构建时间明显增加。

3. 难以调试由于 DataBinding 库使用了代码生成，因此在调试时可能会出现一些困难。例如，开发者可能会发现在使用 DataBinding 时，调试器可能会跳过某些代码行，或者某些变量的值可能无法正确显示。

### 3.4.4 DataBinding 应用场景

1. **MVVM 架构**：DataBinding 可以帮助开发者实现 MVVM 架构中的视图模型（ViewModel）和视图（View）之间的绑定。通过 DataBinding，开发者可以将视图和数据模型分离，提高代码的可读性和可维护性。

  

2. **布局优化**：DataBinding 可以帮助开发者实现布局优化，例如通过使用绑定表达式和条件语句来控制 UI 组件的可见性。通过布局优化，可以使得应用程序的 UI 更加灵活和可定制。

  

3. **多语言支持**：DataBinding 可以帮助开发者实现多语言支持，例如通过使用绑定表达式来动态设置 UI 组件的文本。通过多语言支持，可以使得应用程序的用户界面更加友好和易用。

  

4. **动态主题**：DataBinding 可以帮助开发者实现动态主题，例如通过使用绑定表达式来动态设置 UI 组件的颜色和样式。通过动态主题，可以使得应用程序的 UI 更加美观和精致。

### 3.4.5 DataBinding 原理分析

#### 类图

![](https://mmbiz.qpic.cn/mmbiz_png/dlQfNRHyZWRzU86EBX5xOuvYHsTr39C1QutvIxict3IR0rlSDhQlgjGfrIeGygas465kdbR5ju5IHDaIRgrotIw/640?wx_fmt=png)

#### 源码

DataBinding 的原理主要包括以下几个方面：

1. **布局文件的解析和生成**：在 DataBinding 中，布局文件会被解析并生成对应的 ViewDataBinding 类。这个类包含了布局文件中定义的所有 UI 组件的引用，以及绑定到这些 UI 组件的数据对象。

  

2. **数据对象的绑****定**：DataBinding 会在编译时生成代码来完成 UI 组件和数据对象的绑定。生成的代码会被包含在 BR 类中。这个类包含了应用程序中所有用于绑定的变量的引用。当数据对象发生变化时，DataBinding 会自动更新 UI 组件的值。

3. **观察者模式的应用**：DataBinding 使用观察者模式来保持 UI 和数据之间的同步。当数据对象发生变化时，DataBinding 会自动更新 UI 组件的值。在 DataBinding 中，数据对象是被观察的对象，而 UI 组件是观察者。

  

4. **双向绑定的实现**：DataBinding 支持双向绑定，即当 UI 组件的值发生变化时，DataBinding 会自动更新数据对象的值。这是通过使用双向绑定适配器来实现的。适配器会在 UI 组件的值发生变化时自动更新数据对象的值。

  

5. **属性转换器的使用**：DataBinding 还支持属性转换器，可以用来将数据对象中的值转换为 UI 组件可以显示的值。例如，可以使用属性转换器将日期格式化为特定的格式。

  

6. **数据绑定表达式的应用**：DataBinding 还支持数据绑定表达式，可以用来在布局文件中动态计算 UI 组件的值。例如，可以使用数据绑定表达式将两个文本框中的值相加并将结果显示在第三个文本框中。

总的来说，Android Jetpack DataBinding 的原理是基于数据绑定和观察者模式的。它通过生成代码来完成 UI 组件和数据对象之间的绑定，并使用观察者模式来保持 UI 和数据之间的同步。同时，DataBinding 还支持双向绑定、属性转换器和数据绑定表达式等特性，使得它可以满足更加复杂的 UI 和数据交互需求。

### 3.4.6 DataBinding 注意事项

1. **错误：Cannot find the setter for attribute ‘XXX’ with parameter type XXX**。这个错误通常是由于绑定表达式中的变量类型不正确或布局文件中的属性名称不正确导致的。可以检查变量类型和属性名称是否正确。

  

2. **错误：Could not find accessor XXX**。这个错误通常是由于布局文件中使用了不存在的变量或方法导致的。可以检查布局文件中使用的变量和方法是否存在。

  

3. **错误：Variable XXX has incompatible type XXX**。这个错误通常是由于 DataBinding 的变量类型和布局文件中的变量类型不一致导致的。可以检查 DataBinding 代码中变量的类型和布局文件中变量的类型是否一致。

  

4. **错误：DataBinding 不支持 lambda 表达式**。如果使用 lambda 表达式，编译时会出现错误。可以使用方法引用或匿名内部类来替代 lambda 表达式。

  

5. **性能问题：在使用数据绑定表达式时，要注意表达式的复杂度**。复杂的表达式可能会影响应用程序的性能。可以尝试将复杂的表达式分解成更简单的表达式，以提高应用程序的性能。

  

6. **混淆问题**：如果应用程序使用了代码混淆，那么需要将 DataBinding 的类排除在混淆范围之外，否则会导致编译错误或运行时异常。

  

7. **ViewStub 问题**：在使用 DataBinding 的布局文件中，不能包含 ViewStub，否则会导致编译错误。

### 3.4.7 ButterKnife VS ViewBinding VS RoboBinding VS DataBinding

![](https://mmbiz.qpic.cn/mmbiz_png/dlQfNRHyZWRzU86EBX5xOuvYHsTr39C1jFFdOicXYmfezCDiawhkKg6IH0RH3hiaCaib88wUoHG9BnbSojL98K4pCw/640?wx_fmt=png)  

_4_

总结与展望

《Android 业务架构 · 基础篇 · Jetpack 四件套》一文首先通过 4W2H 全方位的讲解了 Jepack 对 Android 业务开发的价值，然后通过基础定义、基础使用、优劣势分析、应用场景、原理分析、注意事项等多维度分析了 Jetpack 四件套。  

在我们工作中，可维护、可扩展、可测试和可重用的业务架构对于提高应用程序的质量和效率意义非凡，而 JetPack 是帮助开发者快速地组织和管理应用程序的代码的工具包。

这也是小木箱强烈建议大家学习 Jetpack 很重要的原因。希望通过这篇文章能够让你意识到 Jetpack 对业务开发的重要性。

* * *

最后推荐一下我做的网站，玩 Android: _wanandroid.com_ ，包含详尽的知识体系、好用的工具，还有本公众号文章合集，欢迎体验和收藏！

推荐阅读：

[Android 进阶必会 Gradle 初探](http://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650849256&idx=1&sn=71bf88bc699e1a4d309f4f0535ff4cee&chksm=80b76b76b7c0e260017fdeb86b802a14d30552d398c3ca674ce889f02d52ee2b0b529c95530c&scene=21#wechat_redirect)  

[万字长文，Android Bitmap 相关的一切](http://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650849253&idx=1&sn=ff29a869a7540e0aaec01f0a306db1b2&chksm=80b76b7bb7c0e26d818442513b594ee3a259c01dc7d115a115932d86b0e795aa4bc6815107de&scene=21#wechat_redirect)  

[Android Framework 一学就会，你学 “废” 了吗？](http://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650849250&idx=1&sn=56c0700f1473d4f2d32644fa8d98f8a2&chksm=80b76b7cb7c0e26a5eac629693d7b994babaedb6edaf076e611c762baa53faedb14af73ca1e9&scene=21#wechat_redirect)  

![](https://mmbiz.qpic.cn/mmbiz_jpg/MOu2ZNAwZwP4yDt9RiaN89t9lxTz0vZWZy9sYR54YefTFFBPmPLwnAN9PNicI0rZznIYt4r2Q40DbAAiatTS1MlVw/640?wx_fmt=jpeg)

**扫一扫** 关注我的公众号

如果你想要跟大家分享你的文章，欢迎投稿~

┏(＾0＾)┛明天见！