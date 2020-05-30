# NotePad笔记本应用

## 一、NoteList中显示条目增加时间戳显示

1.首先在notelist_item.xml添加时间戳的布局，显示时间的模块位于标题模块下方。

```xml

<LinearLayout  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@android:id/text1"
    android:layout_width="match_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:gravity="center_vertical"
    android:paddingLeft="5dip"
    android:singleLine="true"
/>
    <!--添加显示时间的-->
    <TextView
        android:id="@+id/time"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceSmall"
        android:paddingLeft="5dip"
        android:textColor="@color/colorWhite"/>
```

2.查看数据库（NotePadProvider.java）,NotePad.Notes.COLUMN_NAME_CREATE_DATE(创造日期)和NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE(修改时间)都在数据库里。

```java
@Override
public void onCreate(SQLiteDatabase db) {
    db.execSQL("CREATE TABLE " + NotePad.Notes.TABLE_NAME + " ("
            + NotePad.Notes._ID + " INTEGER PRIMARY KEY,"
            + NotePad.Notes.COLUMN_NAME_TITLE + " TEXT,"
            + NotePad.Notes.COLUMN_NAME_NOTE + " TEXT,"
            + NotePad.Notes.COLUMN_NAME_CREATE_DATE + " INTEGER,"
            + NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE + " INTEGER,"
            + NotePad.Notes.COLUMN_NAME_BACK_COLOR + " INTEGER"
            + ");");
}
```

3.managedQuery:根据指定的URI路径信息返回包含特定数据的Cursor对象，应用这个方法可以使Activity接管返回数据对象的生命周期，参数解释看注释。

SimpleCursorAdapter :连接数据库与视图。

NoteList.java

```java
Cursor cursor = managedQuery(
        getIntent().getData(),            //URI:  Content Provider 需要返回的资源索引
        PROJECTION,                       //Projection: 用于标识有哪些columns需要包含在返回数据中。
        null,                             //Selection:作为查询符合条件的过滤参数，类似于SQL语句中Where之后的条件判断。
        null,                             //SelectionArgs: 同上.
        NotePad.Notes.DEFAULT_SORT_ORDER  //SortOrder: 用于对返回信息进行排序.
    );

    /*
     * The following two arrays create a "map" between columns in the cursor and view IDs
     * for items in the ListView. Each element in the dataColumns array represents
     * a column name; each element in the viewID array represents the ID of a View.
     * The SimpleCursorAdapter maps them in ascending order to determine where each column
     * value will appear in the ListView.
     */

    // The names of the cursor columns to display in the view, initialized to the title column
    String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE,NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE} ;

    // The view IDs that will display the cursor columns, initialized to the TextView in
    // noteslist_item.xml
    int[] viewIDs = { android.R.id.text1,R.id.time};

    // Creates the backing adapter for the ListView.
    SimpleCursorAdapter adapter
        = new SimpleCursorAdapter(
                  this,                             // The Context for the ListView
                  R.layout.noteslist_item,          // Points to the XML for a list item
                  cursor,                           // The cursor to get items from
                  dataColumns,
                  viewIDs
          );

    // Sets the ListView's adapter to be the cursor adapter that was just created.
    setListAdapter(adapter);
}
```

定义PROJECTION

```java
private static final String[] PROJECTION = new String[] {
        NotePad.Notes._ID, // 0
        NotePad.Notes.COLUMN_NAME_TITLE, // 1
        //显示时间 
        NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,

};
```

结果显示：

![2](NotePad/image/2.png)

虽然显示了但是并不是日常的时间格式。

4.修改NotePadProvider中的insert方法和NoteEditor中的updateNote方法。

NotePadProvider.java（创建笔记时获取时间）

由System.currentTimeMillis获取时间然后使用SimpleDateFormat的format方法转化时间格式。

```java
Long now = System.currentTimeMillis();
        Date date = new Date(now);
        @SuppressLint("SimpleDateFormat") SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String dateTime = format.format(date);

        // If the values map doesn't contain the creation date, sets the value to the current time.
        if (values.containsKey(NotePad.Notes.COLUMN_NAME_CREATE_DATE) == false) {
            values.put(NotePad.Notes.COLUMN_NAME_CREATE_DATE, now);
        }

        // If the values map doesn't contain the modification date, sets the value to the current
        // time.
        if (values.containsKey(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE) == false) {
            values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, dateTime);
        }
```

NoteEditor.java（当修改笔记内容时更新时间）

获取时间后用ContentValues的put方法存入数据库。

```java
private final void updateNote(String text, String title) {
    Long now = System.currentTimeMillis();
    Date date = new Date(now);
    @SuppressLint("SimpleDateFormat") SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    String dateTime = format.format(date);

    // Sets up a map to contain values to be updated in the provider.
    ContentValues values = new ContentValues();
    values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, dateTime);
```

最终结果：

![3](NotePad/image/3.png)

## 二、添加笔记查询功能（根据标题查询）

