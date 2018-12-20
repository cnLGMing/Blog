早已听说 Android 6.0 更新了权限机制，但由于没有学习，在工作中一直尚未使用，为了防止新的权限机制带来的问题，将 `targetSdkVersion` 调为了 **22**，这显然是治标不治本的方法，所以今天抽空学习了 Android 6.0 的运行时权限处理机制。


# 前言

Android 6.0 已发布近两年，新版本的 SDK 给开发者带了一些新控件的同时，也带来了新的**权限机制**。在上几篇文章中，介绍了一些 Android 6.0 带来的新控件，现在我们来学习下全新的权限机制。

---

# 新权限机制的变化

不知你是否和我一样反感一些APP滥用权限。

Android 6.0 之前，若你要使用 APP，你只能被迫同意这个 APP 的所有权限；不同意则意味着不能安装。倘若遇到有良心的开发者开发的 APP ，同意是没有问题的；但是当一个单机的斗地主 APP，需要访问通讯录、发送短信等权限时，你还会同意吗？

Android 6.0 之后，我们可以直接安装一款 APP，当这个 APP 需要使用某一权限时，会弹出提示框询问用户是否同意。当我们认为是不合理的权限时，我们完全可以拒绝此 APP 使用该权限。当我们之前同意(或拒绝)这个 APP 使用某一权限，但一段时间后，觉得不合理时，完全可以去权限设置中心进行对该 APP 进行解除(或授权)该权限。

> 为了更好保护用户的隐私及不影响用户体验，Android 将权限划分为**两种**：

- **Normal Permissions** ，表示正常权限，也就是不涉及用户隐私。这种权限是不需要用户进行授权的，如：手机震动、蓝牙等。
- **Dangerous Permission** ，表示危险权限，也就是涉及用户隐私。这种权限是需要用户进行授权的，如：访问通讯录、发送短信等。

> 其中，Dangerous Permission 危险权限又分为几组。那么分组对权限机制的影响有哪些呢？

假设现在 **A和B是同一组危险权限**。是这样的，当我们的 APP 已被用户授权了 **A 权限**，那么当我们使用到 **B权限** 时，系统会立刻授权，而不需要用户授权。例如：当我们的 APP 已授权了 `SEND_SMS` 权限时，当我们需要使用到 `RECEIVE_SMS` 权限时，系统会立刻授权。也就是说，只要同一组中的权限，只要其中有一个有授权，那么其他组员同样也会被授权。

<u>*为了防止后期版本中组权限的改变，强烈建议使用每一个危险权限都进行权限申请。*</u>

## Normal Permissions

```java
ACCESS_LOCATION_EXTRA_COMMANDS
ACCESS_NETWORK_STATE
ACCESS_NOTIFICATION_POLICY
ACCESS_WIFI_STATE
BLUETOOTH
BLUETOOTH_ADMIN
BROADCAST_STICKY
CHANGE_NETWORK_STATE
CHANGE_WIFI_MULTICAST_STATE
CHANGE_WIFI_STATE
DISABLE_KEYGUARD
EXPAND_STATUS_BAR
GET_PACKAGE_SIZE
INSTALL_SHORTCUT
INTERNET
KILL_BACKGROUND_PROCESSES
MODIFY_AUDIO_SETTINGS
NFC
READ_SYNC_SETTINGS
READ_SYNC_STATS
RECEIVE_BOOT_COMPLETED
REORDER_TASKS
REQUEST_INSTALL_PACKAGES
SET_ALARM
SET_TIME_ZONE
SET_WALLPAPER
SET_WALLPAPER_HINTS
TRANSMIT_IR
UNINSTALL_SHORTCUT
USE_FINGERPRINT
VIBRATE
WAKE_LOCK
WRITE_SYNC_SETTINGS
```

## Dangerous Permission

