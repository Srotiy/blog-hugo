---
title: "使用Python自动观看驾校视频并打卡"
date: 2020-08-15T11:17:18+08:00
draft: false
---



# 抓包

研究了驾考软件后发现APP就是个套壳浏览器，直接与http接口交互即可。

由于我的主设备是iOS，这里使用了QuantumultX 进行调试抓包，有些或许是https通信还需要配置下MITM，我这里全是裸http就不必了。

# 分析

测试后发现，观看视频与提交没有关联，遂跳过视频相关处理，直接到最后的提交部分。

服务端是没有计时器的，只要提交数据的时间符合便可记录学时，主要为StartTimeID记录开始时间，endtime记录经过的时间，提交时服务器。这个StartTimeID为后端生成，应该是有特定的公式，多次尝试后找不到相关规律，无法根据当前时间反推开始时间，直接粗暴的实时获取StartTimeID，使用了Sleep进行等待再提交。

```python
#!/usr/bin/python3
# Author: Srotiy
# 2020.08.15
# 不想看视频不想去驾校，那咋办嘛？
import json
import requests
import time
import random
# Login Information
LoginName = "###"
Password = "###"
Url = "http://"
# TelegramBotInformation
TelegramApi = "#"
TelegramBotToken = "#"
TelegramUserID = "#"
headers = {
    "User-Agent": "aaej-training/201908081930 CFNetwork/1128.0.1 Darwin/19.6.0",
    "Content-Type": "application/json",
    "Accept": "*/*",
    "Connection": "keep-alive"
}
# Login，GetSomeCookie.
def GetCookie():
    global Url, headers, LoginName, Password
    headers['Content-Type'] = "application/json"
    rLogin = requests.post(Url+'stdLoginV3', headers=headers, json={"version":"5","name":LoginName,"pass":Password,"platform":"ios"})
    print(rLogin.json())
    Cookie = 'sid='+rLogin.json()['userToken']
    return Cookie

# GetVideoID
headers['Cookie'] = GetCookie()
rVideolist = requests.post(Url+'video/getVideoList', headers=headers)
allvideoid = []
for videoidList in rVideolist.json()['videoList']:
    if videoidList['km'] == 'km3':
        allvideoid.append(videoidList['id'])

videoid = random.choice(allvideoid)
# GetStartTimeid
headers['Content-Type'] = "application/x-www-form-urlencoded"
VideoInfo = requests.post(Url+'video/playVideo', headers=headers, data='cdId='+videoid).json()
StartTimeid = VideoInfo['sttId']
print("StartTimeid: "+StartTimeid)
print("VideoID: "+videoid)
WaitTime = random.randint(2760,3600)
print('WaitTime: '+str(WaitTime)+'s')
time.sleep(WaitTime)
# QuitVideo & SaveStudyTime
headers['Content-Type'] = "application/json"
headers['Cookie'] = GetCookie()
rQuit = requests.post(Url+'video/quitVideoMobile', headers=headers, json={"isOver":"true","endTime":random.randint(2000,3000),"isContinueStudy":"false","videoPara":StartTimeid+"-"+videoid}).json()
# Send Message To Telegram
print(rQuit)
requests.get(TelegramApi+TelegramBotToken+"/sendMessage?chat_id="+TelegramUserID+"&text=Videoid: "+videoid+"\nStartTimeid："+StartTimeid+"\n"+rQuit['msg']+'\n用时: '+str(WaitTime)+'s', headers=headers).json()
# Get Study Time Info
time.sleep(300)
headers['Cookie'] = GetCookie()
km1time = requests.post(Url+'getStudytimeInfo', headers=headers).json()[0]['CURRENTTIME']
km3time = requests.post(Url+'getStudytimeInfo', headers=headers).json()[1]['CURRENTTIME']
requests.get(TelegramApi+TelegramBotToken+"/sendMessage?chat_id="+TelegramUserID+"&text=科目一："+km1time+"分钟\n科目三："+km3time+"分钟", headers=headers).json()
```