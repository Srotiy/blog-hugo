---
title: "dd & hdparm测试磁盘io"
date: 2016-04-14T11:17:18+08:00
draft: false
---

```bash
dd if=/dev/zero of=iotest bs=64k count=16k conv=fdatasync && rm -f iotest
hdparm -t /dev/xxx
```