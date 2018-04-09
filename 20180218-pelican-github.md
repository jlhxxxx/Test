title: windows下用Pelican+Github搭建静态博客
Creator: Aman
date: 2018-02-17 02:11:53
category: Programming
tags: github, pelican, blog
slug: pelican-github

## 一、使用Github Pages创建个人博客页面

1. 首先到Github进行账号注册：<https://github.com/>。
2. 创建新仓库，不用新建readme.md，注意仓库名使用*username.github.io*的格式，这里*username*改成自己的用户名。
3. 创建SSH密钥并上传到Github。

## 二、安装Python、Pelican、Markdown和make

1. 安装Python。

2. 安装Pelican。建议在虚拟环境virtualenv下使用。

  * 安装virtualenv（可选）

    ```
    pip install virtualenv
    ```

  * 为了使用virtualenv更方便，可以借助 virtualenvwrapper（可选）

    ```python
    pip install virtualenvwrapper-win
    ```

    配置虚拟环境目录，默认创建的虚拟环境位于C:\Users\username\下，可以通过环境变量 `WORKON_HOME` 来定制。

    通过计算机-->属性-->高级系统设置-->环境变量-->在系统变量中新建“变量名”：`WORKON_HOME`，变量值：`你自定义的路径`。

  * 创建并激活虚拟环境（可选）

    ```python
    mkvirtualenv pelican
    ```

    常用命令：

    ```
    #激活虚拟环境
    workon {虚拟环境目录}
    #停止虚拟环境
    deactivate
    #进入虚拟环境目录
    cdvirtualenv
    #列出所有虚拟环境
    lsvirtualenv
    #检查虚拟环境是否激活（其他方法也可）
    pip list
    ```

  * 安装pelican

    ```python
    pip install pelican
    ```

3. 安装markdown

  ```python
  pip install markdown
  ```

4. 安装make并设置好环境变量[下载地址](http://gnuwin32.sourceforge.net/packages/make.htm)

## 三、创建博客骨架

1. 搭建博客目录（若使用虚拟环境必须在激活虚拟环境前提下执行下列命令，blog文件夹不需要在虚拟环境文件夹下）

  ```powershell
  mkdir blog
  cd blog
  pelican-quickstart
  ```

2. 根据提示一步步输入相应的配置项，不知道如何设置的接受默认即可，后续可以通过编辑pelicanconf.py文件更改配置。完成后将会在根目录生成以下文件

  ```powershell
  .
  |-- content                # 所有文章放于此目录
  │   └── (pages)            # 存放手工创建的静态页面
  |-- develop_server.sh      # 用于开启测试服务器
  |-- Makefile               # 方便管理博客的Makefile
  |-- output                 # 静态生成文件
  |-- pelicanconf.py         # 配置文件
  |-- publishconf.py         # 配置文件
  ```

3. 进入output文件夹，把自己刚刚建好的username.github.io版本库clone下来，注意使用SSH方式，这里以及后文中的username要替换成自己的Github用户名

  ```powershell
  cd output
  git clone git@github.com:username/username.github.io.git
  ```

4. 设置一键上传部署到Github。打开根目录下的Makefile文件，修改以下三个地方，设置完后，以后写完文章就可以通过在blog根目录下执行`make github`进行**一键部署**了。

  ```makefile
  OUTPUTDIR=$(BASEDIR)/output/username.github.io    
  publish:    
      $(PELICAN) $(INPUTDIR) -o $(OUTPUTDIR) -s $(CONFFILE) $(PELICANOPTS)    
  github: publish    
      cd $(OUTPUTDIR); git add . ; git commit -am 'your comments'; git push 
  ```

## 四、通过Markdown试写博文并上传Github发布

1. 创建一个页面：这里以创建 About页面为例。在content目录创建pages子目录：

  ```powershell
  mkdir content/pages
  ```

  然后创建About.md并填入下面内容（一定要有title）：

  ```markdown
  title: About Me        
  date: 2013-04-18       

  About me content
  ```

  相关介绍请参见[官方文档](http://pelican-zh.readthedocs.org/en/latest/zh-cn/)

2. 创建导航目录项：Menu Item设置。在你的博客中，可设置相应的菜单项，菜单项是通过pelicanconf.py设置的，具体如下所示：

  ```makefile
  MENUITEMS = (("ITEM1","http://github.com"),
               ("ITEM2",URL),
              ......)
  ```

3. 在Makefile目录下执行以下命令，即可在本机http://127.0.0.1:8000看到效果。

  ```makefile
  make publish
  make serve
  ```

**参考资料：**

1. [windows下安装Python虚拟环境virtualenvwrapper-win](http://www.cnblogs.com/suke99/p/5355894.html)
2. [Pelican＋Github博客搭建详细教程](http://www.cnblogs.com/cciejh/p/blog_building.html)
3. [pelican创建博客常见问题汇总](http://doc.okbase.net/WJ5888/archive/45851.html)