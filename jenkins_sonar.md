#  Jenkins +Sonar自动化代码检测

1. 安装插件[SonarQube Scanner for Jenkins](http://redirect.sonarsource.com/plugins/jenkins.html)

2. Jenkins->系统管理->全局工具配置->SonarQube Scanner 安装，自动安装最新版本即可。

3. 工程配置->构建：Execute SonarQube Scanner

    JDK要1.8+，Analysis properties

    ```
    sonar.projectKey=chenyingyu-fzggfwwt
    sonar.projectName=Jenkins-fzggfwwt
    sonar.projectVersion=1.0.0
    sonar.sourceEncoding=UTF-8
    sonar.projectDescription=福州技能提升补贴申报端
    sonar.links.homepage=http://10.10.1.103:81/svn/jy/人事人才/福州市/公共服务网厅/trunk/fzggfwwt
    sonar.language=java
    
    sonar.modules=fzggfwwt-auth,fzggfwwt-person
    fzggfwwt-auth.sonar.projectName=auth
    fzggfwwt-person.sonar.projectName=person
    
    sonar.java.libraries=../lib/*.jar
    sonar.sources=src
    sonar.java.binaries=target/classes
    ```

* jenkins报错1：

    ```
    ERROR: Failed to get Node.js version. No CSS files will be analyzed.
    java.io.IOException: Cannot run program "node": CreateProcess error=2, 系统找不到指定的文件。
    ```

    **解决**：

    1. 安装NodeJS插件

    2. Jenkins->系统管理->全局工具配置->NodeJS 安装：自动安装建议选择稳定版（开发版可能无法下载）
    3. 工程配置->构建环境：勾选`Provide Node & npm bin/ folder to PATH`，选择上一步安装的NodeJS。

* jenkins报错2：

    ```
    ERROR: Error during SonarQube Scanner execution
    ERROR: Error when executing blame for file src/main/webapp/WEB-INF/weblogic.xml
    ERROR: Caused by: svn: E170001: Authentication required for '<http://10.10.1.103:81> svn repository'
    ```

    **解决**：

    参考：[sonar-scanner扫描代码出错 SonarQube svn: E170001](https://www.cnblogs.com/YatHo/p/7345077.html)

    1. Jenkins->系统管理->系统配置，svn选择1.8以上版本
    2. 参考教程，打开sonarqube的控制台，使用admin登录后 ，在配置->SCM->菜单中，将`Disabled the SCM Sensor`设置为`true`

* Jenkins报错3：

    ```
    错误: -source 1.5 中不支持 diamond 运算符
    [ERROR]   (请使用 -source 7 或更高版本以启用 diamond 运算符)
    ```

    **解决**：

    * 原因是Jenkins默认编译用的JDK是1.5版本的，若要对不同项目使用不同JDK，需要安装JDK Parameter插件。

* Jenkins报错4：

    [Sonarqube scanner report upload error 500](https://stackoverflow.com/questions/43143792/sonarqube-scanner-report-upload-error-500)

* jenkins：

    如果在创建项目时候，没有“创建一个Maven 项目”的选项

    安装插件：Maven Integration

### 参考文章

* [使用 Jenkins 与 Sonar 集成对代码进行持续检测](<https://www.ibm.com/developerworks/cn/devops/1612_qusm_jenkins/index.html>)