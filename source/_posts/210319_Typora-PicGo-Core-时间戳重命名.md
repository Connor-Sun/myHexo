---
title: Typora+PicGo-Core+时间戳重命名
abbrlink: 38835
date: 2021-03-19 21:26:18
tags: 
  - Typora
  - 折腾
categories: 
  - 工具
description: 本文主要分为两部分内容：</br>
	1. 在Typora为PicGo-Core安装super-prefix插件，实现上传图片时使用时间戳自动重命名；
	2. 个性化调控super-prefix插件的重命名格式。
---

# Typora+PicGo-Core+时间戳重命名

参考链接：[Typora自动上传图片配置，集成PicGo-Core，文件以时间戳命名](https://blog.csdn.net/in_the_road/article/details/105733292) 

在使用Typora写文档的时候总是觉得图片管理非常麻烦，经常被文件夹层级搞心态，而且想要迁移文档非常的麻烦，所以就找了找自动上传图床方面的教程，发现了[PicGo](https://github.com/Molunerfinn/PicGo)和[PicGo-Core](https://picgo.github.io/PicGo-Core-Doc/)。

关于图床的建立、PicGo的配置以及相应Typora配置，网上有很多前辈的教学了，这里我就不详细讲述我配置的过程了，以后哪天闲下来再写一写。

说一说图床选择方面的问题，我个人还是倾向于稳定快速为主的，虽然国内国外都有一些免费的图床可以使用，但是我个人是选择了阿里云的OSS来搭建图床，[具体教程](https://www.baidu.com/)哈哈开个玩笑。选择阿里云是因为我觉得即使我不写文档了阿里云可能还会健在，而且资费默认按需计费，个人用户使用一年几块钱最多了，图片量很大的话可以买套餐，40G存储空间一年也就9块钱。比起自己管理图片的麻烦，我觉得这几块钱花的还是很值的。

## 为 PicGo-Core 安装 super-prefix

[super-prefix](https://github.com/gclove/picgo-plugin-super-prefix) 就是使用时间戳重命名图片主角了，是一个可以**优雅的**生成文件存储路径的PicGo插件。

### 1. 安装 Node.js

安装插件需要Node.js环境。

1. 下载：https://nodejs.org/zh-cn/，普通用户直接下载LTS长期支持版

2. 安装msi文件，选择安装路径，`next` 不要停

3. 检测PATH环境变量是否配置了Node.js，点击开始 => 运行 => 输入”cmd” => 输入命令”path”，输出如下结果：
   ![](http://connor-sun-pic.oss-cn-shanghai.aliyuncs.com/imghost/2021/03/19/20210319-141541.png)

   我们可以看到环境变量中已经包含了 `C:\Program Files\nodejs\` 

4. 安装成功后，打开cmd命令行输入 `Node` ，出现类似如下提示则表示 Node.js 安装成功。
   ![](http://connor-sun-pic.oss-cn-shanghai.aliyuncs.com/imghost/2021/03/19/20210319-114604.png)

### 2. 在 Node.js 中下载 super-prefix

1. 首先需要找到Typora自动下载的PicGo-Core的执行文件路径
   ![](http://connor-sun-pic.oss-cn-shanghai.aliyuncs.com/imghost/2021/03/19/20210319-114926.png)
   ![](http://connor-sun-pic.oss-cn-shanghai.aliyuncs.com/imghost/2021/03/19/20210319-114809.png)

2. 更改cmd路径

   ```shell
   cd C:\Users\你自己的用户名\AppData\Roaming\Typora\picgo\win64
   ```

   在此路径执行安装操作

   ```shell
   .\picgo.exe install super-prefix
   ```

### 3. 修改 PicGo-Core 的配置文件

![](http://connor-sun-pic.oss-cn-shanghai.aliyuncs.com/imghost/2021/03/19/20210319-115450.png)

是一个JSON文件，打开后将其内容修改如下：

```json
{
  "picBed": {
    "uploader": "aliyun",
    "aliyun": {
      "accessKeyId": "",		// 阿里云AccessKey中的用户登录名称
      "accessKeySecret": "",	// 添加用户的时候记录的KeySecret
      "bucket": "", 			// 存储空间名 bucket名称
      "area": "", 				// 存储区域代号 如：oss-cn-shanghai
      "path": "", 				// 自定义存储路径
      "customUrl": "", 			// 自定义域名，注意要加 http:// 或者 https://
      // 针对图片的一些后缀处理参数 PicGo 2.2.0+ PicGo-Core 1.4.0+
      "options": ""
    }
  },
  // 插件设置
  "picgoPlugins": {
    "picgo-plugin-super-prefix": true
  },
  "picgo-plugin-super-prefix": {
    "prefixFormat": "YYYY/MM/DD/",
    "fileFormat": "YYYYMMDD-HHmmss"
  }
}
```

然后就可以再次点击 `验证图片上传选项` 就可以验证时间戳改名能否正常工作了。

## 折腾过程的记录

最后记录一下我自己更多一些的折腾过程。

### 添加新的功能

因为最开始对 `super-prefix` 的默认功能不是特别满意，可能是一直以来自己管理图片路径，所以总想要控制点什么。

最初我装好了 `super-prefix` 后，总想着还能保留下文件名，以 `时间戳 + 文件名 + 后缀` 的形式保存文件，这样原始文件名中的信息还能保留。也不想要按日期分文件夹的功能，因为可能同一篇文档中的图片会被分到两个文件夹中。

因为这种想法我就去找了下 `super-prefix` 的代码存放位置，在如下位置找到了控制重命名方案的代码。

```
C:\Users\用户名\.picgo\node_modules\picgo-plugin-super-prefix\dist\index.js
```

是用 JS 写的脚本，虽然没学过 JS，但是依靠自己多年基于 Baidu & Google 的编程经验，强行修改了一下，同时还能自动检测文件名是否已经修改过，如果已经修改过就不再次重命名的功能。

修改的部分如下，大致在 `index.js` 的60行左右。

```javascript
if (userConfig.fileFormat != undefined && userConfig.fileFormat != '' && String(fileName).search(/[0-9]{0} ( - | _ ){1} [0-9]{6}/)) {
    /*
    if (i > 0) {
        fileName = prefix + dayjs_1.default().format(userConfig.fileFormat) + '-' + i + ctx.output[i].extname;
    }
    else {
        fileName = prefix + dayjs_1.default().format(userConfig.fileFormat) + ctx.output[i].extname;
    }
    */
    fileName = prefix + dayjs_1.default().format(userConfig.fileFormat) + '_' + String(fileName).split(".", 1) + '_' + i + ctx.output[i].extname;
}
```

改这一部分的时候还遇到了一个新问题，改了之后再上传图片发现重命名失败，查了 `log` 发现有报错：`SyntaxError: missing ) after argument list`。

![](http://connor-sun-pic.oss-cn-shanghai.aliyuncs.com/imghost/2021/03/19/20210319-122129.png)

当时检查了一下发现我确实把 `if` 的右括号落下了，结果加上了还是报同样的错误。最后又进行了一次基于百度的Debug发现，原来是 `string.search()` 中，想要写正则表达式需要前后使用 `/` 包围。

### 剪掉加的功能

刚加好自己想要的功能还有点沾沾自喜，但越用越感觉到原作者的设计还是很有道理的。

1. 关于日期做路径

   图片多了就发现，不使用日期做路径，图床内图片文件就会非常混乱，相比之下一篇文档中的文件在不同文件夹真的无所谓；

2. 关于文件名修改

   用久了也会发现，其实保留文件名没什么意义，更多属的文件名并不包含自身的信息，也是以时间戳来命名的，比如我自己使用 `Snipaste` 和QQ截图，直接粘贴的文件名就是 `image + 时间戳` 的形式，这时候如果保留文件名，文件名就会变成冗杂的一串；

   而且就算有少数图片是规范命名的，其实也不会去看，加这个功能徒增烦恼。

虽然折腾了一圈之后还是把功能调回原作者的版本了，但整体过程还是很有趣的，这次折腾后的感受就是：

多为自己剪枝，不必要的东西一般就是不需要。



## 遗留的新问题

Typora的自动上传没有想象中的方便，把文档中已上传到图床的图片复制到其他文档插入还会重新上传一次并重新重命名，就会造成图床中同样的图片保存了两份。

> TODO: 给Typora提交Issue？