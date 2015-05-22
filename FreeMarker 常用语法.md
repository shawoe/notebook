## FreeMarker 常用语法 ##
+ FreeMarker 官方网站：[http://www.freemarker.org](http://www.freemarker.org)
+ Freemarker 英文在线文档：[http://www.freemarker.org/docs/index.html](http://www.freemarker.org/docs/index.html)
+ FreeMarker 中文文档下载：[http://sourceforge.net/projects/freemarker/files/chinese-manual/FreeMarker_Manual_zh_CN.pdf/download](http://sourceforge.net/projects/freemarker/files/chinese-manual/FreeMarker_Manual_zh_CN.pdf/download)

### 空值的处理 ###

Freemarker中显示某对象使用 `${name}`。但如果name为null，Freemarker就会报错。如果需要判断对象是否为空：

通过设置默认值 `${name!""}` 来避免对象为空的错误。如果name为空，就以默认值（ ! 后的字符）显示。对象 user，name 为 user 的属性，这时 user 和 name 都有可能为空，可以写成 `${(user.name)!""}` ，表示 user 或者 name 为 null ，都显示为空。

一个不存在的变量和一个是null的变量，对于FreeMarker来说是一样的，所以这里所指的丢失包含这两种情况。

当然也可以在变量名后面通过放置 ?? 来询问FreeMarker一个变量是否存在。将它和 if 指令合并，那么如果 user变量不存在的话将会忽略整个代码段。

关于多级访问的变量，比如 animals.python.price ，书写代码： `animals.python.price!0` ，仅当 animals.python 存在而仅仅最后一个子变量 price 可能不存在（这种情况下我们假设价格是 0）。

如果 animals 或者 python不存在，那么模板处理过程将会以“未定义的变量”错误而停止。为了防止这种情况的发生，可以这样来书写代码： `(animals.python.price) !0` 。

这种情况下当 animals 或 python 不存在时表达式的结果仍然是 0。 对于 ?? 也是同样用来的处理这种逻辑的：`animals.python.price??` 对比 `(animals.python.price)??` 来看。

### 插值的问题 ###

使用者在使用插值时经常犯的一个错误是：在不能使用插值的地方使用了它。插值只能在 本区段中使用。一个典型的错误使用是 `<#if ${isBig}>Wow!</#if>`， 这 是 语 法 上 的 错 误 。 只 能 这 么 来 写 ： `<#if isBig>Wow!</#if>`。

 `<#if "${isBig}">Wow!</#if>`写法也是错误的， 因为 if 指令的参数需要的是布尔值，而这里是字符串，那么就会引起运行时的错误。

注意字符序列 __${__ 和 __#{__ 有特殊的含义，它们被用做插入表达式的数值（典型的应用是：`"Hello ${user}!"` ）。 如果想要打印 __${__ ，就要使用下面所说的原生字符串。

一种特殊的字符串就是原生字符串。在原生字符串中，反斜杠和 __${__ 没有特殊的含义，它们被视为普通的字符。 为了表明字符串是原生字符串，在开始的引号或单引号之前放置字母r，例如： `${r"${foo}"}` 和 `${r"C: \foo\bar"}` 将会打印：__${foo}__ 和 __C: \foo\bar__ 。
