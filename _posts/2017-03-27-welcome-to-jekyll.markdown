---
layout: post
title:  "nodejs+express+mongoDB建站总结!"
date:   2017-03-27 16:49:20 +0800
categories: post 2017 03
comments: true
---

## 一、使用教程
### 1.nodejs安装
去[++nodejs官网++](https://nodejs.org/en/)根据自己需求下载相应资源进行安装，网上安装的教程很多不赘述。
### 2.mongoDB及可视化工具robomongo安装
网站的数据通过mongoDB进行存储，去[mongoDB官网](https://www.mongodb.com/download-center?jmp=nav#community)进行下载安装，命令行工具看着不是特别方便，所以可以用免费的数据可视化工具robomongo进行管理，可以去[robomongo官网](https://robomongo.org/)下载安装。
### 3.下载源代码及配置
下载一个zip的压缩包，然后解压缩，里面是没有数据，没有模块文件的，所以需要自己进行配置，才能使用的，首先打开命令行工具，用cd命令进入到解压完毕的目录，然后通过下面的命令进行依赖模块的下载安转。然后目录中就多了一个node_modules文件夹。

```
npm install

```
依赖模块安装完成后，接下来需要配置数据了，首先是mongoDB的连接，（需要指定一个数据存储位置，我一般就放在刚刚解压的源代码根目录），进入根目录创建一个data文件夹，再进入data文件夹创建一个webData文件夹，然后打开命令行工具，用cd命令进入源代码根目录，输入下面代码进行数据目录指定

```
mongod --dbpath data/webData
```
完毕后，mongoDB算是连接上了，数据就存在指定的目录中（以后每次运行网站都需要用命令行，开启数据库）。这个时候就可以打开可视化工具robomongo了，它会自动弹出connect对话框，具体就不展开了。

现在我们就可以网数据库里面导入数据了，在源代码根目录中有一个 top250_books_info 文件夹，里面有一个json文件，是豆瓣top250的书籍信息，然后打开命令行工具，用cd命令进入上述文件夹。通过以下命令将数据导入进mongoDB数据库。

```
mongoimport -d webData -c books books.json

```
现在打开robomongo就可以看到webData名称的database了，里面的collections有一个叫books的集合，那就是刚刚导入的数据了。

### 4.启动网站
需要配置的地方全部配置完成，这个时候就可以打开命令行工具了，进入根目录运行下面命令

```
npm start
```
打开浏览器输入[网站地址](http://localhost:3000/) http://localhost:3000/ 就可以进入登陆页了，这里还有一个地方需要注意的，就是前台注册的账号全都是user（user就是只有浏览权限的用户，管理员是admin）所以我们需要借助robomongo进行权限修改，进入collections里面（前提是你已经注册了账户）然后找到你想要设置为管理员的账户，然后编辑数据，admin那一项把user设置为admin即可，管理员账户会把后台入口展示出来，后台可以添加，删除，更新书籍数据，还可以删除用户数据。
