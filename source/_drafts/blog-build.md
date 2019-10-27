---
title: Hexo + Next Theme + Github 的个人托管博客搭建
tags: Hexo
---
作为一名码农，从校园开始就希望有一个独属于自己的博客站点，有想法不行动，拖拖拖，折腾来折腾去，无意间发现了`Hexo`，惊鸿一瞥，就走不动路了，心心念念开始了入坑之路

<!-- more -->

##   安装Hexo的环境要求

**git+node.js** 以下为基于`windows`系统的操作，安装过程请执行百度

[git和ssh key 生成 ]( https://www.jianshu.com/p/a3b4f61d4747)

如果有兴趣的同学可以尝试nvm进行node.js的安装和管理

## 安装Hexo Cli客户端

打开cmd控制台，输入npm安装hexo客户端的指令：

```
npm install -g hexo-cli
```

**npm** 为`node.js`的包管理工具的执行指令

**-g** 代表`npm`全局安装`hexo-cli`，避免在当前计算机中进行`hexo`客户端的多版本的重复安装

执行结果如下：

<img src="blog_build_hexo-cli.png" alt="blog_build_hexo-cli" style="zoom:145%;" />

## 安装Hexo

安装好`hexo-cli`之后，接下来就需要在本地安装`hexo`，执行指令如下：

```
npm install -g hexo
```

执行结果如下：

<img src="blog_build_hexo.png" alt="hexo_install" style="zoom:145%;" />

##   初始化个人的博客本地站点

安装好`Hexo`之后，输入**hexo -v** 查看是否安装成功

```
hexo -v
```

执行结果如下：

<img src="blog_build_hexo-v.png" alt="blog_build_hexo-v" style="zoom:145%;" />

找到一个自己需要放博客站点的文件夹路径，此处为`myblog`：

```
hexo init myblog[本地站点名称]
```

**myblog**也是本地的站点根目录文件夹名称，执行可能会比较缓慢，建议通过国内镜像，如淘宝镜像`cnpm`进行下载安装

执行结果如下：

<img src="blog_build_hexo-init.png" alt="blog_build_hexo-init" style="zoom:145%;" />

### 项目文件结构

站点结构如下：

![blog_build_blog_projects](blog_build_filetree.png)

| 名称            | 用途                                                         |
| --------------- | ------------------------------------------------------------ |
| node_modules    | hexo需要的依赖包                                             |
| scaffolds       | 站点生成文章的模板                                           |
| source          | 用来存放个人的博客文章`.md`格式                              |
| public          | 存放`.md`文件生成之后的静态文件和相关博客资源（发布生成之后的文件存储位置） |
| theme           | 博客展示也主题                                               |
| **_config.yml** | hexo博客站点的相关配置文件                                   |

### 本地测试博客站点

在`myblog\source\_posts`存在一个默认的hello-world.md文件，可尝试在本地进行访问，操作如下：

1. 切换执行目录到**myblog**文件夹下

```
cd myblog[你的站点文件夹路径,此处为myblog]
```

2. 执行**hexo**的**generate**生成静态文件

```
hexo generate
或
hexo g
```

执行结果如下：
<img src="blog_build_hexo-g.png" alt="blog_build_hexo-g" style="zoom:145%;" />

3. 发布后本地运行监听服务

```
hexo server
或
hexo s
```

执行结果如下：
<img src="blog_build_hexo_s.png" alt="blog_build_hexo-s" style="zoom:145%;" />

4. 本地测试访问

在浏览器中访问：`http://localhost:4000/`

查看到如下效果在表明博客生成成功并能够正常访问

![blog_build_blogtest](blog_build_test.png)

##   Github搭建个人仓库

注册`Github账户`，添加一个仓库，仓库命名格式为 **当前账户名.github.io**

##   本地构建ssh公钥私钥

启动git bash执行创建全局git相关github的账户名和github的注册使用的邮箱的指令

```
git config --global user.name "github账户名"
git config --global user.email "github注册邮箱"
```

验证配置的信息

```
git config user.name
```

输出：“github账户名”

```
git config user.email
```

输出：“github注册邮箱”

创建ssh，生成相关的文件，youremail可以和github的注册邮箱不同

```
ssh-keygen -t rsa -C "youremail"
```



生成单个SSH Key时可以连续回车，不输入密钥文件名字和密码 ，记下文件存储路径，一般在[C:/Users/用户名/.ssh]路径下

![blog_build_git_ssh](blog_build_git_ssh.png)

其中**id_rsa**保存着自己的私钥，这是需要访问`Github`时，本地作为信息凭证的保留文件，妥善保管

**id_rsa.pub**为公钥，需要添加到`Github账户`中，便于本地能够提交变更到github对应账户，

##   Github添加个人公钥

在Github的`Settings`中找到` SSH and GPG keys `的选项，点击`New SSH key`按钮，设定好key的名称和将本地生成的**id_rsa.pub**文件内的内容全部复制到key内容框中，无错后点击保存即可

<img src="blog_build_github_addssh.png" alt="blog_build_github_test" style="zoom:90%;" />



`Github`上操作成功后，执行如下git指令用于验证以上配置是否成功：

```
git -T git@github.com
```

执行结果如下：

<img src="blog_build_ssh_githubtest.png" alt="blog_build_ssh_githubtest" style="zoom:145%;" />

##   本地yml文件配置Github对应的仓库

接下来就可以对本地的博客站点进行配置了，找到博客站点所在根目录下的**_config.yml**文件，找到deploy节点进行如下配置：

```
deploy:
  type: git
  repo: https://github.com/YourgithubName/YourgithubName.github.io.git
  branch: master
```

配置好以后，测试一下`hexo`对于该站点是否正确，无异常输出则表示配置没有问题，若有异常请根据提示，自行解决异常

```
hexo g
```

无异常之后，本地通过`npm`安装`hexo-deployer-git`工具包，便于本地站点生成的文件发布到github的对应仓库

```
npm install -g hexo-deployer-git
```

##   发布部署博客到Github

安装成功`hexo-deployer-git`后，开始再次执行生成本地站点

```
hexo clean
hexo g
```

Ps:每步执行成功后方可执行下一步指令，不允许同时执行

之后执行本地执行`hexo`服务，在浏览器中进行**本地测试博客站点**相关操作，执行之后，就可以发布部署当前博客到`Github`对应账户：

```
hexo deploy
或
hexo d

```

部署成功后，可查看`Github`的仓库中是否出现相关本地站点生成的发布文件`public`文件夹下的相关内容

在浏览器中输入`http://yourgithubname.io`尝试访问你的站点，效果和本地访问的效果一致，至此，个人的网站就托管于`Github`发布部署成功

## 设定主题为Next Theme

到上述的**发布和部署到Github**的操作流程，基本上博客就算是成功了，接下来就需要开始自己的个性化定制了，之前提到过，**_config.yml**为博客站点的配置文件，`Next Theme`实际上是`Hexo`的一个主题系列，个人比较喜欢，所以就决定将原有默认的主题替换掉，替换主题的设定也是从**_config.yml**进行修改：

```
theme: landscape

```

默认主题是**landscape**，根据`yml`文件中默认的提示，去到[hexo themes](https://hexo.io/themes/)，找到自己喜欢的主题，此处为`next`，找到`next`主题，去到对一个的`Github`仓库，下载下来之后，放到Themes文件夹中，将`_config.yml`文件中的**theme**修改为**next**并保存

```
theme: next

```

此时需要清除一次`hexo`原有生成的站点，用于改变站点主题重新生成

```
hexo clean
hexo g
hexo s

```

测试无误后，再执行发布指令，发布到`Github`对应的仓库

```
hexo d

```

再次访问`http://yourgithubname.io`，主题变更为`Next`则表示成功

