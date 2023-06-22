---
title: 记录第一次vue-nodejs-mongodb项目服务器部署
date: 2022-06-21 15:12:49
tags:
---



记录自己第一次部署项目，中间遇到很多errors，但好在花了两天时间成功部署了。先放出部署成功的项目链接

👉👉👉      [师大Buy 校园闲置交易平台](http://101.34.27.188:8080/)     👈👈👈

（先注册一个账号使用即可体验）



## 流程

本项目包含三个子项目

1. 前台展示
2. 后台管理
3. 后端nodejs服务器+mongodb数据库



大概流程

- 开发好前端与后端程序

- 购买服务器（和域名）

- 安全组、开放端口与设置规则

- xshell操作

- 服务器上安装 和配置node，mongodb 所需环境

- nginx设置代理

- 上传项目代码到服务器上

- 启动 express 服务器

  

## 购买服务器

我的服务器信息：

- 腾讯云轻量服务器
- 运算组件：2核CPU、2GB内存 (通用型-2核2G-40G-300G)
- 系统盘：40GB SSD云硬盘 (通用型-2核2G-40G-300G)
- 流量包：300GB/月流量包 (通用型-2核2G-40G-300G)
- 地域：上海
- 镜像：CentOS 8



买好服务器后可以进入控制台，对服务器进行操作。可以尝试一下开机关机，之后最重要的是重置密码，这个密码后面通过xshell登录的时候经常用到，所以要记住。



## 安全组、开放端口与设置规则

在腾讯云控制台的搜索框中搜索安全组，打开`云服务器 -网络安全-安全组`。里面可以设置一些安全组规则，意思就是这里可以打开一些端口，以便项目使用。

![](https://github.com/janice143/myblog.github.io/blob/master/images/server_1.png?raw=true)



一开始可以新建安全组，使用一些默认的安全组规则。下面这张图展示就是默认的安全组规则，可以看到他已经放通了22,80等端口可以使用。

![](https://github.com/janice143/myblog.github.io/blob/master/images/server_2.png?raw=true)



本项目中的三个项目分别使用了8080（前台），9528(后台)，3000（后端），27017（数据库）端口，所以要用到这些端口，我们就得自己去开放，因为默认的安全组这些端口是没有开放的。

开放这些端口的时候，需要在安全组里添加（可以在原来默认的安全组里新添，也可以新建安全组），也需要在服务器的防火墙里添加，只有这两个地方都放通了，端口才能访问。

![](https://github.com/janice143/myblog.github.io/blob/master/images/server_3.png?raw=true)



## 登录和操作服务器

### xshell操作

我电脑是windows系统，所以安装了xshell来登录服务器。如果是 mac系统，好像可以直接在终端，通过`ssh root@公网IP`命令行连接到服务器。因为我没用过，所以这里只讲xshell如何登录和操纵。

安装xshell的时候记得顺带安装xftp，可以用来上传文件，不仅如此，我还发现可以用来浏览文件夹，甚至修改编辑文件，很好用！

![](https://github.com/janice143/myblog.github.io/blob/master/images/server_4.png?raw=true)



打开xshell的时候，会弹出一个会话框，选择新建一个会话，然后命名一下，点击连接，这时候会弹出一个输入密码的对话框，输入一开始你在浏览器上的服务器控制台上重置的密码，就登录了。

![](https://github.com/janice143/myblog.github.io/blob/master/images/server_5.png?raw=true)



*centos*是基于linux建立的操作系统，所以用的指令和linux一样。而linux指令其实和DOS是一样的，只是命令的表达上可能有点儿不一样，所以遇到这个黑框不要害怕。



下面说一下xshell里面常用的linux指令

```
cd 进入目录
ls 查看当前目录下的文件
mkdir  创建文件夹
mv 移动或重命名
rm -rf  删除整个文件夹
```

大概知道这些你就有了部署项目的基础了，中途如果遇到其他的需要，可以上网查看。

你可以先试试`ls`，看看当前文件夹里有什么文件，这感觉就好像是在windows系统上浏览文件夹，只不过在windows上是可视化的操作界面。

### xftp使用

xftp是个很好用的可视化界面，一开始我是看别人说用来上传文件的，但是后来我用的时候，我发现它很好用来浏览文件，找回了使用windows一样的感觉。

当你进入xftp软件的时候，首先也会弹出一个会话框，新建会话，重命名，然后连接，输入密码。和使用xshell一样。

![](https://github.com/janice143/myblog.github.io/blob/master/images/server_6.png?raw=true)



要传什么文件给服务器，先在右框中选择文件夹（新建文件夹），找好存放的位置后，在左框中浏览你电脑的文件，找到对应的文件后右键 传输。

值得注意的是，在连接服务器后，初始文件夹的位置是/root，你可以从xftp软件的右框中看到，也可以在xshell里面`ls`看到。

在xftp软件上右框的文件夹中有 ... (三个点)，点击一下，可以返回上一级，你会发现，这里有很多初始文件夹。知道这些文件夹的存在，对于我们下一步的操作很重要，这里暂时放开，我们继续后面。





## 服务器上安装node，mongodb 

浏览到这里，我们已经知道一些基本的xshell指令，知道如何通过xshell登录服务器，通过xftp上传文件。下面我们开始在服务器上安装项目所需环境。

一般在新服务器创建后，建议先升级一下 CentOS：

```bash
yum -y update
```



在升级之后，我的yum指令就下载不了东西了，报错`Fix Failed to download metadata for repo`，[解决办法](https://techglimpse.com/failed-metadata-repo-appstream-centos-8/)是

**Step 1:** Go to the `/etc/yum.repos.d/` directory.

```
[root@autocontroller ~]# cd /etc/yum.repos.d/
```

**Step 2:** Run the below commands

```
[root@autocontroller ~]# sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
[root@autocontroller ~]# sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
```

**Step 3:**现在yum就可以使用了



### nodejs

网上的博客描写安装Nodejs很多，甚至还不一样，很容易踩坑，但是我推荐看[这篇博客](https://juejin.cn/post/6844903605040857095)，另外我也在这讲一下步骤。

```bash
# 1 安装wget
yum -y install wget

# 2 切换到/usr/local/src目录，选择这个文件夹存放nodejs
cd /usr/local/src

# 3 去nodejs下载官网复制下载链接，我的服务器是centos 8, 64位，所以我选择了
https://nodejs.org/dist/v16.15.1/node-v16.15.1-linux-x64.tar.xz

# 4 使用 wget 安装方式
wget https://nodejs.org/dist/v16.15.1/node-v16.15.1-linux-x64.tar.xz

# 这时候这个压缩包就在当前文件夹下，现在需要解压它
# 5 解压
xz -d node-v16.15.1-linux-x64.tar.xz
tar -xvf node-v16.15.1-linux-x64.tar

# 6 建立软连接
ln -s /node-v16.15.1-linux-x64/bin/node /usr/local/bin
ln -s /node-v16.15.1-linux-x64/bin/npm /usr/local/bin

# 这时候你可以切换到/usr/local/bin目录，看看有没有npm，node文件夹，有的话说明成功了
# 7 试试node,npms是否能使用
node -v
npm -v

# 打印出了版本好就说明没问题
```



### mongodb

推荐看[官网的步骤](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-red-hat/#verify-that-mongodb-has-started-successfully)  （有官方看官方，否则看别人的博客很容易踩坑）

官网给出了如何在centos上下载mongodb 5，我也在这里写出步骤



#### 1 配置yum

xftp的作用来了，打开该软件，在`/etc/yum.repos.d`文件夹下创建`mongodb-org-5.0.repo`文件，选择记事本打开，粘贴下面代码

```bash
[mongodb-org-5.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/5.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-5.0.asc
```

这个操作就好像告诉yum去指定的baseurl去下载资源，不配置的话yum不指导哪里找资源下载。



#### 2 安装mongodb安装包

```
yum install -y mongodb-org
```



#### 3 验证安装结果

```bash
rpm -qa |grep mongodb
```

如果输出很多Mongodb相关的包时，说明你的服务器已经有这些包了



#### 4 data目录和log目录

默认情况下，MongoDB 会创建一下`mongod`用户，该用户会使用data和log目录

- `/var/lib/mongo` (the data directory)
- `/var/log/mongodb` (the log directory)

我们这里就使用它默认的一个位置来存放mongodb的data和log。



![](https://github.com/janice143/myblog.github.io/blob/master/images/server_7.png?raw=true)



#### 5 启动MongoDB服务

```bash
systemctl start mongod.service
```

我这里启动报错，后来我看到了[这篇文章](https://askubuntu.com/questions/823288/mongodb-loads-but-breaks-returning-status-14#comment1255871_823288)，（看高赞回答），我发现权限错误，我的`/tmp/mongodb-27017.lock`所有者是root，需要改为mongod。

> permission settings on `/var/lib/mongodb` and `/tmp/mongodb-27017.lock` are wrong. You will have to change the owner to monogdb user

在xshell输入一下指令即可

```none
chown -R mongod:mongod /var/lib/mongodb
chown mongod:mongod /tmp/mongodb-27017.sock
```



#### 6 验证服务开启

```
执行: mongo
输出： MongoDB shell version v5.0.9
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb

```



到现在我们已经完成了部署难点的很大一部分。下面我们看看怎么在服务器运行我们三个项目



## nginx设置代理

不需要懂nginx原理，我们可以先使用它看看。

参考[这篇文章](https://zhuanlan.zhihu.com/p/431796992)，我这里也引用一下

### 先查看服务器是否有nginx

```text
whereis nginx
```



### 安装nginx

```text
yum install -y nginx
```



### 查看nginx安装目录

命令如下：

```text
whereis nginx
```

### 启动nginx

**启动**

```text
nginx
```

直接输入nginx即可启动服务，打开浏览器，访问服务器公网地址，出现如下界面则代表启动成功。

![](https://github.com/janice143/myblog.github.io/blob/master/images/server_8.png?raw=true)



这里访问默认的是80端口，因为nginx服务就是默认的80端口，如果不能访问，大致有几种原因：

- nginx未启动
- 服务器安全组未加入80端口
- nginx配置错误

**停止**

停止nginx服务，命令如下：

```text
nginx -s stop
```

此时访问浏览器则无法访问。

**重启**

重启nginx服务，一般用于修改配置文件之后，命令如下：

```text
nginx -s reload
```



### 配置nginx

nginx启动后，在浏览器上输入公网地址，就可以打开默认的一个html页面。

但是这个页面和端口都是默认的，如果需要显示我们自己的页面，就需要配置nginx，配置文件为`/etc/nginx/nginx.conf`，使用xftp找到该文件夹，并打开编辑。

也可以在xshell上cd找到并vim打开，但是这个很不方便，不能快速复制粘贴和定位。所以推荐使用我用xftp发现的隐藏功能。

在配置文件中，重点看server 对象，要改的也是这个对象，我的配置如下

```bash
   
	# 默认端口
    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
        location / {
        }
        error_page 404 /404.html;
            location = /40x.html {
        }
        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
	
	# 前台展示的打开的服务代理
	server {
        listen       8080;
        server_name  localhost;
		root         /root/lan/schoolstore/frontshop/dist;

        # Load configuration files for the default server block.
        # include /etc/nginx/default.d/*.conf;

        location / {
			root   /root/lan/schoolstore/frontshop/dist;
            index  index.html;
            try_files $uri $uri/ @router;
            autoindex on;
       }
	   
	    location @router{
            rewrite ^.*$ /index.html last;
        }

        location /api/ {
            proxy_set_header X-Real-IP $remote_addr;
            proxy_pass http://0.0.0.0:3000;
        }

        gzip on;

        gzip_buffers 32 4k;

        gzip_comp_level 6;

        gzip_min_length 200;

        gzip_types text/css text/xml application/javascript;

        gzip_vary on;

        #error_page  404              /404.html;
        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
	
	# 后台管理的服务代理
	server {
        listen       9528;
        server_name  localhost;
		root         /root/lan/schoolstore/cms/dist;

        # Load configuration files for the default server block.
        # include /etc/nginx/default.d/*.conf;

		# 找到要渲染的页面
        location / {
            # try_files $uri $uri/ /index.html;
			root   /root/lan/schoolstore/cms/dist;
            index  index.html;
            try_files $uri $uri/ @router;
            autoindex on;
       }
	   # 找到路由
	    location @router{
            rewrite ^.*$ /index.html last;
        }
       # 后端数据
        location /api/ {
            proxy_set_header X-Real-IP $remote_addr;
            proxy_pass http://0.0.0.0:3000;
        }

        gzip on;

        gzip_buffers 32 4k;

        gzip_comp_level 6;

        gzip_min_length 200;

        gzip_types text/css text/xml application/javascript;

        gzip_vary on;

        #error_page  404              /404.html;
        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

```

主要就是修改root 和 location。

我开了两个代理的：前台展示打开的服务代理8080，和管理后台打开的服务代9528，这个项目是分开端口访问的。
修改配置文件后 执行` nginx -s reload`，如果项目已经上传，理论上访问ip:8080可以打开前台展示项目，访问ip:9528，可以打开后台展示项目。但是我们现在没讲到上传文件。所以会访问不成功，而80端口我还保留了默认的页面，还是可以访问到的，不妨试一试。



## 上传项目代码到服务器

主要有三个项目的文件需要上传：前台，后台，后端

我们先把后端部署好，前台上传比较容易，并且后端部署好后，前台部署可以拿到数据。



### 在服务器上使用nodejs

不着急直接上传nodejs文件，我们先看看服务器上nodejs怎么使用，因为一开始对于一个服务器小白来说，我们要上传什么东西都不知道，要不要打包也不知道，上传后端和前台一样也不知道。所以带着这些疑问，我们先上网搜搜，怎么在服务器上使用nodejs。



参考[文章](https://help.aliyun.com/document_detail/50775.html) ，主要看【步骤二：部署测试项目】，本文这里也给出步骤，主要是参考了这篇文章。

1 打开vscode，在自己电脑里新建一个example.js文件，复制一下代码，然后node example.js就可以在http://localhost:3000/看到·`hello word`

```js
const http = require('http');
const hostname = '0.0.0.0';
const port = 3000;
const server = http.createServer((req, res) => { 
    res.statusCode = 200;
    res.setHeader('Content-Type', 'text/plain');
    res.end('Hello World\n');
}); 

server.listen(port, hostname, () => { 
    console.log(`Server running at http://${hostname}:${port}/`);
});
```

这是在自己电脑运行的效果，现在我们要想这个example.js文件放到服务器上，然后再服务器上运行，最后在自己电脑的浏览器上输入对应网址，可以看到同样的`hello world`。

2 把example.js文件通过xftp上传到服务器上的/root文件夹下。然后再xshell中切换到该文件夹。通过`ls`指令可以看到该js文件，输入一以下指令运行该js文件。（和在自己电脑的控制台上一样）

```bash
node example.js
```

打开浏览器并访问`http://<ECS实例公网IP地址>:<项目端口号>`。本示例中，<项目端口号>为3000。你会发现该文件运行了同样效果。



**这就是node在服务器上的使用，总的来说，其实和在自己电脑运行一样，也就是说我们要把js文件上传到服务器，如果项目大的话，会有很多文件夹，还会有node modules，所以这些也要上传。这些文件都有，node js文件就可以运行了！**



### 在服务器上部署Node.js + MongoDB 项目

我的后端项目还用到了数据库，刚才我们只学到node项目怎么部署，但是node+mongodb呢？现在我们来看看

参考[教学视频](https://www.youtube.com/watch?v=-oTdaFDvLo4)



下面的讲解是假设你已经知道了怎么在自己电脑上开发node+mongodb项目，如果不知道建议先掌握这部分知识，再来看服务器上的部署。



重点是在node 项目文件里设置Mongodb数据库连接，这个链接怎么看呢？在xshell里输入mongo就可以看到，

![](https://github.com/janice143/myblog.github.io/blob/master/images/server_9.png?raw=true)



图中红色框就是你的数据库的连接地址。但是在node项目里，？后面的字符串不需要写进入，同时还要也一个明确的数据库名称，我的数据库连接如下图所示。

![](https://github.com/janice143/myblog.github.io/blob/master/images/server_10.png?raw=true)



处理好mongodb的连接地址，下面的步骤其实和Node项目一样，接下来我们真正开始讲解本项目的部署。



### 上传本项目后端代码

有的博客说可以用git上传，大概的意思就是服务器其实也是一台电脑，在这台里面通过git拉取项目，前提是你已经在自己的电脑里把项目文件git到了github上。但是这样很复杂，服务器这台电脑还需要配置git使用，不然就会没有权限，拉取不到项目文件。我弄了一会没弄出来我就另辟蹊径了。

我的方式：

1 在自己电脑里把项目上传到github上，忽略node modules等开发文件，(我的电脑使用git很久了，已经配置好了，如果第一次使用，也是要配置的，但是在可视化的windows上配置应该比centos上配置容易点)。

2 上传之后在git clone拉取下来

3 把新文件同xftp上传到服务器上

4 xshell切换到项目文件下，输入`node install`下载项目所需包

5 node server.js文件运行即可



#### pm2使用



>  PM2 是一个守护进程管理器，它将帮助您管理和保持您的应用程序在线

其实说到这里后端项目就已经部署好了，但是很多博客都介绍了pm2这个工具，这个工具可以替代node server.js，同样让后端启动，但是不同的是，pm2的使用可以保证项目永久运行在服务器上，且不会一报错后端服务器就挂了，而且运行中还可以进行其他操作。

1 安装

```bash
npm install -g pm2
```

2 切换当前工作目录到项目server.js文件夹中，执行指令

```bash
pm2 start server.js
```

pm2的其他操作

```
pm2 stop server.js // 关闭
pm2 list //查看所用已启动项目
```



到现在为止后端已经部署完毕了

### 上传本项目前端代码

前端代码上传比较容易，参考[文章](https://zhuanlan.zhihu.com/p/431796992)



我的项目是vue项目，所以npm run build打包出一个dist文件夹即可。然后把这个文件夹上传到服务器。此时无需重启nginx，刷新浏览器，可以看到我们的网站已经部署成功了

**前台**

![](https://github.com/janice143/myblog.github.io/blob/master/images/server_11.png?raw=true)



**后台**

![](https://github.com/janice143/myblog.github.io/blob/master/images/server_12.png?raw=true)



## 本项目有关地址

部署项目重点参考了三篇文章

1 [服务器小白的我,是如何成功将 node+mongodb 项目部署在服务器上并进行性能优化的](https://segmentfault.com/a/1190000017143278)

2 [记录react+node+mongodb项目部署到服务器的过程](https://juejin.cn/post/7004678485788590111#heading-14)

3 [前端Vue项目打包部署实战教程](https://zhuanlan.zhihu.com/p/431796992)

非常推荐看这三篇，少走很多弯路。



我的个人博客地址，里面偶尔会做一些笔记

https://janice143.github.io/myblog.github.io/



本项目部署地址

http://101.34.27.188:8080/#/home



我的github项目

https://github.com/janice143?tab=repositories



一些bug清单，附带解决问题的网站

1 [nginx配置500内部错误](https://qdmana.com/2022/03/202203131953219539.html)

2 [ECS服务器部署Nodejs出现错误Error: Listen EADDRNOTAVAIL](https://developer.aliyun.com/article/92134)

3 mongodb的导入json文件

`mongoimport --jsonArray --db schoolstore --collection categories --file cat.json`

4 [linux服务器升级后，yum安装出错](https://techglimpse.com/failed-metadata-repo-appstream-centos-8/)





-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

前端小菜鸟，研二在校生（马上要研三了），非科班，物电学院，专业你猜

大二接触前端，但是有了专业课老师上课要抽同学讲课，然后我就学了一下简单的三件套，做了个转盘抽签网页，没做出来，但是我再网上找了代码改了改弄出来了。这是我第一次的前端开发 😁😁😁

大二大三是人生巅峰，拿国奖，发SCI了，觉得自己有搞研究的天分，准备本专业考研了。

到了研究生发现自己不适合读博，想找工作，不想当物理老师，不想从事本专业，然后又开始拿起前端三件套，进阶前端之路了。I have no idea what I'm doing...

最近提前批、秋招要来了，好紧张，我要冲去找工作了！

祝我好运！

have fun here!



对了，后面还有很多文章想写的，等我有时间都会给它写了！

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------











