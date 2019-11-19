# Android入门

## 1. 项目结构

> AndroidDemo01
>
>    --src(源码文件夹)
>
> ​            MainActivity.java:主界面类
>
>    --gen (自动生成的源码文件夹)
>
> ​           R.java  :对应res文件夹
>
> ​           drawble:图片
>
> ​            layout:布局
>
> ​            string:字符串
>
> ​    --res
>
> ​              drawable-xxx:图片文件夹，为了适应不同分辨率的手机
>
> ​             layout:界面的布局文件，功能类似html
>
> ​            values:常量文件夹
>
> ​                        strings.xml ：包含固定字符串,在布局中引用 @string/名字
>

## 2. Activity 组件

> 用来提供一个能让用户操作并与之交互的界面

## 3. Intent

> Intent是Activity，Service,BroadcastReceiver这三个应用组件的信使
>
> 意图还可以携带数据,注意Intent并不是安卓的四大组件之一。
>
> * 显示意图：明确指定的目标组件的意图
>   * 创建对象：`Intent(Context context,Class clazz)`
>   * 何时使用：当操作当前自己应用的组件时使用
> * 隐式意图：没有明确指定目标组件的意图
>   * 创建对象：`Intent(String action)`
>   * 何时使用：当操作其他应用的组件时使用

### 3.1 设置点击监听的2种方法

> * Activity中添加监听 `view.setonClickListener(OnClickListener listener);`
> * 布局添加监听
>   * layout中：`android:onclick="方法名"`
>   * Activity中：public void 方法名(View view)
> * 设置长按监听  `view.setOnLongClickListener(OnLongClickListener listener)`

## 4. 用户界面

## 5. 注册广播接收器

> * 方式一：配置文件注册(静态注册)
>
>   ```xml
>          <!-- 静态注册 -->
>           <receiver android:name=".MyReceiver001">
>               <intent-filter >
>                   <action android:name=".MyReceiver001.action"/>
>               </intent-filter>
>           </receiver>
>   ```
>
> * 方式二：硬编码注册(动态注册)
>
>   ```java
>               /**创建receiver对象*/
>   			MyReceiverByDncy receiver = new MyReceiverByDncy();
>   			/**创建过滤器对象*/
>   			IntentFilter filter= new IntentFilter("com.njcit.demo.MyReceiverByStatic.action");
>   			/**动态注册*/
>   			this.registerReceiver(receiver, filter);
>   ```

