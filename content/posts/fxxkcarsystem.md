---
title: "使用Python自动观看驾校视频并打卡"
date: 2020-08-15T11:17:18+08:00
draft: false
---
# 贴代码

```python
#!/usr/bin/python3
#Author: Srotiy
#2020.08.15
#不想看视频不想去驾校，那咋办嘛？
import json
import requests
import time
import random
#填入用户名及密码
LoginName = "###"
Password = "###"
Url = "http://"
#填入通知机器人Token令牌及Api地址
TelegramApi = "#"
TelegramBotToken = "#"
TelegramUserID = "#"
headers = {
    "User-Agent": "aaej-training/201908081930 CFNetwork/1128.0.1 Darwin/19.6.0",
    "Content-Type": "application/json",
    "Accept": "*/*",
    "Connection": "keep-alive"
}
#登陆，获取Cookie
def GetCookie():
    global Url, headers, LoginName, Password
    headers['Content-Type'] = "application/json"
    rLogin = requests.post(Url+'stdLoginV3', headers=headers, json={"version":"5","name":LoginName,"pass":Password,"platform":"ios"})
    print(rLogin.json())
    Cookie = 'sid='+rLogin.json()['userToken']
    return Cookie

# GetVideoid
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
#WaitTime = random.randint(5,10)
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