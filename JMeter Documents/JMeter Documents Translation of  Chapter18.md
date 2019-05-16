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
    
    > **使用前缀（prefix）和后缀（suffix）**
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
| Content encoding                  | 指定用于读取消息源文件的编码：<ul><li>`RAW`：文件没有变量支持，并使用默认系统字符集加载它。  `DEFAULT`：使用默认系统编码加载文件，但依赖于XML prolog的XML除外。如果文件包含变量，则将对其进行处理。  `Standard charsets`：指定的编码（有效或无效）用于读取文件和处理变量指定用于读取消息源文件的编码： | 是                          |
| Use non-persistent delivery mode? | 是否设置DeliveryMode.NON_PERSISTENT（默认为false）           | 否                          |
| JMS Properties                    | JMS属性是特定于底层消息传递系统的属性。您可以设置值的名称，值和类（类型）。默认类型为String。例如：对于WebSphere 5.1 Web服务，您需要设置JMS Property targetService以通过JMS测试Web服务。 | 否                          |

 对于MapMessage类型，JMeter将源读取为文本行。每行必须有3个字段，用逗号分隔。这些领域是：

- 入境名称
- 对象类名，例如“ String ”（如果未指定，则假定为java.lang包）
- 对象字符串值

的valueOf（字符串）

```
名，字符串，实施例
大小，整数，1234
```

> Object消息已实现，其工作方式如下： 
>
> - 将包含对象及其依赖项的JAR放在jmeter_home / lib /文件夹中
> - 使用XStream将对象序列化为XML
> - 将结果放在以.txt或.obj为后缀的文件中，或将XML内容直接放在文本区域中
>
>  请注意，如果消息位于文件中，则在使用“文本区域”时将不会替换属性。 

下表显示了在配置JMS时可能有用的一些值：

| Apache [ActiveMQ](http://activemq.apache.org/) | 值（S）                                                | 评论                                                         |
| ---------------------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| 上下文工厂                                     | org.apache.activemq.jndi.ActiveMQInitialContextFactory | .                                                            |
| 提供者URL                                      | VM：//本地主机                                         |                                                              |
| 提供者URL                                      | VM：（经纪人：（VM：//本地主机）持续= FALSE）          | 禁用持久性                                                   |
| 队列参考                                       | dynamicQueues / QUEUENAME                              | [动态定义](http://activemq.apache.org/jndi-support.html#JNDISupport-Dynamicallycreatingdestinations) QUEUENAME到JNDI |
| 主题参考                                       | dynamicTopics / TOPICNAME                              | [动态地将](http://activemq.apache.org/jndi-support.html#JNDISupport-Dynamicallycreatingdestinations) TOPICNAME [定义](http://activemq.apache.org/jndi-support.html#JNDISupport-Dynamicallycreatingdestinations)为JNDI |

[【返回目录】]()

**参数（Parameters）**

| 属性（Attribute）        | 描述                                                         | 是否必须               |
| ------------------------ | ------------------------------------------------------------ | :--------------------- |
| 名称                     | 树中显示的此采样器的描述性名称。                             | 否                     |
| TCPClient classname      | 要使用的JSR223脚本语言的名称。<blockquote>支持的语言比下拉列表中显示的要多。如果在JMeter lib目录中安装了相应的jar，则可使用相应的语言。</blockquote> | 是                     |
| 脚本文件                 | 要用作JSR223脚本的文件的名称，如果使用文件的相对路径，则它将使用相对于系统属性“`user.dir`” 所引用的目录 | 否                     |
| 参数                     | 要传递给脚本文件或脚本的参数列表。                           | 否                     |
| 缓存编译脚本（如果可用） | 如果选中（建议）并且使用的语言支持[Compilable](https://docs.oracle.com/javase/8/docs/api/javax/script/Compilable.html)接口（Groovy就是其中之一，java，beanshell和javascript都不是），JMeter将编译并缓存脚本，且使用它的MD5哈希值作为唯一缓存密钥 | 否                     |
| 脚本                     | 要传递给JSR223语言的脚本                                     | 是（除非提供脚本文件） |



[【返回目录】]()