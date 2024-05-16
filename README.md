package twolistview;



import android.content.Context;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseExpandableListAdapter;
import android.widget.ImageView;
import android.widget.TextView;

import com.example.myapplication.R;

import java.util.List;
import java.util.Map;

public class MyAdapter extends BaseExpandableListAdapter{
    private Context mContext;
    // 每组的标题
    private List<String> groupTitle;
    //子项是一个map，key是group的id，每一个group对应一个ChildItem的list
    private Map<Integer, List<ChildItem>> childMap;
    public MyAdapter(Context context, List<String> groupTitle, Map<Integer, List<ChildItem>> childMap){
        this.mContext = context;
        this.groupTitle = groupTitle;
        this.childMap = childMap;
    }
    public interface CustomCallback {
        void onUiChange(int data);
    }

    CustomCallback customCallback;
    public  void callBacksetCallBack(CustomCallback callBack) {
        customCallback = callBack;
    }



    /**
     * 取得分组数
     */
    @Override
    public int getGroupCount() {
        return groupTitle.size();
    }
    /**
     * 取得指定分组的子元素数
     */
    @Override
    public int getChildrenCount(int groupPosition) {
        if (childMap.get(groupPosition) !=null) {
            return childMap.get(groupPosition).size();

        } else {
            customCallback.onUiChange(groupPosition);
            return 0;
        }

    }

    /**
     * 获取指定组的数据对象。
     * @param groupPosition
     * @return
     */
    @Override
    public Object getGroup(int groupPosition) {
        return groupTitle.get(groupPosition);
    }

    /**
     * 获取指定子项的数据对象。
     * @param groupPosition 每组的id
     * @param childPosition 子项的id
     * @return
     */
    @Override
    public Object getChild(int groupPosition, int childPosition) {
        //我们这里返回一下每个item的名称，以便单击item时显示
        return childMap.get(groupPosition).get(childPosition).getTitle();
    }

    /**
     * 得指定分组的ID
     * @param groupPosition 组的ID
     * @return groupPosition
     */
    @Override
    public long getGroupId(int groupPosition) {
        return groupPosition;
    }

    @Override
    public long getChildId(int groupPosition, int childPosition) {
        return childPosition;
    }

    /**
     * 判断分组和子项的ID是否稳定。
     * @return
     */
    @Override
    public boolean hasStableIds() {
        return true;
    }

    @Override
    public View getGroupView(int groupPosition, boolean isExpanded, View convertView, ViewGroup parent) {
        GroupHolder groupHolder = null;
        if (convertView == null){
            Log.e("ylllls", "-------");
            convertView = LayoutInflater.from(mContext).inflate(R.layout.group_item,null);
            groupHolder = new GroupHolder();
            groupHolder.groupImg = convertView.findViewById(R.id.img_group);
            groupHolder.groupTitle =  convertView.findViewById(R.id.tv_group_title);
            convertView.setTag(groupHolder);
        }else {
            Log.e("ylllls", "+++++++++++++++++++++++++++++++++");
            groupHolder = (GroupHolder) convertView.getTag();
        }
        // 如果group被展开
        if (isExpanded){
            groupHolder.groupImg.setBackgroundResource(R.drawable.ic_messages_like_selected);
        }else {
            groupHolder.groupImg.setBackgroundResource(R.drawable.ic_messages_like_unselected);
        }
        groupHolder.groupTitle.setText(groupTitle.get(groupPosition));

        return convertView;
    }

    @Override
    public View getChildView(int groupPosition, int childPosition, boolean isLastChild, View convertView, ViewGroup parent) {
        ChildHolder childHolder = null;
        if (convertView == null) {
            convertView = LayoutInflater.from(mContext).inflate(R.layout.child_item, null);
            childHolder = new ChildHolder();
            childHolder.childImg = convertView.findViewById(R.id.img_child);
            childHolder.childTitle =  convertView.findViewById(R.id.tv_child_text);
            convertView.setTag(childHolder);
        }else {
            childHolder = (ChildHolder) convertView.getTag();
        }
        childHolder.childImg.setBackgroundResource(childMap.get(groupPosition).get(childPosition).getMarkerImgId());
        childHolder.childTitle.setText(childMap.get(groupPosition).get(childPosition).getTitle());
        return convertView;
    }