```java
// 联系人
group:android.permission-group.CONTACTS  
  permission:android.permission.WRITE_CONTACTS
  permission:android.permission.GET_ACCOUNTS
  permission:android.permission.READ_CONTACTS
// 电话
group:android.permission-group.PHONE
  permission:android.permission.READ_CALL_LOG
  permission:android.permission.READ_PHONE_STATE
  permission:android.permission.CALL_PHONE
  permission:android.permission.WRITE_CALL_LOG
  permission:android.permission.USE_SIP
  permission:android.permission.PROCESS_OUTGOING_CALLS
  permission:com.android.voicemail.permission.ADD_VOICEMAIL
// 日历
group:android.permission-group.CALENDAR
  permission:android.permission.READ_CALENDAR
  permission:android.permission.WRITE_CALENDAR
// 相机
group:android.permission-group.CAMERA
  permission:android.permission.CAMERA
// 传感器
group:android.permission-group.SENSORS
  permission:android.permission.BODY_SENSORS
// 位置
group:android.permission-group.LOCATION
  permission:android.permission.ACCESS_FINE_LOCATION
  permission:android.permission.ACCESS_COARSE_LOCATION
// 存储
group:android.permission-group.STORAGE
  permission:android.permission.READ_EXTERNAL_STORAGE
  permission:android.permission.WRITE_EXTERNAL_STORAGE
// 麦克风
group:android.permission-group.MICROPHONE
  permission:android.permission.RECORD_AUDIO
// 短信
group:android.permission-group.SMS
  permission:android.permission.READ_SMS
  permission:android.permission.RECEIVE_WAP_PUSH
  permission:android.permission.RECEIVE_MMS
  permission:android.permission.RECEIVE_SMS
  permission:android.permission.SEND_SMS
  permission:android.permission.READ_CELL_BROADCASTS
```

---

# 使用步骤

在学习之前，一直听说 Android 6.0 换了新的权限机制，使用起来很麻烦。现在学了之后感觉只是在使用权限之前加了一层判断，繁琐点。不多说了，开始吧。

1. 跟之前一样，在 `AndroidManifest` 清单文件中，添加需要的权限。

   > 试图申请一个没有声明的权限，可能会导致程序崩溃。

2. 核实是否已授权权限

   ```java
   // 例如：核实是否拥有发送短信的权限
   if (ContextCompat.checkSelfPermission(MainActivity.this,
               Manifest.permission.SEND_SMS)
           != PackageManager.PERMISSION_GRANTED) {
       // 需要申请授权
   } else {
       // 表示已授权
   }
   ```

   PS：核实的权限的 API ：

   `ContextCompat.checkSelfPermission(@NonNull Context context, @NonNull String permission)；` 

   翻看源码可看到，此方法有两个返回：

   - PackageManager.PERMISSION_DENIED，值为 **-1**，表示需要申请授权
   - PackageManager.PERMISSION_GRANTED，值为 **0**，表示已授权

3. 申请授权

   ```java
   ActivityCompat.requestPermissions(MainActivity.this,
           new String[]{Manifest.permission.SEND_SMS},
           MY_PERMISSIONS_REQUEST_SEND_SMS);
   ```

   PS：申请权限的 API：

   `ActivityCompat.requestPermissions(final @NonNull Activity activity, final @NonNull String[] permissions, final int requestCode)` 

   参数说明：

   - 第一个：Context
   - 第二个：需要申请的权限字符串数组 [说明可以同时申请多个权限，但系统会通过对话框逐一询问用户是否授权]
   - 第三个：申请返回的请求码

4. 处理权限申请回调：

   ```java
   @Override
   public void onRequestPermissionsResult(int requestCode,
                                @NonNull String[] permissions,
                                @NonNull int[] grantResults) {
       switch (requestCode) {
           case MY_PERMISSIONS_REQUEST_SEND_SMS: {
               if (grantResults.length > 0
                       && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                   // 拥有权限
               } else {
                   // 当弹出的申请权限申请提示框被拒绝时的逻辑             
               }
               return;
           }
       }
       super.onRequestPermissionsResult(requestCode, permissions, grantResults);
   }
   ```

   执行申请回调后，首先是通过 `requestCode` 找到到你的申请，然后进行验证 `grantResults` 对应的申请结果，这个数组对应了申请时的权限数组，你同时申请几个权限，`grantResults` 的长度就为多少，其分别记录了每个权限的申请结果。也就是说，当你申请成功时，你就可进行下一步的逻辑。

