# 安卓功能案例


这篇文章中，将常见的一些安卓功能实现做一个简单的说明，以便在以后开发中随时查看。

## 动画

### alpha动画

可以通过设定对象的alpha动画来实现对象的透明度，如果对象原先的alpha设定为0，则可以逐渐显示

```java

ImageView img = (ImageView)findViewById(R.id.xxx);
img.animate()
    .alpha(0f)
    .setDuration(2000);
```





### 位置动画

设定x和y的坐标来进行位置上的变化，`translationYBy, translationXBy`

```java
img.animate()
    .tanslationYBy(1000f)
    .setDuration(2000);
```





### 旋转动画

通过`rotation(xxxf)`来完成旋转动画

### 缩放动画

通过`scaleX, scaleY`来完成缩放动画

### 联和动画

以上的动画可以合并在一起，完成特殊的动画动能。

```java
img.animate()
    .translationXBy(1000f)
    .translationYBy(1000f)
    .rotationBy(3600)
    .setDuration(2000);
```





## 播放视频

1. 将视频文件放置在`res/raw`下面
2. 添加`VideoView`控件
3. 关键是找到视频的路径
```java
videoView.setVideoPath = "android.resource://" + getPackageName() + "/" + R.raw.demovideo);
videoView.start();
```

4. 为视频播放添加控制
```java
MediaController media = new MediaController(this);
media.setAnchorView(videoView);
videoView.setMediaController(media);
...
```




## 播放音频

1. 将音频文件放置在`res/raw`下面
2. 使用`MediaPlayer`播放音频
```java
MediaPlayer mplayer = MediaPlayer.create(this, R.raw.mp3);
mplayer.start();
```

3. 可以调用`mplayer.pause() mplayer.stop()`来暂停和停止播放。

### 控制音量

控制音量主要是通过`AudioManager`来进行的

```java
//获取服务
AudioManager audioManager = (AudioManager)getSystemService(Context.AUDIO_SERVICE);
//获取音乐播放最大音量
int maxVolume = audioManger.getStreamMaxVolume(AudioManager.STREAM_MUSIC);
//获取当前系统播放音量
int currentVolume = audioManager.getStreamVolume(AudioManager.STREAM_MUSIC);

//可以通过SeekBar来进行音量的设置
audioManager.setStreamVolume(AudioManager.STREAM_MUSIC, progress.value, 0);
```





### 控制播放进度

可以通过`MediaPlayer`来获取当前播放的音乐的信息，并设置播放进度

```java
//获取音乐长度
mplayer.getDuration();

//通过定时器任务来更新进度条的位置
new Timer().scheduleAtFixedRate(new TimerTask() {
    @Override
    public void run() {
        scrubber.setProgress(mplayer.getCurrentPosition());
    }
}, 0, 100); //立即开始，每隔100毫秒调用一次

//可以拖拽进度挑来进行定位
mplayer.seekTo(progress.value);
```





## 简单的ListView

ListView是移动客户端最常见的组件，能够显示很多的条目。

```java
ListView listView = (ListView)findViewById(R.id.listView);

ArrayList<String> myFriend = new ArrayList<>();
myFriend.add("Zhang San");
myFriend.add("Li si");
myFriend.add("Wang wu");
myFriend.add("Zhao liu");
myFriend.add("Liu qi");

ArrayAdapter<String> arrayAdapter = new ArrayAdapter<String>(this, android.R.layout.simple_list_item_1, myFriend);

listView.setAdapter(arrayAdapter);
```



设定点击条目的事件

```java
//设置点击条目的事件
listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
    @Override
    public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
        Log.i("ZJL", myFriend.get(position));
    }
});
```



在设置字符串数组中，可以应用`asList`的便捷方法来进行处理，这需要静态导入`java.util.Arrays.asList`，并将`myFrind`设置为final对象

```java
import static java.util.Arrays.asList;

final ArrayList<String> myFriend = new ArrayList<String>(asList("Zhang San", "Li si", "Wang wu", "Zhao liu", "Liu qi"));
```





## 安卓中的定时任务

可以使用`Handler`和`Runnable`来构造一个定时执行的任务。