    /**
     * 判断指定子项是否可以被选中。
     * @param groupPosition
     * @param childPosition
     * @return
     */package twolistview;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.util.Log;
import android.view.ContextMenu;
import android.view.MenuItem;
import android.view.View;
import android.widget.ExpandableListView;
import android.widget.Toast;

import com.example.myapplication.R;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class MainActivity extends AppCompatActivity {
    private ExpandableListView expandList;
    private List<String> groupData;//group的数据源
    private Map<Integer, List<ChildItem>> childData;//child的数据源
    private MyAdapter myAdapter;
    final int CONTEXT_MENU_GROUP_DELETE = 0;//添加上下文菜单时每一个菜单项的item ID
    final int CONTEXT_MENU_GROUP_RENAME = 1;
    final int CONTEXT_MENU_CHILD_EDIT = 2;
    final int CONTEXT_MENU_CHILD_DELETE = 3;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main77);
        initData();
        initView();
    }

    private void initData() {
        groupData = new ArrayList<>();
       // groupData.add("第一组");
       // groupData.add("第二组");
       // groupData.add("第三组");
        for (int i = 0; i < 3; i++) {
            groupData.add("第"+i+ "组");

        }
        List<ChildItem> childItems = new ArrayList<>();
        for (int i = 0; i < 5; i++) {
            ChildItem childData = new ChildItem("标题："+i, R.drawable.ic_launcher_background);
            childItems.add(childData);
        }
        List<ChildItem> childItems2 = new ArrayList<ChildItem>();
        for (int i = 0; i < 2; i++) {
            ChildItem childData1 = new ChildItem("标题："+i, R.drawable.ic_launcher_background);
            childItems2.add(childData1);
        }
        List<ChildItem> childItems3 = new ArrayList<ChildItem>();
        for (int i = 0; i < 20; i++) {
            ChildItem childData1 = new ChildItem("标题："+i, R.drawable.ic_launcher_background);
            childItems3.add(childData1);
        }
        childData = new HashMap<Integer, List<ChildItem>>();
        childData.put(0, childItems);
        childData.put(1, childItems2);
       /* for (int i = 0; i < 1000; i++) {
            if (i%3 ==0) {
                childData.put(i, childItems);
            } else if (i%3 == 1){
                childData.put(i, childItems2);
            } else {
                childData.put(i, childItems3);
            }
        }*/

        myAdapter = new MyAdapter(this, groupData, childData);
        myAdapter.callBacksetCallBack(new MyAdapter.CustomCallback() {
            @Override
            public void onUiChange(int data) {
                String ss = Thread.currentThread().getName();
                Log.e("ylllls", groupData.get(data));
                Toast.makeText(MainActivity.this, groupData.get(data), Toast.LENGTH_SHORT);

            }
        });
    }

    private void initView() {
        expandList = findViewById(R.id.expandlist);
        expandList.setAdapter(myAdapter);
        // 添加上下文的菜单
        registerForContextMenu(expandList);
        // 给子项添加点击事件
        expandList.setOnChildClickListener(new ExpandableListView.OnChildClickListener() {
            @Override
            public boolean onChildClick(ExpandableListView parent, View v, int groupPosition, int childPosition, long id) {
                Log.e("ylllls","555555");
                Toast.makeText(MainActivity.this, "你单击了：" +myAdapter.getChild(groupPosition, childPosition), Toast.LENGTH_SHORT).show();

                if (childPosition ==4){
                    List list = childData.get(groupPosition);
                    ChildItem childData1 = new ChildItem("wo shi xinjiade   ：", R.drawable.ic_launcher_background);

                    list.add(childData1);
                    myAdapter.notifyDataSetChanged();
                }

                return true;
            }
        });

        expandList.setOnGroupClickListener(new ExpandableListView.OnGroupClickListener() {
            @Override
            public boolean onGroupClick(ExpandableListView expandableListView, View view, int i, long l) {
                Log.e("ylllls","6666666");

                return false;
            }
        });
    }
    /*
     * 添加上下文菜单
     */
    @Override
    public void onCreateContextMenu(ContextMenu menu, View v,
                                    ContextMenu.ContextMenuInfo menuInfo) {

        super.onCreateContextMenu(menu, v, menuInfo);
        ExpandableListView.ExpandableListContextMenuInfo info = (ExpandableListView.ExpandableListContextMenuInfo)menuInfo;
        int type = ExpandableListView.getPackedPositionType(info.packedPosition);
        if (type == ExpandableListView.PACKED_POSITION_TYPE_GROUP) {
            menu.setHeaderTitle("Options");
            menu.add(0, CONTEXT_MENU_GROUP_DELETE, 0, "删除");
            menu.add(0, CONTEXT_MENU_GROUP_RENAME, 0, "重命名");
        }
        if (type == ExpandableListView.PACKED_POSITION_TYPE_CHILD) {
            menu.setHeaderTitle("Options");
            menu.add(1, CONTEXT_MENU_CHILD_EDIT, 0, "编辑");
            menu.add(1, CONTEXT_MENU_CHILD_DELETE, 0, "删除");
        }

    }
    /*
     * 每个菜单项的具体点击事件
     */
    @Override
    public boolean onContextItemSelected(MenuItem item) {

        ExpandableListView.ExpandableListContextMenuInfo info = (ExpandableListView.ExpandableListContextMenuInfo)item.getMenuInfo();
        switch (item.getItemId()) {
            case CONTEXT_MENU_GROUP_DELETE:
                Toast.makeText(this, "这是group的删除", Toast.LENGTH_SHORT).show();
                break;
            case CONTEXT_MENU_GROUP_RENAME:
                Toast.makeText(this, "这是group的重命名", Toast.LENGTH_SHORT).show();
                break;
            case CONTEXT_MENU_CHILD_EDIT:
                Toast.makeText(this, "这是child的编辑", Toast.LENGTH_SHORT).show();
                break;
            case CONTEXT_MENU_CHILD_DELETE:
                Toast.makeText(this, "这是child的删除", Toast.LENGTH_SHORT).show();
                break;

            default:
                break;
        }

        return super.onContextItemSelected(item);
    }
}

