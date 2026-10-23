---
title: "Android 使用ListView的下拉刷新"
date: 2016-02-09T13:36:33+00:00
url: "/2016/02/android-listview/"
draft: false
---

原文：[Android Swipe Down to Refresh ListView Tutorial](http://www.androidhive.info/2015/05/android-swipe-down-to-refresh-listview-tutorial/)

大量的Android程序都提供了下拉刷新的功能。以前我们可以使用自定义的来完成功能，现在Google已经在android.support.v4中提供了这个功能。

## 1 Android SwipeRefreshLayout

下拉刷新组件非常容易使用，在需要下拉刷新的view之外包裹SwipeRefreshLayout即可。我们使用ListView。当用户向下滑动view的时候就会触发onRefresh()方法，我们需要在这个方法里面实现我们的获取最新数据的逻辑。

```xml
<android.support.v4.widget.SwipeRefreshLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/swipe_refresh_layout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">
 
    <!-- place your view here -->
 
</android.support.v4.widget.SwipeRefreshLayout>
```





## 2 案例 JSON

这个案例显示IMDB top 250的数据。每次请求给出20个结果，需要传送`offset`参数，初始为0。

url： [http://api.androidhive.info/json/imdb_top_250.php?offset=0](http://api.androidhive.info/json/imdb_top_250.php?offset=0)

## 3 建立Android项目

- 建立项目，选择`Blank Activity`
- 打开`app`文件夹下面的`build.gradle`，添加`volley`库依赖，我们使用volley来获取JSON。

build.gradle

```java
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:22.1.0'
    compile 'com.mcxiaoke.volley:library-aar:1.0.0'
}
```




- 在 `res => values`下面的`colors.xml`中添加颜色资源。这个颜色用于设置ListView的背景颜色和影片的排名颜色。

colors.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string-array name="movie_serial_bg">
        <item>#24c6d5</item>
        <item>#57dd86</item>
        <item>#ad7dcf</item>
        <item>#ff484d</item>
        <item>#fcba59</item>
        <item>#24c6d5</item>
    </string-array>
</resources>
```




- 在项目包下新建三个包，`app, activity, helper`
- 在 `app`包下建立 `MyApplication.java`，这是单实例的应用，初始化volley核心对象。

MyApplication.java

```java
package info.androidhive.swiperefresh.app;
 
import android.app.Application;
import android.text.TextUtils;
 
import com.android.volley.Request;
import com.android.volley.RequestQueue;
import com.android.volley.toolbox.Volley;
 
/**
 * Created by Ravi on 13/05/15.
 */
 
public class MyApplication extends Application {
 
    public static final String TAG = MyApplication.class
            .getSimpleName();
 
    private RequestQueue mRequestQueue;
 
    private static MyApplication mInstance;
 
    @Override
    public void onCreate() {
        super.onCreate();
        mInstance = this;
    }
 
    public static synchronized MyApplication getInstance() {
        return mInstance;
    }
 
    public RequestQueue getRequestQueue() {
        if (mRequestQueue == null) {
            mRequestQueue = Volley.newRequestQueue(getApplicationContext());
        }
 
        return mRequestQueue;
    }
 
    public <T> void addToRequestQueue(Request<T> req, String tag) {
        req.setTag(TextUtils.isEmpty(tag) ? TAG : tag);
        getRequestQueue().add(req);
    }
 
    public <T> void addToRequestQueue(Request<T> req) {
        req.setTag(TAG);
        getRequestQueue().add(req);
    }
 
    public void cancelPendingRequests(Object tag) {
        if (mRequestQueue != null) {
            mRequestQueue.cancelAll(tag);
        }
    }
}
```




- 在`AndroidManifest.xml`中添加`MyApplication.java`类到`<application>`标记。当然还需要`INTERET`权限。

AndroidManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="info.androidhive.swiperefresh">
 
    <uses-permission android:name="android.permission.INTERNET"/>
 
    <application
        android:name=".app.MyApplication"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme">
        <activity
            android:name=".activity.MainActivity"
            android:label="@string/app_name">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
 
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
 
</manifest>
```






- 为listview 创建定制的adaper class。在`res=>layout`中建立list_row.xml。

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">
 
    <TextView
        android:id="@+id/serial"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="25dp"
        android:layout_margin="5dp"
        android:layout_alignParentLeft="true"
        android:textSize="20dp"
        android:textStyle="bold" />
 
    <TextView
        android:id="@+id/title"
        android:layout_toRightOf="@id/serial"
        android:layout_centerVertical="true"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textStyle="bold"
        android:paddingLeft="20dp"
        android:textSize="18dp" />
 
</RelativeLayout>
```




- 在 `helper`包中建立`Movie.java`，listview中的每一行都是这个模型

```java
package info.androidhive.swiperefresh.helper;
 
/**
 * Created by Ravi on 13/05/15.
 */
public class Movie {
    public int id;
    public String title;
 
    public Movie() {
    }
 
    public Movie(int id, String title) {
        this.title = title;
        this.id = id;
    }
}
```




- 在`helper`包中建立`SwipeListAdapter.java`。这是我们的custom adpater。

```java
package info.androidhive.swiperefresh.helper;
 
import android.app.Activity;
import android.content.Context;
import android.graphics.Color;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.TextView;
 
import java.util.List;
 
import info.androidhive.swiperefresh.R;
 
/**
 * Created by Ravi on 13/05/15.
 */
public class SwipeListAdapter extends BaseAdapter {
    private Activity activity;
    private LayoutInflater inflater;
    private List<Movie> movieList;
    private String[] bgColors;
 
    public SwipeListAdapter(Activity activity, List<Movie> movieList) {
        this.activity = activity;
        this.movieList = movieList;
        bgColors = activity.getApplicationContext().getResources().getStringArray(R.array.movie_serial_bg);
    }
 
    @Override
    public int getCount() {
        return movieList.size();
    }
 
    @Override
    public Object getItem(int location) {
        return movieList.get(location);
    }
 
    @Override
    public long getItemId(int position) {
        return position;
    }
 
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
 
        if (inflater == null)
            inflater = (LayoutInflater) activity
                    .getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        if (convertView == null)
            convertView = inflater.inflate(R.layout.list_row, null);
 
        TextView serial = (TextView) convertView.findViewById(R.id.serial);
        TextView title = (TextView) convertView.findViewById(R.id.title);
 
        serial.setText(String.valueOf(movieList.get(position).id));
        title.setText(movieList.get(position).title);
 
        String color = bgColors[position % bgColors.length];
        serial.setBackgroundColor(Color.parseColor(color));
 
        return convertView;
    }
 
}
```




- 修改 `activity_main.xml`。使用listview来显示电影的列表，在外边包裹`SwipeRefreshLayout`完成滑动刷新。

```xml
<android.support.v4.widget.SwipeRefreshLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/swipe_refresh_layout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">
 
    <ListView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:id="@+id/listView">
 
    </ListView>
 
</android.support.v4.widget.SwipeRefreshLayout>
```




- 打开`MainActivity.java`进行下面的修改完成下来刷新。<ul> <li>实现 `SwipeRefreshLayout.OnRefreshListener`中的`onRefresh()`方法
- 实现`fetchMovie()`方法获取volley的JSON数组修改列表视图
- 在`onRefresh()`方法中调用`fetchMovie()`

</li>
</ul>

```java

package info.androidhive.swiperefresh.activity;
 
import android.os.Bundle;
import android.support.v4.widget.SwipeRefreshLayout;
import android.support.v7.app.ActionBarActivity;
import android.util.Log;
import android.widget.ListView;
import android.widget.Toast;
 
import com.android.volley.Response;
import com.android.volley.VolleyError;
import com.android.volley.toolbox.JsonArrayRequest;
 
import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;
 
import java.util.ArrayList;
import java.util.List;
 
import info.androidhive.swiperefresh.R;
import info.androidhive.swiperefresh.app.MyApplication;
import info.androidhive.swiperefresh.helper.Movie;
import info.androidhive.swiperefresh.helper.SwipeListAdapter;
 
 
public class MainActivity extends ActionBarActivity implements SwipeRefreshLayout.OnRefreshListener {
 
    private String TAG = MainActivity.class.getSimpleName();
 
    private String URL_TOP_250 = "http://api.androidhive.info/json/imdb_top_250.php?offset=";
 
    private SwipeRefreshLayout swipeRefreshLayout;
    private ListView listView;
    private SwipeListAdapter adapter;
    private List<Movie> movieList;
 
    // initially offset will be 0, later will be updated while parsing the json
    private int offSet = 0;
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
 
        listView = (ListView) findViewById(R.id.listView);
        swipeRefreshLayout = (SwipeRefreshLayout) findViewById(R.id.swipe_refresh_layout);
 
        movieList = new ArrayList<>();
        adapter = new SwipeListAdapter(this, movieList);
        listView.setAdapter(adapter);
 
        swipeRefreshLayout.setOnRefreshListener(this);
 
        /**
         * Showing Swipe Refresh animation on activity create
         * As animation won't start on onCreate, post runnable is used
         */
        swipeRefreshLayout.post(new Runnable() {
                                    @Override
                                    public void run() {
                                        swipeRefreshLayout.setRefreshing(true);
 
                                        fetchMovies();
                                    }
                                }
        );
 
    }
 
    /**
     * This method is called when swipe refresh is pulled down
     */
    @Override
    public void onRefresh() {
        fetchMovies();
    }
 
    /**
     * Fetching movies json by making http call
     */
    private void fetchMovies() {
 
        // showing refresh animation before making http call
        swipeRefreshLayout.setRefreshing(true);
 
        // appending offset to url
        String url = URL_TOP_250 + offSet;
 
        // Volley's json array request object
        JsonArrayRequest req = new JsonArrayRequest(url,
                new Response.Listener<JSONArray>() {
                    @Override
                    public void onResponse(JSONArray response) {
                        Log.d(TAG, response.toString());
 
                        if (response.length() > 0) {
 
                            // looping through json and adding to movies list
                            for (int i = 0; i < response.length(); i++) {
                                try {
                                    JSONObject movieObj = response.getJSONObject(i);
 
                                    int rank = movieObj.getInt("rank");
                                    String title = movieObj.getString("title");
 
                                    Movie m = new Movie(rank, title);
 
                                    movieList.add(0, m);
 
                                    // updating offset value to highest value
                                    if (rank >= offSet)
                                        offSet = rank;
 
                                } catch (JSONException e) {
                                    Log.e(TAG, "JSON Parsing error: " + e.getMessage());
                                }
                            }
 
                            adapter.notifyDataSetChanged();
                        }
 
                        // stopping swipe refresh
                        swipeRefreshLayout.setRefreshing(false);
 
                    }
                }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                Log.e(TAG, "Server Error: " + error.getMessage());
 
                Toast.makeText(getApplicationContext(), error.getMessage(), Toast.LENGTH_LONG).show();
 
                // stopping swipe refresh
                swipeRefreshLayout.setRefreshing(false);
            }
        });
 
        // Adding request to request queue
        MyApplication.getInstance().addToRequestQueue(req);
    }
 
}
```





#### 测试运行

## 4 PHP class 生成 JSON

```php
<?php
 
