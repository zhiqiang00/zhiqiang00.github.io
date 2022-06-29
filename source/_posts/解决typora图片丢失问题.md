---
title: 解决typora图片丢失问题
date: 2022年3月14日
categories: [工具使用]
tags: [typora，Markdown, 图床]
katex: true
cover: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/08c72b270c54abbd8924e4c8888f7ee6-wFna77QtSi0-24c6d7.jpg
top_img: https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/20/9d2244833e878e2169062087c9ab0874-wallhaven-g72p87-af7e51.jpg
---

## 解决typora图片丢失问题

Typora + PicGo-Core + Github实现图片上传Github

### 1. 配置nodejs环境

需要电脑配置有nodejs环境，参考[windows下安装nodejs](https://segmentfault.com/a/1190000023871608)；

### 2. 安装picgo

在配置好nodejs环境后，用npm安装picgo，命令如下：

```python
npm install picgo -g
```

&emsp;&emsp; 安装完成后，输入命令`where picgo`, 记录第一个路径。

![image-20220317173319087](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/27/6ee1a58f4cc3e0c92f621bb752362bf7-6ee1a58f4cc3e0c92f621bb752362bf7-image-20220317173319087-c22abc-3dbff1.png)

### 3. 安装github-plus

官方提供的github上传图库不好用，安装一款新的上传插件`github-plus`, 命令行执行：

```
picgo install github-plus
```

安装好后会有“插件安装成功”的提示。

### 4. 创建github仓库

- 想创建一个空的仓库

  ![image-20220317174326731](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/17/bd3cd8ad91cc9d6e069bbae4075737ec-image-20220317174326731-a81285.png)

- 新建个人access token

  ![image-20220317174949176](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/17/89a89a420259a1427056092ed05412b8-image-20220317174949176-f54dab.png)

  

  ![image-20220317175218256](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/17/503449f7ab7039d77e31e281528f9b98-image-20220317175218256-c4954e.png)
  
  **将得到的token进行保存，后面要用，否则就不再可见了！！！**

### 5. Typora图像设置

在`Typora`中配置图像上传信息，设置PicGo的配置信息。

![image-20220317173725450](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/17/40c886408bf1fd2aad6b06464e3afda6-image-20220317173725450-d97b7e.png)

- 首先上传服务选择`PicGo-Core(command line)`
- 打开配置文件，在打开的配置文件，添加相关信息

```json
{
  "picBed": {
    "uploader": "githubPlus",
    "current": "githubPlus",
    "githubPlus": {
      "repo": "zhiqiang00/Picbed",
      "branch": "main",
      "token": "ghp_6UpTgRay*******************",
      "path": "blog-images",
      "curtomUrl": "",
      "origin": "github"
    }
  },
  "picgoPlugins": {
    "picgo-plugin-github-plus": true,
    "picgo-plugin-rename-file": true
  },
  "picgo-plugin-rename-file": {
    "format": "{y}/{m}/{d}/{hash}-{origin}-{rand:6}"
  },
  "picgo-plugin-github-plus": {
    "lastSync": "2022-06-27 07:33:56"
  }
}
```

### 6. 测试

- 将上传服务修改为如图所示
- 自定义命令
- 点击验证图片上传选项
- 如果成功，如下图所示

![image-20220317184212335](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/17/df35251e0c1eff57e1e1ed9b65f1632a-image-20220317184212335-7b46be.png)

![image-20220317184702101](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/17/5d13e0a0a3ea08e93a64338fec735ef6-image-20220317184702101-9860f4.png)

### 7. 图片上传

- 将图片拖入Typora中，然后在图片单击右键，图片上传即可。

- 另外也可以在设置了设置每次复制图片的时候，自动上传，如图：

  ![image-20220317205722537](https://raw.githubusercontent.com/zhiqiang00/Picbed/main/blog-images/2022/03/17/6876e8c70f73cdc6034f321c10b35b88-image-20220317205722537-05ce94.png)

- 还可以在格式里选择图片，一次性上传全部图片。

### 8. 图片重命名

由于我们日后博客数量较多，图片也会很多，所以将图片合理命名也很重要。更重要的是，如果图片命名重复，会报错无法上传，重命名会保证图片名字不同。

`picgo-plugin-rename-file` 插件可以帮我们安装一定的规则将文件进行重命名，具体设置请看[github](https://github.com/liuwave/picgo-plugin-rename-file)。

- 输入一下命令安装:

```
picgo install rename-file
```

- 安装完成后，打开`picgo`的配置文件`C:\Users\xxx\.picgo\config.json`，或者像上面第五步那样打开配置文件。**末尾最后一个大括号前**添加一下信息即可。具体命名方式可以去github上看，

```
,
"picgo-plugin-rename-file": {
	"format": "{y}/{m}/{d}/{hash}-{origin}-{rand:6}"
}
```

**参考博客**

1. [Typora + PicGo-Core + Github 实现图片上传到Github ](https://www.cnblogs.com/xiaowj/p/13934555.html)
2. [Typora+PicGo-Core(command line)+SMMS、github、gitee实现Typora图片上传到图床](https://blog.csdn.net/brawly/article/details/106436042?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-0.pc_relevant_default&spm=1001.2101.3001.4242.1&utm_relevant_index=3)
3. [typora+PicGO-Core上传图片到github](https://blog.csdn.net/a554521655/article/details/113443338)