-------------------------------

package twolistview;



public class GroupItem {
    private String title;
    private int imageId;
    private GroupItem(String title,int imageId){
        this.title = title;
        this.imageId = imageId;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public int getImageId() {
        return imageId;
    }

    public void setImageId(int imageId) {
        this.imageId = imageId;
    }
}

-------------------------

package twolistview;



    public class ChildItem {
        private String title;
        private int markerImgId;
        public ChildItem(String title,int markerImgId){
            this.markerImgId = markerImgId;
            this.title = title;
        }

        public String getTitle() {
            return title;
        }

        public void setTitle(String title) {
            this.title = title;
        }

        public int getMarkerImgId() {
            return markerImgId;
        }

        public void setMarkerImgId(int markerImgId) {
            this.markerImgId = markerImgId;
        }
    }

------------------
chileitem
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="horizontal"
    android:layout_height="match_parent">

    <ImageView
        android:layout_marginTop="10dp"
        android:layout_height="50dp"
        android:layout_width="50dp"
        android:layout_centerVertical="true"
        android:layout_marginLeft="20dp"
        android:id="@+id/img_child"/>

    <TextView
        android:layout_marginTop="21dp"
        android:id="@+id/tv_child_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerVertical="true"
        android:layout_marginLeft="20dp"
        android:layout_toRightOf="@id/img_child"
        android:text="xiangjiao"
        android:textSize="16sp" />

</LinearLayout>

-------------------------
group item

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="horizontal"
    android:layout_height="match_parent">

    <ImageView
        android:layout_marginTop="10dp"
        android:layout_height="50dp"
        android:layout_width="50dp"
        android:layout_centerVertical="true"
        android:layout_marginLeft="10dp"
        android:id="@+id/img_group"/>

    <TextView
        android:layout_marginTop="21dp"
        android:id="@+id/tv_group_title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerVertical="true"
        android:layout_marginLeft="20dp"
        android:layout_toRightOf="@id/img_child"
        android:text="水果"
        android:textSize="16sp" />

</LinearLayout>
    @Override
    public boolean isChildSelectable(int groupPosition, int childPosition) {
        return true;
    }
    private class GroupHolder
    {
        ImageView groupImg;
        TextView groupTitle;
    }
    private class ChildHolder
    {
        ImageView childImg;
        TextView childTitle;
    }

}

----------------------------

