在两年前，在做智能农业检测系统项目时，就有机会接触过传感器方面的知识，只是当时自己并没有直接接触到这方面的代码，而是进行数据的处理。所以仅仅是擦肩而过的感觉。

目前已经有很多传感器集成到了 Android 手机中，经常听到的有：光线传感器、方向传感器、加速度传感器、重力传感器、陀螺仪传感器等等。在开发中很有可能会遇到使用传感器的知识。通过传感器，我们可以开发各种新颖的应用程序，例如：指南针、水平仪、重力感应游戏等等。

由于 Android 为提供了强大的管理服务，所以在 Android 中开发传感器应用还是相对比较简单的。下面就简单说 Android 中传感器方面的知识，开发传感器应用时的步骤。

# 传感器的使用步骤
1. **获取传感器管理服务**，通过 Context 的 `getSystemService(Context.SENSOR_SERVICE)` 方法；
2. **获取指定类型的传感器**，通过 SensorManager 的 `getDefaultSensor(int type)` 方法；
3. **注册指定传感器的注册监听**，通过 SensorManager 的 
  `registerListener(SensorEventListener listener, Sensor sensor, int samplingPeriodUs)`；
  - listener：是指监听传感器的时间监听器，需要实现 `SensorEventListener 接口`;
  - sensor：是指传感器对象;
  - samplingPeriodUs：是指传感器数值的刷新频率，有以下4种：
    - `SENSOR_DELAY_FASTEST = 0` ：最快，即延迟最小咯。只有特别依赖传感器数据的应用推荐采用这个频率。缺点：耗电、由于传递的是原始数据，所以算法处理不好将会影响应用的性能。
    - `SENSOR_DELAY_GAME = 1` : 顾名思义，适合游戏的刷新频率，能够适合实时性的需求。
    - `SENSOR_DELAY_NORMAL = 3` : 正常频率，适用于对实时性不是特别高的应用。
    - `SENSOR_DELAY_UI = 2` : 很明显，这个适用于用户界面更新的频率，具有省电、开销小的优点，缺点就是延迟大。
4. **记得不用的时候需要取消注册的监听服务**，通过SensorManager 的 `unregisterListener(SensorEventListener listener) `。

以上就是传感器的基本使用步骤。

> 看起来很简单对吧，其实实现起来确实也不难。下面就写一个简单的 Demo，这个 Demo 将使用到**光线传感器**，将当前光线强度显示在手机界面上。

**先来看下效果图：**（用手挡了光线）

![光线传感器效果图](https://github.com/cnLGMing/Blog/blob/master/Pictures/Android%E4%BC%A0%E6%84%9F%E5%99%A81-%E5%85%89%E7%BA%BF%E4%BC%A0%E6%84%9F%E5%99%A81-1.gif?raw=true)

布局界面很简单，这里就不给出来了，就一个 TextView 显示光线强度。

关键代码，如下：

```java
import android.content.Context;
import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.widget.TextView;

/**
 * @创建者 　　　　cnLGMing
 * @博客 　　　　  www.cnLGMing.com
 * @创建时间 　　　16/8/11 17:01
 * @描述 　　　   光线传感器的简单使用
 */
public class MainActivity extends AppCompatActivity implements SensorEventListener {

    private TextView      mTvLgiht;
    // 传感器管理
    private SensorManager mSensorManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mTvLgiht = (TextView) findViewById(R.id.tv_lgiht);
        // 获取传感器管理
        mSensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);

        // 为光线传感器设置监听事件
        mSensorManager.registerListener(this,
                mSensorManager.getDefaultSensor(Sensor.TYPE_LIGHT),
                SensorManager.SENSOR_DELAY_UI);
    }

    @Override
    protected void onDestroy() {
        // 取消光线传感器的监听事件
        mSensorManager.unregisterListener(this);
        super.onDestroy();
    }


    /**
     * 当传感器的数值发生变化时,回调此方法
     *
     * @param sensorEvent
     */
    @Override
    public void onSensorChanged(SensorEvent sensorEvent) {
        // 获取变化的数值
        float[] values = sensorEvent.values;
        mTvLgiht.setText("当前的光线强度为: " + values[0] + " lux");
    }

    /**
     * 当传感器的精度发生变化时,回调此方法
     *
     * @param sensor
     * @param i
     */
    @Override
    public void onAccuracyChanged(Sensor sensor, int i) {

    }
}
```

Andrroid 传感器1  到此结束。




# 项目地址
[Github 传送门](https://github.com/cnLGMing/AndroidDemo/tree/master/LightSensorDemo)



------

> **文章若有不对之处，欢迎在 `Issues` 指正，谢谢~**
>
> **邮箱：liuguangmingcn@163.com**

