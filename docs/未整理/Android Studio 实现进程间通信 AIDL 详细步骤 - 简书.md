> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.jianshu.com](https://www.jianshu.com/p/95c6fb890140) ![](http://upload-images.jianshu.io/upload_images/10618242-8fda237470e30b29.jpg)

> 从事 Android 开发已经好几个年头了，写博客的想法从没有忘记过，而写博客的行动从没有落实过。实现一个功能第一次踩了很多坑，过段时间实现同样的功能还会踩同样的坑，很大一部分原因是因为没有整理、总结。所以我觉得非常有必要将自己掌握的东西总结出来，提升自己能力的同时也能帮助到别人。废话不多说，本篇将是我的第一篇技术博客（从老土的标题应该就能看出来😂），详细讲解 Android studio 实现 AIDL 通信步骤。

概述
==

Android IPC 方式有：Intent 附加 extras 传递信息、通过共享文件方式共享数据、Messenger、AIDL、ContentProvider、Socket。AIDL 相比于其他几种 IPC 方式有很多优点：功能强大，支持一对多并发通信，支持实时通讯，可以调用其他进程的方法，应用场景广泛，但缺点就是使用稍微复杂，不过没有关系，本篇将详细讲解 AIDL 实现步骤，只要动手实现一次就会显得简单了很多。

举个应用场景的栗子：我的小米、小米商城、小米金融、小米视频都属于 MIUI 系统的预装应用，它们都是独立的 apk，当我们打开我的小米，注册登陆之后，再打开小米其他预装应用比如小米视频，会发现小米视频已经登陆了，这是怎么回事呢？

其实这就涉及到了 AIDL，当打开小米视频的时候，小米视频（进程）会去调用我的小米（进程）里的方法判断是否登陆，如果登陆还会获取登陆信息（如用户名，昵称等），这样就实现了小米视频自动登陆了。

说明
==

*   本篇重点讲解 studio 实现 AIDL 步骤，不涉及到 AIDL 原理；
*   本篇通过两个 apk 实现基于 AIDL 的 IPC，更切合实际应用场景；
*   本篇先实现服务端 AIDLService 的编写，再实现客户端 AIDLClient 的编写；
*   本篇实现的功能是客户端调用服务端方法获取图书列表和向服务端添加图书；
*   本篇将会详细讲解每个步骤；
*   文章末尾附上源码；
*   thanks 《Android 第一行代码》、《Android 开发艺术探索》；

服务端实现详细步骤
=========

#### 1、新建项目 AIDLService

注：服务端的包名是`com.glong.aidlservice`；

![](http://upload-images.jianshu.io/upload_images/10618242-00cec5b0baa1ef32.png)

#### 2、新建 Book.java，实现可序列化，提供 get/set 方法

`Book.java`

```
package com.glong.aidlservice;

import android.os.Parcel;
import android.os.Parcelable;

public class Book implements Parcelable {
    private int bookId;
    private String bookName;

    public Book(){
    }

    public Book(int bookId,String bookName){
        this.bookId = bookId;
        this.bookName = bookName;
    }


    @Override
    public int describeContents() {
        return 0;
    }

    @Override
    public void writeToParcel(Parcel dest, int flags) {
        dest.writeInt(this.bookId);
        dest.writeString(this.bookName);
    }

    protected Book(Parcel in) {
        this.bookId = in.readInt();
        this.bookName = in.readString();
    }

    public static final Parcelable.Creator<Book> CREATOR = new Parcelable.Creator<Book>() {
        @Override
        public Book createFromParcel(Parcel source) {
            return new Book(source);
        }

        @Override
        public Book[] newArray(int size) {
            return new Book[size];
        }
    };

    public int getBookId() {
        return bookId;
    }

    public void setBookId(int bookId) {
        this.bookId = bookId;
    }

    public String getBookName() {
        return bookName;
    }

    public void setBookName(String bookName) {
        this.bookName = bookName;
    }
}


```

这一步很简单，五秒搞定，还不能五秒搞定的童鞋[戳这里](https://blog.csdn.net/ligen52/article/details/54669956)

#### 3、新建 AIDL Folder

![](http://upload-images.jianshu.io/upload_images/10618242-91ca6f49f45a400c.png)

生成的 aidl 文件夹跟 java 文件夹属于同一层级

![](http://upload-images.jianshu.io/upload_images/10618242-4518079465f28834.png)

#### 4、在 aidl 目录下新建 IBookManager.aidl

IDE 会自动在 aidl 文件夹下创建`com.glong.aidlservice`包，并在该包下帮我们生成`IBookManager.aidl`

![](http://upload-images.jianshu.io/upload_images/10618242-a9e0064f0eac0471.png)

我们稍做改动增加两个接口，`getBookList()`获取图书列表 `addBook(in Book book)`向服务端添加一本书

`IBookManager.aidl`

```
package com.glong.aidlservice;
import com.glong.aidlservice.Book;
// Declare any non-default types here with import statements

interface IBookManager {

    /*
     * 向客户端提供获取图书列表接口
     */
    List<Book> getBookList();

    /*
     * 向客户端提供添加图书接口
     */
    void addBook(in Book book);
}


```

**注意：**

*   第二行显式`import`了 Book 类，AIDL 中所有 Parcelable 对象要显式的`import`进来，而此处我们接口中使用到了自定义的 Parcelable 对象 Book 类型；
    
*   在 AIDL 文件中用到了自定义的 Parcelable 对象时，必须新建一个和该对象同名的.aidl 文件，所以接下来我们还需新建`Book.aidl`；
    
    _以上两点如果没有 import 或者 import 包名有误或者没有创建 Parcelable 对象对应的.aidl 文件，项目就不能够编译通过，会报 "Process'command 'D:\SDK\build-tools\27.0.3\aidl.exe''finished with non-zero exit value 1" 错误，找不到对应的对象；_
    
*   `addBook()`方法形参前的 in 表示方向。AIDL 中除基本数据类型，其他类型的参数必须标上方向：`in、out`或者`inout`，`in`表示输入性参数，`out`表示输出型参数，`inout`表示输入输出型参数；
    

#### 5、在 aidl 目录下的 com.glong.aidlservice 包下新建 Book.aidl

注意如果直接 New → ADIL→ AIDL File 然后输入 Book，IDE 会提示 "interface Name must be unique"，我们使用如下步骤创建 Book.aidl；

![](http://upload-images.jianshu.io/upload_images/10618242-4a45d90c9e67b67a.png)

`Book.aidl`

```
package com.glong.aidlservice;

parcelable Book;


```

#### 6、Build 编译项目

编译完成后将项目结构模式切换成 Project，在 build 文件夹下可以看到 IDE 帮我们生成了`IBookManager.java`由于本篇重点将步骤，这个文件不多做讲解，项目结构重新切回到 Android 模式

![](http://upload-images.jianshu.io/upload_images/10618242-1d313efb1b6ce094.png)

#### 7、实现远程 Service

在 java 文件夹的`com.glong.aidlservice`包下新建`BookManagerService.java`

![](http://upload-images.jianshu.io/upload_images/10618242-810f1443452d959e.png)

`BookManagerService.java`

```
package com.glong.aidlservice;

import android.app.Service;
import android.content.ContentValues;
import android.content.Intent;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.os.Binder;
import android.os.IBinder;
import android.os.RemoteException;
import android.support.annotation.Nullable;
import android.util.Log;

import com.glong.aidlservice.database.MyDatabaseHelper;

import java.util.ArrayList;
import java.util.List;

public class BookManagerService extends Service {
    private static final String TAG = "guo.BookManagerService";

    private SQLiteDatabase database;
    private List<Book> mBookList = new ArrayList<>();

    private Binder mBinder = new IBookManager.Stub() {

        @Override
        public List<Book> getBookList() throws RemoteException {
            return mBookList;
        }

        @Override
        public void addBook(Book book) throws RemoteException {
            addBookIntoDB(book);
        }
    };

    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        return mBinder;
    }

    @Override
    public void onCreate() {
        super.onCreate();
        Log.d(TAG, "service onCreate");

        queryBooksFromDB();
    }

    /**
     * 从数据库中获取Book列表，数据库操作{@link MyDatabaseHelper}
     */
    private void queryBooksFromDB() {
        Log.i(TAG, "service starting query Book from db.");
        MyDatabaseHelper dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 1);
        database = dbHelper.getReadableDatabase();
        Cursor cursor = database.query("Book", null, null, null, null, null, null);
        if (cursor.moveToFirst()) {
            do {
                int bookId = cursor.getInt(cursor.getColumnIndex("bookId"));
                String bookName = cursor.getString(cursor.getColumnIndex("bookName"));
                Book book = new Book(bookId, bookName);
                mBookList.add(book);
            } while (cursor.moveToNext());
        }
        cursor.close();
    }

    private void addBookIntoDB(Book book) {
        Log.i(TAG, "service starting add Book to db.");
        ContentValues values = new ContentValues();
        values.put("bookId", book.getBookId());
        values.put("bookName", book.getBookName());
        database.insert("Book", null, values);
    }
}


```

服务端的 Book 列表存储在数据库中，当客户端远程连接服务端时，`BookManagerService`服务开启，并且在`onCreate()`中查找数据库初始化`mBookList`图书列表，下面再附上`MyDatabaseHelper.java`

```
package com.glong.aidlservice.database;

import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.util.Log;

public class MyDatabaseHelper extends SQLiteOpenHelper {

    private static final String TAG = "long.MyDatabaseHelper";

    private static final String CREATE_BOOK = "create table Book (" +
            "id integer primary key autoincrement," +
            "bookId integer," +
            "bookName text)";

    public MyDatabaseHelper(Context context, String name, SQLiteDatabase.CursorFactory factory, int version) {
        super(context, name, factory, version);
    }

    @Override
    public void onCreate(SQLiteDatabase sqLiteDatabase) {
        sqLiteDatabase.execSQL(CREATE_BOOK);
        Log.i(TAG, "Book database create succeeded!");
    }

    @Override
    public void onUpgrade(SQLiteDatabase sqLiteDatabase, int i, int i1) {

    }
}


```

代码很简单，就是简单的创建数据库。  
最后不要忘记在`AndroidMenifest.xml`中配置该服务，并且指定启动的 Action，以便客户端通过此 Action 启动远程服

```
    <service android:>
        <intent-filter>
            <action android: />
        </intent-filter>
    </service>


```

#### 8、给数据库添加数据，方面后面我们后面验证客户端是否请求到了服务端的数据

修改`MainActivity.java`代码：

```
package com.glong.aidlservice;

import android.content.ContentValues;
import android.database.sqlite.SQLiteDatabase;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;

import com.glong.aidlservice.database.MyDatabaseHelper;

public class MainActivity extends AppCompatActivity {

    private SQLiteDatabase database;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        MyDatabaseHelper dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 1);
        database = dbHelper.getReadableDatabase();

        //如果数据库为空就向数据库添加几个数据
        if (!database.query("Book", null, null, null, null, null, null).moveToFirst()) {
            addBooksToDB();
        }
    }

    private void addBooksToDB() {
        ContentValues values = new ContentValues();
        values.put("bookId", 0);
        values.put("bookName", "玉女真经");
        database.insert("Book", null, values);
        values.clear();

        values.put("bookId", 1);
        values.put("bookName", "辟邪剑法");
        database.insert("Book", null, values);
        values.clear();

        values.put("bookId", 2);
        values.put("bookName", "如来神掌");
        database.insert("Book", null, values);
        values.clear();

        values.put("bookId", 3);
        values.put("bookName", "降龙十八掌");
        database.insert("Book", null, values);
        values.clear();

        values.put("bookId", 4);
        values.put("bookName", "九阴真经");
        database.insert("Book", null, values);
        values.clear();

        values.put("bookId", 5);
        values.put("bookName", "九阳神功");
        database.insert("Book", null, values);
        values.clear();

        values.put("bookId", 6);
        values.put("bookName", "打狗棒法");
        database.insert("Book", null, values);
        values.clear();

        values.put("bookId", 7);
        values.put("bookName", "乾坤大挪移");
        database.insert("Book", null, values);
        values.clear();

        values.put("bookId", 8);
        values.put("bookName", "易筋经");
        database.insert("Book", null, values);
        values.clear();

        values.put("bookId", 9);
        values.put("bookName", "六脉神剑");
        database.insert("Book", null, values);
        values.clear();

        values.put("bookId", 10);
        values.put("bookName", "黯然销魂掌");
        database.insert("Book", null, values);
        values.clear();

        values.put("bookId", 11);
        values.put("bookName", "九阴白骨爪");
        database.insert("Book", null, values);
        values.clear();

        values.put("bookId", 12);
        values.put("bookName", "独孤九剑");
        database.insert("Book", null, values);
    }
}


```

同样代码很简单，先在`onCreate()`方法中获取`database`对象，判断如果`database`没有数据，则调用`addBooksToDB()`方法添加十几本书（加上这个判断就不会出现每进入一次服务端应用，数据库就多一组重复的数据）。

到此，服务端已经写完了，下面开始写客户端。

客户端实现详细步骤
=========

客户端的实现前三步和服务端一样，这里依旧贴上步骤

#### 1、新建项目 AIDLClient

注：客户端的包名是`com.glong.aidlclient`

![](http://upload-images.jianshu.io/upload_images/10618242-c81c9f800e8dee82.png)

#### 2、新建 Book.java，实现可序列化，提供 get/set 方法

和服务端`Book.java`保持一致

`Book.java`

```
package com.glong.aidlclient;

import android.os.Parcel;
import android.os.Parcelable;

public class Book implements Parcelable {
    private int bookId;
    private String bookName;

    public Book() {
    }

    public Book(int bookId, String bookName) {
        this.bookId = bookId;
        this.bookName = bookName;
    }

    @Override
    public int describeContents() {
        return 0;
    }

    @Override
    public void writeToParcel(Parcel dest, int flags) {
        dest.writeInt(this.bookId);
        dest.writeString(this.bookName);
    }

    protected Book(Parcel in) {
        this.bookId = in.readInt();
        this.bookName = in.readString();
    }

    public static final Parcelable.Creator<Book> CREATOR = new Parcelable.Creator<Book>() {
        @Override
        public Book createFromParcel(Parcel source) {
            return new Book(source);
        }

        @Override
        public Book[] newArray(int size) {
            return new Book[size];
        }
    };

    public int getBookId() {
        return bookId;
    }

    public void setBookId(int bookId) {
        this.bookId = bookId;
    }

    public String getBookName() {
        return bookName;
    }

    public void setBookName(String bookName) {
        this.bookName = bookName;
    }
}


```

#### 3、新建 AIDL Folder

![](http://upload-images.jianshu.io/upload_images/10618242-ad1fc1d95750ff07.png)

#### 4、在 aidl 目录下新建 IBookManager.aidl

这里需要注意两点：

*   客户端的`IBookManager.aidl`必须跟服务端的`IBookMananger.aidl`包名相同，否则客户端运行连接服务端时会报 SecturityException；
    
*   客户端的`IBookManager.aidl`必须跟服务端的`IBookManager.aidl`拥有相同的方法和方法数，否则在通过 AIDL 进行 IPC 时会出现乱码等现象；
    
    总之，客户端和服务端的`IBookManager.aidl`保持一致（_除 import 的 Parcelable 对象部分_），包括包名和 aidl 文件夹一下的路径，所以这一步的创建`IBookManager.aidl`和服务端的创建稍有不同，aidl 文件夹下 → New → Package
    

![](http://upload-images.jianshu.io/upload_images/10618242-80f5a76dd7e5215b.png)

然后输入服务端`IBookManager.aidl`的包名，点击 OK

![](http://upload-images.jianshu.io/upload_images/10618242-adbae6e37fe79cfc.png)

接着在该包下创建`IBookMananger.aidl`，New → FIle

![](http://upload-images.jianshu.io/upload_images/10618242-4d2070835d3f2ce4.png)

![](http://upload-images.jianshu.io/upload_images/10618242-4fb46358e643b2b4.png)

接下来实现`IBookManager.aidl`，或者 copy 服务端`IBookManager.aidl`代码，修改下 import 的包即可（_import 的包和当前工程的包对应_）

`IBookManager.aidl`

```
package com.glong.aidlservice;

import com.glong.aidlclient.Book;
interface IBookManager{

    /*
     * 获取Book列表
     */
    List<Book> getBookList();

    /*
     *添加Book
     */
     List<Book> addBook(in Book book);

}


```

可以看到`IBookManager.aidl`文件包名和服务端包名一样  
值得注意的是`import com.glong.aidlclient.Book`和服务端的略有区别，`import`的包名和当前工程包名须对应，所以接下来创建的`Book.aidl`在`com.glong.aidlclient`包下

#### 5、在 aidl 目录下新建 com.glong.aidlclient 包，并在该包下新建 Book.aidl

先新建 Package `com.glong.aidlclient`

![](http://upload-images.jianshu.io/upload_images/10618242-ea34cb2081d33206.png)

![](http://upload-images.jianshu.io/upload_images/10618242-3e12d308167b9375.png) 新建 com.glong.aidlclient

接着在`com.glong.aidlclient`包下新建`Book.aidl`，新建之后工程目录是这样的

![](http://upload-images.jianshu.io/upload_images/10618242-97099f1e50adc8dc.png)

  
`Book.aidl`

```
package com.glong.aidlclient;

parcelable Book;


```

#### 6、Build 编译项目

这一步跟服务端一样，不过多赘述

#### 7、客户端实现

*   首先绑定远程服务
    
*   绑定成功后将服务端返回的 Binder 对象转换成 AIDL 接口
    
*   通过这个 AIDL 接口调用远程方法
    

`MainActivity.java`

```
package com.glong.aidlclient;

import android.content.ComponentName;
import android.content.Context;
import android.content.Intent;
import android.content.ServiceConnection;
import android.os.IBinder;
import android.os.RemoteException;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.widget.Toast;

import com.glong.aidlservice.IBookManager;

import java.util.List;

public class MainActivity extends AppCompatActivity {

    private static final String TAG = "long.MainActivity";
    private static final String SERVICE_ACTION = "com.glong.aidlservice.action.BookManagerService";
    private static final String SERVICE_PACKAGE = "com.glong.aidlservice";

    private IBookManager mBookManager;

    ServiceConnection connection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName componentName, IBinder iBinder) {
            mBookManager = IBookManager.Stub.asInterface(iBinder);
            try {
                List<Book> books = mBookManager.getBookList();
                if (books == null || books.size() == 0) return;
                for (int i = 0; i < books.size(); i++) {
                    Book book = books.get(i);
                    Log.i(TAG, i + ".[bookId:" + book.getBookId() + ",bookName:" + book.getBookName() + "]");
                }
            } catch (RemoteException e) {
                e.printStackTrace();
                Toast.makeText(MainActivity.this, "获取Book列表失败", Toast.LENGTH_SHORT).show();
            }
        }

        @Override
        public void onServiceDisconnected(ComponentName componentName) {

        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        bindService();
    }

    /**
     * 连接远程服务
     */
    private void bindService() {
        Intent intent = new Intent();
        intent.setPackage(SERVICE_PACKAGE);
        intent.setAction(SERVICE_ACTION);
        bindService(intent, connection, Context.BIND_AUTO_CREATE);
    }
}


```

代码依旧很简单，这里使用隐式的方式绑定远程服务，在`onServiceConnected()`回调方法中获取服务端 Book 列表并打印出来。

**到这里，服务端的实现也完成了，以上是整个 AIDL 实现步骤，后面内容属于验证和扩展可以不用关心**

验证
==

*   run AIDLService 工程
*   run AIDLClient 工程
*   查看日志

![](http://upload-images.jianshu.io/upload_images/10618242-78d44282f13fc024.png) 验证

我们调用远程服务的接口成功了!

扩展
==

*   客户端调用远程服务`getBookList()`接口返回数据后，展示数据；
*   客户端调用`addBook(Book book)`接口增加图书

修改`activity_main.xml`

```
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">


    <Button
        android:id="@+id/btn_book_list"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="获取Book列表"
        android:background="#603300cc"
        android:textSize="22sp" />

    <Button
        android:id="@+id/btn_book_add"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="right|bottom"
        android:background="#903300cc"
        android:text="添加图书"
        android:textSize="22sp"
        android:visibility="gone" />

    <android.support.v7.widget.RecyclerView
        android:id="@+id/recycler_view_book_list"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:visibility="gone" />


</FrameLayout>


```

两个 Button 分别对应获取图书列表功能和添加图书功能，RecyclerView 展示图书列表

#### 1、实现展示图书列表功能

`MyAdapter.java`

```
package com.glong.aidlclient;

import android.graphics.Color;
import android.support.annotation.NonNull;
import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

import java.util.List;

public class MyAdapter extends RecyclerView.Adapter<MyAdapter.ViewHolder> {

    private List<Book> mBooks;

    public MyAdapter(List<Book> books) {
        mBooks = books;
    }

    @NonNull
    @Override
    public ViewHolder onCreateViewHolder(@NonNull ViewGroup viewGroup, int i) {
        View view = LayoutInflater.from(viewGroup.getContext()).inflate(R.layout.item_view, viewGroup, false);
        //这里给view设置一下间隙
        RecyclerView.LayoutParams lp = (RecyclerView.LayoutParams) view.getLayoutParams();
        lp.setMargins(0, 10, 0, 10);
        view.setBackgroundColor(Color.YELLOW);
        return new ViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull ViewHolder viewHolder, int i) {
        Book book = mBooks.get(i);
        viewHolder.bookIdTextView.setText("\t\t" + (i + 1) + "." + "《" + book.getBookName() + "》");
        viewHolder.bookNameTextView.setText("ID:" + String.format("%08d", book.getBookId()));
    }

    @Override
    public int getItemCount() {
        return mBooks.size();
    }

    static class ViewHolder extends RecyclerView.ViewHolder {

        TextView bookIdTextView;
        TextView bookNameTextView;

        public ViewHolder(@NonNull View itemView) {
            super(itemView);
            bookIdTextView = itemView.findViewById(R.id.tv_book_id);
            bookNameTextView = itemView.findViewById(R.id.tv_book_name);
        }
    }
}


```

`item_view.xml`

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal">

    <TextView
        android:id="@+id/tv_book_id"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:gravity="center_vertical"
        android:minHeight="?android:attr/listPreferredItemHeightSmall"
        android:paddingEnd="?android:attr/listPreferredItemPaddingEnd"
        android:paddingStart="?android:attr/listPreferredItemPaddingStart"
        android:textAppearance="?android:attr/textAppearanceListItemSmall"
        tools:ignore="RtlCompat" />

    <TextView
        android:id="@+id/tv_book_name"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:gravity="center_vertical"
        android:minHeight="?android:attr/listPreferredItemHeightSmall"
        android:paddingEnd="?android:attr/listPreferredItemPaddingEnd"
        android:paddingStart="?android:attr/listPreferredItemPaddingStart"
        android:textAppearance="?android:attr/textAppearanceListItemSmall" />

</LinearLayout>


```

修改`MainAcitivty.java`

```
package com.glong.aidlclient;

import android.content.ComponentName;
import android.content.Context;
import android.content.Intent;
import android.content.ServiceConnection;
import android.os.Bundle;
import android.os.IBinder;
import android.os.RemoteException;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.DividerItemDecoration;
import android.support.v7.widget.LinearLayoutManager;
import android.support.v7.widget.RecyclerView;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.glong.aidlservice.IBookManager;

import java.util.ArrayList;
import java.util.List;

public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    private static final String TAG = "long.MainActivity";
    private static final String SERVICE_ACTION = "com.glong.aidlservice.action.BookManagerService";
    private static final String SERVICE_PACKAGE = "com.glong.aidlservice";

    private IBookManager mBookManager;

    private Button mBtnRequestBooks;
    private Button mBtnAddBook;
    private RecyclerView mRecyclerView;

    private List<Book> mBooks = new ArrayList<>();
    private MyAdapter mAdapter;

    private AddBookDialog mDialog;

    ServiceConnection connection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName componentName, IBinder iBinder) {
            mBookManager = IBookManager.Stub.asInterface(iBinder);
            try {
                List<Book> list = mBookManager.getBookList();
                if (list != null && list.size() > 0) {
                    mBooks.addAll(list);
                }
                mRecyclerView.setVisibility(View.VISIBLE);
                mBtnRequestBooks.setVisibility(View.GONE);
                mBtnAddBook.setVisibility(View.VISIBLE);
                mAdapter.notifyDataSetChanged();
            } catch (RemoteException e) {
                e.printStackTrace();
                Toast.makeText(MainActivity.this, "获取Book列表失败", Toast.LENGTH_SHORT).show();
            }
        }

        @Override
        public void onServiceDisconnected(ComponentName componentName) {
            Toast.makeText(MainActivity.this, "onServiceDisconnected!", Toast.LENGTH_SHORT).show();
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initViews();
        initRecyclerView();
    }

    private void initViews() {
        mBtnAddBook = findViewById(R.id.btn_book_add);
        mBtnRequestBooks = findViewById(R.id.btn_book_list);

        mBtnRequestBooks.setOnClickListener(this);
        mBtnAddBook.setOnClickListener(this);
    }

    private void initRecyclerView() {
        mRecyclerView = findViewById(R.id.recycler_view_book_list);
        LinearLayoutManager linearLayoutManager = new LinearLayoutManager(this);
        mRecyclerView.setLayoutManager(linearLayoutManager);
        mRecyclerView.addItemDecoration(new DividerItemDecoration(this, DividerItemDecoration.VERTICAL));
        mAdapter = new MyAdapter(mBooks);
        mRecyclerView.setAdapter(mAdapter);
    }

    /**
     * 连接远程服务
     */
    private void bindService() {
        Intent intent = new Intent();
        intent.setPackage(SERVICE_PACKAGE);
        intent.setAction(SERVICE_ACTION);
        bindService(intent, connection, Context.BIND_AUTO_CREATE);
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()) {
            case R.id.btn_book_list:
                requestBookList();
                break;
            case R.id.btn_book_add:
                showAddBookDialog();
                break;
            default:
                break;
        }
    }

    /**
     * show 添加图书的Dialog
     */
    private void showAddBookDialog() {
        if (mDialog == null)
            mDialog = new AddBookDialog(this, new AddBookDialog.OnDialogDismissCallback() {
                @Override
                public void onOkButtonPressed(int bookId, String bookName) {
                    Log.d(TAG, "Ok button pressed!");
                    addBook(bookId, bookName);
                }

                @Override
                public void onCancelButtonPressed() {

                }
            });
        mDialog.show();
    }

    /**
     * 添加Book{@link Book}
     */
    private void addBook(int bookId, String bookName) {
        Log.i(TAG, "client add Book starting...id:" + bookId + " ,bookName:" + bookName);
        if (mBookManager == null) {
            Log.i(TAG, "mBookManager == null!!");
            return;
        }
        try {
            Book book = new Book(bookId, bookName);
            mBookManager.addBook(book);

            mBooks.add(book);
            mAdapter.notifyDataSetChanged();
            Toast.makeText(this, "Add succeed", Toast.LENGTH_SHORT).show();
        } catch (RemoteException e) {
            e.printStackTrace();
            Toast.makeText(this, "添加失败", Toast.LENGTH_SHORT).show();
        }
    }

    /**
     * 获取Book列表
     */
    private void requestBookList() {
        bindService();
    }
}


```

#### 2、实现向服务端添加图书功能

功能很简单，点击 “添加图书” 按钮后弹出 dialog，输入 bookId 和 bookName，点击“确定”

`AddBookDialog.java`

```
package com.glong.aidlclient;

import android.app.Activity;
import android.app.Dialog;
import android.content.Context;
import android.os.Bundle;
import android.support.annotation.NonNull;
import android.text.TextUtils;
import android.view.View;
import android.widget.AutoCompleteTextView;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

public class AddBookDialog extends Dialog implements View.OnClickListener {

    private OnDialogDismissCallback callback;
    private Button mBtnOk;
    private Button mBtnCancel;
    private EditText mBookIdTextView;
    private AutoCompleteTextView mBookNameTextView;

    public AddBookDialog(@NonNull Context context, OnDialogDismissCallback callback) {
        this(context, R.style.dialog, callback);
    }

    public AddBookDialog(@NonNull Context context, int themeResId, OnDialogDismissCallback callback) {
        super(context, themeResId);
        this.callback = callback;
        setOwnerActivity((Activity) context);
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.dialog_layout);
        setCanceledOnTouchOutside(false);

        mBtnOk = findViewById(R.id.button_ok);
        mBtnCancel = findViewById(R.id.button_cancel);
        mBookIdTextView = findViewById(R.id.edit_book_id);
        mBookNameTextView = findViewById(R.id.edit_book_name);

        mBtnOk.setOnClickListener(this);
        mBtnCancel.setOnClickListener(this);
    }

    @Override
    public void show() {
        super.show();
        mBookIdTextView.setText("");
        mBookNameTextView.setText("");
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()) {
            case R.id.button_ok:
                pressedOkBtn();
                break;
            case R.id.button_cancel:
                pressedCancelBtn();
                break;
            default:
                break;
        }
    }

    private void pressedCancelBtn() {
        dismiss();
    }

    private void pressedOkBtn() {
        String idString = mBookIdTextView.getText().toString();
        String name = mBookNameTextView.getText().toString();

        if (idString.isEmpty()) {
            Toast.makeText(getContext(), "ID不能为空", Toast.LENGTH_SHORT).show();
            return;
        }
        if (!idString.matches("\\d+")) {
            Toast.makeText(getContext(), "ID格式不正确", Toast.LENGTH_SHORT).show();
            return;
        }
        if (TextUtils.isEmpty(name)) {
            Toast.makeText(getContext(), "书名不能为空", Toast.LENGTH_SHORT).show();
            return;
        }
        dismiss();
        if (callback == null) return;
        int id = Integer.parseInt(idString);
        callback.onOkButtonPressed(id, name);
    }

    public interface OnDialogDismissCallback {
        /**
         * 通过点击确定按钮返回的
         *
         * @param bookId
         * @param bookName
         */
        void onOkButtonPressed(int bookId, String bookName);

        /**
         * 通过点击取消按钮返回的
         */
        void onCancelButtonPressed();
    }
}


```

Dialog 的布局文件`dialog_layout.xml`

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFCC99"
    android:orientation="vertical">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:layout_marginTop="20dp"
        android:text="添加图书"
        android:textColor="#60000000" />

    <LinearLayout
        android:layout_width="300dp"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:layout_marginTop="20dp"
        android:orientation="horizontal"
        android:paddingLeft="10dp"
        android:paddingRight="10dp">

        <TextView
            android:layout_width="80dp"
            android:layout_height="wrap_content"
            android:lines="1"
            android:text="Book ID:"
            android:textColor="#60000000" />

        <EditText
            android:id="@+id/edit_book_id"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:hint="请输入ID"
            android:inputType="number" />
    </LinearLayout>

    <LinearLayout
        android:layout_width="300dp"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:layout_marginTop="20dp"
        android:orientation="horizontal"
        android:paddingLeft="10dp"
        android:paddingRight="10dp">

        <TextView
            android:layout_width="80dp"
            android:layout_height="wrap_content"
            android:lines="1"
            android:text="Book Name:"
            android:textColor="#60000000" />

        <AutoCompleteTextView
            android:id="@+id/edit_book_name"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:hint="请输入Name"
            android:inputType="text" />

    </LinearLayout>

    <FrameLayout
        android:layout_width="300dp"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:layout_marginBottom="20dp"
        android:layout_marginTop="20dp">

        <Button
            android:id="@+id/button_ok"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="left|center_vertical"
            android:layout_marginLeft="50dp"
            android:text="确定" />

        <Button
            android:id="@+id/button_cancel"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="right|center_vertical"
            android:layout_marginRight="50dp"
            android:text="取消" />
    </FrameLayout>
</LinearLayout>


```

现在重新 run AIDLClient 工程效果如下:

![](http://upload-images.jianshu.io/upload_images/10618242-9b0caea9b6b11f4d.gif) c8b51ab6-f17a-401a-8541-dff13d5c80fb.gif

[源码下载](https://github.com/krouky/AIDLSample)
============================================