# 前言

因公司业务调整原因, 需要自己实现 `后端(Java)和前端(React)` 实现图片的上传和加载功能. 第一反应, 我的天,我可是 Android 猿...可是我也没办法啦, 慢慢分析, 慢慢写吧...
# 渐进
不过, 想想觉得后端那块是直接从同事那扩展接口, 应该比较简单...然后我就一顿 Google , 挣扎了很久, 决定使用 `hmkcode` 的

- [Spring MVC + jQuery File Upload ](http://hmkcode.com/spring-mvc-jquery-file-upload-multiple-dragdrop-progress/)
- [Spring-Framework/spring-mvc-jquery-file-upload](https://github.com/hmkcode/Spring-Framework/tree/cf05c914ef29a648251733c7f78e6e9c75d32883/spring-mvc-jquery-file-upload)

是的, 没错...这原本是很快就能解决的问题, 可是由于自己没有写过后端项目, 然后就被一堆 `xxx.xml` 配置文件搞懵逼了...

当我把 `hmkcode` 的上传和下载代码, 集成进后端时, 以为这样就能实现上传和下载功能, 兴冲冲的切换到前端进行测试, 当我点击 **上传** 时, 我遇到了第一个问题, 后端报出异常信息: 

```java
java.lang.IllegalStateException: Current request is not of type [org.springframework.web.multipart.MultipartHttpServletRequest]
```

尝试自己去解决, 一分钟后, 我发现我也是懵逼啊, 完全不知道这些配置文件是干嘛的呀...那还是选择 Google 吧...是的, 我找到了 [解决方法](https://coderanch.com/t/660415/framework/Spring-MultipartException-current-request-multipart) , 是的, 如你所见, 就是这个没有配置

```xml
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <!-- 设置上传文件最大的大小属性,单位:Byte -->
    <property name="maxUploadSize" value="5242880"/>
</bean> 
```

可是, 往哪里配置呢? 第一感觉当然是, 这不是 Java 代码, 那么只能去找各种 `xxx.xml` 文件, 终于被我找到了, 在 `src/main/resources` 目录下, 有一个 `spring-restfull.xml` 有类似的配置, 先添加上去看看效果吧...然后又是切换到前端测试, 显然我太天真了, 后端报出异常信息:

```java
java.lang.NoClassDefFoundError: org/apache/commons/fileupload/FileItemFactory
```

什么? 没有这个类? 不会吧...索性搜索了一把, [解决方法](https://stackoverflow.com/questions/5166898/java-lang-noclassdeffounderror-org-apache-commons-fileupload-fileitemfactory)  原来是我并没有去获取这个库,

```xml
<dependency>
	<groupId>commons-fileupload</groupId>
	<artifactId>commons-fileupload</artifactId>
	<version>1.3.1</version>
</dependency>
```



就相当于在 使用Android Studio 开发 Android 时, 没有在 Gradle 进行 `compile xxx.xxx.... ` 的操作, 怎么能调用呢...很幸运, 这位热心的同行解释的很清楚, 我马上就找到了项目中的 `pom.xml` 添加了配置. 记得去 [Commons FileUpload](http://mvnrepository.com/artifact/commons-fileupload/commons-fileupload) 查看版本哦...可能是开发 Android 时的习惯, 我一般只会用比较多人使用的版本, 所以我这里选择了 `1.3.1` ...当然少不了 `Run as/ Maven install` 进行同步一下嘛...然后, 我又切换到前端测试了. 哈, 上传实现了...

接下来就是加载功能了, 在 `hmkcode` 的库中,其实现的是下载功能, 而我要的是前端显示, 并不是下载...所以这部分得自己实现...

在找到提供接口的 ` Controller` 后, 开始看一些同事的代码, 首先定位是使用 **Get 请求方式**, 然后去读图片, 通过**流的方式**进行, 传输出去....当时的大概思路就是这样.

开始编写代码

```java
/**
 * 获取文件
 * @param response
 * @param picName
 */
@RequestMapping(value = "/getpicture/{picName}", method = RequestMethod.GET)
public void getPicture(HttpServletResponse response, @PathVariable("picName") String picName) {
	// 具体实现 省略...
}
```

然后切换到前端请求图片, 好...问题来了, 例如我要请求 `cnlgming.png` , 但是后端打印出来的 **picName 总是 cnlgming** , 咦? 后缀名呢!!!!  我能怎么办的, 又没使用过 **Spring MVC**, 当然是继续一顿搜...解决方式: [Spring MVC @PathVariable getting truncated](https://stackoverflow.com/questions/3526523/spring-mvc-pathvariable-getting-truncated) , 没错.最终就是在路径名后加上 ":.+" , 代码如下:

```java
/**
 * 获取文件
 * @param response
 * @param picName
 */
@RequestMapping(value = "/getpicture/{picName:.+}", method = RequestMethod.GET)
public void getPicture(HttpServletResponse response, @PathVariable("picName") String picName) {
    // 具体实现 省略...
}
```

终于实现了....看来以后要多多学习其他方面的知识了...

> 2017.09.09 补充

在闲暇时间看了 Spring MVC 的相关文档, 了解到 `@RequestMapping` 注解支持在 URI 模板变量中使用正则表达式。语法是 **{varName:regex}** , 第一部分表示变量名, 第二部分表示所要应用的正则表达式, 这两部分使用 `:` 相连接.

> 文章若有不对之处，欢迎留言指正，谢谢~
> 邮箱：liuguangmingcn@163.com