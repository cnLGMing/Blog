这篇文章主要简述 Android 中多个传感器的同时使用。在 [Android 传感器1](https://github.com/cnLGMing/Blog/issues/21) 博客中，只是简单介绍了 Android 常见的几种传感器，且只是拿光线传感器做了个例子，但是没有进一步讲解其他传感器。现在就来讲讲其他常用的传感器。

下面先简单介绍下各个传感器的功能，介绍完后将通过一个小 Demo 介绍使用。

# 光线传感器
光线传感器就是用于获取设备所处环境的**光的强度**。**单位**：勒克斯（lux）。介于 [上一篇博客](https://github.com/cnLGMing/Blog/issues/21) 已经讲过，这里不再多讲。

# 温度传感器
温度传感器用于获取设备周边的温度，单位：摄氏度（℃）。据我所知，目前世面上的手机中的温度传感器只适用于测量手机内部的温度（用于硬件保护），并不能测量外部温度。所以这里就不多介绍了。

# 加速度传感器
加速度传感器用于获取设备 X、Y、Z 三个方向上的加速度。

需要注意的是：传感器坐标系统和设备屏幕的坐标系统是不一样的。

> **传感器坐标系统** 中的 X轴是指向屏幕的右边，Y轴是指向屏幕的上边，Z轴是指向垂直于屏幕向外。

> **屏幕坐标系统** 中的 X轴是指向屏幕的右边，Y轴是指向屏幕的下边，并没有 Z轴。 

**如图所示：**

![传感器坐标系统](https://github.com/cnLGMing/Blog/blob/master/Pictures/Android%E4%BC%A0%E6%84%9F%E5%99%A82-%E4%BC%A0%E6%84%9F%E5%99%A8%E5%9D%90%E6%A0%87%E7%B3%BB%E7%BB%9F2-2.png?raw=true)

# 重力传感器
重力传感器会得到一个三维向量，这个三维向量可现实重力的方向和强度。重力传感器的坐标系统跟加速度传感器的坐标系统是相同的（参考上边的左图）。
> **注意：** 当设备处于静止时，重力传感器的数值应该与加速度传感器的数值 `相等`。

# 线性加速度传感器
线性加速度传感器会得到一个三维向量，这个三维向量是设备在各个方向上的加速度（不包括重力加速度）。线性加速度传感器的坐标系统与加速度传感器的坐标系统是相同的（参考上边的左图）。
> 线性加速度传感器、重力传感器与加速度传感器，三者之间是有关系的。
>
> **加速度传感器 = 重力传感器 + 线性加速度传感器**

# 方向传感器
方向传感器用于感应设备的摆放状态。方向传感器返回三个角度，通过这三个角度可以确定手机的摆放状态。
> **第一个角度：** 是指手机顶部朝向与正北方的夹角。即当手机绕着 Z轴旋转时，产生的角度。*_1.角度为`0°`时，表明手机顶部为正北；2.角度为`90°`时，表明手机顶部为正东；3.角度为`180°`时，表明手机顶部为正南；4.角度为`270°`，表明手机顶部为正西。_*
>
> **第二个角度：** 是指手机顶部或尾部翘起的角度。即当手机绕着 X轴倾斜时，产生的角度(-180° ~ 180°)。_*1.当手机屏幕朝上水平放在桌子上时，角度为`0°`；2.将手机顶部抬起，沿底部旋转，直到手机屏幕朝下，角度将由`0°减小到-180°`；3.将手机底部抬起，沿顶部旋转，直到手机屏幕朝上，角度将由`0°增加到180°`。*_
>
> **第三个角度：** 是指手机左侧或右侧翘起的角度。即当手机绕着 Y轴倾斜时，产生的角度(-90° ~ 90°)。_*1.当手机屏幕朝上水平放在桌子上时，角度为`0°`；2.将手机左侧抬起，沿右侧旋转，直到手机与桌面垂直，角度将由`0°减小到-90°`；3. 将手机右侧抬起，沿左侧旋转，直到手机与桌面垂直，角度将由`0°增加到90°`*_

# 陀螺仪传感器
陀螺仪传感器可得到设备的旋转速度，即设备绕 X、Y、Z轴的旋转速度。单位：弧度/秒。陀螺仪传感器的坐标系统与加速度传感器的坐标系统是相同的（参考上边的左图）。*旋转速度为正值时表示逆时针旋转；负值时表示顺时针旋转。*
> 返回的三个值分别对应的是设备绕 X、Y、Z 轴旋转的角速度。

# 压力传感器
 压力传感器可以得到设备所处环境的压力的大小。

# 磁场传感器
磁场传感器可得到设备外部的磁场强度。众所周知，即使周围没有任何直接磁场，设备也同样会处于地球的大磁场中。随着设备的摆放状态的改变，周围磁场在设备的X、Y、Z 方向上的影响也会随之发生改变。磁场传感器会得到三个数值，分别对应的是周围磁场分解到 X、Y、Z方向的磁场分量。单位：微特斯拉(μT)。

# 代码演示
常用的传感器功能就介绍到这吧。再继续的话，很多人就不耐烦了，拼命的说，我要的效果和代码。那先来看看效果：

![Android传感器2](https://github.com/cnLGMing/Blog/blob/master/Pictures/Android%E4%BC%A0%E6%84%9F%E5%99%A82-%E4%BC%A0%E6%84%9F%E5%99%A8%E9%9B%86%E5%90%882-1.gif?raw=true)

从效果图中可以看到，没有温度传感器的值，这是因为手机一般不具备温度传感器，所以没有返回数值。因为涉及到硬件，所以需要使用真实设备进行演示，模拟器是不支持的。

那代码怎么实现的呢？接下来，我们来看看布局文件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.cnlgming.sensorsdemo.MainActivity">

    <ScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">

            <TextView
                android:id="@+id/tv_orientation"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"/>

            <View
                android:layout_width="match_parent"
                android:layout_height="2dp"
                android:background="#000"/>

            <TextView
                android:id="@+id/tv_gyroscope"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"/>

            <View
                android:layout_width="match_parent"
                android:layout_height="2dp"
                android:background="#000"/>

            <TextView
                android:id="@+id/tv_magnetic"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"/>

            <View
                android:layout_width="match_parent"
                android:layout_height="2dp"
                android:background="#000"/>

            <TextView
                android:id="@+id/tv_gravity"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"/>

            <View
                android:layout_width="match_parent"
                android:layout_height="2dp"
                android:background="#000"/>

            <TextView
                android:id="@+id/tv_linear_acceleration"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"/>

            <View
                android:layout_width="match_parent"
                android:layout_height="2dp"
                android:background="#000"/>

            <TextView
                android:id="@+id/tv_temperature"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"/>

            <View
                android:layout_width="match_parent"
                android:layout_height="2dp"
                android:background="#000"/>

            <TextView
                android:id="@+id/tv_light"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"/>

            <View
                android:layout_width="match_parent"
                android:layout_height="2dp"
                android:background="#000"/>

            <TextView
                android:id="@+id/tv_pressure"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"/>
        </LinearLayout>
    </ScrollView>
</RelativeLayout>
```

Java 代码如下：

```java
package com.cnlgming.sensorsdemo;

import android.content.Context;
import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.widget.TextView;

public class MainActivity extends AppCompatActivity implements SensorEventListener {

    private TextView mTvOrientation;
    private TextView mTvGyroscope;
    private TextView mTvMagnetic;
    private TextView mTvGravity;
    private TextView mTvLinearAcceleration;
    private TextView mTvTemperature;
    private TextView mTvLight;
    private TextView mTvPressure;

    // Sensor 管理服务
    private SensorManager mSensorManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        assignViews();
        mSensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);
    }

    @Override
    protected void onResume() {
        super.onResume();
        // 注册传感器监听器

        // 方向传感器
        mSensorManager.registerListener(this,
                mSensorManager.getDefaultSensor(Sensor.TYPE_ORIENTATION),
                SensorManager.SENSOR_DELAY_UI);
        // 陀螺仪传感器
        mSensorManager.registerListener(this,
                mSensorManager.getDefaultSensor(Sensor.TYPE_GYROSCOPE),
                SensorManager.SENSOR_DELAY_UI);

        // 磁场传感器
        mSensorManager.registerListener(this,
                mSensorManager.getDefaultSensor(Sensor.TYPE_MAGNETIC_FIELD),
                SensorManager.SENSOR_DELAY_UI);

        // 重力传感器
        mSensorManager.registerListener(this,
                mSensorManager.getDefaultSensor(Sensor.TYPE_GRAVITY),
                SensorManager.SENSOR_DELAY_UI);

        // 线性加速度传感器
        mSensorManager.registerListener(this,
                mSensorManager.getDefaultSensor(Sensor.TYPE_LINEAR_ACCELERATION),
                SensorManager.SENSOR_DELAY_UI);

        // 温度传感器
        mSensorManager.registerListener(this,
                mSensorManager.getDefaultSensor(Sensor.TYPE_AMBIENT_TEMPERATURE),
                SensorManager.SENSOR_DELAY_UI);

        // 光线传感器
        mSensorManager.registerListener(this,
                mSensorManager.getDefaultSensor(Sensor.TYPE_LIGHT),
                SensorManager.SENSOR_DELAY_UI);

        // 压力传感器
        mSensorManager.registerListener(this,
                mSensorManager.getDefaultSensor(Sensor.TYPE_PRESSURE),
                SensorManager.SENSOR_DELAY_UI);

    }

    @Override
    protected void onDestroy() {
        // 退出时取消传感器监听器
        mSensorManager.unregisterListener(this);
        super.onDestroy();
    }

    private void assignViews() {
        mTvOrientation = (TextView) findViewById(R.id.tv_orientation);
        mTvGyroscope = (TextView) findViewById(R.id.tv_gyroscope);
        mTvMagnetic = (TextView) findViewById(R.id.tv_magnetic);
        mTvGravity = (TextView) findViewById(R.id.tv_gravity);
        mTvLinearAcceleration = (TextView) findViewById(R.id.tv_linear_acceleration);
        mTvTemperature = (TextView) findViewById(R.id.tv_temperature);
        mTvLight = (TextView) findViewById(R.id.tv_light);
        mTvPressure = (TextView) findViewById(R.id.tv_pressure);
    }

    /**
     * 传感器数值改变时的回调
     *
     * @param sensorEvent
     */
    @Override
    public void onSensorChanged(SensorEvent sensorEvent) {
        // 获取变化的传感器对应的数值
        int type = sensorEvent.sensor.getType();
        // 获取变化的值
        float[] values = sensorEvent.values;

        StringBuilder sb = null;
        // 根据不同的传感器做出不同的响应
        switch (type) {
            case Sensor.TYPE_ORIENTATION:
                sb = new StringBuilder();
                sb.append("方向传感器的数值:");
                sb.append("\n绕 X 轴旋转的角度:");
                sb.append(values[0]);
                sb.append("\n绕 Y 轴旋转的角度:");
                sb.append(values[1]);
                sb.append("\n绕 Z 轴旋转的角度:");
                sb.append(values[2]);
                mTvOrientation.setText(sb.toString());
                break;
            case Sensor.TYPE_GYROSCOPE:
                sb = new StringBuilder();
                sb.append("陀螺仪传感器的数值:");
                sb.append("\n绕 X 轴旋转的角速度:");
                sb.append(values[0]);
                sb.append("\n绕 Y 轴旋转的角速度:");
                sb.append(values[1]);
                sb.append("\n绕 Z 轴旋转的角速度:");
                sb.append(values[2]);
                mTvGyroscope.setText(sb.toString());
                break;
            case Sensor.TYPE_MAGNETIC_FIELD:
                sb = new StringBuilder();
                sb.append("磁场传感器的数值:");
                sb.append("\nX 轴上的磁场:");
                sb.append(values[0]);
                sb.append("\nY 轴上的磁场:");
                sb.append(values[1]);
                sb.append("\nZ 轴上的磁场:");
                sb.append(values[2]);
                mTvMagnetic.setText(sb.toString());
                break;
            case Sensor.TYPE_GRAVITY:
                sb = new StringBuilder();
                sb.append("重力传感器的数值:");
                sb.append("\nX 轴方向上的重力:");
                sb.append(values[0]);
                sb.append("\nY 轴方向上的重力:");
                sb.append(values[1]);
                sb.append("\nZ 轴方向上的重力:");
                sb.append(values[2]);
                mTvGravity.setText(sb.toString());
                break;
            case Sensor.TYPE_LINEAR_ACCELERATION:
                sb = new StringBuilder();
                sb.append("线性加速度传感器的数值:");
                sb.append("\nX 轴方向上的线性加速度:");
                sb.append(values[0]);
                sb.append("\nY 轴方向上的线性加速度:");
                sb.append(values[1]);
                sb.append("\nZ 轴方向上的线性加速度:");
                sb.append(values[2]);
                mTvLinearAcceleration.setText(sb.toString());
                break;
            case Sensor.TYPE_AMBIENT_TEMPERATURE:
                sb = new StringBuilder();
                sb.append("温度传感器的数值:");
                sb.append("\n当前的温度:");
                sb.append(values[0]);
                mTvTemperature.setText(sb.toString());
                break;
            case Sensor.TYPE_LIGHT:
                sb = new StringBuilder();
                sb.append("光线传感器的数值:");
                sb.append("\n当前的光线强度:");
                sb.append(values[0]);
                mTvLight.setText(sb.toString());
                break;
            case Sensor.TYPE_PRESSURE:
                sb = new StringBuilder();
                sb.append("压力传感器的数值:");
                sb.append("\n当前的压力:");
                sb.append(values[0]);
                mTvPressure.setText(sb.toString());
                break;
            default:
                break;
        }
    }

    /**
     * 传感器精度改变时的回调
     *
     * @param sensor
     * @param i
     */
    @Override
    public void onAccuracyChanged(Sensor sensor, int i) {

    }
}
```

从效果图及代码也可以看出，演示 Demo 相对比较简单。




# 项目地址
[Github 传送门](https://github.com/cnLGMing/AndroidDemo/tree/master/SensorsDemo)

---

> 文章若有不对之处，欢迎留言指正，谢谢~
>
> 邮箱：[liuguangmingcn@163.com](mailto:liuguangmingcn@163.com)