1.在list_options_menu.xml添加搜索item

```xml
<!-- 搜索 -->
<item
    android:id="@+id/menu_search"
    android:title="@string/menu_search"
    android:icon="@android:drawable/ic_search_category_default"
    app:showAsAction="always">
</item>
```

2.在NoteList的onOptionsItemSelected添加搜索功能，根据itemid响应事件，true表示该方法执行完毕后，点击事件不会再向下一个事件处理方法传递了。

```java
@Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.menu_add:
                startActivity(new Intent(Intent.ACTION_INSERT, getIntent().getData()));
                return true;
            case R.id.menu_paste:
                startActivity(new Intent(Intent.ACTION_PASTE, getIntent().getData()));
                return true;
            case R.id.menu_search:
                Intent intent = new Intent();
                intent.setClass(NotesList.this,NoteSearch.class);
                NotesList.this.startActivity(intent);
                return true;
            default:
                return super.onOptionsItemSelected(item);
        }
}
```

3.新建一个名为NoteSearch的activity和布局文件note_search.xml

SearchView是Android原生的搜索框控件，它提供了一个用户界面，可以让用户在文本框内输入文字，并允许通过看监听器监控用户输入，当用户输入完成后提交搜索时，也可通过监听器执行实际的搜索。

布局文件中定义了一个SearchView组件，并为该SearchView组件定义了一个ListView组件，该ListView组件用于为SearchView组件显示自动完成列表。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <SearchView
        android:id="@+id/search_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:iconifiedByDefault="false"
        android:queryHint="输入搜索内容..."
        android:layout_alignParentTop="true">
    </SearchView>

    <ListView
        android:id="@android:id/list"    //在上面的ListView的id命名方式与往常“@+id/”的方式有些不同，之前用“@+id/”方式尝试过，但是运行会出错，可能是由于是继承ListAcitivity的缘故。(来自csdn的一位博主)
              
        android:layout_width="match_parent"
        android:layout_height="wrap_content"></ListView>

</LinearLayout>
```

4.NoteSearch的构造大部分与NoteList相同，因为都是显示笔记列表

onQueryTextChange：用户输入字符时激发该方法

onQueryTextSubmit：单击搜索按钮时激发该方法，没有按钮所以return false

```java
public class NoteSearch extends ListActivity implements SearchView.OnQueryTextListener {
    private static final String[] PROJECTION = new String[] {
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            //显示时间
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, // 2
    };


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.note_search);
        Intent intent = getIntent();
        if (intent.getData() == null) {
            intent.setData(NotePad.Notes.CONTENT_URI);
        }
        SearchView searchview = findViewById(R.id.search_view);
        //为查询文本框注册监听器
        searchview.setOnQueryTextListener(NoteSearch.this);
    }

    @Override
    public boolean onQueryTextSubmit(String s) {
        return false;
    }

    @Override
    public boolean onQueryTextChange(String s) {
        SearchView searchview = findViewById(R.id.search_view);
        searchview.setOnQueryTextListener(NoteSearch.this);
        String selection = NotePad.Notes.COLUMN_NAME_TITLE + " Like ? ";
        String[] selectionArgs = { "%"+s+"%" };
        Cursor cursor = managedQuery(
                getIntent().getData(),            // Use the default content URI for the provider.
                PROJECTION,                       // Return the note ID and title for each note. and modifcation date
                selection,
                selectionArgs,
                NotePad.Notes.DEFAULT_SORT_ORDER  // Use the default sort order.
        );
        String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE ,  NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE };
        int[] viewIDs = { android.R.id.text1 , R.id.time };
        SimpleCursorAdapter adapter
                = new SimpleCursorAdapter(
                this,                             // The Context for the ListView
                R.layout.noteslist_item,          // Points to the XML for a list item
                cursor,                           // The cursor to get items from
                dataColumns,
                viewIDs
        );
        setListAdapter(adapter);
        return true;


    }
    @Override
    protected void onListItemClick(ListView l, View v, int position, long id) {
        // Constructs a new URI from the incoming URI and the row ID
        Uri uri = ContentUris.withAppendedId(getIntent().getData(), id);
        // Gets the action from the incoming Intent
        String action = getIntent().getAction();
        // Handles requests for note data
        if (Intent.ACTION_PICK.equals(action) || Intent.ACTION_GET_CONTENT.equals(action)) {
            // Sets the result to return to the component that called this Activity. The
            // result contains the new URI
            setResult(RESULT_OK, new Intent().setData(uri));
        } else {
            // Sends out an Intent to start an Activity that can handle ACTION_EDIT. The
            // Intent's data is the note ID URI. The effect is to call NoteEdit.
            startActivity(new Intent(Intent.ACTION_EDIT, uri));
        }
    }

}
```

5.在AndroidManifest.xml注册NoteSearch

```xml
<activity android:name=".NoteSearch"
    android:label="@string/title_notes_search">
</activity>
```

结果：

![4](NotePad/image/4.png)

![5](NotePad/image/5.png)