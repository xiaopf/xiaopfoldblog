---
layout: post
title:  "verdaccio搭建私有npm仓库，缓存npm公共包"
date:   2019-06-06 16:00:20 +0800
categories: post 2019 06
comments: true
---

### 私有npm仓库的特点：

- 私有npm仓库可以搭建在局域网内，不对外开放
- 对于发布和下载npm包可以配置权限管理
- 私有npm仓库可以管理私有包，不用上传到公共npm仓库的包
- 缓存下载过得npm包文件，再次下载可以大大加快下载速度
- 对于私有包走私有仓库，对于下载过的包走缓存，对于未下载过的包走公共仓库

### verdaccio 是什么

官网给出的简介 ： Verdaccio 是一个 Node.js创建的轻量的私有npm proxy registry

verdaccio是fork的[sinopia](https://github.com/rlidwka/sinopia)，后者是最初的搭建私有npm的选择，不过已经好多年不维护了，而verdaccio则是从sinopia衍生出来并且一直在维护中的，所以verdaccio是更好的选择。

- 它是基于Node.js的网页应用程序
- 它是私有npm registry
- 它是本地网络proxy
- 它是可插入式应用程序
- 它相当容易安装和使用
- 我们提供Docker和Kubernetes支持
- 它与yarn, npm 和pnpm 100% 兼容
- 它forked于sinopia@1.4.0并且100% 向后兼容。
- Verdaccio 表示意大利中世纪晚期fresco 绘画中流行的一种绿色的意思。

### verdaccio 搭建
#### 全局安装 verdaccio
```
npm install -g verdaccio --unsafe-perm
```
加上--unsafe-perm选项是为了防止gyp ERR! permission denied权限问题报错，报错如下：

![image](https://note.youdao.com/yws/api/personal/file/B7740D2419AA4F07864FA8BFBE4E121A?method=download&shareKey=8f409f0b542e79fbc1c522e9e41d701d)

加上--unsafe-perm之后安装

![image](https://note.youdao.com/yws/api/personal/file/7E0D796DB5DA4980A0D801A0821143C3?method=download&shareKey=91bd26ce0b39374bb9c946eca562d3a8)


#### 启动 verdaccio

安装完成后直接在命令行中输入verdaccio启动,
```
verdaccio
```
会输出如下结果

![image](https://note.youdao.com/yws/api/personal/file/333CCAB2020043C1A129BC79D3303ECD?method=download&shareKey=49b3fa0d059c1609a530272a74bce3ec)

其中第一行就是verdaccio的配置文件目录，相关的配置都在config.yaml中去进行配置，[详细的verdaccio配置文件文档](https://verdaccio.org/docs/zh-CN/configuration)

```
C:\Users\xiaopengfei\.config\verdaccio\config.yaml
```
进入这个目录中打开着个配置文件在最下面添加下面监听端口，这个在服务器上启动很重要，否则服务器外网ip加端口是无法访问仓库的。

```
listen: 0.0.0.0:4873
```

C:\Users\xiaopengfei\.config\verdaccio 这个地址下面不仅有config.yaml,还有一个==storage==文件夹，这个文件夹就是用来存放私有包及缓存公有包的仓库。

第四行的地址也就是verdaccio私有仓库的前端页面，由于是在本地启动的，所以是localhost，如果是服务上安装，对应服务器ip地址即可。

在浏览器中输入地址 http://localhost:4873/ ，即可看到

![image](https://note.youdao.com/yws/api/personal/file/0B257F2AC50E49728F5EDB5AF22AADF9?method=download&shareKey=fb9002c481a9205f75e55de2dc87ce62)

#### 登录到私有仓库上
首先在命令行中输入下面添加用户的命令

```
npm adduser --registry http://localhost:4873
```

![image](https://note.youdao.com/yws/api/personal/file/F0C50B21337F4BF7B5A528F96FA9E3A1?method=download&shareKey=44c2aa9b081a88372700c4bf414f041d)

然后回到浏览器中点击 login 按钮进行登录

#### 发布 npm 包到私有仓库

具体怎么制作npm包，不作讲解，一般的包文件包含以下文件

![image](https://note.youdao.com/yws/api/personal/file/FE61BF6195744A9890197725C03406BD?method=download&shareKey=926fb04ebdd189af9d5d29fb738a3ef5)


发布包文件到私有仓库命令如下(需要到包文件下面去执行命令)
```
npm publish --registry http://localhost:4873
```

如果我们用一个npm公有包去上传的话会报错如下

![image](https://note.youdao.com/yws/api/personal/file/3502C4E03C0F4F5E895CA31890021610?method=download&shareKey=25e8c6616af3cf3f9336964b51d8242d)

为了测试我们可以把上面的包进行改造文件夹改个名字fakeAxios，再把package.json里面的所有axios改成fakeAxios，然后在去fakeAxios文件夹下执行上面的命令，就可以将包发布到私有仓库了，成功后界面上会显示包的信息，如下图，说明上传成功了。

![image](https://note.youdao.com/yws/api/personal/file/D48A9348827D485CB594655D039937B4?method=download&shareKey=474e989a8e2f7dd72f26d28bed5ec351)

#### 从私有仓库中下载私有包

##### 全局安装 nrm 

nrm是 npm registry 管理工具, 能够查看和切换当前使用的registry。

```
npm install -g nrm
```

将私有仓库的地址添加到nrm管理工具中,add后面跟的是个自己起的名字

```
 nrm add xiao http://localhost:4873
```
成功后会提示  add registry xiao success

使用nrm ls可查到我们可以使用的所有镜像源地址，*是正在使用的，也就是首选项

```
nrm ls
```
![image](https://note.youdao.com/yws/api/personal/file/013859D38745446295BAE43F481BB6E5?method=download&shareKey=de09ef6848d61b4cac3c5c6397f1d2a0)

切换首选项为私有仓库的源，以后下载包的时候就会先走私有仓库，如果没有再走其他的地址去下载。

```
nrm use xiao //名字是上面起的那个
```

以上操作完之后，以后 npm install 或者 yarn add 就可以下载私有包了。

#### 缓存 npm 公共包

配置好私有仓库之后，每次我们下载公共包的时候，会自动把包文件缓存在
```
C:\Users\xiaopengfei\.config\verdaccio\storage
```
私有包也是会存在storage这个文件夹下面的，当公共包被缓存之后，再次下载的时候，会首先在这个仓库中获取，大大的加速了包文件的下载速度，但是verdaccio官网上面显示缓存的过期时间默认是2分钟，如果想避免缓存的话可以在config.yml中设置 cache：false
```
uplinks:
  npmjs:
    url: https://registry.npmjs.org/
    cache: false
```
延长缓存过期时间,增加maxage,如果不经常更新依赖，这是一个有效的策略

注意 maxage 的缩进，缩进有问题的话，用verdaccio启动会报错！

```
uplinks:
  npmjs:
    url: https://registry.npmjs.org/
    maxage: 30m
```


下面测试一下安装webpack的个包。

首先直接下载webpack耗费时间如下

![image](https://note.youdao.com/yws/api/personal/file/54FF562EE980492194EAEC46958D8A01?method=download&shareKey=4f00d6dd4c736a75f82aaf045513b9fc)

有缓存后的下载，明显快了很多（测试了很多次下载webpack，发现下载速度和硬盘类型也有关，固态硬盘下明显会比机械硬盘快一些）

![image](https://note.youdao.com/yws/api/personal/file/84AB10A71B004286B55F50A8EB01F431?method=download&shareKey=8655864cc15e00dfc9eeefb53a44dbc8)

在我们 npm install 包的时候，verdaccio 开启的服务命令行中，会不停的有 http 请求

![image](https://note.youdao.com/yws/api/personal/file/2376198112D24AA3B3A65C064312A4F3?method=download&shareKey=57cda3a87fa8f3dc7c8371482e7b9ee3)

#### 关于守护进程

在windows下安装 pm2 守护 verdaccio 的进程，status 一直报错，没有解决掉，后来在阿里云的服务器上安装了 pm2 也遇到了一些问题，不过全都解决掉了。


##### 全局安装 pm2

安装完成verdaccio之后，我们需要安装pm2来守护进程，[关于pm2详细用法](https://pm2.io/doc/zh/runtime/overview/)

```
npm install -g pm2
```
输入下面命令可以验证是否安装成功（如遇command not found，看后文，遇到的问题）
```
pm2 -V
```

##### 用 pm2 启动 verdaccio
先关闭之前直接用 verdaccio 启动的服务，用下面的命令启动
```
pm2 start verdaccio
```
服务启动后如下图

![image](https://note.youdao.com/yws/api/personal/file/A58FD440519246EB8B3490CAAC1607BF?method=download&shareKey=efe1cfaaa04fee6695d01f6ccc545bb6)

##### 访问私有仓库

在浏览器中输入 服务器对外ip 加上端口号 4873 访问私有仓库的页面，
这里需要注意的是，需要在阿里云上配置4873端口安全组，还有前文说的在config.yml最后添加 listen: 0.0.0.0:4873,否则都无法在浏览器中访问到私有仓库页面





##### 遇到的问题

1.明明全局安装了 verdaccio 和 pm2 但是在命令行中报错 command not found

###### 解决：
需要把node的安装目录添加到环境变量中，我的是

```
/usr/local/node/bin
```
pm2 和 verdaccio 都是在bin文件夹下面，

2.用 pm2 start verdaccio 状态（就是status）一直error 或 stopped，有时候可能是 online，但是用 pm2 show 0 看详情的话还是 error，

###### 解决：
将启动的verdaccio删除，
```
pm2 delete 0 //这个0 是id号，上面的show后面的也是id号
```
查看pm2进程
```
ps -ux | grep pm2
```
杀掉pm2进程
```
kill -9  xxxx //xxxx是pm2进程对应的 pid号
```
最后在用pm2 start verdaccio 启动进程，再用 pm2 show 0 （id号）查看详情

![image](https://note.youdao.com/yws/api/personal/file/E561CA455150431DA604B259C59E4899?method=download&shareKey=c70298e6e9a8622213a488c63ccba39c)

在网站中输入地址，即可看到私有仓库页面了，这样进程就可以长时间存在了。