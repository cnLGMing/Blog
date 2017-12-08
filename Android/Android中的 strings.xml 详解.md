Android 为了让 APP 国际化（更好的在不同语言环境中使用），同时为了方便开发者进行适配多语言，提供了 strings.xml ，并建议开发者在 UI 界面相关的使用到的文本使用 strings.xml ，进行统一处理。尽量避免采用硬编码。strings.xml 除了为了多语言适配，还有其他一些重要的作用。

<!--more-->

### 基本使用方法

基本使用的方法，相信在初学 Android 时，就学过，也比较基础，那我在这就一笔带过了。

**例如：** 需要在界面中显示中文 **“你有多少篇文章？”** 

```xml
<!--在 values 的 strings.xml 中的设置-->
 <string name="article_total">你有多少篇文章？</string>
```

```java
// 在 java 代码中通过以下代码获取
getResources().getString(R.string.article_total);
```

---

### 国际化（多语言适配）

现在来说下多语言适配，为了演示，这里只适配英语，其他语言相似。

首先，需要在项目的 `res` 目录创建适配英语的 values 文件夹，在 Android Studio 中，可以通过以下方式快速创建。

**第一步：**

![1.png](https://ooo.0o0.ooo/2016/06/13/575eccbdead81.png)

**第二步：**

![2.png](https://ooo.0o0.ooo/2016/06/13/575eccbc748e8.png)

当完成上面两个步骤后，在 `res` 目录下，会生成 `values-en` 以及 `strings.xml` （还带国旗，是不是很有心）。

**效果图：**

![3.png](https://ooo.0o0.ooo/2016/06/13/575eccbc1567f.png)

**例如：**

当需要在中文环境下时，显示 **“你有多少篇文章？”** ；

在英语环境时，显示 **“How many articles do you have ?”**

```xml
<!--在 values 的 strings.xml 中的设置-->
<string name="article_total">你有多少篇文章？</string>

<!--在 values-en 的 strings.xml 中的设置-->
<string name="article_total">How many articles do you have ?</string>
```

```java
// 在 java 代码中通过以下代码获取
getResources().getString(R.string.article_total);
```

**wiki：**

1. 在进行多语言适配时，不同 values 中的同一字符串需要设置**相同的 name**；
2. Android 会自动根据语言环境加载不同的 strings.xml；
3. 若没有找到对应的语言适配的 values 时，系统将加载默认 values 中的 strings.xml


---

### 带参数

有时字符串会有一部门是相同的，变化的的总是在固定的位置，那么此时，我们可以使用带参数的方法进行配置。

例如：针对上面的`“你有多少篇文章？”`，进行回答：`“我有一篇文章。”`、`“我有两篇文章。”`

```xml
<!--在 values 的 strings.xml 中的设置-->
<string name="my_article_total">我有%s篇文章。</string>
<string name="my_article_total_ing">我有%1$s篇文章，正在写第%2$s篇。</string>
<string name="one">一</string>
<string name="two">二</string>
<string name="three">三</string>

<!--在 values-en 的 strings.xml 中的设置-->
<string name="my_article_total">I have %s article.</string>
<string name="my_article_total_ing">I have %1$s article. I'm writing %2$s.</string>
<string name="one">one</string>
<string name="two">two</string>
<string name="three">the third</string>
```

```java
// 在 java 代码中通过以下代码进行获取
getResources().getString(R.string.my_article_total, getString(R.string.one));
getResources().getString(R.string.my_article_total, getString(R.string.two));
getResources().getString(R.string.my_article_total_ing, getString(R.string.two), getString(R.string.three));
```

**效果图：**

![GIF.gif](https://ooo.0o0.ooo/2016/06/18/576577bf789d3.gif)

**wiki：**

1. 其中 `%s`  表示字符串，若只有一个参数时，直接使用 `%s`  即可；若有两个及两个以上时，需要使用编号进行区分，将 `%s 修改为 %编号$s  ` ，然后在 java 代码在中，按照编号进行设置即可。
2. 更多参数格式，请参考 [Formatter的使用方法](https://developer.android.com/reference/java/util/Formatter.html)，

---

### 根据数量，是否为复数，添加 String

不知道你有没有注意到，当在英文状态下，且数量为复数时，其英语单词还是单数形式，这是不对的。那么需要怎么写呢？接下来，我们就来学习当参数为数量，且需要根据这个参数是单数还是复数，显示对应的单词。

要适配多种语言的复数是比较困难的一件事，所以 Android 已经提供了对应的方法 --> `plurals` ，使用方法如下：

```xml
<!--在 values 的 strings.xml 中的设置-->
<plurals name="article_count">
    <item quantity="one">我有 1 篇文章。</item>
    <item quantity="other">我有 %d 篇文章。</item>
</plurals>

<!--在 values-en 的 strings.xml 中的设置-->
<plurals name="article_count">
    <item quantity="one">I have only one article.</item>
    <item quantity="other">I have %d articles.</item>
</plurals>
```

```java
// 在 java 代码中通过以下代码进行获取
int count = 1;
getResources().getQuantityString(R.plurals.article_count, count, count);
count = 100;
getResources().getQuantityString(R.plurals.article_count, count, count);
```

**效果图：**

![GIF.gif](https://ooo.0o0.ooo/2016/06/20/5768ab11ba12c.gif)

**wiki：**

1. 通过 [Android提供的API](https://developer.android.com/guide/topics/resources/string-resource.html#Plurals) 可以发现，语言之间的复数是有差异，而且很容易搞混。`quantity` 中各合法值的解释：
   ![1.png](https://ooo.0o0.ooo/2016/06/20/5768adb4592bc.png)
2. 当 `item` 中有单引号或双引号时，需要进行转义；
3. 当 `item` 中，包含了数字格式的字符串 `%d` ,则在获取时需要传递两个参数，如上面的两个 **count** ，第一个 count 用于显示合适的字符串；第二个 count 用于插入到占位符 `%d` 中。 

---

### 添加特殊符号

在 **strings.xml** 中，有些字符是不能直接表示出来的。比如：空格。

那么需要的时候怎么使用呢？**使用ASCII 码表示** 。

```xml
<string name="space01"> 测试空格 </string>
<string name="space02">&#160;测试空格&#160;</string>
```

**效果图：**

![](https://ooo.0o0.ooo/2016/06/21/5768dfa81d544.png)

**wiki：**

1. 当不能直接表示出来时，使用 **ASCII 码** 进行表示。格式：`&#ASCII码;`
2. 当不能直接表示出来时，使用 **Unicode 码** 进行表示。格式：`\u码值`
3. 可以设置`formatted="false"`

```xml
<string name="demo1" formatted="false">目前的百分比为10%，预期是20%</string>
<string name="demo2">目前的百分比为10&#37;，预期是20&#37;</string>
<string name="demo3">目前的百分比为10\u0025 ;，预期是20\u0025;</string>
```
---

### 使用 HTML 样式

在某种特定的场景，我们需要强调一些关键字/词，需要进行加粗或颜色修改等操作。这个时候，为了减少控件，使这句话就在同一个 string 中，我们可以使用 HTML 样式对字符串进行修饰。

- 需要显示：**<u>*刘广明*</u>**，下午好！

```xml
<string name="hello"><u><i><b>刘广明</b></i></u>, 下午好</string>
```

效果图：

![](https://ooo.0o0.ooo/2016/06/21/5768e42f8e40a.png)

- 修改颜色（有两种方法）

```xml
// 第一种方法：在 Java 代码中直接设置
mTvHello.setText(Html.fromHtml("<font color='#00ff00'>刘广明</font>,下午好！"));

// 第二种方法
<!-- 在 strings.xml 中配置 -->
<string name="hello">
    <![CDATA[
        <font color="#0066ff"><b>刘广明</b></font>
    ]]>，下午好
</string>

// 在 Java 代码中获取：
mTvHello.setText(Html.fromHtml(getResources().getString(R.string.hello)));
```

**效果图：**

![](https://ooo.0o0.ooo/2016/06/21/5768ee79139c5.png)

**wiki：**

1. 目前 `strings.xml` 自带的支持：加粗`<b>`、下划线`<u>`、斜体`<i>`这三个标签

---

> **文章若有不对之处，欢迎指正，谢谢~**
> **邮箱: liuguangmingcn@163.com**