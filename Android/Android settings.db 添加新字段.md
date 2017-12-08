`Settings` 对于 Android 系统工程师来说，是一个经常需要改动的部分之一。这两天都是看 Settings 的源码，就我个人而言，Settings 整个项目的代码量还是挺多的。你觉得呢？

那如何给 Android 的 `settings.db` 数据库添加新的字段？接下来，我就说说我是怎么做的。如有不妥之处，欢迎留言，谢谢~

---
# 背景
今天遇到这样一个需求：在 Android 的 settings.db 中添加一个新字段，用于保存一个特殊含义的值。这里假设是需要保存 wifi mac address。

---
# 简述
Settings 的数据都是保存在Android系统中的 `data/data/com.android.providers.settings/databases/settings.db` 数据库中(可以通过 `DDMS` 或使用命令查看，当然前提是有 **root 权限**)，而这些数据的默认值则是在 Android 源码中 `frameworks/base/packages/SettingsProvider/res/values/defaults.xml` 文件中定义的。

---
# 如何添加新字段
如果需要在 settings.db 中添加新的字段，可以通过以下步骤进行实现[这里假设需要添加保存 wifi mac address 的字段]：
## 第一步
在 `defaults.xml` 中为新字段的数据定义一个默认值(也可以通过代码进行设置)

``` xml
<string name="def_save_wifi_mac">00:00:00:00:00:00</string>

Tips：(需要与第二步中的类型对应)
字符串类型：string，例如：`<string name="def_save_wifi_mac">00:00:00:00:00:00</string>`
布尔类型：bool，例如源码中：`<bool name="def_wifi_on">false</bool>`
整型：integer，例如源码中：`<integer name="def_wifi_sleep_policy">2</integer>`
小数：fraction，例如源码中：`<fraction name="def_window_animation_scale">100%</fraction>`
```
## 第二步
仅仅在给默认值是不行的，还需要在 `DatabaseHelper.java` 中的 `loadSystemSettings(SQLiteDatabase db)` 方法中添加字段

``` java
// 路径：Android 源码中的 
// frameworks/base/packages/SettingsProvider/src/com/android/providers/settings/DatabaseHelper.java
// loadSystemSettings(SQLiteDatabase db)方法中添加新字段
/**
 * 第一个参数：SQLiteStatement(默认写stmt)
 * 第二个参数：新添加字段的名称
 * 第三个参数：新字段的默认值(即，在第一步中添加的默认值)
 */
loadStringSetting(stmt, "wifi_mac_address", R.string.def_save_wifi_mac);

Tips:(需要与第一步中的类型对应)
获取字符串类型：loadStringSetting(stmt, 新字段名称, R.string.默认值名称)
获取布尔类型：loadBooleanSetting(stmt, 新字段名称 ,R.bool.默认值名称)
获取整型：loadIntegerSetting(stmt, 新字段名称 ,R.integer.默认值名称)
获取小数：loadFractionSetting(stmt, 新字段名称 ,R.fraction.默认值名称)
```
## 第三步
重新编译一个 SettingsProvider.apk，覆盖安装即可

## 第四步
经过前面三个步骤，理论上就已经添加完了，那是否成功呢？现在就来检验下，检验有三种方法，
### 1.借助 DDNS 检验
settings.db 数据库的路径是：`data/data/com.android.providers.settings/settings.db` ,然后将数据库导出，进行验证
​	
### 2.代码检验
在 APP 中可以通过 `Settings.System` 中的 get 和 put 方法获取和设置，验证是否有新字段
![获取方法](https://github.com/cnLGMing/Blog/blob/master/Pictures/get.png?raw=true)
![设置方法](https://github.com/cnLGMing/Blog/blob/master/Pictures/put.png?raw=true)

### 3.adb shell 命令检验
settings.db 数据库的路径是：`data/data/com.android.providers.settings/settings.db` ,然后通过 sqlite3 进行查看，是否有新字段

---
到此，此篇博文就到尾声了。希望对你有所帮助.

---
> **若本文有误或你有更好的方法，欢迎留言，谢谢~**
> **邮箱: liuguangmingcn@163.com**

​	