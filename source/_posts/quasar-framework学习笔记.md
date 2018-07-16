---
title: quasar-framework学习笔记
date: 2017-09-18 15:33:03
tags: 前端
---

官方网站： http://quasar-framework.org/



一、安装

1、安装quasar-cli
```
npm install -g quasar-cli
```
2、使用quasar starter kits初始化项目

quasar提供了多套开发套件，可以使用quasar list查看当前可用的套件

![image](https://s3.51cto.com/wyfs02/M02/A5/82/wKioL1m_O1eT3Q0pAABXMmiJl1s631.png-wh_500x0-wm_3-wmp_4-s_2298758612.png)

其中官方提供5套开发套件，使用方法如下

quasar init <starter_kit_name> <folder_name>

例如想安装默认套件，安装到当前目录的demo文件夹下

quasar init default demo ，  然后进入demo目录npm install即可



二、quasar-cli

1、quasar支持通过开发模式启动，支持热重载

可以通过    quasar dev [theme] 来启动项目

例如：

quasar dev 或者 quasar dev mat 使用mat主题启动

quasar dev ios 使用ios主题启动

    

使用default套件安装启动后：

![image](https://s2.51cto.com/wyfs02/M00/A5/82/wKioL1m_O3PRp4iCAADRMPh9wa0536.png-wh_500x0-wm_3-wmp_4-s_2126497933.png)

使用pwa套件安装启动后：

![image](https://s5.51cto.com/wyfs02/M02/06/D2/wKiom1m_O6-TcP0iAACHPE4-FNk809.png-wh_500x0-wm_3-wmp_4-s_1484336957.png)
剩余的0.13版本，webpack1和vue1在此就不做赘述了

另外发现非官方提供的这个meteor安装后 quasar dev会报错：

![image](https://s2.51cto.com/wyfs02/M00/06/D2/wKiom1m_O76wb_EEAABEDgxZhPk122.png-wh_500x0-wm_3-wmp_4-s_3669924860.png)

    

2、模板

一些通用页面框架，可以在根目录下的templates文件夹下提前写好，需要创建新页面时，直接通过 quasar new [templates文件夹下的模板文件] [文件名称]

使用default套件搭建好项目后，templates下默认会给出三个模板，layout，page，component，也可以自己在里写好。以layout举例：

quasar new layout temp

![image](https://s3.51cto.com/wyfs02/M02/06/D2/wKiom1m_PnmjIPwdAAALjGbp1ec407.png-wh_500x0-wm_3-wmp_4-s_1467778965.png)

这是src/components下就会多出一个根据layout模板创建好的temp.vue文件，文件的内容与layout页面一模一样

![image](https://s2.51cto.com/wyfs02/M01/A5/83/wKioL1m_PrrRn7iQAAFGXOKUe-M750.png-wh_500x0-wm_3-wmp_4-s_2007252321.png)

3、演示app

通过quasar dev --play 启动时可以同时生成一个二维码

![image](https://s3.51cto.com/wyfs02/M02/A5/83/wKioL1m_P-miUfnXAAAwDmkdQM8120.png-wh_500x0-wm_3-wmp_4-s_3774861385.png)

手机扫描后可直接查看页面

![image](https://s2.51cto.com/wyfs02/M00/06/D3/wKiom1m_QE7CVK4uAAKMQvOGc6A015.png-wh_500x0-wm_3-wmp_4-s_718534324.png)

酷炫！

4、应用程序包装

目前可用的 App Wrappers ：  Cordova 和 Electron, 官网描述未来将会提供更多的App Wrappers，支持Chrome浏览器拓展程序的开发以及Progressive Web App开发。

[Cordova:   安卓/ios](http://quasar-framework.org/guide/cordova-wrapper.html) 

[Electron:   桌面应用](http://quasar-framework.org/guide/electron-wrapper.html) 

使用方式参考上方文档

5、用ESlint 来检查你的ES6 js文件

quasar lint

6、启动服务器调试

npm run build后打包好的项目 会放在dist文件夹下，quasar serve启动
```
# Serve dist folder
# (must build app before that or else current folder will be served):
$ quasar serve

# ..or serve a full or relative path
$ quasar serve dist
$ quasar serve ./dist
$ quasar serve /work/quasar-app/dist
```
三、杂项

1、支持的浏览器：

Chrome

Firefox

IE11+

Edge

Safari 9.1+

Opera

Android Browser 4.4+

2、RTL支持

RTL（左对右）支持处于非常早期的发展阶段，实验性的，不适合生产，有一些bug。

3、故障排除和提示

1)、构建app花费时间过长。

在某些情况下，您可能会注意到构建需要非常长的时间才能完成。这是一个明确的迹象，你的HTML Vue模板有语法错误的地方。示例：< < > >，其中一个字符中断html：< <

2)、禁用eslint

官网不推荐，如果需要关闭eslint的话，可以到/build/webpack.base.conf.js文件，删除module/rules下的这段代码
```
{ // eslint        
    enforce: 'pre',
    test: /\.(vue|js)$/,
    loader: 'eslint-loader',
    include: projectRoot,
    exclude: /node_modules/
}
```
3)、远程调试移动应用程序

参考[文档](http://quasar-framework.org/guide/cordova-wrapper.html#Remote-Debugging)

4)、win10: Unexpected token import

当用Win10 64位时有可能会报这个错误，这是因为Npm， 解决方法:

-     npm install -g npm-windows-upgrade
-     npm-windows-upgrade
5)、禁用 IOS rubber band effect

这个rubber band effect就是那个弹动效果，当层在一个方向上移动时，它的弹力可以超出它的约束，这就叫做弹力。它有点像是橡皮筋效果 当你在用Cordova 构建ios app时，如果想要禁用这个弹动效果，可以在config.xml增加:

<Preference name = "DisallowOverscroll" value = "true" />