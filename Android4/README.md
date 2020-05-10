



## 实验四：自定义WebView验证隐式Intent的使用

#### 第一个应用：获取URL地址并启动隐式Intent的调用。

MainActivity.java

```java
package com.example.intent1;

import androidx.appcompat.app.AppCompatActivity;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.view.View.OnClickListener;
import android.widget.EditText;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.layout);
        Button goButton = (Button) findViewById(R.id.hhh);
        final EditText text=(EditText)findViewById(R.id.editText);
      goButton.setOnClickListener(new OnClickListener() {
          @Override
          public void onClick(View view) {
              Intent intent = new Intent();
              String text1=text.getText().toString();
              //将url字符串解析为uri对象;
              Uri uri = Uri.parse(text1);
                  //设置data
                  intent.setData(uri);
              intent.setAction(Intent.ACTION_VIEW);
                  startActivity(intent);
              }
          });
    }

}
```

layout.xml

```html
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <EditText
        android:id="@+id/editText"
        android:layout_width="300dp"
        android:layout_height="50dp"
        android:layout_marginLeft="70dp"
        android:text="https://www.baidu.com/"/>
    <Button
        android:id="@+id/hhh"
        android:layout_width="100dp"
        android:layout_height="40dp"
        android:layout_gravity="center"
        android:text="浏览该网页"
        tools:ignore="InvalidId" />

</LinearLayout>
```

#### 第二个应用：自定义WebView来加载URL

BrowserActivity.java

```java
package com.example.intent2;

import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.webkit.WebView;
import android.webkit.WebViewClient;

import java.net.MalformedURLException;
import java.net.URL;

public class BrowserActivity extends AppCompatActivity {
    WebView webView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_browser);
        //通过id获取控件
        WebView webView =(WebView)findViewById(R.id.webView);
       //访问网页       
        webView.loadUrl("https://www.baidu.com");
        //系统默认会通过手机浏览器打开网页，为了能够直接通过WebView显示网页，则必须设置
        webView.setWebViewClient(new WebViewClient(){
         @Override
         public boolean shouldOverrideUrlLoading(WebView view, String url) {
        //使用WebView加载显示url
        view.loadUrl(url);
        //返回true
        return true;
        }
         });


    }
}
```

activity_browser.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
<WebView
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:id="@+id/webView"/>
</LinearLayout>
```

AndroidManifests.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.example.intent2">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme"
        android:usesCleartextTraffic="true">
        <activity
            android:name=".BrowserActivity"
            android:label="@string/title_activity_browser"
        >
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT"/>
                <category android:name="android.intent.category.BROWSABLE"/>
                <data android:scheme="http"></data>
                <data android:scheme="https"></data>
            </intent-filter>

        </activity>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

    </application>
    <!--添加网络权限-->
    <uses-permission android:name="android.permission.INTERNET"/>

</manifest>
```

### 运行截图:

![1](https://github.com/Hhj20190915/Android/blob/master/Android4/image/1.jpg)

![2](Android4/image/2.jpg)

![3](https://github.com/Hhj20190915/Android/blob/master/Android4/image/3.jpg)

