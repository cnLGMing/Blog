# Elvis 操作符 

Elvis 操作符表达, 写法 `?:`

**下面举个例子:** 当我们遇到一个可为空的引用 `x` 时, 我们会这样解释它 "若 x 不为空, 使用它; 否则, 使用别的不为空的值 y" . 

```kotlin
var value: Int = x?.length ?: y
```

解释: 若 `?:` 左侧表达式不为空, Elvis 操作符就返回其左侧表达式得到的结果, 否则计算并返回右侧表达式得到的结果. **需要注意:** 当且仅当左侧为空时，才会对右侧表达式进行计算并返回。

其对应的 `if...else` :

```kotlin
var value: Int = if (x != null) x.length else y
```

这样带来了什么好处呢? 通过之前的学习, 了解到在 Kotlin 中, `throw` 和 `return` 都是属于表达式范畴的, 那么它们自然而然的可以用在 Elvis 操作符右侧了. 举个例子:

```kotlin
fun getInfo(person: Person): String? {
    var age = person.getAge() ?: return null
    var sex = person.getSex() ?: throw IllegalArgumentException("sex expected")
    // balabala....
    return ""
}
```

这样再也不用也一堆 `if...else` 了...

这几天都在学习 Kotlin, 真心觉得比 Java 好用多了...



---

> 文章若有不对之处，欢迎留言指正，谢谢~
>
> 邮箱：[liuguangmingcn@163.com](mailto:liuguangmingcn@163.com)