// sleep for 2 sec show that the androd swipe refresh will be visible for sometime
sleep(2);
 
// all top 250 movies
$movies = array("The Shawshank Redemption", "The Godfather", "The Godfather: Part II", "The Dark Knight", "Pulp Fiction", "Schindler's List", "12 Angry Men", "The Good, the Bad and the Ugly", "The Lord of the Rings: The Return of the King", "Fight Club", "The Lord of the Rings: The Fellowship of the Ring", "Star Wars: Episode V - The Empire Strikes Back", "Forrest Gump", "Inception", "One Flew Over the Cuckoo's Nest", "The Lord of the Rings: The Two Towers", "Goodfellas", "The Matrix", "Star Wars", "Seven Samurai", "City of God", "Se7en", "The Usual Suspects", "The Silence of the Lambs", "It's a Wonderful Life", "Interstellar", "Léon: The Professional", "Life Is Beautiful", "Once Upon a Time in the West", "Casablanca", "American History X", "Saving Private Ryan", "Spirited Away", "Raiders of the Lost Ark", "City Lights", "Psycho", "Rear Window", "The Intouchables", "Whiplash", "Modern Times", "The Green Mile", "Terminator 2: Judgment Day", "Memento", "The Pianist", "The Departed", "Apocalypse Now", "Gladiator", "Sunset Blvd.", "Dr. Strangelove or: How I Learned to Stop Worrying and Love the Bomb", "Back to the Future", "Alien", "The Prestige", "The Lion King", "The Great Dictator", "The Lives of Others", "Cinema Paradiso", "Django Unchained", "The Shining", "Paths of Glory", "The Dark Knight Rises", "American Beauty", "WALL·E", "North by Northwest", "Aliens", "Citizen Kane", "Grave of the Fireflies", "Vertigo", "M", "Oldboy", "Das Boot", "Amélie", "Princess Mononoke", "Star Wars: Episode VI - Return of the Jedi", "Once Upon a Time in America", "Toy Story 3", "Reservoir Dogs", "A Clockwork Orange", "Braveheart", "Taxi Driver", "Double Indemnity", "Witness for the Prosecution", "Requiem for a Dream", "To Kill a Mockingbird", "Lawrence of Arabia", "Eternal Sunshine of the Spotless Mind", "Full Metal Jacket", "Bicycle Thieves", "The Sting", "Singin' in the Rain", "Amadeus", "Monty Python and the Holy Grail", "Snatch.", "2001: A Space Odyssey", "For a Few Dollars More", "Rashomon", "L.A. Confidential", "The Kid", "All About Eve", "The Apartment", "Inglourious Basterds", "Toy Story", "The Treasure of the Sierra Madre", "A Separation", "Indiana Jones and the Last Crusade", "Yojimbo", "The Third Man", "Some Like It Hot", "Metropolis", "Batman Begins", "Unforgiven", "Scarface", "Like Stars on Earth", "Raging Bull", "Up", "3 Idiots", "Downfall", "Chinatown", "The Great Escape", "Die Hard", "The Hunt", "On the Waterfront", "Heat", "Mr. Smith Goes to Washington", "Pan's Labyrinth", "Good Will Hunting", "The Bridge on the River Kwai", "My Neighbor Totoro", "Ikiru", "The Seventh Seal", "The Gold Rush", "Ran", "Wild Strawberries", "The General", "Blade Runner", "The Elephant Man", "Lock, Stock and Two Smoking Barrels", "The Secret in Their Eyes", "The Wolf of Wall Street", "Casino", "Gran Torino", "Howl's Moving Castle", "Warrior", "The Big Lebowski", "V for Vendetta", "Rebecca", "The Bandit", "Gone Girl", "The Deer Hunter", "Judgment at Nuremberg", "Cool Hand Luke", "How to Train Your Dragon", "It Happened One Night", "Fargo", "A Beautiful Mind", "Gone with the Wind", "Trainspotting", "Into the Wild", "Rush", "Dial M for Murder", "The Maltese Falcon", "The Sixth Sense", "Mary and Max", "Finding Nemo", "The Thing", "The Wages of Fear", "Hotel Rwanda", "No Country for Old Men", "Incendies", "Rang De Basanti", "Kill Bill: Vol. 1", "Platoon", "Life of Brian", "Butch Cassidy and the Sundance Kid", "Network", "A Wednesday", "Munna Bhai M.B.B.S.", "Touch of Evil", "There Will Be Blood", "12 Years a Slave", "Annie Hall", "The 400 Blows", "Stand by Me", "The Princess Bride", "Persona", "The Grand Budapest Hotel", "Amores Perros", "Ben-Hur", "Diabolique", "In the Name of the Father", "The Grapes of Wrath", "Million Dollar Baby", "Sin City", "Hachi: A Dog's Tale", "Nausicaä of the Valley of the Wind", "The Wizard of Oz", "The Best Years of Our Lives", "Gandhi", "The Avengers", "The Bourne Ultimatum", "Donnie Darko", "Shutter Island", "Stalker", "8½", "Guardians of the Galaxy", "Strangers on a Train", "Infernal Affairs", "Twelve Monkeys", "Fanny and Alexander", "Before Sunrise", "Boyhood", "Jaws", "The Imitation Game", "The Battle of Algiers", "The Terminator", "High Noon", "Groundhog Day", "Harry Potter and the Deathly Hallows: Part 2", "Memories of Murder", "The King's Speech", "Ip Man", "Monsters, Inc.", "Notorious", "Rocky", "Dog Day Afternoon", "Barry Lyndon", "La Haine", "The Truman Show", "Who's Afraid of Virginia Woolf?", "A Fistful of Dollars", "Dil Chahta Hai", "The Night of the Hunter", "Pirates of the Caribbean: The Curse of the Black Pearl", "Lagaan: Once Upon a Time in India", "Castle in the Sky", "Jurassic Park", "X-Men: Days of Future Past", "La Strada", "The Help", "Roman Holiday", "Wild Tales", "The Big Sleep", "Spring, Summer, Fall, Winter... and Spring", "Le Samouraï", "Prisoners", "Underground", "The Graduate", "Paris, Texas", "Solaris", "Three Colors: Red", "Papillon");
 
// reading offset from get parameter
$offset = isset($_GET['offset']) && $_GET['offset'] != '' ? $_GET['offset'] : 0;
 
// page limit
$limit = 20;
 
 
$movies_array = array();
 
// loop through page movies
for ($j = $offset; $j < $offset + $limit && $j < sizeof($movies); $j++) {
    $tmp = array();
    $tmp['rank'] = $j + 1;
    $tmp['title'] = $movies[$j];
 
    array_push($movies_array, $tmp);
}
 
// printing json response
echo json_encode($movies_array);
?>
```
