## 实验一:SimpleAdapter

main.xml

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ListView
        android:id="@+id/my_list"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

</LinearLayout>
```

simpleitem.xml

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal">


    <TextView
        android:id="@+id/names"
        android:layout_width="80dp"
        android:layout_height="30dp"
        android:paddingLeft="10dp"
        android:layout_marginTop="5dp"
        android:textColor="@color/colorPrimary"
        android:textSize="10dp" />

    <TextView
        android:layout_width="200dp"
        android:layout_height="wrap_content" />

    <ImageView
        android:id="@+id/header"
        android:layout_width="50dp"
        android:layout_height="50dp"
         />
</LinearLayout>
```

MainActivity.java

```java
package com.example.myapplication3;

import android.annotation.SuppressLint;
import android.app.Activity;
import android.os.Bundle;
import java.util.HashMap;
import java.util.Map;
import java.util.List;
import android.app.ListActivity;
import android.util.Log;
import android.view.View;
import android.widget.AdapterView;
import android.widget.SimpleAdapter;
import android.widget.ListView;
import android.widget.Toast;

import java.util.ArrayList;


public class MainActivity extends Activity {
    private List<Map<String,Object>> lists;
    private SimpleAdapter adapter;
    private ListView listView;
    //定义数据
    private String[] names = new String[]
            {"Lion","Tiger","Monkey","Dog","Cat","Elephant"};
    private  int[] imageid = new int[]
            {R.mipmap.lion,R.mipmap.tiger,R.mipmap.monkey,R.mipmap.dog,
             R.mipmap.cat,R.mipmap.elephant};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        ListView list = findViewById(R.id.my_list);
        //创建list集合
        List<Map<String,Object>> listItems =
                new ArrayList<>();
        for (int i=0;i<names.length;i++)
        {
            Map<String,Object> listItem =new  HashMap<>();
            listItem.put("header",imageid[i]);
            listItem.put("names",names[i]);
            listItems.add(listItem);
        }
        SimpleAdapter simpleAdapter=new SimpleAdapter(this,listItems,R.layout.simpleitem,
                new String[]{"header","names"}
                ,new int[]{R.id.header,R.id.names});
        list.setAdapter(simpleAdapter);


        list.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> adapterView, View view, int i, long l) {
                Toast.makeText(getApplicationContext(),names[i],Toast.LENGTH_SHORT).show();
            }
        });
        }
    }
```

图片:

![1](https://github.com/Hhj20190915/Android/blob/master/AndroidTest2/image/1.7.png)

## 实验二：AlertDialog

main.xml

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <TextView
        android:id="@+id/textView"
        android:layout_width="match_parent"
        android:layout_height="70dp"
        android:text="ANDROID APP"
        android:background="#fffacd"
        android:textColor="#000000"
        android:gravity="center"
        android:textSize="20dp"/>
    <EditText
        android:id="@+id/name"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="UserName"
        android:textColor="@color/colorPrimary"
        android:textSize="20dp"/>
    <EditText
        android:id="@+id/password"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="PASSWORD"
        android:textSize="20dp"/>
   <LinearLayout
       android:layout_width="match_parent"
       android:layout_height="wrap_content"
       android:layout_marginBottom="5dp"
       android:orientation="horizontal"
       android:paddingLeft="5dp"
       android:paddingRight="5dp">
       <Button
           android:id="@+id/button1"
           android:layout_width="200dp"
           android:layout_height="wrap_content"
           android:text="Cancel"
           android:background="#ffe4e1"
           android:textColor="#000000"/>
       <Button
           android:layout_width="200dp"
           android:layout_height="wrap_content"
           android:text="Sign in"
           android:background="#ffe4e1"
           android:textColor="#000000" />
</LinearLayout>
</LinearLayout>
```

MainActivity.java

```java
package com.example.dialogbuild;

import androidx.appcompat.app.AppCompatActivity;

import android.graphics.Typeface;
import android.os.Bundle;
import android.view.View;
//import android.support.v7.app.AlertDialog;
//import android.support.v7.app.AppCompatActivity;
//版本问题，换成这个
import androidx.appcompat.app.AppCompatActivity;
import androidx.appcompat.app.AlertDialog;
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        AlertDialog.Builder builder =new  AlertDialog.Builder(this);
        View view = View.inflate(MainActivity.this,R.layout.main,null);
        builder.setView(view);

    }
}
```

图片:

![2](https://github.com/Hhj20190915/Android/blob/master/AndroidTest2/image/2.5.png)

## 实验三：使用XML定义菜单

menu1.xml

```
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
        android:title="size">
        <menu>
            <group android:checkableBehavior="single">
                <item android:id="@+id/size1"
                    android:title="size1"/>
                <item android:id="@+id/size2"
                    android:title="size2"/>
                <item android:id="@+id/size3"
                    android:title="size3"/>
            </group>
        </menu>
    </item>
    <item android:id="@+id/item2"
        android:title="option">
    </item>
    <item android:id="@+id/item3"
        android:title="color">
        <menu>
            <group android:checkableBehavior="single">
                <item android:id="@+id/colorRed"
                    android:title="RED"/>
                <item android:id="@+id/colorBlack"
                    android:title="BLACK"/>
            </group>
        </menu>
    </item>
