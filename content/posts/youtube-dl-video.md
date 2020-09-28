---
title: "使用ytb-dl下载ytb视频"
date: 2018-05-01T11:17:18+08:00
draft: false
---
# 安装ytb-dl

## GitHub地址

```
https://github.com/rg3/youtube-dl
```

## Linux上安装

```
sudo curl -L https://yt-dl.org/downloads/latest/youtube-dl -o /usr/local/bin/youtube-dl`
`sudo chmod a+rx /usr/local/bin/youtube-dl
```

## Windows上安装

你可以在官网下载
`https://yt-dl.org/latest/youtube-dl.exe`

# 安装ffmpeg

为啥要安装ffmpeg呢?
因为在ytb上,1080p及以上分辨率的视频都是音频和视频分离开的
下下来的视频是没有音频的,要使用ffmpeg合成
(无损合成,并不会损失画质)

## 在Debian上安装

```
sudo apt update`
`sudo apt install ffmpeg
```

## 在Windows上添加变量

`https://ffmpeg.zeranoe.com/builds/`
在这个网站下载
然后解压到c盘下面
应该看起来是这样的
`C:\ffmpeg-20180424-d9706f7-win64\bin\ffmpeg.exe` 然后是添加变量
![test](https://do0.me/srotiy.jpg) ![path](https://do0.me/2018/05/01/ytb1.jpg)
![path](https://do0.me/2018/05/01/ytb2.jpg)

# 使用

格式
`youtube-dl --proxy 代理地址 -F 链接`
像这样`youtube-dl --proxy http://192.168.24.100:1088 -F https://www.youtube.com/watch?v=rgKLYjiC5MU`
192.168.24.100是我内网中的一台intel E3-1265L的Debian机器搭建的内网HTTPproxy
本地的话就用一些其他东西代替
![format](https://do0.me/2018/05/01/ytb3.jpg)
参数-F(大写)是列出可以下载的视频的一些情况
看一下格式，我这里下载个2560x1090的MP4版本,音频为m4a的视频
id是264+140
`youtube-dl --proxy http://192.168.24.100:1088 -f 264+140 https://www.youtube.com/watch?v=rgKLYjiC5MU`
-f(小写)是用来下载的
![download](https://do0.me/2018/05/01/ytb4.jpg)
下载在当前目录下了
并自动使用了ffmpeg合并
![info](https://do0.me/2018/05/01/ytb5.jpg)
![play](https://do0.me/2018/05/01/ytb6.jpg)
完