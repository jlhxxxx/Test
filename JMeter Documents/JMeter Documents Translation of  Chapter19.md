

# 19. 属性参考

## 19 导言

本文档介绍 JMeter 属性。在 `jmeter.properties` 或 `reportgenerator.properties` 中的属性应该在 `user.properties` 文件中设置。这些属性通常在类加载时解析，所以仅在重新启动 JMeter 后才会生效。

## 19.1 语言

**参数（Parameters）**

属性（Attribute）| 描述| 是否必须
---------|----------|---------
language | 首选 GUI 语言。注释掉使用 JVM 默认语言环境的语言。<br/>例如：<br/><pre>language=en</pre><blockquote> 该属性是唯一必须在`jmeter.properties`文件中设置的属性</blockquote></br><blockquote>要完全配置语言，请确保您设置了语言环境，请参阅[国际化：了解 Java 平台中的语言环境](http://www.oracle.com/us/technologies/java/locale-140624.html)。英语示例：</br><pre>-Duser.language = en -Duser.region = EN</pre></blockquote> | 否
locales.add | 可添加到显示列表的其他语言环境。<br/>当前的默认列表是：`en`，`fr`，`de`，`no`，`es`，`tr`，`ja`，`zh_CN`，`zh_TW`，`pl`，`pt_BR`。<br/>请参阅 `JMeterMenuBar＃makeLanguageMenu()`<br/>这些条目是用逗号分隔的语言名称列表。<br/>例如：<br/><pre>locales.add=zu</pre> | 否

## 19.2 XML 解析器

**参数（Parameters）**

属性（Attribute）| 描述| 是否必须
---------|----------|---------
xpath.namespace.config | 包含名称空间映射的属性文件的路径，格式为 `prefix = Namespace`。例如：<br/><pre>ns=http://biz.aol.com/schema/2006-12-18</pre> | 否
xpath2query.parser.cache.size | 用于存储已编译的XPath查询的XPath2查询缓存，默认为`400` | 否 

## 19.3 SSL 配置

> SSL（Java）系统属性现在在 `system.properties` 中。<br/>
> JMeter 不再将`jmeter.properties`中的`javax.xxx`属性转换到系统属性中。这些现在必须在 `system.properties` 文件或命令行中定义。`system.properties` 文件提供了更多的灵活性。

**参数（Parameters）**

属性（Attribute）| 描述| 是否必须
---------|----------|---------
https.sessioncontext.shared | 现在，默认情况下，SSL 会话环境是按每个线程创建的，而不是共享的。<br/>可以通过将此属性设置为`true`来启用旧行为。默认为：`false` |否
https.default.protocol | 请注意，https 默认协议可能因 JVM 的版本而异。请参阅[诊断TLS，SSL和HTTPS](https://blogs.oracle.com/java-platform-group/entry/diagnosing_tls_ssl_and_https) 以及 [Bug 58236](https://bz.apache.org/bugzilla/show_bug.cgi?id=58236) 。默认 HTTPS 协议级别： <br/><pre>https.default.protocol=TLS</pre>可能需要变更为：<br/><pre>https.default.protocol=SSLv3</pre> |否
https.socket.protocols | 要启用的协议列表。如果发现目标服务器有问题, 则可能只能选择一个子集。<br/>当服务器不支持Socket版本协商时，这是必需的，这可能导致错误，例如： <br/>` javax.net.ssl.SSLPeerUnverifiedException: peer not authenticated `或` java.net.SocketException: Connection reset `。<br/>见 [Bug 54759](https://bz.apache.org/bugzilla/show_bug.cgi?id=54759) ，例如：<br/><pre>https.socket.protocols=SSLv2Hello SSLv3 TLSv1</pre> |否
https.cipherSuites | 可用于 HTTPS 连接的以逗号分隔的 SSL 密码套件列表。在与大量用户一起运行时，可能需要使用密码套件的子集来匹配预期的客户端行为或减少 jmeter 中的加密开销。如果 JVM 不支持指定的密码套件，或者 HTTPS 服务器支持的密码套件不与此列表有交集，则可能发生错误。请参阅 [JSSE参考指南](https://docs.oracle.com/javase/8/docs/technotes/guides/security/jsse/JSSERefGuide.html#Customization) 。例如： <br/><pre>https.cipherSuites=TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_RSA_WITH_AES_128_GCM_SHA256</pre> 如果未指定，JMeter 将使用 JVM 支持的默认密码套件列表。 |否
httpclient.reset_state_on_thread_group_iteration | 启动新的线程组迭代时重置 HTTP 状态。总之，`true` 表示下一次迭代与新用户相关联。 `false`表示下一次迭代与同一用户关联。`true`涉及：<br/><ul><li>关闭打开的连接</li><li>重置SSL状态</li></ul>默认为：`true` |否
https.use.cached.ssl.context | 控制是否允许在迭代之间重用 SSL 上下文缓存。<br/> 将值设置为`false`以在每次迭代时重置 SSL 上下文。 <br/>默认为：`true` <br/><blockquote>**强烈反对使用**，你应该使用正确值的` httpclient.reset_state_on_thread_group_iteration `</blockquote> |否
https.keyStoreStartIndex | 开始包含许多条目的密钥库的索引。<br/>默认使用条目`0`，即第一个。默认为：`0`。 |否
https.keyStoreEndIndex | 结束包含许多条目的密钥库的索引。<br/>默认为：`0`。 |否
## 19.4 外观（Look and Feel）配置

**参数（Parameters）**

| 属性（Attribute）           | 描述                                                         | 是否必须 |
| --------------------------- | ------------------------------------------------------------ | -------- |
| jmeter.laf.windows_10       | Swing 默认的 UI 类名。<br/>当鼠标悬停在选项/外观选择列表上时, 可用的 LAF 类名现在显示为工具提示文本。<br/>你可以使用完整的类名，如下所示，也可以使用`System`或者` CrossPlatform `之一，JMeter 将调用` UIManager.get<name>LookAndFeelClassName() `返回相应的字符串。<br/>LAF 会被` os.name `重写（小写，空格会被替换成 ‘_’）。<br/><blockquote><h4> LAF 属性查找顺序 </h4>就拿` Windows 10`的`os.name`作为例子。<br/>Jmeter 首先会拿整个名称去查找属性<pre>jmeter.laf.windows_10=javax.swing.plaf.metal.MetalLookAndFeel</pre>如果查找失败，OS序列会将第一个空格之前的视为`os.name`。在我们的例子中，JMeter 会查找这样一个属性<pre>jmeter.laf.windows=com.sun.java.swing.plaf.windows.WindowsLookAndFeel</pre></blockquote><blockquote>设置为 System LAF 时，mac 显然看起来更好<pre>jmeter.laf.mac=System</pre>如果设置失败，JMeter 默认 LAF 可以这样定义：<pre>jmeter.laf=System</pre>如果上述的`jmeter.laf`都没有定义，JMeter 将使用`CrossPlatform` 的 LAF。这是因为`CrossPlatform`的 LAF 通常要比` System `的 LAF 看起来更好。详情参见 [Bug 52026](https://bz.apache.org/bugzilla/show_bug.cgi?id=52026) 。</blockquote><blockquote>改变外观（LAF）可通过JMeter GUI 菜单 选项（Options） > 外观（Look and Feel），要确保变更完全有效需要重启 JMeter。</blockquote> | 否       |
| jmeter.loggerpanel.display  | 显示 LoggerPanel。<br/>默认为：`false`                       | 否       |
| https.socket.protocols      | 即使关闭，也允许 LogViewer Panel 接收日志事件。<br/>自版本2.12时起启用<br/><blockquote>注意这会对性能产生一些影响，但由于 GUI 模式不能用于负载测试，因此是可以接受的。</blockquote>默认为：`true` | 否       |
| meter.loggerpanel.maxlength | 保留在 LoggerPanel 中的最大行数。`0`表示没有限制。<br/>默认为：`1000` | 否       |
| jmeter.gui.refresh_period   | 以`ms`为单位的间隔时段，用于处理侦听器的事件。<br/>默认为：`500` | 否       |

## 19.5 工具栏显示

**参数（Parameters）**

| 属性（Attribute）         | 描述                                                         | 是否必须 |
| ------------------------- | ------------------------------------------------------------ | -------- |
| jmeter.toolbar.icons      | 工具栏图标定义。<br/>默认为`org/apache/jmeter/images/toolbar/icons-toolbar.properties` | 否       |
| jmeter.toolbar            | 工具栏列表。<br/>默认为：<pre>new,open,close,save,save_as_testplan,\|,cut,copy,paste,\|,expand,collapse,toggle,\|,test_start,test_stop,test_shutdown,\|,test_start_remote_all,test_stop_remote_all,test_shutdown_remote_all,\|,test_clear,test_clear_all,\|,search,search_reset,\|,function_helper,help</pre> | 否       |
| jmeter.toolbar.icons.size | 可用的尺寸为：`22×22`，`32×32`，`48×48`。HiDPI模式的建议值是<pre>jmeter.toolbar.icons.size = 48x48的</pre>默认为：`22x22` | 否       |
| jmeter.icons              | 图标定义。备用集：<pre>jmeter.icons=org/apache/jmeter/images/icon_1.properties</pre>历史图标集（不建议使用）：<pre>jmeter.icons=org/apache/jmeter/images/icon_old.properties</pre>默认为：`org/apache/jmeter/images/icon.properties` | 否       |
| jmeter.tree.icons.size    | 可用的尺寸为：`19×19`，`24×24`，`32×32`，`48×48`。对HiDPI显示有用（见下文）。<br/>默认值：`19x19 `
HiDPI屏幕的建议值，如3200x1800：`32x32` | 否       |
| jmeter.hidpi.mode         | HiDPI模式。激活‘伪’-HiDPI 模式。允许增加在 Linux 或 Windows 中使用高分辨率屏幕时 JVM 无法正确管理的某些 UI 元素的大小。<br/>默认为：`false` | 否       |
| jmeter.hidpi.scale.factor | HiDPI比例因子。HiDPI的建议值：`2.0`。默认为：`1.0`           | 否       |
| not_in_menu               | 要在JMeter GUI中不显示的组件（GUI类名称或静态标签）。<br/>这些元素已弃用，将在下一版本中删除：<pre>MongoDB Script, MongoDB Source Config</pre>默认为：<pre>org.apache.jmeter.protocol.mongodb.sampler.MongoScriptSampler, org.apache.jmeter.protocol.mongodb.config.MongoSourceElement</pre> | 否       |
| undo.history.size         | 撤消历史记录中的项目数。<br/>默认情况下，功能被禁用（0），因为已知且未修复错误 [Bug 57043](https://bz.apache.org/bugzilla/show_bug.cgi?id=57043)，[ Bug 57039](https://bz.apache.org/bugzilla/show_bug.cgi?id=57039)和[ Bug 57040](https://bz.apache.org/bugzilla/show_bug.cgi?id=57040)。将其设置为大于零的数字（25可以是一个很好的默认值）。
它越大，消耗的内存就越多。默认为：0 | 否       |
| gui.quick_X               | 热键增加JMeter的组件，其中*X*是快捷键，例如：<pre>gui.quick_0 = ThreadGroupGui<br/>gui.quick_1 = HttpTestSampleGui <br/>gui.quick_2 = RegexExtractorGui <br/>gui.quick_3 = AssertionGui <br/>gui.quick_4 = ConstantTimerGui <br/>gui.quick_5 = TestActionGui <br/>gui.quick_6 = JSR223PostProcessor <br/>gui.quick_7 = JSR223PreProcessor <br/>gui.quick_8 = DebugSampler <br/>gui.quick_9 = ViewResultsFullVisualizer</pre>当您按Ctrl  +  0时，上面的代码将添加相应的元素 ... Ctrl  +  9（在Mac上为⌘  +  0 ... ⌘  +  9） | 否       |