</menu>
```



layout.xml

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
  <TextView
      android:id="@+id/txt"
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:text="这是一个测试"/>
</LinearLayout>
```



MainActivity.java

```java
package com.example.myapplication4;

import androidx.appcompat.app.AppCompatActivity;
import android.graphics.Color;
import android.view.Menu;
import android.view.MenuInflater;
import android.view.MenuItem;
import android.widget.TextView;

import android.widget.Toast;
import android.os.Bundle;

public class MainActivity extends AppCompatActivity {
    private TextView txt;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.layout);
        txt= findViewById(R.id.txt);
    }
    @Override
    public boolean onCreateOptionsMenu(Menu menu){
        MenuInflater inflator=new MenuInflater(this);
        inflator.inflate(R.menu.menu1,menu);
        return super.onCreateOptionsMenu(menu);
    }
    @Override
    public boolean  onOptionsItemSelected(MenuItem item){
        switch (item.getItemId())
        {
            case R.id.size1:
                txt.setTextSize(10*2);
                break;
            case R.id.size2:
                txt.setTextSize(16*2);
            case R.id.size3:
                txt.setTextSize(20*2);
                break;
            case R.id.item2:
                Toast.makeText(this,"普通菜单项",Toast.LENGTH_SHORT).show();
            case R.id.colorRed:
                txt.setTextColor(Color.parseColor("#ff0000"));
                break;
            case R.id.colorBlack:
                txt.setTextColor(Color.parseColor("#000000"));
                break;
        }
        return true;
    }


}
```

图片:

![4](https://github.com/Hhj20190915/Android/blob/master/AndroidTest2/image/3.5.png)

![5](https://github.com/Hhj20190915/Android/blob/master/AndroidTest2/image/3.6.png)

![6](https://github.com/Hhj20190915/Android/blob/master/AndroidTest2/image/3.7.png)

![8](https://github.com/Hhj20190915/Android/blob/master/AndroidTest2/image/3.8.png)

## 实验四：ActionMode

main.xml

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
   <ListView
       android:id="@+id/txt"
       android:layout_width="match_parent"
       android:layout_height="wrap_content"/>
</LinearLayout>
```

menu.xml

```
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <item
        android:id="@+id/menuSelect"
        app:showAsAction="always"
        android:title="Select"/>

</menu>
```

simpleitem.xml

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <ImageView
        android:id="@+id/header"
        android:layout_width="70dp"
        android:layout_height="70dp"
        android:src="@mipmap/ic_launcher"
        />

    <TextView
        android:id="@+id/names"
        android:layout_width="335dp"
        android:layout_height="70dp" />

</LinearLayout>
```



MainActivity.java

```java
package com.example.myapplication;

import android.app.Activity;
import android.graphics.Color;
import android.os.Bundle;
import android.view.ActionMode;
import android.view.Menu;
import android.view.MenuInflater;
import android.view.MenuItem;
import android.widget.AbsListView;
import android.widget.ListView;
import android.widget.SimpleAdapter;
import android.widget.TextView;
import android.view.ContextMenu;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class MainActivity extends Activity {
    private TextView txt;
    private ListView listView;
    private String[] names =new String[]
            {"one","two","three","four","five"};
    private int[] image =new int[]
            {R.mipmap.ic_launcher,R.mipmap.ic_launcher,R.mipmap.ic_launcher,R.mipmap.ic_launcher,R.mipmap.ic_launcher};
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        ListView list=findViewById(R.id.txt);
        List<Map<String,Object>> listItems =
                new ArrayList<>();
        for (int i=0; i<names.length;i++)
        {
            Map<String,Object> listItem =new HashMap<>();
            listItem.put("header",image[i]);
            listItem.put("names",names[i]);
            listItems.add(listItem);
        }
        SimpleAdapter simpleAdapter=new SimpleAdapter(this,listItems,R.layout.simpleitem,
                new String[]{"header","names"},
                new int[]{R.id.header,R.id.names});
        list.setAdapter(simpleAdapter);

        list.setChoiceMode(ListView.CHOICE_MODE_MULTIPLE_MODAL);
        list.setMultiChoiceModeListener(new AbsListView.MultiChoiceModeListener() {
            @Override
            public void onItemCheckedStateChanged(ActionMode actionMode, int i, long l, boolean b) {
                // Here you can do something when items are selected/de-selected,
                // such as update the title in the CAB

                actionMode.setTitle("Selected");


            }

            @Override
            public boolean onCreateActionMode(ActionMode actionMode, Menu menu) {
                MenuInflater inflater = actionMode.getMenuInflater();
                inflater.inflate(R.menu.menu,menu);
                return true;
            }

            @Override
            public boolean onPrepareActionMode(ActionMode actionMode, Menu menu) {
                return false;
            }

            @Override
            public boolean onActionItemClicked(ActionMode actionMode, MenuItem menuItem) {
                return false;
            }

            @Override
            public void onDestroyActionMode(ActionMode actionMode) {

            }
        });

    }
```

图片:

![9](https://github.com/Hhj20190915/Android/blob/master/AndroidTest2/image/4.1.png)

## 实验总结：

1.通过这次实验发现自己其实有很多基础的知识没有掌握，有时间会补回来的

2.不懂为什么typora一直image load fail.....