---

还有一个很重要的 API 需要介绍：

```java
if (ActivityCompat.shouldShowRequestPermissionRationale(thisActivity,
        Manifest.permission.READ_CONTACTS)) 
	// 向用户解释为何申请该权限
}
```

---

# [发送短信]的例子

```java
public class MainActivity extends AppCompatActivity {
    /**
     * 用于申请权限后的回调
     */
    private static final int MY_PERMISSIONS_REQUEST_SEND_SMS = 1;

    private EditText mEdNumber;
    private EditText mEdContent;
    private Button   mBtnSend;
    private String   mNumber;
    private String   mContent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initViews();
        initData();
        initListener();
    }

    private void initListener() {
        // 点击后,发送短信
        mBtnSend.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if (ContextCompat.checkSelfPermission(MainActivity.this,
                        Manifest.permission.SEND_SMS)
                        != PackageManager.PERMISSION_GRANTED) { // 表示不同意,需要申请授权

                    ActivityCompat.requestPermissions(MainActivity.this,
                            new String[]{Manifest.permission.SEND_SMS},
                            MY_PERMISSIONS_REQUEST_SEND_SMS);
                } else {
                    // 发送短信
                    sendSms(mNumber, mContent);
                }
            }
        });
    }

    private void initData() {
        mNumber = mEdNumber.getText().toString().trim();
        mContent = mEdContent.getText().toString().trim();
    }

    private void initViews() {
        mEdNumber = (EditText) findViewById(R.id.ed_number);
        mEdContent = (EditText) findViewById(R.id.ed_content);
        mBtnSend = (Button) findViewById(R.id.btn_send);
    }

    /**
     * 发送短信
     *
     * @param number  手机号码
     * @param content 短信内容
     */
    private void sendSms(String number, String content) {
        // 获取短信管理器
        SmsManager smsManager = SmsManager.getDefault();

        // 拆分短信内容（手机短信内容长度不能超过70字）
        ArrayList<String> divideMessages = smsManager.divideMessage(content);
        for (String mes : divideMessages) {
            smsManager.sendTextMessage(number, null, mes, null, null);
        }
    }


    @Override
    public void onRequestPermissionsResult(int requestCode,
                                           @NonNull String[] permissions,
                                           @NonNull int[] grantResults) {
        switch (requestCode) {
            case MY_PERMISSIONS_REQUEST_SEND_SMS: {
                if (grantResults.length > 0
                        && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                    // 拥有权限,直接发送
                    sendSms(mNumber, mContent);
                } else {
                    // 当弹出的申请权限申请提示框被拒绝时的逻辑
                    Toast.makeText(MainActivity.this, "拒绝了发送短信的权限", Toast.LENGTH_SHORT).show();
                }
                return;
            }
        }
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    }
}
```

需要注意：
- 并非所有的通过Intent的方式都不需要申请权限。一般情况下，你是通过Intent打开另一个app，让用户通过该app去做一些事情，你只关注结果（onActivityResult），那么权限是不需要你处理的，而是由打开的app去处理。更多请参考 [Consider Using an Intent](http://developer.android.com/intl/zh-cn/training/permissions/best-practices.html#perms-vs-intents)。

---

# 项目地址
[Github 传送门](https://github.com/cnLGMing/AndroidDemo/tree/master/AndroidNewPermission)

---

**参考博文：**
[鸿洋- Android 6.0 运行时权限处理完全解析](http://blog.csdn.net/lmj623565791/article/details/50709663)



------

> **文章若有不对之处，欢迎在 `Issues` 指正，谢谢~**
>
> **邮箱：liuguangmingcn@163.com**

