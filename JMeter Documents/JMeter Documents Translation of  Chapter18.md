

--# 18. 组件参考
<!-- TOC -->

- [18 导言](#18-导言)
- [18.1 取样器](#181-取样器)
    - [FTP 请求](#ftp-请求)
    - [HTTP 请求](#http-请求)
    - [哈哈](#哈哈)

<!-- /TOC -->

## 18 导言

> 少数测试元件使用JMeter属性来控制它们的行为。这些属性通常在类被加载时解析。这通常发生在测试计划执行前，所以不能通过[__setProperty()](http://jmeter.apache.org/usermanual/functions.html#__setProperty)函数来改变设置。

## 18.1 取样器（Samplers）

取样器执行JMeter实际的工作。每个取样器（[测试活动](http://jmeter.apache.org/usermanual/component_reference.html#Flow_Control_Action)除外）生成一个或多个样本结果。样本结果具有各种属性（成功/失败，经过时间，数据大小等），并且可以在各种监听器中查看。

### FTP 请求（FTP Request）

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

请参阅：

* [断言](http://jmeter.apache.org/usermanual/test_plan.html#assertions)
* [FTP默认请求](http://jmeter.apache.org/usermanual/component_reference.html#FTP_Request_Defaults)
* [创建FTP测试计划](http://jmeter.apache.org/usermanual/build-ftp-test-plan.html)

[【返回目录】]()

### HTTP 请求（HTTP Request）

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

![HTTP Request Advanced config fields](http://jmeter.apache.org/images/screenshots/http-request-advanced-tab.png)*HTTP请求高级配置字段*

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

请参阅：

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

### JDBC请求（JDBC Request）

此采样器允许您将JDBC请求（SQL查询）发送到数据库。

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

请参阅：

* [构建数据库测试计划](http://jmeter.apache.org/usermanual/build-db-test-plan.html)
* [JDBC Connection Configuration](http://jmeter.apache.org/usermanual/component_reference.html#JDBC_Connection_Configuration)

> 当前版本的JMeter使用UTF-8作为字符编码。以前使用平台默认值。

> 确保变量名称在测试计划中是唯一的。

[【返回目录】]()

### Java请求（Java Request）





[【返回目录】]()