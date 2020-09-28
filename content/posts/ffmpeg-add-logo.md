---
title: "Ffmpeg给视频添加水印"
date: 2018-04-16T11:17:18+08:00
draft: false
---
# 安装ffmpeg

Windows在这下载[ffmpeg-3.4.2-win64-static.zip](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4.2-win64-static.zip)
下不动请加速
在debian9中有预编译包
`sudo apt update`
`sudo apt install ffmpeg`
这里安装的ffmpeg不是最新版本
在debian9的testing中有最新版，需要设置优先级等
详细教程看这里[如何在Debian中启用测试版/不稳定版的软件库](https://linux.cn/article-3288-1.html)

# 加水印

```
ffmpeg -i input.mp4 -vf "movie=logo.svg,scale= 484:599[watermask]; [in] [watermask] overlay=718:241 [out]" out.mp4
```

input.mp4是输入文件
output.mp4是输出文件
movie=logo.svg
logo.svg是水印文件
484:599是水印分辨率请根据原图比例和视频调整若等比例放大需要同时扩大两个数字的倍数
overlay=718:241是从左上角开始图片左上顶点的横坐标和纵坐标
本事例是使用1920x1080视频素材
其他分辨率可能overlay要做改动。