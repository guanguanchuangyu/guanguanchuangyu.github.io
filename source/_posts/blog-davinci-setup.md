---
title: Windows中安装Davinci
tags:
  - Tools
categories:
  - 江湖秘籍
date: 2020-04-18 21:29:48
---


>Davinci 是一个 DVaaS（Data Visualization as a Service）平台解决方案，面向业务人员/数据工程师/数据分析师/数据科学家，致力于提供一站式数据可视化解决方案。既可作为公有云/私有云独立部署使用，也可作为可视化插件集成到三方系统。用户只需在可视化 UI 上简单配置即可服务多种数据可视化应用，并支持高级交互/行业分析/模式探索/社交智能等可视化功能。

<!--more-->

当前教程将介绍`Davinci`在`Windows`中的环境搭建操作步骤。

# 环境要求

`JDK≥1.8`

`MySql≥5.5`

`Mail Server 邮箱服务器`

`Chrome`或`phantomjs`支持浏览器操作

系统发布包（不是`SourceCode`）

# 解压软件

在系统中，需要软件常用位置解压`Davinci`发布包，结构如下：

![blog-unzip](https://file.budbud.cn/ggcyblog/blog-davinci-setup/blog-unzip.png)

# 数据库配置

数据库脚本为`bin`下的`davinci.sql`

## 初始化数据库

在`mysql`中添加名称为`davinci0.3`的数据库，用于后续执行`sql`脚本使用

![blog-mysql-adddb](https://file.budbud.cn/ggcyblog/blog-davinci-setup/blog-mysql-adddb.png)

### 方式一【推荐】

切换到`mysql`程序所在`bin`目录下，确保`mysql`服务是运行状态：

![blog-mysql-service](https://file.budbud.cn/ggcyblog/blog-davinci-setup/blog-mysql-service.png)

执行如下指令，对数据库进行初始化，`davinci.sql`在`Davinci`解压路径下的`bin`文件夹中，参考如下，其中`davinci0.3`为本地`mysql`中提前建立好的数据库名称：

```
mysql -P 3306 -h localhost -u 用户名 -p密码 davinci0.3 < 脚本所在绝对路径/bin/davinci.sql
```

![blog-initsql](https://file.budbud.cn/ggcyblog/blog-davinci-setup/blog-initsql.png)

#### 执行结果

`cmd`中执行结构，除了以下提示信息外，无其他异常提示

![blog-mysql-initdb01](https://file.budbud.cn/ggcyblog/blog-davinci-setup/blog-mysql-initdb01.png)

数据库中生成对应表结构

![blog-mysql-tables](https://file.budbud.cn/ggcyblog/blog-davinci-setup/blog-mysql-tables.png)

### 方式二

配置全局环境变量`MYSQL_HOME`对应`Mysql`的`bin`文件夹路径，配置全局环境`DAVINCI3_HOME`对应为`Davinci`项目解压路径中的`bin`文件夹

配置好之后，执行`Davinci`解压路径下`bin`中的`initdn.bat`脚本

## 修改本地配置

将`config`文件夹下的`application.yml.example`和`datasource_driver.yml.examply`尾缀`example`去除

![blog-config](https://file.budbud.cn/ggcyblog/blog-davinci-setup/blog-config.png)

其中`application.yml`中包含了，系统中基本的配置，如`web`访问`ip`地址以及端口号，项目的根目录等配置

## 配置数据库账户

打开`application.yml`，找到`datasource`节点，添加上`mysql`对应的访问账户以及密码，由于是`yml`文件，需要注意属性和属性值之间必须带有`至少一个空格`

```yaml
  datasource:
    url: jdbc:mysql://localhost:3306/davinci0.3?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull&allowMultiQueries=true
    username: 用户名
    password: 用户密码
```

## 配置邮箱服务器

`Davinci`默认使用时，需要注册账户，需要邮箱激活，所以需要配置一个邮箱服务器对应的账户

同样是在`application.yml`文件中配置，找到`mail`节点

```yaml
  mail:
    host: 邮箱服务器
    port: 端口号
    username: 用户名
    fromAddress: 发送地址，一般和用户名一致
    password: SMTP提供的客户端密钥
    nickname: 个人定义
```

配置邮箱服务器详见，[mail配置](https://edp963.github.io/davinci/docs/zh/1.1-deployment#243-mail-配置)

# 启动项目

在`Davinci`解压文件路径下的`bin`文件夹中，存在着完整脚本执行文件`.sh`结尾为`shell`脚本，`.bat`为适用于`windows`的`bat`脚本，此处使用`bat`脚本

![blog-run-bat](https://file.budbud.cn/ggcyblog/blog-davinci-setup/blog-run-bat.png)

在`bin`文件夹下，执行`cmd`指令执行`run.bat`和`start.bat`脚本

```bash
run.bat start
```

弹出系统运行窗，执行结果如下：

![blog-run-start](https://file.budbud.cn/ggcyblog/blog-davinci-setup/blog-run-start.png)

在浏览器中，输入`http://localhost:8080/`可显示登录页面，则代表服务启动成功

![blog-login](https://file.budbud.cn/ggcyblog/blog-davinci-setup/blog-login.png)

# 注册账户

开始【注册账户】

![blog-davinci-register](https://file.budbud.cn/ggcyblog/blog-davinci-setup/blog-davinci-register.png)

填写用户名、邮箱、密码，点击注册，出现激活提示页面

![blog-email-active](https://file.budbud.cn/ggcyblog/blog-davinci-setup/blog-email-active.png)

登录对应注册时填写的邮箱，进行邮箱的激活操作，此处以`QQ邮箱`为例

![blog-email-QQ](https://file.budbud.cn/ggcyblog/blog-davinci-setup/blog-email-QQ.png)

激活成功后，`Davinci`能够自动完成首次登录

![blog-davinci-main](https://file.budbud.cn/ggcyblog/blog-davinci-setup/blog-davinci-main.png)

至此就可以开始，通过官方给定的用户文档进行，`Davinci`的操作了

# 用户使用文档

对系统的基础使用，可以参考[官方使用文档](https://edp963.github.io/davinci/docs/zh/1.1-deployment)

