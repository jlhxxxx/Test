# 21. 正则表达式

## 21.1 概述

JMeter 包含模式匹配软件 [Apache Jakarta ORO](http://attic.apache.org/projects/jakarta-oro.html) 。 
在 Jakarta 网站上有一些相关文档，例如：[模式匹配字符摘要](http://archimedes.fas.harvard.edu/scrapbook/jakarta-oro-2.0.6/docs/api/org/apache/oro/text/regex/package-summary.html)。  

在 [OROMatcher 用户指南](http://www.savarese.org/oro/docs/OROMatcher/index.html)上还有关于该产品旧版本的文档 ，也许会有些用处。  

模式匹配与 Perl 中的模式匹配非常类似。Perl 的完整安装会包含大量关于正则表达式的文档——查找 `perlrequick`， `perlretut`， `perlre` 和 `perlreref`。  

有必要强调“包含（contains）”和“匹配（matches）”之间的区别，它们被用在响应断言（Response Assertion）测试元件上： 

"_包含（contains）_" 

* 意味着正则表达式至少与目标的某个部分相匹配，所以 '`alphabet`' “包含” '`ph.b.`'，因为正则表达式匹配子字符串 '`phabe`'。

"_匹配（matches）_" 

* 意味着正则表达式匹配整个目标。所以 '`alphabet`' 和 '`al.*t`' 是“匹配”的 。 

这种情况下，它相当于将正则表达式封装在 `^` 和 `$` 中，即 '`^al.*t$`'。  

然而，情况并非总是如此。例如，正则表达式 '`alp|.lp.*`' “包含”于 '`alphabet`'，但并不“匹配” '`alphabet`'。  

这是为什么？原因是当模式匹配器在 '`alphabet`' 中找到序列 '`alp`' 时，它将停止尝试其他组合——而 '`alp`' 与 '`alphabet`' 不同，它不包含 '`habet`'。

>不同于 Perl，没有必要将正则表达式用 // 封装。

那么如果没有尾部的 `/`，该如何使用 `ismx` 等修饰符呢？解决方法是使用扩展的正则表达式，即 `/abc/i` 变成 `(?i)abc`。请参阅下面的[修饰符的放置](#215-修饰符的放置)。

## 21.2 举例

### 提取单个字符串

假设您想要匹配网页的以下部分： 
`name="file" value="readme.txt">` 
并且要提取 `readme.txt`。 
一个符合的正则表达式： 
`name="file "value ="(.+?)">`

上面的特殊字符是： 

`(` 和 `)` 

* 这些包围匹配字符串的部分将被返回  

`.` 
* 匹配任何字符

`+`
* 一次或多次

`？`
* 不要贪婪，即在第一次匹配成功时停止 

注意：如果没有 `？`，`.+` 在第一个 `">` 后会继续寻找，直到最后一个 `">` ——这可能不是我们所期望的。 

注意：虽然上面的正则表达式可用，但使用下面的表达式会更有效率：  
`name ="file" value ="([^"]+)">`   
其中 `[^"]` ——表示匹配任意（除了 `"` ），在这种情况下，匹配引擎只要找到第一个 `"` 就可以停止查找，而在之前的情况下，引擎必须检查它是否已经找到 `">` 而不是 `" >`。

### 提取多个字符串

假设你想匹配一个网页的以下部分：  
`name ="file.name" value ="readme.txt"`，你想提取 `file.name` 和 `readme.txt`。  
符合的正则表达式：  
`name="([^"]+)" value="([^"]+)"`  
这将创建 2 个匹配组，可以在 JMeter 正则表达式提取器（Regular Expression Extractor）模板（Template）中用 `$1$` 和 `$2$` 来引用。

JMeter 正则表达式提取器将组的值保存在指定变量中。

例如，假设：

* 引用名称：`MYREF`
* 正则表达式：`name="(.+?)" value="(.+?)"`
* 模板：`$1$$2$`

>不要将正则表达式放在 / / 中

以下变量将被设置：

`MYREF`

* `file.namereadme.txt`  

`MYREF_g0`

* `name="file.name" value="readme.txt"`  

`MYREF_g1`：

* `file.name`  

`MYREF_g2`

* `readme.txt`  

这些变量可以在 JMeter 后续的测试计划中引用，形如 `${MYREF}`，`${MYREF_g1}` 等。

## 21.3 行模式

模式匹配可以有不同的表现方式，这取决于多行和单行修饰符的设置。注意单行和多行操作符之间没有任何关系，它们可以单独指定。

### 单行模式
单行模式只影响元字符 '`.`' 的解释。

默认情况下， '`.`' 匹配除换行符之外的任何字符。在单行模式下, '`.`' 也匹配换行符。

### 多行模式
多行模式只影响元字符 '`^`' 和 '`$`' 的解释。

默认情况下 '`^`' 和 '`$`' 只匹配字符串的开头和结尾。在多行模式下， '`^`' 匹配每一行的开头， '`$`' 匹配每一行的结尾。

## 21.4 元字符

正则表达式使用特定字符作为元字符——这些字符对匹配（RE）引擎有特殊的意义。这些字符必须用 `\`（反斜杠）前缀来转义，转义之后才能将它们视为普通字符。以下是元字符及其含义的列表（如有疑问，请查阅 ORO 文档）。

`(` 和 `）`
* 分组

`[` 和 `]`
* 字符集合

`{` 和 `}`
* 重复

`*`，`+` 和 `？`
* 重复

`.`
* 通配符

`\`
* 转义字符

`|`
* 选择符

`^` 和 `$`
* 字符串或行的开始和结束

>请注意，ORO 不支持关键字 \Q 和 \E。[在其他的 RE 引擎中，这些可以用来引用 RE 的一部分，使元字符代表它们自己。]可以使用函数来执行相同的操作，请参阅 [`${__escapeOroRegexpChars(valueToEscape)}`](http://jmeter.apache.org/usermanual/functions.html#__escapeOroRegexpChars)。

ORO 支持以下 Perl5 扩展正则表达式。

`(?#text)`
* 内嵌注释，可以使 `text` 内容被忽略。

`(?:regexp)`
* 像"`()`"一样获取匹配，但并不保存匹配结果。

`(?=regexp)`
* 零长度正向肯定断言。例如，`\w+(?=\s)` 匹配一个后跟空格的单词，匹配结果不包含空格。

`(?!regexp)`
* 零长度正向否定断言。例如，`foo(?!bar)` 匹配任何 '`foo`' 后面不跟 '`bar`' 的结果。请注意，这是一个零长度断言，这意味着 `a(?!b)d` 匹配 `ad`，因为 `a` 后面是一个非 `b` 字符（`d`），而 `d` 符合此断言。

`(?imsx)`
* 一个或多个嵌入模式匹配修饰符。`i` 启用大小写不敏感，`m` 启用多行输入处理，`s` 启用单行输入处理，`x` 启用扩展的空白注释。

**请注意：不支持反向肯定断言——`(?<=regexp)` 。**

## 21.5 修饰符的放置
修饰符可以放在正则表达式的任何地方，然后从放置点开始作用。[ ORO 中有个 bug 使它们不能放在正则表达式的最后。不过，对其他没有影响。]

单行 `(?s)` 和多行 `(?m)` 修饰符通常放置在正则表达式的开头。

忽略大小写修饰符 `(?i)` 可以适用于正则表达式的一部分，例如：

    Match ExAct case or (?i)ArBiTrARY(?-i) case 
将匹配 `Match ExAct case or arbitrary case` 以及 `Match ExAct case or ARBitrary case`，但不匹配 `Match exact case or ArBiTrARY case`。

## 21.6 测试正则表达式
从 JMeter 2.4 开始，监听器（Listener）[查看结果树](http://jmeter.apache.org/usermanual/component_reference.html#View_Results_Tree)（View Results Tree）包含一个正则表达式测试器，用于直接在采样器（Sampler）响应数据上测试正则表达式。

还有一个[网站](http://www.regexplanet.com/advanced/java/index.html)可以测试 Java 正则表达式。

另一种方法是使用简单的测试计划来测试正则表达式。Java 请求（Java Request ）采样器可用于生成示例，或 HTTP （HTTP Request）采样器可用于加载文件。添加 Debug Sampler 和树视图监听器，可以无需访问任何外部服务器，快速测试出正则表达式的更改。