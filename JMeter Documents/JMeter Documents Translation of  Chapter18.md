--# 18. 组件参考
<!-- TOC -->

- [18 导言](#18-导言)
- [18.1 取样器](#181-取样器)
    - [FTP 请求](#ftp-请求)
    - [HTTP 请求](#http-请求)
    - [哈哈](#哈哈)

<!-- /TOC -->

英文文档地址：<http://jmeter.apache.org/usermanual/component_reference.html>

## 18 导言

> 少数测试元件使用JMeter属性来控制它们的行为。这些属性通常在类被加载时解析。这通常发生在测试计划执行前，所以不能通过[__setProperty()](http://jmeter.apache.org/usermanual/functions.html#__setProperty)函数来改变设置。

## 18.1 取样器

取样器执行JMeter实际的工作。每个取样器（[测试活动](http://jmeter.apache.org/usermanual/component_reference.html#Flow_Control_Action)除外）生成一个或多个样本结果。样本结果具有各种属性（成功/失败，经过时间，数据大小等），并且可以在各种监听器中查看。

### FTP 请求

此控制器允许您将FTP“检索文件”或“上传文件”的请求发送到FTP服务器。如果要将多个请求发送到同一FTP服务器，请考虑使用[FTP默认请求](http://jmeter.apache.org/usermanual/component_reference.html#FTP_Request_Defaults)配置元件，这样您就不必为每个FTP请求生成控制器输入同样的信息。下载文件时，它可以存储在磁盘（本地文件）或响应数据中，或两者都存储。

延迟设置为登录所需的时间。

![Screenshot for Control-Panel of FTP Request](http://jmeter.apache.org/images/screenshots/ftptest/ftp-request.png)    
*FTP请求控制面板的截图*

**参数（Parameters）**

| 属性（Attribute）     | 描述                                                         | 是否必须     |
| --------------------- | ------------------------------------------------------------ | :----------- |
| 名称                  | 树中显示的此采样器的描述性名称。                             | 否           |
| 服务器名称或IP        | FTP服务器的域名或IP地址。                                    | 是           |
| 端口                  | 使用的端口。如果此值`>0`，则使用此特定端口，否则JMeter使用默认FTP端口。 | 否           |
| 远程文件              | 要检索的文件或要上载的目标文件的名称。                       | 是           |
| 本地文件              | 要上载的或下载的目标文件（默认为远程文件名）。               | 是，如果上载 |
| 本地文件内容          | 提供上载内容，覆盖本地文件属性。                             | 是，如果上载 |
| get(RETR) / put(STOR) | 是否检索或上传文件。                                         | 是           |
| 使用二进制模式？      | 选中此项以使用二进制模式（默认ASCII）                        | 是           |
| 保存文件响应？        | 是否将检索到的文件的内容存储在响应数据中。如果模式为ASCII，则内容将在[查看结果树](http://jmeter.apache.org/usermanual/component_reference.html#View_Results_Tree)中可见。 | 是，如果下载 |
| 用户名                | FTP帐户用户名。                                              | 通常是       |
| 密码                  | FTP帐户密码。注意这在测试计划中是可见的。                    | 通常是       |

**另请参阅：**

* [断言](http://jmeter.apache.org/usermanual/test_plan.html#assertions)
* [FTP默认请求](http://jmeter.apache.org/usermanual/component_reference.html#FTP_Request_Defaults)
* [创建FTP测试计划](http://jmeter.apache.org/usermanual/build-ftp-test-plan.html)

[【返回目录】]()

### HTTP 请求

此取样器允许您将HTTP/HTTPS请求发送到Web服务器。它还允许您控制JMeter是否分析内含图像和其他资源的HTML文件，并发送HTTP请求以检索它们。以下类型嵌入的资源将被检索：

* 图片
* 小程序
* 样式表（CSS）和从这些文件引用的资源
* 外部脚本
* frames，iframes
* 背景图像（body，table，TD，TR）
* 背景声音

默认解析器是`org.apache.jmeter.protocol.http.parser.LagartoBasedHtmlParser`。这可以通过“ `htmlparser.className` ” 属性来变更 - 详细信息请参阅`jmeter.properties`。

如果要将多个请求发送到同一Web服务器，请考虑使用[HTTP请求默认值](http://jmeter.apache.org/usermanual/component_reference.html#HTTP_Request_Defaults)配置元件，这样您就不必为每个HTTP请求输入相同的信息。

或者，您可能希望使用JMeter的[HTTP代理服务器](http://jmeter.apache.org/usermanual/component_reference.html#HTTP(S)_Test_Script_Recorder)来创建它们，而不是手动添加HTTP请求。如果您有大量或者包含许多参数的HTTP请求，这可以节省您的时间。

**有两种不同的测试元件用于定义取样器：**

AJP/1.3 取样器

  使用Tomcat mod_jk协议（允许在AJP模式下测试Tomcat而不需要Apache httpd）。AJP 取样器不支持多文件上传； 只会使用第一个文件。

HTTP请求

  这有一个实现下拉框，它选择要使用的HTTP协议实现：

* `Java`

  使用JVM提供的HTTP实现。与HttpClient实现相比，它有一些限制 - 见下文。

* `HTTPClient4`

  使用Apache HttpComponents HttpClient 4.x.

* 空值

  不设置HTTP取样器的实现方式，所以依赖于HTTP请求默认值（如果存在）或根据`jmeter.properties`中定义的`jmeter.httpsampler`属性。

Java HTTP实现有一些限制：

* 无法控制连接的重用方式。当JMeter释放连接时，它可能会也可能不会被同一个线程重用。
* API最适合单线程使用 - 各种设置通过系统属性来定义，因此适用于所有连接。
* 不支持Kerberos身份验证。
* 它不支持使用Keystore Config进行基于客户端的证书测试。
* 重试机制更方便控制。
* 它不支持虚拟主机。
* 它仅支持以下方法：`GET`，`POST`，`HEAD`，`OPTIONS`，`PUT`，`DELETE`和`TRACE`。
* 通过[DNS缓存管理器](http://jmeter.apache.org/usermanual/component_reference.html#DNS_Cache_Manager)能够更好地控制DNS缓存

> 注意：`FILE`协议仅用于测试目的。无论使用哪个HTTP取样器，它都由相同的代码处理。

如果请求需要服务器或代理登录授权（即浏览器将创建弹出对话框），则还必须添加[HTTP授权管理器](http://jmeter.apache.org/usermanual/component_reference.html#HTTP_Authorization_Manager)配置元件。对于正常登录（即用户在表单中输入登录信息），您需要确定表单提交按钮的作用，并使用适当的方法（通常是`POST`）和表单定义中的相应的参数创建HTTP请求。如果页面使用HTTP，则可以使用JMeter代理捕获登录序列。

每个线程使用单独的SSL上下文。如果要使用单个SSL上下文（而不是浏览器的标准行为），请设置JMeter属性：

    https.sessioncontext.shared=true

默认情况下，从5.0版开始，SSL线程在线程组迭代期间保留，并在每次测试迭代时重置。如果在您的测试计划中同一个用户多次迭代，那么您应该将其设置为false。

    httpclient.reset_state_on_thread_group_iteration=true

> 注意：这不适用于Java HTTP实现。

JMeter默认为SSL协议级别TLS。如果服务器需要不同的级别，例如`SSLv3`，可以变更JMeter属性，例如：

    https.default.protocol=SSLv3

JMeter还允许通过变更`https.socket.protocols`属性来启用其他协议。

如果请求使用cookies，那么您还需要一个 [HTTP Cookie管理器](http://jmeter.apache.org/usermanual/component_reference.html#HTTP_Cookie_Manager)。您可以将这些元件添加到线程组或HTTP请求中。如果您有多个需要授权或cookies的HTTP请求，请将元件添加到线程组。这样，所有HTTP请求控制器将共享相同的授权管理器和Cookie管理器。

如果请求使用“URL重写”技术来保持会话，请参阅 [6.1使用URL重写处理用户会话](http://jmeter.apache.org/usermanual/build-adv-web-test-plan.html#session_url_rewriting) 获取其他配置步骤。

![Screenshot for Control-Panel of HTTP Request](http://jmeter.apache.org/images/screenshots/http-request.png)  
*HTTP请求控制面板的截图*

![HTTP Request Advanced config fields](http://jmeter.apache.org/images/screenshots/http-request-advanced-tab.png)  
*HTTP请求高级配置字段*

**参数（Parameters）**

| 属性（Attribute）                        | 描述                                                         | 是否必须                 |
| ---------------------------------------- | ------------------------------------------------------------ | :----------------------- |
| 名称                                     | 树中显示的此采样器的描述性名称。                             | 否                       |
| 服务器名称或IP                           | web服务器的域名或IP地址，例如`www.example.com`。【不包含`http://`前缀。】注意：如果在信息头管理器中定义了“`Host`”信息头，则将其用作虚拟主机名。<blockquote>服务器是必需的，除非：<ul><li>它由[HTTP请求默认值](http://jmeter.apache.org/usermanual/component_reference.html#HTTP_Request_Defaults)提供</li><li>或路径字段中设置了包含方案，主机和端口（`scheme://host:port`）的完整URL</li></ul></blockquote> | 否                       |
| 端口                                     | Web服务器侦听的端口。默认值：`80`                            | 否                       |
| 连接超时                                 | 连接超时。等待连接打开的毫秒数。                             | 否                       |
| 响应超时                                 | 响应超时。等待响应的毫秒数。请注意，这适用于每次等待响应。如果服务器响应以多个块发送，则总体经过的时间可能比超时时间长。<br />[断言持续时间](http://jmeter.apache.org/usermanual/component_reference.html#Duration_Assertion)可以用于检测需要很长时间才能完成的响应。 | 否                       |
| 服务器（代理）                           | 要执行请求的代理服务器的主机名或IP地址。【不包含`http://`前缀。】 | 否                       |
| 端口                                     | 代理服务器侦听的端口。                                       | 否，除非指定了代理主机名 |
| 用户名                                   | （可选）代理服务器的用户名。                                 | 否                       |
| 密码                                     | （可选）代理服务器的密码。（注意，它在测试计划中没有加密存储） | 否                       |
| 实现                                     | `Java`，`HttpClient4`。如果未指定（也没有通过HTTP请求默认值来定义），则默认值取决于JMeter`jmeter.httpsampler`属性。如果还取不到值，则使用HttpClient4实现。 | 否                       |
| 协议                                     | `HTTP`，`HTTPS` 或`FILE`。默认值：`HTTP`。                  | 否                       |
| 方法                                     | `GET`，`POST`，`HEAD`，`TRACE`，`OPTIONS`，`PUT`，`DELETE`，`PATCH`（不支持 `JAVA`实现）。使用`HttpClient4`时，还允许以下与WebDav相关的方法：`COPY`，`LOCK`，`MKCOL`，`MOVE`， `PROPFIND`，`PROPPATCH`，`UNLOCK`，`REPORT`，`MKCALENDAR`， `SEARCH`。<br />可以使用JMeter`httpsampler.user_defined_methods`属性为HttpClient4预定义更多方法 。 | 是                       |
| 内容编码                                 | 要使用的内容编码（用于`POST`，`PUT`，`PATCH`和`FILE`）。这是要使用的字符编码，与HTTP的Content-Encoding信息头无关。 | 否                       |
| 自动重定向                               | 将基础http协议处理程序设置为自动遵循重定向，因此JMeter不会看到它们，也不会显示为样本。应该只用于`GET`和`HEAD`请求。HttpClient取样器将拒绝将其用于`POST`或`PUT`的尝试。<blockquote>警告：有关cookie和信息头的处理，请参阅下文。</blockquote> | 否                       |
| 跟随重定向                               | 这仅在未启用“ `自动重定向` ”时有效。如果设置，JMeter取样器将检查响应是否为重定向，如果是，则遵循它。初始重定向和进一步响应将显示为额外的样本。父样本的URL和数据字段将从最终（非重定向）样本中获取，但是父样本的字节计算和已用时间包含所有样本。延迟时间从初始响应获取。请注意，HttpClient取样器可能会记录以下消息：<code>"Redirect requested but followRedirects is disabled"</code>这可以被忽略。 <br/>JMeter将在绝对和相对重定向URL中折叠“ `/../segment` ”形式的路径。例如，`http://host/one/../two`将折叠为`http://host/two`。如有必要，可以通过设置JMeter属性`httpsampler.redirect.removeslashdotdot=false`来抑制该行为。 | 否                       |
| 使用KeepAlive                          | JMeter设置Connection：`keep-alive`信息头。这在默认的HTTP实现下无法正常工作，因为连接重用不在用户控制之下。它应该与Apache HttpComponents HttpClient实现一起使用。 | 否                       |
| 对POST使用multipart/form-data | 使用`multipart/form-data`或`application/x-www-form-urlencoded` 的post请求 | 否                       |
| 与浏览器兼容的信息头                   | 使用`multipart/form-data`时，会抑制`Content-Type`和 `Content-Transfer-Encoding`信息头； 仅发送`Content-Disposition`信息头。 | 否                       |
| 路径                                   | 资源的路径（例如，`/servlets/myServlet`）。如果资源需要查询字符串参数，请将参数添加到“同请求一起发送参数”部分中。<blockquote>作为特殊情况，如果路径以“ `http://` ”或“ `https://` ” 开头，则将其用作完整URL。</blockquote>在这种情况下，服务器，端口和协议字段将被忽略； `GET`和`DELETE`方法也会忽略参数。另外要注意的是，路径没有编码 - 除了用`％20`替换空格 - 因此可能需要对不安全的字符进行编码以避免诸如`URISyntaxException`之类的错误。 | 是                       |
| 同请求一起发送参数                 | 查询字符串将从您提供的参数列表中生成。每个参数都有一个`名称`和 `值`，用于对参数进行编码的选项，以及包含或排除等号的选项（某些应用程序在值为空字符串时不希望出现等号）。查询字符串将以正确的方式生成，具体取决于您所选择的“方法”（即，如果您选择`GET`或`DELETE`，查询字符串将附加到URL上，如果是`POST`或`PUT`，则将单独发送）。此外，如果使用multipart form发送文件，则将使用multipart form规范创建查询字符串。**有关参数处理的更多信息，请参见下文。**<br />此外，您可以指定是否应对每个参数进行URL编码。如果您不确定这意味着什么，最好选择它。如果您的值包含如下字符，则通常需要编码：<ul><li>ASCII控制字符</li><li>非ASCII字符</li><li>保留字符：URL在定义其语法时将某些字符用于特殊用途。如果这些字符没有在URL中的特殊用途中使用，则需要对它们进行编码，例如：'`$`'，'`＆`'，'`+`'，'`，`'，'`/`'，'`：`'，'`；`'，'`=`'，'`？`'，'` @`'</li><li>不安全的字符：由于各种原因，某些字符可能会在URL中被误解。这些字符也应始终编码，例如：'` `'，'`<`'，'`>`'，'`＃`'，'`％`'，...</li></ul> | 否                       |
| 文件路径                               | 要发送的文件的名称。如果留空，JMeter将不发送文件，如果填写，JMeter会自动将请求作为multipart form请求发送。<br />如果是`POST`或`PUT`或`PATCH`请求，并且只有一个文件其“参数名称”属性（见下文）被省略，则该文件将作为请求的整个消息体发送，即不添加任何封装器。这允许发送任意消息体。此功能适用于`POST`请求，也适用于`PUT`请求。 **有关参数处理的更多信息，请参见下文。** | 否                       |
| 参数名称                               | Web请求参数“ `name` ”的值。                      | 否                       |
| MIME类型                               | MIME类型（例如，`text/plain`）。如果它是`POST`或`PUT`或`PATCH`请求，并且省略了' `name` '属性（见下文）或者仅从参数值构造了消息体，那么该字段的值将作为`content-type`信息头的值。 | 否                       |
| 从HTML文件获取所有内含的资源      | 告诉JMeter解析HTML文件并发送文件中引用的所有图像，Java小程序，JavaScript文件，CSS等的HTTP/HTTPS请求。请参阅下面获得更多细节。 | 否                       |
| 保存响应为MD5哈希？               | 如果选中此选项，则响应不会存储在样本结果中。相反，计算并存储响应数据的32位MD5哈希值。这用于测试大量数据。 | 否                       |
| 内含的网址必须匹配：                  | 如果选中，则必须填入正则表达式，用于与找到的所有内含的URL匹配。因此，如果您只想从`http://example.com/`下载内含的资源，请使用以下表达式： `http://example\.com/.*` | 否                       |
| 使用并发池                             | 使用并发连接来获取内含的资源。                        | 否                       |
| 数量                               | 用于获取内含的资源的并发连接池大小。                       | 否                       |
| 源地址类型                               | *【仅适用于HTTPClient实现的HTTP请求】* <br/>要区分源地址值，请选择以下类型：<ul><li>选择*IP/主机名*以使用特定IP地址或（本地）主机名</li><li>选择*设备*以选择该接口的第一个可用地址，该地址可以是IPv4或IPv6</li><li>选择*设备 IPv4*以选择设备名称的IPv4地址（如`eth0`，`lo`，`em0`等）</li><li>选择*设备 IPv6*以选择设备名称的IPv6地址（如`eth0`，`lo`，`em0`等）</li></ul> | 否                       |
| 源地址字段                               | *【仅适用于HTTPClient实现的HTTP请求】* <br/>此属性用于启用IP欺骗。它会覆盖此样本的默认本地IP地址。JMeter主机必须具有多个IP地址（即IP别名，网络接口，设备）。该值可以是主机名，IP地址或网络接口设备，例如“`eth0`”或“lo”或“`wlan0`”。<br />如果定义了属性`httpclient.localaddress`，则用于所有HttpClient请求。 | 否                       |

> 使用自动重定向时，仅为初始URL发送cookies。这可能会导致重定向到本地服务器的网站出现意外行为。例如，如果`www.example.com`重定向到`www.example.co.uk`。在这种情况下，服务器可能会返回两个URL的cookies，但JMeter只会接收最后一个主机的cookies，即`www.example.co.uk`。如果测试计划中的下一个请求使用`www.example.com`，而不是`www.example.co.uk`，它将无法获得正确的cookies。同样，信息头只对初始请求发送，对于重定向不会被发送。通常只是手动创建的测试计划会出现这个问题，因为使用记录器创建的测试计划将从跟随重定向的URL。

**参数处理：** 
对于`POST`和`PUT`方法，如果没有要发送的文件，并且省略了参数的名称，则通过连接参数的所有值来创建消息体。注意这些值的连接不会添加任何行尾字符。在值字段中可以使用`__char()`函数来添加它们。这允许发送任意消息体。如果设置了编码标志，则对值进行编码。另请参阅上面的MIME类型，了解如何控制发送的`content-type`信息头。  
对于其他方法，如果缺少参数的名称，则忽略该参数。这允许使用由变量定义的可选参数。

当请求只有未命名的参数（或根本没有参数）时，您可以选择切换到`消息体数据`选项卡。此选项在以下情况（以及其他情况）中很有用：

* GWT RPC HTTP请求
* JSON REST HTTP请求
* XML REST HTTP请求
* SOAP HTTP请求

> 注意一旦离开Tree节点，除非清除`消息体数据`选项卡下的数据，否则无法切换回参数选项卡。

在`消息体数据`模式下，除了最后一行之外，每行发送时都将附加`CRLF`。要在最后一行数据之后发送`CRLF`，只需确保其后面有空行。（空行虽然看不到，但是可以通过观察光标是否可以放在后续行上看出。）

![Figure 1 - HTTP Request with one unnamed parameter](http://jmeter.apache.org/images/screenshots/http-request-raw-single-parameter.png)  
*图1 - 带有一个未命名参数的HTTP请求*

![Figure 2 - Confirm dialog to switch](http://jmeter.apache.org/images/screenshots/http-request-confirm-raw-body.png)  
*图2 - 确认要切换的对话框*

![Figure 3 - HTTP Request using Body Data](http://jmeter.apache.org/images/screenshots/http-request-raw-body.png)  
*图3 - 使用消息体数据的HTTP请求*

**方法的处理：** 
`GET`，`DELETE`，`POST`，`PUT`和`PATCH`请求方法的工作方式类似，除了3.1之外

[^1]: 什么3.1，我也不知道

，只有`POST`方法支持multipart请求或文件上传。`PUT`和`PATCH`方法消息体必须提供以下之一：

* 将消息体定义为具有空参数名称字段的文件；在这种情况下，MIME类型用作Content-Type
* 将消息体定义为没有名称的参数值
* 使用“ `消息体数据”`选项卡

`GET`，`DELETE`和`POST`方法有一种额外的参数传递方式，就是通过使用`参数`选项卡。 `GET`，`DELETE`，`PUT`和`PATCH`需要Content-Type。如果不使用文件，添加一个信息头管理器到取样器并在那里定义Content-Type。

JMeter扫描内含资源的响应。它使用`HTTPResponse.parsers`属性，它是一个解析器的ID列表，例如`htmlParser`，`cssParser`和`wmlParser`。对于找到的每个id，JMeter会检查另外两个属性：

* `id.types` - 内容类型列表
* `id.className` - 用于提取内含资源的解析器

有关设置的详细信息，请参阅`jmeter.properties`文件。如果未设置`HTTPResponse.parser`属性，则JMeter将恢复为先前的行为，即仅扫描`text/html`响应。

**模拟慢速连接**：

`HttpClient4`和`Java` 取样器支持慢速连接的模拟；请参阅`jmeter.properties中`的以下条目：

    # Define characters per second > 0 to emulate slow connections
    #httpclient.socket.http.cps=0
    #httpclient.socket.https.cps=0

然而`Java`取样器仅支持慢速HTTPS连接。

**响应大小计算** 

> `Java`实现不包括传输的开销，例如在响应消息体中的信息头部分。  
> `HttpClient4`实现包含整个响应消息体的开销，所以该值可能比在响应内容中包含的字节数更大。

**重试处理** 
默认情况下，HttpClient4和Java实现的重试设置为0，这意味着不会尝试重试。  
对于HttpClient4，可以通过设置相关的JMeter属性来设置重试次数，例如：

    httpclient4.retrycount=3

> 使用HC4实现，默认情况下将在Idempotent Http方法上进行重试。如果要重试所有方法，请设置属性
> 
>        httpclient4.request_sent_retry_enabled=true

注意Java实现在默认情况下也不会重试，您可以通过设置更改它

    http.java.sampler.retries=3

**注意：证书不符合算法约束** 
如果您在具有SSL证书（本身或其信任链中的 SSL 证书）的网站上运行HTTPS请求，该证书使用MD2的签名算法（如`md2WithRSAEncryption`）或大小小于1024位，您可能会遇到以下错误：`java.security.cert.CertificateException: Certificates does not conform to algorithm constraints` 。

此错误与Java 8中增强的安全性有关。

要允许您执行HTTPS请求，可以通过编辑Java`jdk.certpath.disabledAlgorithms`属性来降级Java安装的安全性。根据您的情况，删除MD2值或取消大小约束。

此属性在此文件中：

```
JAVA_HOME/jre/lib/security/java.security
```

有关详细信息，请参阅 [Bug56357](https://bz.apache.org/bugzilla/show_bug.cgi?id=56357)。

**另请参阅：**

* [断言](http://jmeter.apache.org/usermanual/test_plan.html#assertions)
* [构建Web测试计划](http://jmeter.apache.org/usermanual/build-web-test-plan.html)
* [构建高级Web测试计划](http://jmeter.apache.org/usermanual/build-adv-web-test-plan.html)
* [HTTP授权管理器](http://jmeter.apache.org/usermanual/component_reference.html#HTTP_Authorization_Manager)
* [HTTP Cookie管理器](http://jmeter.apache.org/usermanual/component_reference.html#HTTP_Cookie_Manager)
* [HTTP信息头管理器](http://jmeter.apache.org/usermanual/component_reference.html#HTTP_Header_Manager)
* [HTML链接解析器](http://jmeter.apache.org/usermanual/component_reference.html#HTML_Link_Parser)
* [HTTP代理服务器](http://jmeter.apache.org/usermanual/component_reference.html#HTTP(S)_Test_Script_Recorder)
* [HTTP请求默认值](http://jmeter.apache.org/usermanual/component_reference.html#HTTP_Request_Defaults)
* [HTTP请求和会话ID：URL重写](http://jmeter.apache.org/usermanual/build-adv-web-test-plan.html#session_url_rewriting)

[【返回目录】]()

### JDBC 请求

此取样器允许您将JDBC请求（SQL查询）发送到数据库。

在使用之前，您需要设置 [JDBC连接配置](http://jmeter.apache.org/usermanual/component_reference.html#JDBC_Connection_Configuration)配置元件。

如果提供了“变量名称”列表，则对于Select语句返回的每一行，将使用相应列和对应的行数的值设置变量（如果提供变量名称）。例如，如果Select语句返回2行3列，并且变量列表是`A,,C`，则将设置以下变量：

```
A _＃= 2（行数）
A_1 =第1列，第1行
A_2 =第1列，第2行
C _＃= 2（行数）
C_1 =第3列，第1行
C_2 =第3列，第2行
```

如果Select语句返回零行，则`A_＃`和`C_＃`变量将设置为`0`，并且不会设置其他变量。

必要时清除旧变量 - 例如，如果第一个select检索六行而第二个select仅返回三行，则额外的第四行，第五行和第六行的变量将被删除。

> 延迟时间设置为获取连接所花费的时间。

![Screenshot for Control-Panel of JDBC Request](http://jmeter.apache.org/images/screenshots/jdbctest/jdbc-request.png)  
*JDBC Request的控制面板的截图*

**参数（Parameters）**

| 属性（Attribute）                                            | 描述                                                         | 是否必须                               |
| ------------------------------------------------------------ | ------------------------------------------------------------ | :------------------------------------- |
| name                                                         | 树中显示的此采样器的描述性名称。                             | 否                                     |
| Variable Name of Pool declared in JDBC Connection Configuration | 连接池绑定的JMeter变量的名称。这必须与[JDBC Connection Configuration](http://jmeter.apache.org/usermanual/component_reference.html#JDBC_Connection_Configuration)的“ `Variable Name` ”字段一致。 | 是                                     |
| Query Type                                                   | 根据语句类型设置：<ul><li>Select Statement</li><li>Update Statement - 也用于插入和删除</li><li>Callable Statement</li><li>Prepared Select Statement</li><li>Prepared Update Statement - 也用于插入和删除</li><li>Commit</li><li>Rollback</li><li>Autocommit(false)</li><li>Autocommit(true)</li><li>编辑 - 这应该是一个变量引用，其值为上述之一</li></ul> | 是                                     |
| SQL Query                                                    | SQL查询语句。<blockquote>不要输入语句结束的分号。</blockquote>通常不需要使用`{`和`}`来封装Callable语句； 但是，如果数据库使用非标准语法，可以使用它们。<blockquote>如果语句封装在`{}`中，JDBC驱动程序会自动转换该语句。</blockquote>例如：<ul><li>`select * from t_customers where id=23`</li><li>`CALL SYSCS_UTIL.SYSCS_EXPORT_TABLE (null, ?, ?, null, null, null)`<li><ul><li>参数值：`tablename`，`filename`</li><li>参数类型： `VARCHAR`，`VARCHAR`</li></ul></li></ul>第二个示例假设您正在使用Apache Derby。 | 是                                     |
| Parameter values                                             | 以逗号分隔的参数值列表。使用`]NULL[`表示`NULL`参数。（如果需要，可以通过定义属性“`jdbcsampler.nullmarker`” 来更改空字符串。） <br/>如果任何值包含逗号或双引号，则列表必须用双引号括起来，并且任何内含的的双引号都必须加倍，例如：<code>"Dbl-Quote: "" and Comma: ,"</code><blockquote>即使您的参数是`OUT`，语句中的值也必须与占位符一样多。即使不使用该值，也必须确保设置该值（例如在Callable 语句中）。</blockquote> | 是，如果prepared或callable语句含有参数 |
| Parameter types                                              | 以逗号分隔的SQL参数类型列表（例如`INTEGER`，`DATE`，`VARCHAR`，`DOUBLE`）或常量的整数值。当您使用驱动程序提供的自定义数据库类型时，可以使用这些整数值（例如`OracleTypes.CURSOR`可以用其整数值`-10`来表示）。<br /> 这些被定义为`java.sql.Types`类中的字段，例如参见：<br/>[java.sql.Types的Javadoc](http://docs.oracle.com/javase/8/docs/api/java/sql/Types.html)。<blockquote>注意：JMeter将使用运行时JVM定义的任何类型，因此如果您在不同的JVM上运行，请务必检查相应的文档</blockquote>**如果可调用语句具有`INOUT`或`OUT`参数，则必须为相应的参数类型添加前缀来指示这些参数，例如，使用“`INOUT INTEGER`”而不是“`INTEGER`”。**<br/>如果未指定，则假定为“`IN`”，即“`DATE`”与“`IN DATE`” 相同。 <br/>如果类型不是`java.sql.Types`中找到的字段之一，JMeter也接受相应的整数，例如，由于`OracleTypes.CURSOR == -10`，所以您可以使用“`INOUT -10`”。 <br/>类型必须同语句中的占位符一样多。 | 是，如果prepared或callable语句含有参数 |
| Variable Names                                               | 以逗号分隔的变量名列表，用于保存Select语句，Prepared Select语句或Callable 语句的返回值。注意当与Callable 语句使用时，变量列表必须与调用返回的`OUT`参数的顺序相同。如果变量名称少于`OUT`参数，则只有和提供变量名称相同数目的结果会存储在线程上下文变量中。如果提供比`OUT`参数更多的变量名称，多余的变量将被忽略。 | 否                                     |
| Result Variable Name                                         | 如果指定，这将创建一个包含行映射列表的Object变量。每个映射都以列名作为键，列数据作为值。用法：<code>columnValue = vars.getObject("resultObject").get(0).get("Column Name")；</code> | 否                                     |
| Query timeout(s)                                             | 设置查询的超时秒数，空值表示0，为无限。`-1`表示不设置用例可能需要的任何查询超时，或者某些驱动程序不支持超时。默认值为0。 | 否                                     |
| Handle ResultSet                                             | 定义如何处理从可调用语句返回的ResultSet：<ul><li>`存储为字符串`（默认） - “变量名称”列表中的所有变量都存储为字符串，当列表中存在时，不会遍历`ResultSet`。`CLOB`将转换为字符串。`BLOB`将被当做UTF-8编码的字节数组转换为字符串。`CLOB`和`BLOB`都将在在`jdbcsampler.max_retain_result_size`字节后被切断。</li><li>`存储为对象` - 变量名称列表上的`ResultSet`类型的变量将存储为对象，可以在后续测试/脚本中访问并迭代，不会遍历`ResultSet`。`CLOB`将像选择了`存储为字符串`一样被处理。`BLOB`将存储为字节数组。`CLOB`和`BLOB`都将在在`jdbcsampler.max_retain_result_size`字节后被切断。</li><li>`记录计数` - `ResultSet`类型的变量将通过显示结果的记录计数来迭代。变量将存储为字符串。对于`BLOB`，将存储对象的大小。</li></ul> | 否                                     |

**另请参阅：**

* [构建数据库测试计划](http://jmeter.apache.org/usermanual/build-db-test-plan.html)
* [JDBC Connection Configuration](http://jmeter.apache.org/usermanual/component_reference.html#JDBC_Connection_Configuration)

> 当前版本的JMeter使用UTF-8作为字符编码。以前使用平台默认值。

> 确保变量名称在测试计划中是唯一的。

[【返回目录】]()

### Java 请求

此取样器允许您控制实现`org.apache.jmeter.protocol.java.sampler.JavaSamplerClient`接口的java类  。通过编写您自己的此接口实现，您可以用JMeter来使用多个线程，输入参数控制和数据收集。

下拉菜单提供了JMeter在其classpath中找到的所有此类实现的列表。可以在下面的表中指定参数 - 由您的实现定义。JMeter提供了两个简单示例（`JavaTest`和`SleepTest`）。 

 `JavaTest`示例取样器对检查测试计划非常有用，因为它允许在几乎所有的字段设置值。这些可以被断言等使用。这些字段允许使用变量，因此可以很容易地看到这些变量的值。 

![Screenshot for Control-Panel of Java Request](http://jmeter.apache.org/images/screenshots/java_request.png)  
*Java 请求的控制面板的截图* 

> 如果`teardownTest`方法未被[AbstractJavaSamplerClient](http://jmeter.apache.org/api/org/apache/jmeter/protocol/java/sampler/AbstractJavaSamplerClient.html)的子类覆盖，则不会调用其`teardownTest`方法。这降低了JMeter内存要求。不会对现有的测试计划产生任何影响。 

> 添加/删除按钮目前不起任何作用。

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否       |
| 类名称            | 要取样的JavaSamplerClient接口的具体实现。                    | 是       |
| 发送带参数的请求  | 将传递给取样类的参数列表。所有参数都以字符串形式发送。请参阅下面的具体设置。 | 否       |

以下参数适用于`SleepTest`和`JavaTest`实现：

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| Sleep_time        | 睡眠时间（ms）                                               | 是       |
| Sleep_mask        | 添加多少“随机性”：<br/>睡眠时间计算如下：<code>totalSleepTime = SleepTime + (System.currentTimeMillis() % SleepMask)</code> | 是       |

以下参数适用于`JavaTest`实现：

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| Label             | 要使用的标签。如果提供，则覆盖`名称`                         | 否       |
| ResponseCode      | 要取样的JavaSamplerClient接口的具体实现。                    | 否       |
| ResponseCode      | 如果提供，则设置SampleResult ResponseCode。                  | 否       |
| Status            | 如果提供，则设置SampleResult Status。如果它等于“`OK` ”（忽略大小写），则状态设置为成功，否则样本被标记为失败。 | 否       |
| SamplerData       | 如果提供，则设置SampleResult SamplerData。                   | 否       |
| ResultData        | 如果提供，则设置SampleResult ResultData。                    | 否       |

[【返回目录】]()

### LDAP 请求

此取样器允许您向LDAP服务器发送不同的LDAP请求（`添加`，`修改`，`删除`和`搜索`）。     

如果要将多个请求发送到同一LDAP服务器，请考虑使用[LDAP默认请求](http://jmeter.apache.org/usermanual/component_reference.html#LDAP_Request_Defaults)配置元件，这样您就不必为每个LDAP请求输入相同的信息。

[登录配置元件](http://jmeter.apache.org/usermanual/component_reference.html#Login_Config_Element)也使用同样的方法发送用户名和密码。

![Screenshot for Control-Panel of LDAP Request](http://jmeter.apache.org/images/screenshots/ldap_request.png)  
*LDAP请求控制面板的屏截图*

创建测试LDAP服务器的测试用例有两种方法。

1. 内置测试用例。
2. 用户定义的测试用例。

测试LDAP有四种测试方案。测试方案如下：

1. 添加测试       

    1. 内置测试：           

        这将在LDAP服务器中添加预定义的条目并计算执行时间。执行测试后，将从LDAP服务器中删除创建的条目。

    2. 用户定义的测试：           

        这将在LDAP服务器中添加条目。用户必须输入表中的所有属性。从表中收集条目以进行添加。计算执行时间。测试后不会删除创建的条目。

2. 修改测试       

    1. 内置测试：           

        这将首先创建预定义的条目，然后修改LDAP服务器中创建的条目。并计算执行时间。执行测试后，将从LDAP服务器中删除创建的条目。

    2. 用户定义的测试：           

        这将修改LDAP服务器中的条目。用户必须输入表中的所有属性。从表中收集条目以进行修改。计算执行时间。该条目不会从LDAP服务器中删除。

3. 搜索测试       

    1. 内置测试：           

        这将首先创建条目，然后搜索属性是否可用。它计算搜索查询的执行时间。在执行结束时，将从LDAP服务器中删除创建的条目。

    2. 用户定义的测试：           

        这将搜索搜索库（同样，由用户定义）中的用户定义条目（搜索过滤）。这些条目应该在LDAP服务器中可用。计算执行时间。

4. 删除测试       

    1. 内置测试：           

        这将首先创建预定义的条目，然后将从LDAP服务器中删除。计算执行时间。

    2. 用户定义的测试：           

        这将删除LDAP服务器中的用户定义条目。这些条目应该在LDAP服务器中可用。计算执行时间。

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须                                         |
| ----------------- | ------------------------------------------------------------ | :----------------------------------------------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否                                               |
| 服务器名称或IP    | LDAP服务器的域名或IP地址。JMeter假定LDAP服务器正在侦听默认端口（`389`）。 | 是                                               |
| 端口              | 要连接的端口（默认为`389`）。                                | 是                                               |
| 根DN              | 用于LDAP操作的基本DN                                         | 是                                               |
| 用户名            | LDAP服务器用户名。                                           | 通常是                                           |
| 密码              | LDAP服务器密码。（注意，这在测试计划中未加密存储）           | 通常是                                           |
| DN入口            | 要创建或修改的上下文的名称；可能不是空的。<blockquote>您必须自己设置对象的正确属性。因此，如果要添加`cn=apache`，`ou=test` ，则必须将`cn`和`apache`添加到表的`name`和`value`中。</blockquote> | 是，如果选择了用户定义的测试和添加测试或修改测试 |
| 删除              | 要删除的上下文的名称；可能不是空的                           | 是，如果选择了用户定义的测试和删除测试           |
| Search base       | 要搜索的上下文或对象的名称                                   | 是，如果选择了用户定义的测试和搜索测试           |
| 搜索过滤器        | 用于搜索的过滤器表达式；可能不会为空                         | 是，如果选择了用户定义的测试和搜索测试           |
| 添加测试          | 使用`name`，`value`这样的键值对在给定上下文中创建新对象      | 是，如果选择了用户定义的测试和添加测试           |
| 修改测试          | 使用`name`，`value`这样的键值对来修改给定的上下文对象        | 是，如果选择了用户定义的测试和修改测试           |

**另请参阅：**

-  [构建LDAP测试计划](http://jmeter.apache.org/usermanual/build-ldap-test-plan.html) 
-  [LDAP默认请求](http://jmeter.apache.org/usermanual/component_reference.html#LDAP_Request_Defaults) 

[【返回目录】]()

### LDAP扩展请求

此取样器可以将所有8个不同的LDAP请求发送到LDAP服务器。它是LDAP取样器的扩展版本，因此配置起来比较困难，但可以更接近真实的LDAP会话。     

如果要将多个请求发送到同一LDAP服务器，请考虑使用[LDAP扩展请求默认值](http://jmeter.apache.org/usermanual/component_reference.html#LDAP_Extended_Request_Defaults)配置元件，这样您就不必为每个LDAP请求输入相同的信息。

![Screenshot for Control-Panel of LDAP Extended Request](http://jmeter.apache.org/images/screenshots/ldapext_request.png)  
*LDAP扩展请求控制面板的截图*

定义了九个测试操作。这些操作如下：

 **线程绑定** 

任何LDAP请求都是LDAP会话的一部分，因此首先要做的是启动与LDAP服务器的会话。使用线程绑定启动此会话，这相当于LDAP的“`bind`”操作。要求用户提供`username `（专有名称）和`password`，用于启动会话。如果未指定密码或密码错误，则会启动匿名会话。注意，省略密码不会使此测试失败，密码错误才会。（注意，这在测试计划中是未加密存储的）

**参数（Parameters）**

| 属性（Attribute）                    | 描述                                                         | 是否必须 |
| ------------------------------------ | ------------------------------------------------------------ | :------- |
| 名称                                 | 树中显示的此采样器的描述性名称。                             | 否       |
| 服务器名称                           | LDAP服务器的名称（或IP地址）。                               | 是       |
| 端口                                 | LDAP服务器监听的端口号。如果省略，则JMeter假定LDAP服务器正在侦听默认端口（`389`）。 | 否       |
| DN                                   | 将用于任何后续操作的基础对象的可分辨名称。它可以用作所有操作的起点。您无法在比此DN更高的级别上启动任何操作！ | 否       |
| Username                             | 要绑定的用户的完整可分辨名称。                               | 否       |
| Password                             | 上述用户的密码。如果省略，将导致匿名绑定。如果不正确，则取样器将返回错误并恢复为匿名绑定。（注意，这在测试计划中是未加密存储的） | 否       |
| Connection timeout (in milliseconds) | 连接超时时间，如果超时连接将中止                             | 否       |
| Use Secure LDAP Protocol             | 使用`ldaps://`方案而不是`ldap://`                            | 否       |
| Trust All Certificates               | 信任所有证书，仅在选中`Use Secure LDAP Protocol`时使用       | 否       |

 **线程解绑** 

这只是结束会话的操作。它等于LDAP“`unbind`”操作。

**参数（Parameters）**

| 属性（Attribute） | 描述                             | 是否必须 |
| ----------------- | -------------------------------- | :------- |
| 名称              | 树中显示的此采样器的描述性名称。 | 否       |

 **单绑定/解绑** 

这是LDAP“`bind`”和“`unbind`”操作的组合。它可用于任何用户的身份验证请求/密码检查。它将打开一个新会话，只是为了检查用户/密码组合的有效性，并再次结束会话。

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否       |
| Username          | 要绑定的用户的完整可分辨名称。                               | 是       |
| Password          | 上述用户的密码。如果省略，将导致匿名绑定。如果不正确，则取样器将返回错误。（注意，这在测试计划中是未加密存储的） | 否       |

 **重命名条目** 

这是LDAP“`moddn`”操作。它可用于重命名条目，也可用于将条目或完整子树移动到LDAP树中的其他位置。

**参数（Parameters）**

| 属性（Attribute）      | 描述                                                         | 是否必须 |
| ---------------------- | ------------------------------------------------------------ | :------- |
| 名称                   | 树中显示的此采样器的描述性名称。                             | 否       |
| Old entry name         | 相对于线程绑定操作中的给定DN，要重命名或移动的对象的当前可分辨名称。 | 是       |
| New distinguished name | 相对于线程绑定操作中的给定DN，要重命名或移动的对象的新可分辨名称。 | 是       |

 **添加测试** 

这是LDAP“`add`”操作。它可用于将任何类型的对象添加到LDAP服务器。

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否       |
| DN入口            | 相对于线程绑定操作中的给定DN，要添加的对象的可分辨名称。     | 是       |
| 添加测试          | 要用于对象的属性及其值的列表。如果需要添加多值属性，则需要将相同的属性及其各自的值多次添加到列表中。 | 是       |

 **删除测试** 

这是LDAP“`delete`”操作，它可用于从LDAP树中删除对象。

**参数（Parameters）**

| 属性（Attribute） | 描述                                                     | 是否必须 |
| ----------------- | -------------------------------------------------------- | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                         | 否       |
| 删除              | 相对于线程绑定操作中的给定DN，要删除的对象的可分辨名称。 | 是       |

 **搜索测试** 

这是LDAP“`search`”操作，用于定义搜索。

**参数（Parameters）**

| 属性（Attribute）         | 描述                                                         | 是否必须 |
| ------------------------- | ------------------------------------------------------------ | :------- |
| 名称                      | 树中显示的此采样器的描述性名称。                             | 否       |
| Search base               | 相对于线程绑定操作中的给定DN，您希望搜索查找的子树的可分辨名称。 | 否       |
| Search Filter             | 搜索过滤必须在LDAP语法中指定。                               | 是       |
| Scope                     | 使用`0`代表baseobject，`1`为onelevel和`2`为subtree的搜索。（默认=`0`） | 否       |
| 大小限制                  | 指定要从服务器返回的最大结果数。（默认值=`0`，表示没有限制）当取样器达到最大结果数时，它将失败并返回错误代码`4` | 否       |
| Time Limit                | 指定服务器可以在搜索上花费的最大（cpu）时间（以毫秒为单位）。注意这并没有说响应时间。（默认为`0`，表示没有限制） | 否       |
| 属性                      | 指定要返回的属性，以分号分隔。空值将返回所有属性             | 否       |
| Return object             | 返回（`true`）或不返回（`false`）对象。默认=`false`          | 否       |
| 废弃的别名                | 如果为`true`，它将废弃别名，如果为`false`，则不会废弃（默认值=`false`） | 否       |
| Parse the search results? | 如果为`true`，则搜索结果将添加到响应数据中。如果为`false`，则将标记 - 无论是否找到结果 - 添加到响应数据中。 | 否       |

 **修改测试** 

这是LDAP“`modify`”操作。它可以用于修改对象。也可用于添加，删除或替换属性的值。

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否       |
| DN入口            | 相对于线程绑定操作中的给定DN，要修改的对象的可分辨名称       | 是       |
| 修改测试          | attribute-value-opCode三角。opCode可以是任何有效的LDAP operationCode（`add`，`delete/remove`或`replace`）。如果未指定`delete`操作的值，则将删除给定属性的所有值。如果指定了`delete`操作的值，则仅删除给定值。如果该值不存在，采样器将无法通过测试。 | 是       |

 **比较** 

这是LDAP“`compare`”操作。它可用于将给定属性的值与某个已知值进行比较。实际上，这主要用于检查某个人是否是某个组的成员。在这种情况下，您可以将用户的DN作为给定值与`groupOfNames`对象的“`member`”属性中的值进行比较。如果比较操作失败，则测试失败并返回错误代码`49`。

**参数（Parameters）**

| 属性（Attribute） | 描述                             | 是否必须 |
| ----------------- | -------------------------------- | :------- |
| 名称              | 树中显示的此采样器的描述性名称。 | 否       |
| DN入口            | 要绑定的用户的完整可分辨名称。   | 是       |
| 比较过滤器        | 格式为"`attribute=value`"        | 是       |

**另请参阅：**

-  [构建LDAP测试计划](http://jmeter.apache.org/usermanual/build-ldapext-test-plan.html) 
-  [LDAP扩展请求默认值](http://jmeter.apache.org/usermanual/component_reference.html#LDAP_Extended_Request_Defaults) 

[【返回目录】]()

### Access Log Sampler

AccessLogSampler 旨在读取访问日志并生成http请求。对于那些不熟悉访问日志的人来说，它是Web服务器记录它接受的每个请求的日志。这意味着每个图像，CSS文件，JavaScript文件，html文件，......

Tomcat使用通用格式的访问日志。这意味着使用通用日志格式的任何Web服务器都可以使用AccessLogSampler。使用通用日志格式的服务器包括：Tomcat，Resin，Weblogic和SunOne。常见的日志格式如下所示：

    127.0.0.1 - - [21/Oct/2003:05:37:21 -0500] "GET /index.jsp?%2Findex.jsp= HTTP/1.1" 200 8343

> 解析器的当前实现仅查看包含HTTP协议方法的引号内的文本（`GET`，`PUT`，`POST`，`DELETE`，...）。其他所有都被剥离并忽略。例如，解析器完全忽略响应代码。

对于将来，过滤出响应代码不是`200`的条目可能会更好。扩展取样器应该相当简单。您必须实现两个接口：

-  `org.apache.jmeter.protocol.http.util.accesslog.LogParser `
-  `org.apache.jmeter.protocol.http.util.accesslog.Generator`

AccessLogSampler的当前实现使用生成器来创建新的HTTPSampler。服务器名称，端口和获取图像由AccessLogSampler设置。接下来，使用整数`1`调用解析器，告诉它解析一个条目。之后，调用`HTTPSampler.sample()`来发出请求。

    samp = (HTTPSampler) GENERATOR.generateRequest();
    samp.setDomain(this.getDomain());
    samp.setPort(this.getPort());
    samp.setImageParser(this.isImageParser());
    PARSER.parse(1);
    res = samp.sample();
    res.setSampleLabel(samp.toString());

`LogParser`中所需的方法是： 

-  `setGenerator(Generator)`
-  `parse(int) `

 实现`Generator`接口的类应该为所有方法提供具体的实现。有关如何实现任一接口的示例，请参阅  `StandardGenerator`和`TCLogParser`。 

![Screenshot for Control-Panel of Access Log Sampler](http://jmeter.apache.org/images/screenshots/accesslogsampler.png)  
*Access Log Sampler控制面板的截图*

#### （Beta代码）

**参数（Parameters）**

| 属性（Attribute）    | 描述                             | 是否必须         |
| -------------------- | -------------------------------- | :--------------- |
| 名称                 | 树中显示的此采样器的描述性名称。 | 否               |
| Server               | Web服务器的域名或IP地址。        | 是               |
| Protocol             | 方案                             | 否（默认为http） |
| Port                 | Web服务器监听的端口。            | 否（默认为80）   |
| Log parser class     | 日志解析器类负责解析日志。       | 是（默认提供）   |
| Filter               | 过滤器类用于过滤掉某些行。       | 否               |
| Location of log file | 访问日志文件的位置。             | 是               |

 `TCLogParser`为每个线程独立处理访问日志。`SharedTCLogParser`和`OrderPreservingLogParser`共享访问文件，即每个线程获取日志中的下一个条目。 

 `SessionFilter`旨在跨线程处理cookies。它不会过滤掉任何条目，但会修改cookie管理器，以便一次一个线程处理给定IP的cookies。如果两个线程尝试处理来自相同客户端IP地址的样本，则将强制一个线程等待直到另一个线程完成。 

 `LogFilter`旨在允许通过文件名和正则表达式来过滤访问日志条目，以及允许替换文件扩展名。但是，目前无法通过GUI进行配置，因此无法真正使用它。 

[【返回目录】]()

### BeanShell 取样器

此取样器允许您使用BeanShell脚本语言编写取样器。 

  **有关使用BeanShell的完整详细信息，请参阅[BeanShell网站](http://www.beanshell.org/)。**  

> 强烈建议迁移到[JSR223 Sampler](http://jmeter.apache.org/usermanual/component_reference.html#JSR223_Sampler)+Groovy以提高性能，支持新的Java功能并限制BeanShell库的维护。

 测试元件支持`ThreadListener`和`TestListener`接口方法。这些必须在初始化文件中定义。有关定义的示例，请参阅文件`BeanShellListeners.bshrc`。 

 BeanShell取样器还支持`Interruptible`接口。`interrupt()`方法可以在脚本或初始化文件中定义。 

![Screenshot for Control-Panel of BeanShell Sampler](http://jmeter.apache.org/images/screenshots/beanshellsampler.png)  
*BeanShell 取样器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute）             | 描述                                                         | 是否必须               |
| ----------------------------- | ------------------------------------------------------------ | :--------------------- |
| 名称                          | 树中显示的此取样器的描述性名称。该名称存储在脚本变量Label中  | 否                     |
| 每次调用前重置bsh.Interpreter | 如果选择此选项，则将为每个样本重新创建解释器。对于某些长时间运行的脚本，这可能是必需的。有关详细信息请参阅[最佳实践 -  BeanShell脚本](http://jmeter.apache.org/usermanual/best-practices#bsh_scripting)。 | 是                     |
| 参数                          | 要传递给BeanShell脚本的参数。这适用于脚本文件；对于GUI中定义的脚本，您可以在脚本本身中使用所需的任何变量和函数引用。参数存储在以下变量中：<br />`Parameters`：包含参数的字符串，作为单个变量<br />`bsh.args`：包含参数的字符串数组，在空格处分割 | 否                     |
| 脚本文件                      | 包含要运行的BeanShell脚本的文件。文件名存储在脚本变量`FileName`中 | 否                     |
| 脚本                          | 要运行的BeanShell脚本。返回值（如果不为`null`）存储为取样器结果。 | 是（除非提供脚本文件） |

> 注意每个取样器实例都有自己的BeanShell解释器，而取样器只能从一个线程调用

 如果定义了“`beanshell.sampler.init`”属性，则将其作为源文件的名称传递给解析器。这可以用于定义常用方法和变量。bin目录中有一个示例init文件：`BeanShellSampler.bshrc`。 

 如果提供了脚本文件，将使用该脚本文件，否则将使用脚本。

> 在将脚本字段传递给解释器之前，JMeter处理函数和变量的引用，因此引用只会被解析一次。脚本文件中的变量和函数引用将逐字传递给解释器，这可能会导致语法错误。为了使用运行时变量，请使用适当的props方法，例如`props.get("START.HMS");` `props.put("PROP1","1234");`  
>  BeanShell目前不支持Java 5语法，例如泛型和增强的for循环。 

在调用脚本之前，BeanShell解释器会设置一些变量：

参数字段的内容放入变量“`Parameters`”中。该字符串也使用单个空格作为分隔符拆分为单独的标记，并将结果列表存储在字符串数组`bsh.args`中。

可设置的BeanShell变量的完整列表如下：

- `log` - [记录器](https://www.slf4j.org/api/org/slf4j/Logger.html) 

-  `Label` - 取样器标签

-  `FileName` - 文件名（如果有）

-  `Parameters` - 参数字段中的文本

-  `bsh.args` - 参数，按上面描述的拆分

-  `SampleResult` - 指向当前[SampleResult](http://jmeter.apache.org/api/org/apache/jmeter/samplers/SampleResult.html)的指针 

-  `ResponseCode`默认为`200 `

-  `ResponseMessage`默认为“`OK`”

-  `IsSuccess`默认为`true` 

-  `ctx` - [JMeterContext](http://jmeter.apache.org/api/org/apache/jmeter/threads/JMeterContext.html)

- `vars` - [JMeterVariables](http://jmeter.apache.org/api/org/apache/jmeter/threads/JMeterVariables.html) - 例如：

        vars.get("VAR1");
        vars.put("VAR2","value");
        vars.remove("VAR3");
        vars.putObject("OBJ1",new Object());
    
- `props` - JMeter属性（`java.util.Properties`类） - 例如：

        props.get("START.HMS");
        props.put("PROP1","1234");

脚本完成后，控制权返回给取样器，并将以下脚本变量的内容复制到[SampleResult](http://jmeter.apache.org/api/org/apache/jmeter/samplers/SampleResult.html)中的相应变量中：

-  `ResponseCode` - 例如`200` 
-  `ResponseMessage` - 例如“`OK`”
-  `IsSuccess` - `true`或`false`

SampleResult和ResponseData根据脚本的返回值来设置。如果脚本返回null，则可以使用方法`SampleResult.setResponseData(data)`直接设置响应，其中data是String或byte数组。data类型默认为“`text`”，但可以通过`SampleResult.setDataType(SampleResult.BINARY)`方法设置为二进制值 。

`SampleResult`变量给予脚本对`SampleResult`内所有字段和方法完全的访问权限。例如，脚本可以访问`setStopThread(boolean)`和`setStopTest(boolean)`方法。这是一个简单的（不是很有用）示例脚本：

    if (bsh.args[0].equalsIgnoreCase("StopThread")) {
        log.info("Stop Thread detected!");
        SampleResult.setStopThread(true);
    }
    return "Data from sample with Label "+Label;
    //or
    SampleResult.setResponseData("My data");
    return null;

另一个例子：
确保在`jmeter.properties`中定义属性`beanshell.sampler.init=BeanShellSampler.bshrc`。以下脚本将显示`ResponseData`字段中所有变量的值： 

	return getVariables();

 有关各种类可用方法的详细信息（[JMeterVariables](http://jmeter.apache.org/api/org/apache/jmeter/threads/JMeterVariables.html)，[SampleResult](http://jmeter.apache.org/api/org/apache/jmeter/samplers/SampleResult.html)等），请查阅Javadoc或源代码。但要注意，滥用任何方法都可能导致难以发现的细微错误。 

[【返回目录】]()

### JRS223 取样器

JSR223取样器允许使用JSR223脚本代码来执行创建/更新变量所需的一个样本或一些计算。

> 如果您不想在运行此取样器时生成[SampleResult](http://jmeter.apache.org/api/org/apache/jmeter/samplers/SampleResult.html)，请调用以下方法：
>
>        SampleResult.setIgnore();

JSR223测试元件具有可以显著提高性能的功能（编译）。要利用这个特性，请使用下面的方法：

- 使用脚本文件而不是内联它们。如果ScriptEngine上有此功能，JMeter将编译并缓存它们。

- 或者使用脚本文本并检查`Cache compiled script if available`属性。

    > 使用此功能时，请确保您的脚本代码不直接在代码中使用JMeter变量而是使用脚本参数，因为缓存只会缓存第一次替换。

    > 要从缓存和编译中受益，用于脚本编写的语言引擎必须实现JSR223 [Compilable](https://docs.oracle.com/javase/8/docs/api/javax/script/Compilable.html)接口（Groovy就是其中之一，java，beanshell和javascript都不是）

    > 当使用Groovy作为脚本语言并且不检查`Cache compiled script if available`
>
    > 时（建议使用缓存），由于版本2.4.6中的Groovy内存泄漏问题，您应该设置JVM属性`-Dgroovy.use.classvalue=true`，请参阅：
>
    > - [GROOVY-7683](https://issues.apache.org/jira/browse/GROOVY-7683)
> - [GROOVY-7591](https://issues.apache.org/jira/browse/GROOVY-7591)
    > - [JDK-8136353](https://bugs.openjdk.java.net/browse/JDK-8136353)

缓存大小由以下JMeter属性（`jmeter.properties`）控制：

	jsr223.compiled_scripts_cache_size=100

> 与[BeanShell 取样器](http://jmeter.apache.org/usermanual/component_reference.html#BeanShell_Sampler)不同，解释器不会在调用之间保存。

> 如果ScriptEngine支持此功能，使用脚本文件或脚本文本+检查`Cache compiled script if available`的JSR223测试元件将执行编译，这可以实现性能的显著提升。

![Screenshot for Control-Panel of JSR223 Sampler](http://jmeter.apache.org/images/screenshots/jsr223-sampler.png)  
*JSR223取样器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute）        | 描述                                                         | 是否必须               |
| ------------------------ | ------------------------------------------------------------ | :--------------------- |
| 名称                     | 树中显示的此采样器的描述性名称。                             | 否                     |
| 脚本语言                 | 要使用的JSR223脚本语言的名称。<blockquote>支持的语言比下拉列表中显示的要多。如果在JMeter lib目录中安装了相应的jar，则可使用相应的语言。</blockquote> | 是                     |
| 脚本文件                 | 要用作JSR223脚本的文件的名称，如果使用文件的相对路径，则它将使用相对于系统属性“`user.dir`” 所引用的目录 | 否                     |
| 参数                     | 要传递给脚本文件或脚本的参数列表。                           | 否                     |
| 缓存编译脚本（如果可用） | 如果选中（建议）并且使用的语言支持[Compilable](https://docs.oracle.com/javase/8/docs/api/javax/script/Compilable.html)接口（Groovy就是其中之一，java，beanshell和javascript都不是），JMeter将编译并缓存脚本，且使用它的MD5哈希值作为唯一缓存密钥 | 否                     |
| 脚本                     | 要传递给JSR223语言的脚本                                     | 是（除非提供脚本文件） |

如果提供了脚本文件，则使用该脚本文件，否则使用脚本。

在调用脚本之前，会设置一些变量。请注意，这些是JSR223变量 - 即它们可以直接在脚本中使用。

- `log` - [记录器](https://www.slf4j.org/api/org/slf4j/Logger.html)

- `Label` - 取样器标签

- `FileName` - 文件名（如果有）

- `Parameters` - 参数字段中的文本

- `args` - 参数，按照上面描述的拆分

- `SampleResult` - 指向当前[SampleResult的](http://jmeter.apache.org/api/org/apache/jmeter/samplers/SampleResult.html)指针

- `sampler` - （[Sampler](http://jmeter.apache.org/api/org/apache/jmeter/samplers/Sampler.html)） - 指向当前Sampler的指针

- `ctx` - [JMeterContext](http://jmeter.apache.org/api/org/apache/jmeter/threads/JMeterContext.html)

- `vars` - [JMeterVariables](http://jmeter.apache.org/api/org/apache/jmeter/threads/JMeterVariables.html) - 例如：

        vars.get("VAR1");
        vars.put("VAR2","value");
        vars.remove("VAR3");
        vars.putObject("OBJ1",new Object());

- `props` - JMeterProperties（`java.util.Properties`类） - 例如：

        props.get("START.HMS");
        props.put("PROP1","1234");
    
- `OUT` - System.out - 例如`OUT.println("message")`

[SampleResult](http://jmeter.apache.org/api/org/apache/jmeter/samplers/SampleResult.html)和ResponseData根据脚本的返回值来设置。如果脚本返回`null`，则可以使用`SampleResult.setResponseData(data)`方法直接设置响应 ，其中data是String或byte数组。data类型默认为“`text`”，但可以通过`SampleResult.setDataType(SampleResult.BINARY)`方法设置为二进制值 。

`SampleResult`变量给予脚本对`SampleResult`内所有字段和方法完全的访问权限。例如，脚本可以访问`setStopThread(boolean)`和`setStopTest(boolean)`方法。

与BeanShell 取样器不同，JSR223 取样器不通过脚本变量设置`ResponseCode`，`ResponseMessage`和样本status。目前改变这些的唯一方法是通过[SampleResult](http://jmeter.apache.org/api/org/apache/jmeter/samplers/SampleResult.html)方法：

- `SampleResult.setSuccessful(true/false)`
- `SampleResult.setResponseCode("code")`
- `SampleResult.setResponseMessage("message")`

[【返回目录】]()

### TCP 取样器

TCP取样器打开与指定服务器的TCP/IP连接。然后发送文本，并等待响应。

如果选择“`Re-use connection`”，则在同一线程中的取样器之间共享连接，前提是使用完全相同的主机名称和端口。不同的主机/端口组合将使用不同的连接，不同的线程也是如此。如果同时选择“ `Re-use connection` ”和“`Close connection`”，则运行采样器后将关闭套接字。在下一个采样器上，将创建另一个套接字。您可能希望在每个线程循环结束时关闭套接字。

如果检测到错误 - 或未选择“`Re-use connection`” - 则关闭套接字。下一个样本将打开另一个套接字。

以下属性可用于控制其操作：

- `tcp.status.prefix`

    状态码之前的文本

- `tcp.status.suffix`

    状态码之后的文本

- `tcp.status.properties`

    用于将状态代码转换为消息的属性文件的名称

- `tcp.handler`

    TCP Handler类的名称（默认`TCPClientImpl`） - 仅在GUI上未指定时使用

处理连接的类由GUI定义，如果未定义，从`tcp.handler`属性里寻找。如果还是找不到，则在

`org.apache.jmeter.protocol.tcp.sampler`包中搜索该类。

用户可以提供自己的实现。该类必须扩展`org.apache.jmeter.protocol.tcp.sampler.TCPClient`。

目前提供以下实现。

- `TCPClientImpl`
- `BinaryTCPClientImpl`
- `LengthPrefixedBinaryTCPClientImpl`

实现的行为如下：

- `TCPClientImpl`

    这种实现是相当基础的。读取响应时，如果配置了`tcp.eolByte`属性，则读取直到行字节结束，否则直到输入流结束。您可以通过设置`tcp.charset`来控制字符集编码，默认为平台默认编码。

- `BinaryTCPClientImpl`

    此实现将GUI输入（必须是十六进制编码的字符串）转换为二进制，并在读取响应时执行相反的操作。读取响应时，如果配置了`tcp.BinaryTCPClient.eomByte`属性，则读取直到消息字节结束，否则直到输入流结束。

- `LengthPrefixedBinaryTCPClientImpl`

    此实现通过在二进制消息数据前加二进制长度字节来扩展BinaryTCPClientImpl。长度前缀默认为2个字节。可以通过设置属性`tcp.binarylength.prefix.length`来变更。

- 超时处理

    如果设置了超时，则读取将在此过期时终止。因此，如果您使用的是`eolByte/eomByte`，请确保超时设置的足够长，否则将过早终止读取。

- 响应处理

    如果定义了`tcp.status.prefix`，则会在响应消息中从此开始搜索文本，直到后缀结束。如果找到任何此类文本，则用于设置响应代码。然后从属性文件（如果提供）中获取响应消息。
    
    > #### 使用前缀（prefix）和后缀（suffix）
    >
    > 例如，如果prefix = “ `[`“和suffix =“`]`”，则下面的响应：
    >
    > ```
    > [J28] XI123,23,GBP,CR
    > ```
    >
    > 响应代码为`J28`。
    
    “`400`”-“`499`”和“`500`”-“`599`” 范围内的响应代码目前被视为失败; 其他的视为成功。[这需要进行配置！]

> 提供的TCP实现不使用登录名/密码。

在测试运行结束时断开套接字。

![Screenshot for Control-Panel of TCP Sampler](http://jmeter.apache.org/images/screenshots/tcpsampler.png)  
*TCP采样器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute）   | 描述                                                         | 是否必须 |
| ------------------- | ------------------------------------------------------------ | :------- |
| 名称                | 树中显示的此采样器的描述性名称。                             | 否       |
| TCPClient classname | TCPClient类的名称。默认为属性`tcp.handler`，使取不到则取`TCPClientImpl`。 | 是       |
| 服务器名称或IP      | TCP服务器的名称或IP                                          | 是       |
| 端口号              | 使用的端口                                                   | 是       |
| Re-use connection   | 如果选中，则连接保持打开状态。否则，数据读取完将关闭。       | 是       |
| 关闭连接            | 如果选择此选项，采样器运行结束将关闭连接。                   | 否       |
| SO_LINGER           | 创建套接字时通过启用/禁用`SO_LINGER`指定延迟时间（以秒为单位）。如果将“`SO_LINGER`”值设置为`0`，可以防止大量套接字处于`TIME_WAIT`状态。 | 否       |
| 行尾（EOL）字节值   | 行尾的字节值，将其设置为`-128`到`+127`范围之外的值以跳过`eol`检查。您可以在`jmeter.properties`文件中设置此项以及`eolByte`属性。如果在TCP取样器配置和`jmeter.properties`文件中同时设置此项，将使用TCP取样器配置中设置的值。 | 否       |
| 连接超时            | 连接超时时间（毫秒，`0`为禁用）。                            | 否       |
| 响应超时            | 响应超时时间（毫秒，`0`为禁用）。                            | 否       |
| 设置无延迟          | 请参阅`java.net.Socket.setTcpNoDelay()`。如果选中，这将禁用Nagle算法，否则将使用Nagle算法。 | 是       |
| 要发送的文本        | 要发送的文本                                                 | 是       |
| 登录用户            | 用户名 - 默认实现不使用                                      | 否       |
| 密码                | 密码 - 默认实现不使用（注意在测试计划中没有加密存储）        | 否       |

[【返回目录】]()

### JMS 发布

JMS发布将消息发布到给定目标（主题/队列）。对于那些不熟悉JMS的人来说，它是用于消息传递的J2EE规范。市场上有许多JMS服务器，还有一些是开源的。

> JMeter不包含任何JMS实现jar；必须从JMS提供方下载并放入lib目录

![Screenshot for Control-Panel of JMS Publisher](http://jmeter.apache.org/images/screenshots/jmspublisher.png)  
*JMS 发布控制面板的截图*

**参数（Parameters）**

| 属性（Attribute）                 | 描述                                                         | 是否必须                    |
| --------------------------------- | ------------------------------------------------------------ | :-------------------------- |
| 名称                              | 树中显示的此采样器的描述性名称。                             | 否                          |
| use JNDI properties file          | 使用`jndi.properties`。注意该文件必须在classpath中 - 例如，通过更新JMeter`user.classpath`属性。如果未选择此选项，JMeter将使用“`JNDI Initial Context Factory`”和“`Provider URL`”字段来创建连接。 | 是                          |
| JNDI Initial Context Factory      | 上下文工厂的名称                                             | 否                          |
| Provider URL                      | JMS提供方的URL                                               | 是，除非使用jndi.properties |
| Destination                       | 消息目标（主题或队列名称）                                   | 是                          |
| Setup                             | 目标设置类型。设置为`At startup`时，目标名称是静态的（即在测试期间名称始终是一致的），设置为`Each sample`时，目标名称是动态的，并在每个样本评估（即，目标名称是可变的） | 是                          |
| Authentication                    | JMS提供方的身份验证要求                                      | 是                          |
| User                              | 用户名                                                       | 否                          |
| Password                          | 密码（注意在测试计划中没有加密存储）                         | 否                          |
| Expiration                        | 消息过期时间（以毫秒为单位）。如果未指定到期时间，则默认值为`0`（永不过期）。 | 否                          |
| Priority                          | 消息的优先级。从`0`（最低）到`9`（最高）共10个优先级。如果未指定优先级，则默认级别为`4`。 | 否                          |
| Reconnect on error codes (regex)  | 强制重新连接的JMSException错误代码的正则表达式。如果为空则不会重新连接 | 否                          |
| Number of samples to aggregate    | 要聚合的样本数                                               | 是                          |
| Message source                    | 获取消息来源：  <ul><li>`From File`表示所有样本都将读取和重用所引用的文件。如果文件名更改，则从JMeter 3.0开始重新加载</li><li>`Random File from folder specified below`表示将从下面指定的文件夹中选择随机文件，此文件夹必须包含扩展名为`.dat`的文件用于字节消息，或扩展名为`.txt`或`.obj`的文件用于对象或文本消息</li><li>`Text area`用于文本或对象消息的消息</li></ul> | 是                          |
| Message type                      | 文本，MAP，对象消息或字节消息                                | 是                          |
| Content encoding                  | 指定用于读取消息源文件的编码：<ul><li>`RAW`：文件没有变量支持，并使用默认系统字符集加载它。 </li><li>`DEFAULT`：使用默认系统编码加载文件，但依赖于XML prolog的XML除外。如果文件包含变量，则将对其进行处理。</li><li> `Standard charsets`：指定的编码（有效或无效）用于读取文件和处理变量 | 是                          |
| Use non-persistent delivery mode? | 是否设置`DeliveryMode.NON_PERSISTENT`（默认为`false`）       | 否                          |
| JMS Properties                    | JMS属性是特定于底层消息传递系统的属性。您可以设置值的名称，值和类（类型）。默认类型为`String`。例如：对于WebSphere 5.1 Web服务，您需要设置JMS Property targetService来通过JMS测试Web服务。 | 否                          |

 对于MapMessage类型，JMeter将源文件读取为文本行。每行必须有3个字段，用逗号分隔。这些字段是：

- 条目名称
- 对象类名，例如“`String`”（如果未指定，则假定为`java.lang`包）
- 对象字符串值

`valueOf(String)`

    name,String,Example
    size,Integer,1234

> 对象消息的实现和工作方式如下： 
>
> - 将包含对象及其依赖项的JAR放在`jmeter_home/lib/`文件夹中
> - 使用XStream将对象序列化为XML
> - 将结果放在以`.txt`或`.obj`为后缀的文件j中，或将XML内容直接放在文本区域中
>
>  请注意，如果消息位于文件中，则在使用文本区域时将不会替换属性。 

下表显示了在配置JMS时可能有用的一些值：

| Apache [ActiveMQ](http://activemq.apache.org/) | 值                                                     | 备注                                                         |
| ---------------------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| 上下文工厂                                     | org.apache.activemq.jndi.ActiveMQInitialContextFactory | .                                                            |
| 提供者URL                                      | vm://localhost                                         |                                                              |
| 提供者URL                                      | vm:(broker:(vm://localhost)?persistent=false)          | 禁用持久性                                                   |
| 队列参考                                       | dynamicQueues/QUEUENAME                                | [动态定义](http://activemq.apache.org/jndi-support.html#JNDISupport-Dynamicallycreatingdestinations)QUEUENAME到JNDI |
| 主题参考                                       | dynamicTopics/TOPICNAME                                | [动态定义](http://activemq.apache.org/jndi-support.html#JNDISupport-Dynamicallycreatingdestinations)TOPICNAME到JNDI |

[【返回目录】]()

### JMS 订阅

JMS订阅将订阅给定目标（主题或队列）中的消息。对于那些不熟悉JMS的人来说，它是用于消息传递的J2EE规范。市场上有许多JMS服务器，还有一些是开源的。

> JMeter不包含任何JMS实现jar；必须从JMS提供方下载并放入lib目录

![Screenshot for Control-Panel of JMS Subscriber](http://jmeter.apache.org/images/screenshots/jmssubscriber.png)   
*JMS 订阅控制面板的截图*

**参数（Parameters）**

| 属性（Attribute）                | 描述                                                         | 是否必须 |
| -------------------------------- | ------------------------------------------------------------ | :------- |
| 名称                             | 树中显示的此采样器的描述性名称。                             | 否       |
| use JNDI properties file         | 使用`jndi.properties`。注意该文件必须在classpath中 - 例如，通过更新JMeter`user.classpath`属性。如果未选择此选项，JMeter将使用“`JNDI Initial Context Factory`”和“`Provider URL`”字段来创建连接。 | 是       |
| JNDI Initial Context Factory     | 上下文工厂的名称                                             | 否       |
| Provider URL                     | JMS提供方的URL                                               | 否       |
| Destination                      | 消息目标（主题或队列名称）                                   | 是       |
| Durable Subscription ID          | 用于持久订阅的ID。首次使用时，相应的队列若不存在，则会由JMS提供方自动生成。 | 否       |
| Client ID                        | 持久订阅时使用的客户端ID。当你有多个线程时，一定要添加一个像`${__threadNum}`这样的变量。 | 否       |
| JMS Selector                     | 由JMS规范定义的消息选择器，仅提取与选择器条件相关的消息。语法使用SQL 92的子部分。 | 否       |
| Setup                            | 目标设置类型。设置为`At startup`时，目标名称是静态的（即在测试期间名称始终是一致的），设置为`Each sample`时，目标名称是动态的，并在每个样本评估（即，目标名称是可变的） | 是       |
| Authentication                   | JMS提供方的身份验证要求                                      | 是       |
| User                             | 用户名                                                       | 否       |
| Password                         | 密码（注意在测试计划中没有加密存储）                         | 否       |
| Number of samples to aggregate   | 要聚合的样本数                                               | 是       |
| Save response                    | 采样器是否存储响应。如果不是，则仅返回响应长度。             | 是       |
| Timeout                          | 指定要超时时间，以毫秒为单位。`0`=none。这是整体聚合超时，而不是每个样本的超时。 | 是       |
| Client                           | 使用哪个客户端实现。它们都创建了可以读取消息的连接。但是，他们使用不同的策略，如下所述：<ul><li>`MessageConsumer.receive()`为每个请求的消息调用`receive()`。保留样本之间的连接，但仅在采样器处于活动状态时才获取消息。这最适合队列订阅。</li><li>`MessageListener.onMessage()`建立一个监听器，将所有传入的消息存储在队列中。侦听器在采样器完成后仍保持活动状态。这最适合主题订阅。</li></ul> | 是       |
| Stop between samples?            | 如果选中，则JMeter 在每个样本的末尾调用`Connection.stop()`（并在每个样本之前调用`start()`）。在多个样本/线程与同一队列连接的某些情况下，这可能很有用。如果未选中，JMeter将在线程的开始调用`Connection.start()`，并且在线程结束之后调用`stop()`。 | 是       |
| Separator                        | 分隔符用于在有多个消息时分隔消息（与设置要聚合的样本数相关）。注意`\n`，`\r`，`\t`都可以。 | 否       |
| Reconnect on error codes (regex) | 强制重新连接的JMSException错误代码的正则表达式。如果为空则不会重新连接 | 否       |
| Pause between errors (ms)        | 发生错误时，订阅服务器的暂停时间，以毫秒为单位               | 否       |

[【返回目录】]()

### JMS点到点

此采样器通过点到点连接（队列）发送并可选地接收JMS消息。与发布/订阅消息不同，它通常用于处理事务。

`request_only`通常用于对JMS系统产生负载。
`request_reply`这个模式将等待此服务发送的回复队列的响应，所以可以用于测试处理发送到请求队列消息的JMS服务的响应时间时。  
`browse`返回当前队列深度，即队列中的消息数。  
`read`从队列中读取消息（如果有的话）。  
`clear`清除队列，即从队列中删除所有消息。

JMeter 在创建队列连接时使用`java.naming.security.[principal|credentials]`属性 - 如果存在。如果不需要此行为，请设置JMeter属性`JMSSampler.useSecurity.properties=false`

> JMeter不包含任何JMS实现jar；必须从JMS提供方下载并放入lib目录

![Screenshot for Control-Panel of JMS Point-to-Point](http://jmeter.apache.org/images/screenshots/jms/JMS_Point-to-Point.png)  
*JMS点到点控制面板的截图*

**参数（Parameters）**

| 属性（Attribute）                            | 描述                                                         | 是否必须 |
| -------------------------------------------- | ------------------------------------------------------------ | :------- |
| 名称                                         | 树中显示的此采样器的描述性名称。                             | 否       |
| QueueConnection Factory                      | 用于连接到消息传递系统的队列连接工厂的JNDI名称。             | 是       |
| JNDI Name Request queue                      | 这是发送消息的队列的JNDI名称。                               | 是       |
| JNDI Name Reply queue                        | 接收队列的JNDI名称。如果此处提供了值并且通信模式为“`Request Response`”， 则将监视此队列以获得发送请求的响应。 | 否       |
| Number of samples to aggregate               | 要聚合的样本数。仅适用于读取通讯模式。                       | 是       |
| JMS Selector                                 | 由JMS规范定义的消息选择器，仅提取与选择器条件相关的消息。语法使用SQL 92的子部分。 | 否       |
| Communication style                          | 通信模式可以是`Request Only`（也称为Fire and Forget），`Request Response`，`Read`，`Browse`，`Clear`：<ul><li>`Request Only`只会发送消息，不会监控回复。因此，它可用于对系统产生负载。</li><li>`Request Response`将发送消息并监控它收到的回复。行为取决于JNDI Name Reply queue的值。如果JNDI Name Reply Queue有值，则此队列用于监视结果。使用请求的消息id和回复的相关id完成请求和回复的匹配。如果JNDI Name Reply queue为空，则临时队列将用于请求者和服务器之间的通信。这与固定回复队列非常不同。使用临时队列，发送线程将阻塞，直到收到回复消息。使用“`Request Response`”模式，您需要一个服务器来侦听发送到请求队列的消息，并将回复发送到`message.getJMSReplyTo()`引用的队列。</li><li>`Read`将从没有附加侦听器的传出队列中读取消息。这可以方便用于测试目的。该方法仅适用于JMS点到点采样器，可以用来处理没有绑定文件的队列（假设使用了jmeter-jms-skip-jndi库）。在使用绑定文件的情况下，可以使用JMS订阅采样器从队列中读取。</li><li>`Browse`将确定当前队列深度而不从队列中删除消息，返回队列中的消息数。</li><li>`Clear`将清除队列，即从队列中删除所有消息。</li></ul> | 是       |
| Use alternate fields for message correlation | 这些复选框选择将用于将响应消息与原始请求进行匹配的字段。<ul><li>`Use Request Message Id`如果选中，将使用request JMSMessageID，否则将使用request JMSCorrelationID。在后一种情况下，必须在请求中指定相关ID。</li><li>`Use Response Message Id`如果选中，将使用Response JMSMessageID，否则将使用Response JMSCorrelationID。</li></ul>有两种常用的JMS关联模式：<ul><li>**JMS关联ID模式**：即以相关性的Ids匹配请求和响应 =>取消选中两个复选框，并提供相关ID。</li><li>**JMS消息ID模式**：即匹配请求消息id与响应相关id =>仅选择“Use Request Message Id”。</li></ul>在这两种情况下，JMS应用程序都负责根据需要填充相关ID。<blockquote>如果使用相同的队列发送和接收消息，则响应消息将与请求消息相同。在这种情况下，要么提供相关ID，要么清除两个复选框; 或选中两个复选框以使用消息Id进行关联。这对于检查原始JMS吞吐量非常有用。</blockquote> | 是       |
| Timeout                                      | 回复消息的超时时间（以毫秒为单位）。如果在指定时间内未收到回复，则特定测试用例将失败，并且将丢弃超时后收到的特定回复消息。默认值为`2000`毫秒。`0`表示不设超时。 | 是       |
| Expiration                                   | 消息过期时间（以毫秒为单位）。如果未指定，则默认值为`0`（永不过期）。 | 否       |
| Priority                                     | 消息的优先级。从`0`（最低）到`9`（最高）有10个优先级。如果未指定优先级，则默认级别为`4`。 | 否       |
| Use non-persistent delivery mode?            | 是否设置`DeliveryMode.NON_PERSISTENT`。                      | 是       |
| Content                                      | 消息的内容。                                                 | 否       |
| JMS Properties                               | MS属性是特定于底层消息传递系统的属性。您可以设置值的名称，值和类（类型）。默认类型为`String`。例如：对于WebSphere 5.1 Web服务，您需要设置JMS Property targetService来通过JMS测试Web服务。 | 否       |
| Initial Context Factory                      | Initial Context Factory是用于查找JMS资源的工厂。             | 否       |
| JNDI properties                              | JNDI属性是底层JNDI实现的特定属性。                           | 否       |
| Provider URL                                 | JMS提供方的URL。                                             | 否       |

[【返回目录】]()

### JUnit 请求

当前实现支持标准JUnit约定和扩展。它还包括`oneTimeSetUp`和`oneTimeTearDown`等扩展。采样器的工作方式与[Java 请求](http://jmeter.apache.org/usermanual/component_reference.html#Java_Request)类似，但 存在一些差异。

* 与使用JMeter的测试接口不同，它会扫描jar文件以寻找扩展JUnit`TestCase`类的类。这包括任何类或子类。
* JUnit测试jar文件应该放在`jmeter/lib/junit`而不是`/lib`目录中。您还可以使用“`user.classpath`”属性指定查找`TestCase`类的位置。
* JUnit采样器不像[Java请求](http://jmeter.apache.org/usermanual/component_reference.html#Java_Request)那样使用名称/值对进行配置。采样器假定`setUp`和`tearDown`将正确配置测试。
* 采样器仅测量测试方法的经过时间，不包括`setUp`和`tearDown`。
* 每次调用测试方法时，JMeter都会将结果传递给监听器。
* 支持`oneTimeSetUp`和`oneTimeTearDown`作为方法完成。由于JMeter是多线程的，我们不能像Maven那样调用`oneTimeSetUp/oneTimeTearDown`。
* 采样器将意外异常报告为错误。标准JUnit测试运行与JMeter实现之间存在一些重要差异。JMeter不是为每个测试创建一个新的类实例，而是为每个采样器创建一个实例并重用它。这可以通过复选框“`Create a new instance per sample`”进行更改。

采样器的当前实现将尝试首先使用字符串构造函数创建实例。如果测试类没有声明字符串构造函数，则采样器将查找空构造函数。示例如下：

> #### JUnit构造函数
>
> 空构造函数：
>
> ```
> public class myTestCase {
>   public myTestCase() {}
> }
> ```
>
> 字符串构造函数：
>
> ```
> public class myTestCase {
>     public myTestCase(String text) {
>        super(text);
>     }
> }
> ```

默认情况下，JMeter将为成功/失败代码和消息提供一些默认值。用户应定义一组唯一的成功和失败代码，并在所有测试中统一使用它们。

> **一般准则**
>
> 如果使用`setUp`和`tearDown`，请确保将方法声明为public。如果不这样做，测试可能无法正常运行。  
> 以下是编写JUnit测试的一般指南，能使它们在JMeter下运行良好。由于JMeter运行多线程，因此记住某些事情非常重要。
>
> * 编写`setUp`和`tearDown`方法，使它们是线程安全的。这通常意味着避免使用静态成员。
> * 使测试方法成为离散的工作单元，而不是长的动作序列。通过将测试方法保持为离散操作，可以更轻松地组合测试方法以创建新的测试计划。
> * 避免使测试方法相互依赖。由于JMeter允许对测试方法进行任意排序，因此运行行为与默认的JUnit行为不同。
> * 如果测试方法是可配置的，请注意存储属性的位置。建议从Jar文件中读取属性。
> * 每个采样器都会创建一个测试类的实例，因此编写测试时，应在`oneTimeSetUp`和`oneTimeTearDown`中进行初始化等配置。

![Screenshot for Control-Panel of JUnit Request](http://jmeter.apache.org/images/screenshots/junit_sampler.png)  
*JUnit 请求控制面板的截图*

**参数（Parameters）**

| 属性（Attribute）                | 描述                                                         | 是否必须 |
| -------------------------------- | ------------------------------------------------------------ | :------- |
| 名称                             | 树中显示的此采样器的描述性名称。                             | 否       |
| Search for JUnit4 annotations    | 选择此项以搜索JUnit4测试（`@Test`注释）                      | 是       |
| Package filter                   | 以逗号分隔的要显示的包列表。例如，`org.apache.jmeter,junit.framework`。 | 否       |
| Class name                       | JUnit测试类的完全限定名称。                                  | 是       |
| Constructor string               | 传递给字符串构造函数的字符串。如果设置了字符串，则采样器将使用字符串构造函数而不是空构造函数。 | 否       |
| Test method                      | 测试方法。                                                   | 是       |
| Success message                  | 测试成功的描述性信息。                                       | 否       |
| Success code                     | 表示测试成功的唯一代码。                                     | 否       |
| Failure message                  | 测试失败的描述性信息。                                       | 否       |
| Failure code                     | 表示测试失败的唯一代码。                                     | 否       |
| Error message                    | 错误描述。                                                   | 否       |
| Error code                       | 一些错误代码。不需要唯一。                                   | 否       |
| Do not call setUp and tearDown   | 设置采样器不要调用`setUp`和`tearDown`。默认情况下，应调用`setUp`和`tearDown`。不调用这些方法可能会影响测试并使其不准确。此选项仅与调用`oneTimeSetUp`和`oneTimeTearDown`一起使用。如果所选方法是`oneTimeSetUp`或`oneTimeTearDown`，则应检查此选项。 | 是       |
| Append assertion errors          | 是否在响应消息中附加断言错误。                               | 是       |
| Append runtime exceptions        | 是否将运行时异常附加到响应消息。仅在未选择“`Append assertion errors`”时适用。 | 是       |
| Create a new Instance per sample | 是否为每个样本创建新的JUnit实例。默认为false，表示创建一个JUnit`TestCase`并重复使用。 | 是       |

识别以下JUnit4注释：

* `@Test`

  用于查找测试方法和类。支持“`expected`”和“`timeout`”属性。

* `@Before`

  在JUnit3中当做`setUp()`对待

* `@After`

  在JUnit3中当做`tearDown()`对待

* `@BeforeClass`，`@AfterClass`

  作为测试方法处理，因此可以根据需要独立运行

> 注意，当前版本JMeter直接运行测试方法，而不是将其留给JUnit。这是为了允许从采样时间中排除`setUp`/`tearDown`方法。因此，采样器时间排除了调用`setUp`/`tearDown`方法及基于其注释的替代方法所花费的时间。

[【返回目录】]()

### 邮件阅读者取样器

邮件阅读者取样器可以使用POP3(S)或IMAP(S)协议读取（可选删除）邮件消息。

![Screenshot for Control-Panel of Mail Reader Sampler](http://jmeter.apache.org/images/screenshots/mailreader_sampler.png)  
*邮件阅读者取样器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute）               | 描述                                                         | 是否必须            |
| ------------------------------- | ------------------------------------------------------------ | :------------------ |
| 名称                            | 树中显示的此采样器的描述性名称。                             | 否                  |
| Server Type                     | 提供者使用的协议：例如`pop3`，`pop3s`，`imap`，`imaps`。或表示服务器协议的另一个字符串。例如`file`用于只读邮件文件提供者。POP3和IMAP的实际提供者名称是`pop3`和`imap` | 是                  |
| Server                          | 服务器的主机名或IP地址。请参阅下面的`file`协议。             | 是                  |
| Port                            | 用于连接服务器的端口号（可选）                               | 否                  |
| Username                        | 用户登录名                                                   | 否                  |
| Password                        | 用户登录密码（注意在测试计划中没有加密存储）                 | 否                  |
| Folder                          | 要使用的IMAP(S)文件夹。请参阅下面的`file`协议。              | 是，如果使用IMAP(S) |
| Number of messages to retrieve  | 设置此项以检索所有或部分消息                                 | 是                  |
| Fetch headers only              | 如果选中，则仅检索邮件信息头。                               | 是                  |
| Delete messages from the server | 如果设置，将在检索后删除邮件                                 | 是                  |
| Store the message using MIME    | 是否将邮件存储为MIME。如果选中，则整个原始消息存储在响应数据中；信息头不会存储，因为它们在数据中可用。如果未选中，则将邮件信息头存储为响应信息头。部分信息头（`Date`，`To`，`From`，`Subject`）存储在消息体中。 | 是                  |
| Use no security features        | 表示与服务器的连接不使用任何安全协议。                       | 否                  |
| Use SSL                         | 表示与服务器的连接必须使用SSL协议。                          | 否                  |
| Use StartTLS                    | 表示与服务器的连接应尝试启动TLS协议。                        | 否                  |
| Enforce StartTLS                | 如果服务器未启动TLS协议，则终止连接。                        | 否                  |
| Trust All Certificates          | 选择后，它将接受独立于CA的所有证书。                         | 否                  |
| Use local truststore            | 选择后，它只接受本地信任的证书。                             | 否                  |
| Local truststore                | 包含受信任证书的文件路径。是针对当前目录的相对路径。<br/>如果查找证书失败，则到包含测试脚本（JMX文件）的目录中寻找。 | 否                  |

> 您可以通过向user.properties添加[此处](https://javaee.github.io/javamail/docs/api/com/sun/mail/pop3/package-summary.html)描述的任何属性来传递与邮件相关的环境属性。

消息存储为主采样器的子样本。多部分消息按部分存储为消息的子样本。

**关于`file`协议的特殊处理：** 
JavaMail中`file`提供可用于从文件中读取原始邮件。`server`字段用于指定路径的父`folder`。应使用名称`n.msg`存储单个消息文件，其中`n`是消息编号。或者，`server`字段可以是包含单个消息的文件的名称。此实现非常基础，主要用于调试目的。

[【返回目录】]()

### 测试活动（Flow Control Action）

测试活动采样器是一个用于条件控制器的采样器。测试元件不是生成样本，而是暂停或停止所选目标。

此采样器也可以与事务控制器结合使用，因为它允许包含暂停而无需生成样本。对于可变延迟，将暂停时间设置为零，并将定时器添加为子级。

完成正在进行的任何样本后 ，“`stop`”操作将停止线程或测试。“`Stop Now`”操作将停止测试，而无需等待样本完成; 它会中断任何活动样本。如果某些线程未能在5秒的时间限制内停止，则将在GUI模式下显示一条消息。您可以使用`Stop`命令尝试停止线程，如果不能停止，可以手动退出JMeter。在CLI模式下，如果某些线程未能在5秒的时间限制内停止，JMeter将退出。

> 可以使用JMeter属性`jmeterengine.threadstop.wait`更改等待时间。以毫秒为单位。

![Screenshot for Control-Panel of Flow Control Action](http://jmeter.apache.org/images/screenshots/test_action.png)  
*测试活动控制面板的截图*

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须         |
| ----------------- | ------------------------------------------------------------ | :--------------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否               |
| Target            | `Current Thread`/`All Threads`( `Pause` 和 `Go to next loop iteration`时忽略) | 是               |
| Action            | `Pause`/`Stop`/`Stop Now`/`Go to next loop iteration`        | 是               |
| Duration          | 暂停多长时间（毫秒）                                         | 是，如果选择暂停 |

[【返回目录】]()

### SMTP 取样器

SMTP取样器可以使用SMTP/SMTPS协议发送邮件。可以为连接（SSL和TLS）以及用户身份验证设置安全协议。如果使用安全协议，则将对服务器证书进行验证。  
有两种方法可以处理此验证：

* `Trust all certificates`

  这将忽略证书链验证

* `Use a local truststore`

  使用此选项，将根据本地信任库文件验证证书链。

![Screenshot for Control-Panel of SMTP Sampler](http://jmeter.apache.org/images/screenshots/smtp_sampler.png)  
*SMTP采样器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute）                          | 描述                                                         | 是否必须              |
| ------------------------------------------ | ------------------------------------------------------------ | :-------------------- |
| 名称                                       | 树中显示的此采样器的描述性名称。                             | 否                    |
| Server                                     | 服务器的主机名或IP地址。请参阅下面的`file`协议。             | 是                    |
| Port                                       | 用于连接服务器的端口号。默认值为：SMTP=25，SSL=465，StartTLS=587 | 否                    |
| Connection timeout                         | 连接超时时间，以毫秒为单位（套接字级别）。默认不设超时。     | 否                    |
| Read timeout                               | 读取超时时间（以毫秒为单位）（套接字级别）。默认不设超时。   | 否                    |
| Address From                               | 将出现在电子邮件中的发件人地址                               | 是                    |
| Address To                                 | 目标电子邮件地址（多个值以“`;`” 分隔）                       | 是，除非指定了CC或BCC |
| Address To CC                              | 抄送目标电子邮件地址（多个值以“`;`” 分隔）                   | 否                    |
| Address To BCC                             | 密送目标电子邮件地址（多个值以“`;`” 分隔）                   | 否                    |
| Address Reply-To                           | 备用回复地址（多个值以“`;`” 分隔）                           | 否                    |
| Use Auth                                   | 指示SMTP服务器是否需要用户身份验证                           | 否                    |
| Username                                   | 用户登录名                                                   | 否                    |
| Password                                   | 用户登录密码（注意在测试计划中没有加密存储）                 | 否                    |
| Use no security features                   | 表示与SMTP服务器的连接不使用任何安全协议。                   | 否                    |
| Use SSL                                    | 表示与SMTP服务器的连接必须使用SSL协议。                      | 否                    |
| Use StartTLS                               | 表示与SMTP服务器的连接应尝试启动TLS协议。                    | 否                    |
| Enforce StartTLS                           | 如果服务器未启动TLS协议，则终止连接。                        | 否                    |
| Trust All Certificates                     | 选择后，它将接受独立于CA的所有证书。                         | 否                    |
| Use local truststore                       | 选择后，它只接受本地信任的证书。                             | 否                    |
| Local truststore                           | 包含受信任证书的文件路径。是针对当前目录的相对路径。<br/>如果查找证书失败，则到包含测试脚本（JMX文件）的目录中寻找。 | 否                    |
| Override System SSL/TLS Protocols          | 在握手时使用的自定义SSL/TLS协议，它是以空格分隔的列表，例如`TLSv1 TLSv1.1 TLSv1.2`。默认为所有支持的协议。 | 否                    |
| Subject                                    | 电子邮件主题。                                               | 否                    |
| Suppress Subject Header                    | 如果选中，则从发送的邮件中省略“`Subject:`”邮件头。这与发送空的“ `Subject:`”邮件头不同，尽管某些电子邮件客户端可能会以相同的方式显示它。 | 否                    |
| Include timestamp in subject               | 包含主题行中的`System.currentTimemillis()`。                 | 否                    |
| Add Header                                 | 可以使用此按钮定义其他邮件头。                               | 否                    |
| Message                                    | 消息体。                                                     | 否                    |
| Send plain body (i.e. not multipart/mixed) | 如果选中，则将主体作为简单消息发送，即不是`multipart/mixed`，如果可能的话。如果消息正文为空并且只有一个文件，则将文件内容作为消息体发送。<blockquote>注意：如果邮件正文不为空，并且至少有一个附加文件，则消息体将使用`multipart/mixed`发送。</blockquote> | 否                    |
| Attach files                               | 要附加到邮件的文件                                           | 否                    |
| Send .eml                                  | 如果设置，将发送`.eml`文件而不是`Subject`，`Message`和`Attach file(s)`字段中的条目 | 否                    |
| Calculate message size                     | 计算消息大小并将其存储在样本结果中。                         | 否                    |
| Enable debug logging?                      | 如果设置，则“`mail.debug`”属性设置为“`true`”                 | 否                    |

[【返回目录】]()

### OS进程取样器

OS进程取样器是一个可用于在本地计算机上执行命令的采样器。  
它应该允许执行可以从命令行运行的任何命令。  
可以启用返回代码的验证，并可以指定预期的返回代码。

注意OS shell通常提供命令行解析。这在操作系统之间有所不同，但通常shell会按照空格拆分参数。一些shell扩展了通配符文件名; 另一些没有。操作系统之间的引用机制也各不相同。采样器故意不进行任何解析或引用处理。必须以可执行文件期望的形式提供命令及其参数。这意味着采样器设置将无法在操作系统之间移植。

许多操作系统都有一些内置命令，这些命令不作为单独的可执行文件提供。例如，Windows`DIR`命令是命令解释器（`CMD.EXE`）的一部分。这些内置函数不能作为独立程序运行，但必须作为参数提供给相应的命令解释器。

例如，Windows命令行：`DIR C:\TEMP`需要指定如下：

* 命令：

  `CMD`

* 参数1：

  `/C`

* 参数2：

  `DIR`

* 参数3：

  `C:\TEMP`

![Screenshot for Control-Panel of OS Process Sampler](http://jmeter.apache.org/images/screenshots/os_process_sampler.png)  
*OS进程取样器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute）        | 描述                                                         | 是否必须 |
| ------------------------ | ------------------------------------------------------------ | :------- |
| 名称                     | 树中显示的此采样器的描述性名称。                             | 否       |
| 命令                     | 要执行的程序名称。                                           | 是       |
| 工作目录                 | 执行命令的目录，默认为系统属性“`user.dir`”引用的文件夹       | 否       |
| 命令行参数               | 传递给程序名称的参数。                                       | 否       |
| 环境变量                 | 执行命令时添加到环境的键/值对。                              | 否       |
| Standard input (stdin)   | 要从中获取输入的文件的名称（`STDIN`）。                      | 否       |
| Standard output (stdout) | 标准输出（`STDOUT`）的输出文件名。如果省略，则捕获输出并作为响应数据返回。 | 否       |
| Standard error (stderr)  | 标准错误（`STDERR`）的输出文件的名称。如果省略，则捕获输出并作为响应数据返回。 | 否       |
| 检查返回码               | 如果选中，则采样器会将返回码与`预期返回代码`进行比较。       | 否       |
| 预期返回代码             | 如果选中“`检查返回码` ” ，需要填写系统调用的预期返回代码。注意500在JMeter中用作错误指示器，因此您不应使用它。 | 否       |
| Timeout                  | 命令超时时间（以毫秒为单位），默认为`0`，表示不设超时。如果命令执行完之前超时了，JMeter将尝试终止操作系统进程。 | 否       |

[【返回目录】]()

### MongoDB脚本（已弃用）

略

[【返回目录】]()

## 18.2 逻辑控制器

逻辑控制器决定采样器的处理顺序。

### 简单控制器

简单逻辑控制器允许您组织采样器和其他逻辑控制器。与其他逻辑控制器不同，该控制器不提供超出存储设备的功能。

![Screenshot for Control-Panel of Simple Controller](http://jmeter.apache.org/images/screenshots/logic-controller/simple-controller.png)  
*简单控制器的控制面板截图*

**参数（Parameters）**

| 属性（Attribute） | 描述                             | 是否必须 |
| ----------------- | -------------------------------- | :------- |
| 名称              | 树中显示的此采样器的描述性名称。 | 否       |


> #### 使用简单控制器
>
> [下载](http://jmeter.apache.org/demos/SimpleTestPlan.jmx)此示例（参见图6）。在此示例中，我们创建了一个测试计划，该计划发送两个Ant HTTP请求和两个Log4J HTTP请求。我们通过把Ant和Log4J请求放在简单逻辑控制器中将它们分组。请记住，简单逻辑控制器对JMeter如何处理您添加到其中的控制器没有影响。因此，在此示例中，JMeter按以下顺序发送请求：Ant Home Page，Ant News Page，Log4J Home Page， Log4J History Page。
>
> 注意，File Reporter配置为将结果存储在当前目录中名为“`simple-test.dat`”的文件中。
>
> ![Figure 6 Simple Controller Example](http://jmeter.apache.org/images/screenshots/logic-controller/simple-example.png)  
> *图6简单控制器示例*

[【返回目录】]()

### 循环控制器

如果将生成器或逻辑控制器添加到循环控制器，除了为线程组指定的循环次数外，JMeter还将循环它们一定次数。例如，如果将一个HTTP请求添加到循环计数为2的循环控制器，并将线程组循环计数配置为3，则JMeter将发送总共`2 * 3 = 6`个 HTTP请求。

> JMeter将循环索引公开为名为`__jm__<Name of your element>__idx`的变量。因此，例如，假设您的循环控制器名称为LC，那么您可以通过`$ {__ jm__LC__idx}`访问循环索引。索引从0开始

![Screenshot for Control-Panel of Loop Controller](http://jmeter.apache.org/images/screenshots/logic-controller/loop-controller.png)

*循环控制器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须             |
| ----------------- | ------------------------------------------------------------ | :------------------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否                   |
| 循环次数          | 每次测试执行时，将重复此控制器的子元素的次数。<br />值`-1`等同于选中`永远`。<br />**特殊情况：**嵌入在[线程组](http://jmeter.apache.org/usermanual/test_plan.html#thread_group)元件中的循环控制器的行为略有不同。除非设置为永久，否则在完成给定的迭代次数后，它将停止测试。<blockquote>在此字段中使用函数时，请注意可能会多次评估它。例如在循环控制器中使用`__Random`，循环控制器的每个子采样器都会将其评估为不同的值，并导致非预期的行为。</blockquote> | 是的，除非选中“永远” |

> #### 循环示例
>
> [下载](http://jmeter.apache.org/demos/LoopTestPlan.jmx)此示例（参见图4）。在此示例中，我们创建了一个测试计划，该计划发送一个HTTP请求一次，并发送另一个HTTP请求五次。
>
> ![Figure 4 - Loop Controller Example](http://jmeter.apache.org/images/screenshots/logic-controller/loop-example.png)  
> *图4 - 循环控制器示例*
>
> 我们为单个线程配置了线程组，并将循环次数配置为1。我们使用循环控制器，而不是让线程组控制循环。您可以看到我们向线程组添加了一个HTTP请求，并向循环控制器添加了另一个HTTP请求。我们将循环控制器循环次数配置为5。
>
> JMeter将按以下顺序发送请求：Home Page，News Page，News Page，News Page，News Page，和News Page。
>
> > 注意，File Reporter配置为将结果存储在当前目录中名为“`loop-test.dat`”的文件中。

[【返回目录】]()

### 仅一次控制器

仅一次逻辑控制器告诉JMeter每个线程只处理一次内部的控制器，并在进一步迭代测试计划期间执行其后的任何请求。

仅一次控制器现在将始终在任何循环父控制器的第一次迭代期间执行。因此，如果将仅一次控制器放置在指定循环5次的循环控制器下，那么仅一次控制器将仅在第一次迭代时通过循环控制器执行（即每5次）。

注意这意味着如果将它置于一个线程组（每个线程每次测试只运行一次），那么仅一次控制器仍然会像预期的那样运行，但现在用户可以更灵活地使用仅一次控制器。

对于需要登录的测试，请考虑将登录请求放在此控制器中，因为每个线程只需登录一次即可建立会话。

![Screenshot for Control-Panel of Once Only Controller](http://jmeter.apache.org/images/screenshots/logic-controller/once-only-controller.png)   
*仅一次控制器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute） | 描述                             | 是否必须 |
| ----------------- | -------------------------------- | :------- |
| 名称              | 树中显示的此采样器的描述性名称。 | 否       |

> #### 仅一次示例
>
> [下载](http://jmeter.apache.org/demos/OnceOnlyTestPlan.jmx)此示例（参见图5）。在此示例中，我们创建了一个测试计划，其中包含两个发送HTTP请求的线程。每个线程向Home Page发送一次请求，然后向Bug Page发送三次请求。虽然我们将线程组配置为迭代三次，但每个JMeter线程只向Home Page发送一次请求，因为此请求位于仅一次控制器内。
>
> ![Figure 5. Once Only Controller Example](http://jmeter.apache.org/images/screenshots/logic-controller/once-only-example.png)  
> *图5.仅一次控制器示例*
>
> 每个JMeter线程将按以下顺序发送请求：Home Page，Bug Page，Bug Page，Bug Page。
>
> 注意，File Reporter配置为将结果存储在当前目录中名为“`loop-test.dat`”的文件中。

[【返回目录】]()

### 交替控制器

如果将生成器或逻辑控制器添加到交替控制器，则JMeter每次循环迭代时将在每个其他控制器之间交替进行。

![Screenshot for Control-Panel of Interleave Controller](http://jmeter.apache.org/images/screenshots/logic-controller/interleave-controller.png)  
*交替控制器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute）         | 描述                                                         | 是否必须 |
| ------------------------- | ------------------------------------------------------------ | :------- |
| 名称                      | 树中显示的此采样器的描述性名称。                             | 否       |
| 忽略子控制器块            | 如果选中，交替控制器将把子控制器当作单个请求元件来处理，并且每个控制器一次只允许一个请求。 | 否       |
| Interleave across threads | 如果选中，交替控制器每次循环迭代时将跨所有线程在每个子控制器之间交替进行。例如在具有4个线程和3个子控制器的配置中，在第一次迭代时线程1将运行第一个子节点，线程2第二个子节点，线程3第三个子线程，线程4第一个子节点，在下一次迭代中，每个线程将运行下一个子控制器 | 否       |

> #### 简单交替示例
>
> [下载](http://jmeter.apache.org/demos/InterleaveTestPlan.jmx)此示例（参见图1）。在此示例中，我们将线程组配置为具有两个线程且循环次数为5，每个线程总共10个请求。请参阅下表，了解JMeter发送HTTP请求的顺序。
>
> ![Figure 1 - Interleave Controller Example 1](http://jmeter.apache.org/images/screenshots/logic-controller/interleave.png)  
> *图1 - 交替控制器示例1*
>
> | 循环迭代 | 每个JMeter线程发送这些HTTP请求                               |
> | :------- | :----------------------------------------------------------- |
> | 1        | News Page                                                    |
> | 1        | Log Page                                                     |
> | 2        | FAQ Page                                                     |
> | 2        | Log Page                                                     |
> | 3        | Gump Page                                                    |
> | 3        | Log Page                                                     |
> | 4        | 因为控制器中没有更多请求，JMeter重新开始并发送第一个HTTP请求，即News Page |
> | 4        | Log Page                                                     |
> | 5        | FAQ Page                                                     |
> | 5        | Log Page                                                     |

> #### 有用的交替示例
>
> [下载](http://jmeter.apache.org/demos/InterleaveTestPlan2.jmx)另一个示例（参见图2）。在此示例中，我们将线程组配置为具有单个线程且循环次数为8。请注意，测试计划有一个外部交替控制器，里面有两个交替控制器。
>
> ![Figure 2 - Interleave Controller Example 2 ](http://jmeter.apache.org/images/screenshots/logic-controller/interleave2.png)
>
> *图2 - 交替控制器示例2*
>
> 外部交替控制器在两个内部控制器之间交替。然后，每个内部交替控制器在每个HTTP请求之间交替。每个JMeter线程将按以下顺序发送请求： Home Page，Interleaved，Bug Page，Interleaved，CVS Page，Interleaved，和FAQ Page，Interleaved。
>
> 注意，File Reporter配置为将结果存储在当前目录中名为“`interleave-test2.dat`”的文件中。
>
> ![         Figure 3 - Interleave Controller Example 3 ](http://jmeter.apache.org/images/screenshots/logic-controller/interleave3.png)
>
> *图3 - 交替控制器示例3*
>
> 如果主交替控制器下的两个交替控制器是简单控制器，那么顺序将是：Home Page，CVS Page，Interleaved，Bug Page，FAQ Page，Interleaved。
>
> 但是，如果在主交替控制器上选中了“`忽略子控制器块`”，那么顺序将是：Home Page，Interleaved，Bug Page，Interleaved，CVS Page，Interleaved，和FAQ Page，Interleaved。

[【返回目录】]()

### 随机控制器

随机逻辑控制器的作用类似于交替控制器，不同之处在于它通过其子控制器和采样器时不是按顺序排列，而是在每次通过时随机选取一个。

> 多个控制器之间的交互可以产生复杂的行为。随机控制器尤其如此。在假设任何给定的交互给出的结果之前进行实验

![Screenshot for Control-Panel of Random Controller](http://jmeter.apache.org/images/screenshots/logic-controller/random-controller.png)

*随机控制器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否       |
| 忽略子控制器块    | 如果选中，交替控制器将把子控制器当作单个请求元件来处理，并且每个控制器一次只允许一个请求。 | 否       |

[【返回目录】]()

### 随机顺序控制器

随机顺序控制器很像简单控制器，因为它的每个子元件最多只执行一次，但节点的执行顺序是随机的。

![Screenshot for Control-Panel of Random Order Controller](http://jmeter.apache.org/images/screenshots/randomordercontroller.png)

*随机顺序控制器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute） | 描述                             | 是否必须 |
| ----------------- | -------------------------------- | :------- |
| 名称              | 树中显示的此采样器的描述性名称。 | 否       |

[【返回目录】]()

### 吞吐量控制器

吞吐量控制器允许用户控制执行的频率。有两种模式：

- Percent executions
- Total executions

`Percent executions`

  使控制器对测试计划执行一定比例的迭代。

`Total executions`

  使控制器在执行了一定数量的执行后停止执行。

与仅一次控制器一样，当父循环控制器重新启动时，此设置将被重置。

> 该控制器的命名不太好，因为它不控制吞吐量。有关可用于调整吞吐量的元件，请参阅[常数吞吐量定时器](http://jmeter.apache.org/usermanual/component_reference.html#Constant_Throughput_Timer)。 

![Screenshot for Control-Panel of Throughput Controller](http://jmeter.apache.org/images/screenshots/throughput_controller.png)

*吞吐量控制器控制面板的截图*

> 当与其他控制器结合使用时，吞吐量控制器会产生非常复杂的行为 - 特别是父项是交替或随机控制器的情况（也非常有用）。

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否       |
| 执行方式          | 控制器是以Percent executions模式还是以Total executions模式运行。 | 是       |
| 吞吐量            | 一个数字。对于Percent executions模式，`0`到`100`之间的数字表示控制器执行的次数百分比。“`50`”表示控制器将在测试计划的一半迭代期间执行。对于Total executions模式，该数字表示控制器将执行的总次数。 | 是       |
| Per User          | 如果选中，则每个用户将使控制器计算是否应在每个用户（每个线程）的基础上执行。如果未选中，则所有用户的计算都将是全局的。例如，如果使用Total executions模式，并取消选中“`per user`”，则为吞吐量指定的数字将是执行的总执行次数。如果选中“`per user`”，那么执行的总数将是用户数量乘以吞吐量的数量。 | 否       |

[【返回目录】]()

### Runtime控制器

Runtime控制器控制其子节点运行多长时间。控制器将运行其子项，直到超出配置的`Runtime(s)`。

![Screenshot for Control-Panel of Runtime Controller](http://jmeter.apache.org/images/screenshots/runtimecontroller.png)

*Runtime控制器控制面板的屏幕截图*

**参数（Parameters）**

| 属性（Attribute） | 描述                                               | 是否必须 |
| ----------------- | -------------------------------------------------- | :------- |
| 名称              | 树中显示的此采样器的描述性名称，同时用于命名事务。 | 是       |
| Runtime (seconds) | 期望的运行时间。0表示不运行。                      | 是       |

[【返回目录】]()

### 如果（If）控制器

If控制器允许用户控制其下方的测试元件（其子元件）是否运行。

默认情况下，条件仅在初始条目时计算一次，但您可以选择对控制器中包含的每个可运行元件进行计算。

If控制器将在内部使用javascript来计算条件，但这会导致性能下降。

![If Controller using javascript](http://jmeter.apache.org/images/screenshots/if_controller_javascript.png)

*If控制器使用javascript*

更好的选项（默认选项）是使用`Interpret Condition as Variable Expression?`，然后在条件字段中，您有2个选项：

- 选项1：使用包含`true`或`false`的变量

    > 如果要测试最后一个样本是否成功，可以使用`${JMeterThread.last_sample_ok}`
    >
    > ![If Controller using Variable](http://jmeter.apache.org/images/screenshots/if_controller_variable.png)
    >
    > *If控制器使用变量*

- 选项2：使用函数（建议使用`${__jexl3()}`）来计算表达式，表达式必须返回`true`或`false`

    ![If Controller using expression](http://jmeter.apache.org/images/screenshots/if_controller_expression.png)

    *If控制器使用表达式*

例如，以前可以使用条件：`${__jexl3(${VAR} == 23)}`并将其评估为`true`/`false`，然后将结果传递给JavaScript，然后返回`true`/`false`。如果选择了Variable Expression选项，则会计算表达式并与“`true`” 进行比较，而无需使用JavaScript。

> 要测试变量是否未定义（或为null），请执行以下操作，假设变量名为`myVar`，表达式如下：
>
> ```
> "${myVar}" == "\${myVar}"
> ```
>
> 或使用：
>
> ```
> "${myVar}" != "\${myVar}"
> ```
>
> 测试变量是否已定义且不为null。

![Screenshot for Control-Panel of If Controller](http://jmeter.apache.org/images/screenshots/if_controller_expression.png)

*IF控制器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute）                           | 描述                                                         | 是否必须 |
| ------------------------------------------- | ------------------------------------------------------------ | :------- |
| 名称                                        | 树中显示的此采样器的描述性名称。                             | 否       |
| Condition (default JavaScript)              | 默认情况下，条件按**JavaScript**代码解析并返回“`true`”或“`false`”，但这可以被覆盖（见下文） | 是       |
| Interpret Condition as Variable Expression? | 如果选择此选项，则条件必须是计算结果为“`true`”的表达式（忽略大小写）。例如，`${FOUND}`或`${__jexl3(${VAR} > 100)}`。与JavaScript情况不同，仅检查条件是否匹配“`true`”（忽略大小写）。<blockquote>对于性能测试，建议在条件中使用[__jexl3](http://jmeter.apache.org/usermanual/functions.html#__jexl3)或[__groovy](http://jmeter.apache.org/usermanual/functions.html#__groovy)函数</blockquote> | 是       |
| Evaluate for all children                   | 是否应对所有子元件进行评估？如果未选中，则仅在输入时评估条件。 | 是       |

> #### 示例（JavaScript）
>
> - `${COUNT} < 10`
> - `"${VAR}" == "abcd"`
>
> 如果解释代码时出错，则假定条件判断为`false`，并在`jmeter.log`中记录消息。
>
> >   注意，性能测试中建议避免使用JavaScript模式。
> >
> > 使用[__groovy](http://jmeter.apache.org/usermanual/functions.html#__groovy)时请注意不要在字符串中使用变量替换，因为使用更改脚本的变量会无法缓存。应该使用`vars.get("myVar")`获取变量**。** 请参阅下面的Groovy示例。  

> #### 示例（变量表达式）
>
> - `${__groovy(vars.get("myVar") != "Invalid" )}` (Groovy检查myVar是否不等于Invalid)
> - `${__groovy(vars.get("myInt").toInteger() <=4 )}` (Groovy 检查myInt是否小于或等于4)
> - `${__groovy(vars.get("myMissing") != null )}` (Groovy 检查myMissing变量是否没有设置)
> - `${__jexl3(${COUNT} < 10)}`
> - `${RESULT}`
> - `${JMeterThread.last_sample_ok}` (检查最后一个样本是否成功)

[【返回目录】]()

### While控制器

While控制器运行其子项，直到条件为“`false`”停止。

> JMeter将循环索引公开为名为`__jm__<Name of your element>__idx`的变量。因此，例如，如果您的While控制器名为WC，那么您可以通过`${__jm__WC__idx}`访问循环索引。索引从0开始

可能的条件值：

- 空白 - 当循环中的最后一个样本失败时退出循环
- `LAST` - 当循环中的最后一个样本失败时退出循环。如果循环之前的最后一个样本失败，不进入循环。
- 其他 - 当条件等于字符串“`false`” 时退出（或不进入）循环

> 条件可以是最终计算为字符串“`false`”的任何变量或函数。这允许根据需要使用[__jexl3](http://jmeter.apache.org/usermanual/functions.html#__jexl3)，[__groovy](http://jmeter.apache.org/usermanual/functions.html#__groovy)函数，属性或变量。

> 请注意，条件被评估两次，一次是在开始采样子项之前，一次是在子项采样结束时，因此将非幂等函数放入Condition（如[__counter](http://jmeter.apache.org/usermanual/functions.html#__counter)）可能会产生问题。

例如：

- `${VAR}` - 其他测试元件将`VAR` 设置为false
- `${__jexl3(${C}==10)}`
- `${__jexl3("${VAR2}"=="abcd")}`
- `${_P(property)}` - 其他某个地方将属性设置为"`false`"

![Screenshot for Control-Panel of While Controller](http://jmeter.apache.org/images/screenshots/whilecontroller.png)

*While控制器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute） | 描述                                               | 是否必须 |
| ----------------- | -------------------------------------------------- | :------- |
| 名称              | 树中显示的此采样器的描述性名称。同时用于命名事务。 | 否       |
| Condition         | 空白，`LAST`，或变量/函数                          | 否       |

[【返回目录】]()

### Switch控制器

Switch控制器的作用类似于[交替控制器](http://jmeter.apache.org/usermanual/component_reference.html#Interleave_Controller) ，它在每次迭代时运行一个子元件，但控制器运行顺序不是按排列顺序，而是按switch value定义。

> switch value也可以是名称。

如果switch value超出范围，它将运行第0个元件，这是数字情况的默认值。如果值为空字符串，它也是运行第0个元件。

如果该值为非数字（且非空），则Switch控制器将查找具有相同名称的元件（要考虑大小写）。如果没有匹配的名称，则选择名为“`default`”（要考虑大小写）的元件。如果没有默认值，则不选择任何元件，并且控制器不会进行任何操作。

![Screenshot for Control-Panel of Switch Controller](http://jmeter.apache.org/images/screenshots/switchcontroller.png)

*Switch控制器控制面板的截图*  

**参数（Parameters）**

| 属性（Attribute） | 描述                                                       | 是否必须 |
| ----------------- | ---------------------------------------------------------- | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                           | 否       |
| *Switch Value*    | 要调用的从属元件的值（或名称）。元件从0开始编号。默认值为0 | 否       |

[【返回目录】]()

### ForEach控制器

ForEach控制器循环遍历一组相关变量的值。将采样器（或控制器）添加到ForEach控制器时，每个采样（或控制器）执行一次或多次，在每个循环期间变量会有新值。输入应包含多个变量，每个变量都用下划线和数字扩展。每个这样的变量都必须有一个值。因此，例如当输入变量的名称为`inputVar`时，应该定义以下变量：

- `inputVar_1 = wendy`
- `inputVar_2 = charles`
- `inputVar_3 = peter`
- `inputVar_4 = john`

注意：“`_`”分隔符现在是可选的。

当返回变量以“`returnVar`” 给出时，ForEach控制器下的采样器和控制器的集合将连续执行`4`次，返回变量具有相应的上述值，并且可以在采样器中使用。

> JMeter将循环索引公开为名为`__jm__<Name of your element>__idx`的变量。因此，例如，如果您的循环控制器名为FEC，那么您可以通过`${__jm__FEC__idx}`访问循环索引。索引从0开始

它特别适合与正则表达式后处理器一起运行。这可以从先前请求的结果数据中“创建”必要的输入变量。通过省略“`_`”分隔符，可以使用ForEach控制器通过输入变量`refName_g`循环遍历组，并且还可以使用形如`refName_${C}_g`的输入变量循环遍历所有匹配中的所有组，其中`C`是计数器变量。

> 如果`inputVar_1`值为`null`，ForEach控制器不会运行任何样本。如果正则表达式没有返回匹配，则会出现这种情况。

![Screenshot for Control-Panel of ForEach Controller](http://jmeter.apache.org/images/screenshots/logic-controller/foreach-controller.png)

*ForEach 控制器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否       |
| 输入变量前缀      | 要用作输入的变量名称的前缀。默认为空字符串作为前缀。         | 否       |
| 开始循环字段      | 变量循环开始字段（独占）（第一个元件在起始索引+1）           | 否       |
| 结束循环字段      | 循环变量的结束字段（包括）                                   | 否       |
| 输出变量名称      | 变量的名称，可以用于循环中采样器中的替换。默认为空变量名，很可能不需要。 | 否       |
| 使用分隔符        | 如果未选中，则忽略“`_`”分隔符。                              | 是       |

> #### ForEach示例
>
> [下载](http://jmeter.apache.org/demos/forEachTestPlan.jmx)此示例（参见图7）。在此示例中，我们创建了一个测试计划，该计划仅发送一次特定的HTTP请求，并将另一个HTTP请求发送到可在页面上找到的每个链接。
>
> ![Figure 7 - ForEach Controller Example](http://jmeter.apache.org/images/screenshots/logic-controller/foreach-example.png)
>
> *图7 - ForEach控制器示例*
>
> 我们为单个线程配置了线程组，并将循环次数配置为1。您可以看到我们向线程组添加了一个HTTP请求，并向ForEach控制器添加了另一个HTTP请求。
>
> 在第一个HTTP请求之后，添加了一个正则表达式提取器，它从返回页面中提取所有html链接并将它们放在`inputVar`变量中
>
> 在ForEach循环中，添加了一个HTTP采样器，它请求从第一个返回的HTML页面中提取的所有链接。

> #### ForEach示例
>
> 这是您可以下载的[另一个示例](http://jmeter.apache.org/demos/ForEachTest2.jmx)。这有两个正则表达式和ForEach控制器。第一个正则有匹配，第二个没有匹配，因此第二个ForEach控制器不运行任何样本
>
> ![Figure 8 - ForEach Controller Example 2](http://jmeter.apache.org/images/screenshots/logic-controller/foreach-example2.png)
>
> *图8 - ForEach控制器示例2*
>
> 线程组有一个线程，循环次数为2。
>
> Sample 1使用JavaTest取样器返回字符串“`a b c d`”。
>
> 正则表达式提取器使用表达式`(\w)\s`匹配一个字母后跟一个空格，并返回字母（而不是空格）。所有匹配都以字符串“`inputVar`” 为前缀。
>
> ForEach 控制器提取包含前缀“`inputVar_`” 的所有变量，并执行其样本，传递变量“`returnVar`”中的值。在这种情况下，它将依次将变量设置为值“`a`” “`b`”和“`c`”。
>
> `For 1`是另一个Java取样，使用返回变量“`returnVar`”作为样本标签的一部分，同时作为采样数据。
>
> `Sample 2`，`Regex 2`和`For 2`几乎相同，只是正则表达式变成“`(\w)\sx`”，这显然无法获得任何匹配。因此`For 2`样本将不会运行。

[【返回目录】]()

### 模块控制器

模块控制器提供了一种在运行时将测试计划片段替换为当前测试计划的机制。

测试计划片段由控制器和其中包含的所有测试元件（采样器等）组成。片段可以位于任何线程组中。如果片段位于线程组中，则可以禁用其控制器以防止片段在模块控制器以外运行。或者，您可以将片段存储在虚拟线程组中，并禁用整个线程组。

可以有多个片段，每个片段下面都有不同系列的采样器。然后，只需在下拉框中选择适当的控制器，即可使用模块控制器在这些多个测试用例之间轻松切换。这为快速轻松地运行许多备用测试计划提供了便利。

片段名称由控制器名称及其所有父名称组成。例如：

```
Test Plan / Protocol: JDBC / Control / Interleave Controller (Module1)
```

**模块控制器使用的**任何**片段都必须具有唯一的名称**，因为该名称用于在重新加载测试计划时查找目标控制器。因此，最好确保控制器名称不是默认值 - 如上例所示 - 否则在将新元素添加到测试计划时可能会意外创建副本。

![Screenshot for Control-Panel of Module Controller](http://jmeter.apache.org/images/screenshots/module_controller.png)

*模块控制器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否       |
| Module to Run     | 模块控制器提供加载到gui中的所有控制器的列表。在运行时选择要替换的那个。 | 是       |

[【返回目录】]()

### Include控制器

Include控制器旨在使用外部JMX文件。要使用它，在测试计划下创建一个测试片段，并在其下面添加任何所需的采样器，控制器等。然后保存测试计划。该文件现在可以作为其他测试计划的一部分包含在内。

为方便起见，还可以在外部JMX文件中添加[线程组](http://jmeter.apache.org/usermanual/component_reference.html#Thread_Group)以进行调试。[模块控制器](http://jmeter.apache.org/usermanual/component_reference.html#Module_Controller)可用于引用试验片段。该[线程组](http://jmeter.apache.org/usermanual/component_reference.html#Thread_Group)在内部进程中将被忽略。

如果测试使用Cookie管理器或用户定义的变量，则应将这些变量放在顶级测试计划中，而不是包含的文件中，否则无法保证它们正常工作。

> 此元素不支持文件名字段中的变量/函数。  
> 但是，如果定义了属性`includecontroller.prefix`，则内容将用于路径名称的前缀。

> 使用Include控制器并包含相同的JMX文件时，请确保Include控制器命名不同以避免遇到已知问题[Bug 50898](https://bz.apache.org/bugzilla/show_bug.cgi?id=50898)。

如果在`prefix`+`Filename`给定的位置找不到该文件，则控制器会尝试打开相对于JMX启动目录下的`Filename`。

![Screenshot for Control-Panel of Include Controller](http://jmeter.apache.org/images/screenshots/includecontroller.png)

*Include控制器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute） | 描述                             | 是否必须 |
| ----------------- | -------------------------------- | :------- |
| 名称              | 树中显示的此采样器的描述性名称。 | 否       |
| 文件名            | 要包含的文件。                   | 是       |

[【返回目录】]()

### 事务控制器

事务控制器生成一个额外的样本，用于测量执行嵌套测试元件所花费的总时间。

> 注意：如果选中“`Include duration of timer and pre-post processors in generated sample`”复选框，则时间包括控制器范围内的所有处理，而不仅仅是样本。

有两种操作模式：

- 嵌套样本后添加额外的样本
- 添加额外的样本作为嵌套样本的父级

生成的采样时间包括嵌套采样器的所有时间，默认情况下（从2.11开始）不包括定时器和前置/后置处理器执行时间，除非选中“`Include duration of timer and pre-post processors in generated sample`”复选框。根据时钟精度，它可能略长于各个采样器加定时器的总和。在控制器记录开始时间之后但在第一个样本开始之前，时钟开始滴答。在最后也一样。

仅当所有子样本都成功时，生成的样本才被视为成功。

在父模式下，仍可以在查看树监听器中看到各个样本，但不再在其他监听器中显示为单独的条目。此外，子样本不会出现在CSV日志文件中，但可以保存到XML文件中。

> 在父模式下，可以将断言（等）添加到事务控制器中。但是，默认情况下，它们将同时应用于单个样本和整个事务样本。要限制断言的范围，请使用简单控制器来包含样本，并将断言添加到简单控制器。父模式控制器当前不正确支持任何类型的嵌套事务控制器。

![Screenshot for Control-Panel of Transaction Controller](http://jmeter.apache.org/images/screenshots/transactioncontroller.png)

*事务控制器控制面板的截图*

**参数（Parameters）**

| 属性（Attribute）                                            | 描述                                                         | 是否必须 |
| ------------------------------------------------------------ | ------------------------------------------------------------ | :------- |
| 名称                                                         | 树中显示的此采样器的描述性名称，用于命名事务。               | 是       |
| Generate Parent Sample                                       | 如果选中，则生成样本作为其他样本的父项，否则样本将作为独立样本生成。 | 是       |
| Include duration of timer and pre-post processors in generated sample | 是否在生成的样本中包括定时器，前置和后置处理延迟。默认值为`false` | 是       |

[【返回目录】]()

### 录制控制器

记录控制器是指示代理服务器应录制样本位置的占位符。在测试运行期间，它没有任何效果，类似于简单控制器。但在使用[HTTP代理服务器](http://jmeter.apache.org/usermanual/component_reference.html#HTTP(S)_Test_Script_Recorder)录制时，所有录制的样本将默认保存在录制控制器下。

![Screenshot for Control-Panel of Recording Controller](http://jmeter.apache.org/images/screenshots/logic-controller/recording-controller.png)

*录制控制器控制面板的截图*  

**参数（Parameters）**

| 属性（Attribute） | 描述                             | 是否必须 |
| ----------------- | -------------------------------- | :------- |
| 名称              | 树中显示的此采样器的描述性名称。 | 否       |

[【返回目录】]()

### 临界部分控制器

临界部分控制器确保其子元件（采样器/控制器等）将仅由一个线程执行，在执行控制器的子节点之前将执行命名锁定。

![Screenshot for Control-Panel of Critical Section Controller](http://jmeter.apache.org/images/screenshots/logic-controller/critical-section-controller.png)

*临界部分控制器控制面板的截图*

下图显示了使用临界部分控制器的示例，如下图所示2个临界部分控制器用来确保：

- `DS2-${__threadNum}`一次只能由一个线程执行

- `DS4-${__threadNum}`一次只能由一个线程执行

    ![Test Plan using Critical Section Controller](http://jmeter.apache.org/images/screenshots/logic-controller/critical-section-controller-tp.png)

    *使用临界部分控制器的测试计划*

**参数（Parameters）**

| 属性（Attribute） | 描述                                                   | 是否必须 |
| ----------------- | ------------------------------------------------------ | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                       | 否       |
| 锁名称            | 锁定将由控制器执行，确保为不相关的部分使用不同的锁名称 | 是       |

> 临界部分控制器仅在一个JVM中获取锁，因此如果使用分布式测试，请确保您的用例不依赖于所有JVM阻塞的所有线程。

[【返回目录】]()

## 18.3 监听器

除了“监听”测试结果之外，大多数监听器扮演多种角色。它们还提供查看，保存和读取已保存测试结果的方法。

注意，监听器在找到它们的范围的末尾处理。

测试结果的保存和读取是通用。各种监听器都有一个面板，用户可以指定要写入（或读取）结果的文件。默认情况下，结果存储为XML文件，通常带有“`.jtl`”扩展名。存储为CSV是最有效的选项，但不如XML（另一个可用选项）详细。

**监听器不处理在CLI模式下的采样数据，但是，如果配置了输出文件，原始数据将被保存。** 为了分析CLI运行生成的数据，您需要将文件加载到相应的监听器中。

> 要读取现有结果并显示它们，请使用文件面板的浏览按钮打开该文件。

如果要在加载新文件之前清除所有当前数据，请在加载文件之前使用菜单项 *“运行”*  →*“清除”* （`Ctrl`+`Shift`+`E`） 或*“运行”* →“*清除全部”* （`Ctrl`+`E`） 。

可以从XML或CSV格式文件中读取结果。从CSV结果文件中读取时，标题（如果存在）用于确定存在哪些字段。 **为了正确解释无标题的CSV文件，必须在`jmeter.properties`中设置相应的属性。**

> JMeter编写的XML文件在头文件中声明版本1.0，而实际文件使用1.1规则序列化。（这样做是出于历史兼容性原因；请参阅[Bug 59973](https://bz.apache.org/bugzilla/show_bug.cgi?id=59973)和[Bug 58679](https://bz.apache.org/bugzilla/show_bug.cgi?id=58679)）这会导致严格的XML解析器失败。考虑使用非严格的XML解析器来读取JTL文件。

> 文件名可以包含函数and/or变量引用。但是，变量引用在客户端 - 服务器模式下不起作用（函数可以正常工作）。这是因为文件是在客户端上创建的，并且客户端不在本地运行测试，因此不会设置变量。

**如果有大量样本，监听器会使用大量内存。** 目前大多数监听器都在其范围内拷贝每个样本的副本，下列监听器除外：

- 简单数据写入器
- BeanShell/JSR223监听器
- 邮件观察器
- 汇总报告

以下监听器不再需要保存每个样本的副本。相反，聚合具有相同经过时间的样本。现在需要更少的内存，特别是如果大多数样本只需要一两秒钟。

- 聚合报告
- 汇总图

要最小化所需的内存量，请使用简单数据写入器，并使用CSV格式。

> JMeter变量可以保存到输出文件中。这只能使用属性指定。有关详细信息， 请参阅[监听器样本变量](http://jmeter.apache.org/usermanual/listeners.html#sample_variables)

有关设置要保存的默认项的完整详细信息，请参阅[监听器默认配置](http://jmeter.apache.org/usermanual/listeners.html#defaults)文档。有关输出文件内容的详细信息，请参阅[CSV日志](http://jmeter.apache.org/usermanual/listeners.html#csvlogformat)格式或[XML日志](http://jmeter.apache.org/usermanual/listeners.html#xmlformat2.1)格式。

> `jmeter.properties`中的条目用于定义默认值；可以使用“配置”按钮为单个侦听器重写这些内容，如下所示。`jmeter.properties`中的设置也适用于使用命令行标志`-l`添加的监听器。

下图显示了结果文件配置面板的示例

![Result file configuration panel](http://jmeter.apache.org/images/screenshots/simpledatawriter.png)

*结果文件配置面板*

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否       |
| 文件名            | 包含样本结果的文件的名称。可以使用相对路径名或绝对路径名指定文件名。相对路径相对于当前工作目录（默认为`bin/`目录）进行解析。JMeter还支持相对于包含当前测试计划（JMX文件）的目录的路径。如果路径名以“`〜/`”（或JMeter`jmeter.save.saveservice.base_prefix`属性中的任何内容）开头，则假定路径相对于JMX文件位置。 | 否       |
| 浏览...           | 文件浏览按钮                                                 | 否       |
| 仅错误日志        | 选择此选项可以写入/只读错误的结果                            | 否       |
| 仅成功日志        | 选择此选项可以写入/读取没有错误的结果。如果未选择“`仅错误日志`”或“`仅成功日志`”，则处理所有结果。 | 否       |
| 配置              | 配置按钮，见下文                                             | 否       |

#### 样本结果保存配置

侦听器可以通过配置弹窗将不同的选项保存到结果日志文件（JTL），如下所示。默认值的定义如[监听器默认配置](http://jmeter.apache.org/usermanual/listeners.html#defaults)文档中所述。后面带有（`CSV`）的选项仅适用于CSV格式；带有（`XML`）的选项仅适用于XML格式。目前CSV格式无法用于保存包含换行符的任何选项。

注意，cookie、方法和查询字符串将保存为“`Sampler Data`”选项的一部分。

![Screenshot for Control-Panel of Sample Result Save Configuration](http://jmeter.apache.org/images/screenshots/sample_result_config.png)

*样本结果保存配置的控制面板的截图*

[【返回目录】]()

### 图形结果

> 在压力测试期间不得使用图形结果，因为它消耗了大量资源（内存和CPU）。仅用于功能测试或测试计划调试和验证期间。

图形结果监听器生成一个简单的图形，用于绘制所有采样时间。沿着图的底部，以毫秒为单位显示当前样本（黑色），所有样本的当前平均值（蓝色），当前标准偏差（红色）和当前吞吐率（绿色）。

吞吐量数字表示服务器处理的实际请求数/分钟数。此计算包括您添加到测试中的任何延迟以及JMeter自身的内部处理时间。像这样进行计算的优点是这个数字代表了一些真实的东西 - 你的服务器实际上每分钟处理了很多请求，你可以增加线程数和/或减少延迟来发现服务器的最大吞吐量。然而，如果您的计算考虑了延迟和JMeter的处理时间，那么您可能不清楚从该数字可以得出什么结论。

![Screenshot for Control-Panel of Graph Results](http://jmeter.apache.org/images/screenshots/graph_results.png)

*图形结果控制面板的截图*

下表简要介绍了图表中的项目。关于统计术语的确切含义的更多细节可以在网上找到 - 例如维基百科 - 或者查阅统计书。

- `数据` - 绘制实际数据值
- `平均值` - 绘制平均值
- `中值` - 绘制[中值](http://jmeter.apache.org/usermanual/glossary.html#Median)（中途值）
- `偏离` - 绘制[标准差](http://jmeter.apache.org/usermanual/glossary.html#StandardDeviation)（衡量变化）
- `吞吐量` - 绘制每单位时间的样本数

显示屏底部的各个数字是当前值。“`最新样本`”是当前经过的采样时间，在图表上显示为“`数据`”。

图表左上角显示的值是90%响应时间最大值。

[【返回目录】]()

### 断言结果

> 在压力测试期间不得使用断言结果，因为它消耗了大量资源（内存和CPU）。仅用于功能测试或测试计划调试和验证期间。

断言结果可视化器显示每个样本的标签。它还报告了作为测试计划一部分的任何[断言](http://jmeter.apache.org/usermanual/test_plan.html#assertions)的失败。

![Screenshot for Control-Panel of Assertion Results](http://jmeter.apache.org/images/screenshots/assertion_results.png)

*断言结果控制面板的截图*

**另请参阅：**

- [响应断言](http://jmeter.apache.org/usermanual/component_reference.html#Response_Assertion)

[【返回目录】]()

### 查看结果树

> 在压力测试期间不得使用查看结果树，因为它消耗了大量资源（内存和CPU）。仅用于功能测试或测试计划调试和验证期间。

查看结果树是一个显示所有样本响应的树，允许您查看任何样本的响应。除了显示响应之外，您还可以看到获得此响应所花费的时间以及一些响应代码。注意请求面板仅显示JMeter添加的信息头。它不显示可由HTTP协议实现添加的任何信息头（例如`Host`）。

有几种方法可以查看响应，可以通过左侧面板底部的下拉框进行选择。

| **渲染**             | **描述**                                                     |
| :------------------- | :----------------------------------------------------------- |
| `CSS/JQuery Tester`  | *CSS/JQuery测试*只适用于文本响应。它在上面板中显示纯文本。“`测试`”按钮允许用户将CSS/JQuery表达式应用于上面板，结果将显示在下面板中。<br />CSS / JQuery表达式引擎可以是JSoup或Jodd，二者实现的语法略有不同。 <br />例如，应用于当前JMeter函数页面的具有`href`属性的选择器`a[class=sectionlink]`将给出以下输出：<pre><code>Match count: 74</code><br /><code>Match[1]=#functions</code></pre> |
| `Document`           | 该*文档视图*将显示来自不同类型，如Microsoft Office（Word，Excel和PowerPoint中97-2003，2007-2010（OPENXML），Apache的OpenOffice的（作家，钙，留下深刻的印象），HTML，gzip的，JAR / ZIP文件中提取文本文件（内容列表），以及关于“多媒体”文件的一些元数据，如mp3，mp4，flv等。支持格式的完整列表可在[Apache Tika格式页面上找到。](http://tika.apache.org/1.2/formats.html)对**Document视图的**要求是下载[ Apache Tika二进制包](http://tika.apache.org/download.html)（**tika-app-xxjar**）并将其放在**JMETER_HOME / lib**目录中。如果文档大于10 MB，则不会显示。要更改此限制，请设置JMeter属性**document.max_size**（unit is byte）或设置为**0**以删除限制。 |
| **HTML**             | 的*HTML视图*试图呈现响应为HTML。渲染的HTML很可能与任何Web浏览器中的视图相比较差; 但是，它确实提供了快速近似，有助于初始结果评估。 不下载图像，样式表等。 |
| **HTML（下载资源）** | 如果选择了*HTML（下载资源）视图*选项，则渲染器可以下载HTML代码引用的图像，样式表等。 |
| **HTML源格式化**     | 如果选择了*HTML源格式化视图*选项，则渲染器将显示由[Jsoup](https://jsoup.org/)格式化和清理的HTML源代码。 |
| **JSON**             | 该*JSON视图*将显示在树风格的响应（也处理JSON嵌入JavaScript的）。 |
| **JSON路径测试程序** | 该*JSON路径测试视图*将让你测试你的JSON-路径表达式，并从一个特定的反应看到所提取的数据。 |
| **Regexp Tester**    | 在*正则表达式测试仪观点*仅适用于文本响应。它在上面板中显示纯文本。“ **测试** ”按钮允许用户将正则表达式应用于上面板，结果将显示在下面板中。 正则表达式引擎与正则表达式提取器中使用的引擎相同。 例如，应用于当前JMeter主页的RE **（JMeter \ w \*）。\***提供以下输出：  `比赛数：26 匹配[1] [0] = JMeter  -  Apache JMeter </ title> 匹配[1] [1] = JMeter的 匹配[2] [0] = JMeter“title =”JMeter“border =”0“/> </a> 匹配[2] [1] = JMeter的 匹配[3] [0] = JMeterCommitters“>贡献者</a>的 匹配[3] [1] = JMeterCommitters … 等等 … ` **[]中** 的第一个数字是匹配号码; 第二个数字是该组。组**[0]**是与整个RE匹配的任何组合。组**[1]**是任何相匹配的1个第一组，即**（JMeter的\ W \*）**在这种情况下。见图9b（下图）。 |
| **文本**             | 默认的“ *文本”视图*显示响应中包含的所有文本。请注意，这仅在响应**内容类型**被视为文本时才有效。如果**内容类型**以下列任何一个开头，则将其视为二进制，否则将其视为文本。`图片/ 音频/ 视频/ ` |
| **XML**              | 该*XML视图*将显示在树风格响应。任何DTD节点或Prolog节点都不会显示在树中; 但是，响应可能包含那些节点。您可以右键单击任何节点，然后展开或折叠其下的所有节点。 |
| **XPath测试程序**    | 该*XPath的测试*只适用于文本响应。它在上面板中显示纯文本。“ **测试** ”按钮允许用户将XPath查询应用于上面板，结果将显示在下面板中。 |
| **边界提取器测试仪** | 该*边界提取仪*只适用于文本响应。它在上面板中显示纯文本。“ **测试** ”按钮允许用户将边界提取器查询应用于上面板，结果将显示在下面板中。 |

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否       |
| Module to Run     | 如果选中，交替控制器将把子控制器当作单个请求元件来处理，并且每个控制器一次只允许一个请求。</blockquote> | 是       |

[【返回目录】]()

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否       |
| Module to Run     | 如果选中，交替控制器将把子控制器当作单个请求元件来处理，并且每个控制器一次只允许一个请求。</blockquote> | 是       |

[【返回目录】]()

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否       |
| Module to Run     | 如果选中，交替控制器将把子控制器当作单个请求元件来处理，并且每个控制器一次只允许一个请求。</blockquote> | 是       |

[【返回目录】]()

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否       |
| Module to Run     | 如果选中，交替控制器将把子控制器当作单个请求元件来处理，并且每个控制器一次只允许一个请求。</blockquote> | 是       |

[【返回目录】]()

**参数（Parameters）**

| 属性（Attribute） | 描述                                                         | 是否必须 |
| ----------------- | ------------------------------------------------------------ | :------- |
| 名称              | 树中显示的此采样器的描述性名称。                             | 否       |
| Module to Run     | 如果选中，交替控制器将把子控制器当作单个请求元件来处理，并且每个控制器一次只允许一个请求。</blockquote> | 是       |

[【返回目录】]()