```java
//Handler 负责进行处理一个定时运行的任务
final Handler handler = new Handler();
//定义要定时运行的任务
Runnable run = new Runnable() {
    //实际运行的代码在run方法中
    @Override
    public void run() {
        //模拟实际运行的代码
        Log.i("ZJL", "a second must have passed...");
        //handler负责1秒后继续运行任务
        handler.postDelayed(this, 1000);
    }
};

//首次开始运行
handler.post(run);
```



安卓也提供了一种叫做`CountDownTimer`的基础类，来让我们完成一种倒计时的任务。

```java
//倒计时10秒，每秒触发任务
 new CountDownTimer(10000, 1000) {
     @Override
     public void onTick(long millisUntilFinished) {
         Log.i("ZJL", "Seconds left: " + millisUntilFinished / 1000);
     }

     @Override
     public void onFinish() {
         Log.i("Zjl", "Countdown Timer Finished");
     }
 }.start();
```



对CountDownTimer来说，在第一次`onTick`方法调用的时候，实际上已经经过了1秒，为了精确定时，我们可以设定总时间稍微多一些毫秒。

```java
new CountDownTimer(10000 + 100, 1000)
```





## 显示和隐藏控件

```plain
view.setVisibility(View.VISIBLE);//显示控件
view.setVisibility(View.INVISIBEL);//隐藏控件，但任然占用空间
```





## 安卓中的异步任务

安卓中的异步任务可以简单的通过`AsyncTask`来完成。在`MainActivity`中添加内部类定义

```java
//第一个参数表示异步类的调用参数类型，第二个表示异步类中间结果的返回类型，第三个参数表示最终的结果返回类型
class DownloadTask extends AsyncTask<String, Void, String> { 
    @Override
    protected String doInBackground(String... params) {
        StringBuilder result = new StringBuilder();

        try {
            URL url = new URL(params[0]);
            HttpURLConnection httpURLConnection = (HttpURLConnection) url.openConnection();

            InputStreamReader isr = new InputStreamReader(httpURLConnection.getInputStream());
            int data;
            while ((data = isr.read()) != -1) {
                result.append((char)data);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }

        Log.i("ZJL", "return result");

        return result.toString();
    }
}
```



在`MainActivity`中可以调用我们的异步类

```java
DownloadTask task = new DownloadTask();
String result = null;
try {
    result = task.execute("http://www.ldxy.cn").get();
} catch (InterruptedException | ExecutionException e) {
    e.printStackTrace();
}

Log.i("ZJL", result);
```



因为涉及到网络访问，需要在`AndroidManifest.xml`中声明权限

```xml
<uses-permission android:name="android.permission.INTERNET"/>
```





### 利用异步任务下载图像

使用异步任务也可以完成图像的下载

```java
//注意返回值是bitmap
class DownloadTask extends AsyncTask<String, Void, Bitmap> {
    @Override
    protected Bitmap doInBackground(String... params) {
        try {
            HttpURLConnection httpURLConnection = (HttpURLConnection) (new URL(params[0])).openConnection();
            return BitmapFactory.decodeStream(httpURLConnection.getInputStream());
        } catch (IOException e) {
            e.printStackTrace();
        }

        return null;
    }
}
```



调用的方式是：

```java
ImageView imageView = (ImageView) findViewById(R.id.imageView);
Bitmap myImage = null;
try {
    myImage = (new DownloadTask()).execute("图像地址").get();
    imageView.setImageBitmap(myImage);
} catch (InterruptedException | ExecutionException e) {
    e.printStackTrace();
}
```





## 处理JSON数据

安卓可以使用`JSONObject`来处理网络获取到的JSON数据。

```java
JSONObject jsonObject = new JSONObject(result);
jsonObject.getString("xxxKey");

JSONArray arr = new JSONArray(jsonString);
for (int i = 0; i < arr.length(); i++) {
    JSONObject item = arr.getJSONObject(i);
    //process item
}
```


---

> 作者: [张景林](https://zhangjinglin.github.io/about/)  
> URL: https://zhangjinglin.github.io/2016/02/android-example/  

