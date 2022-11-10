---
title: "MacBook os和win双系统时间错误"
date: "2022-04-29"
---

```
MacBook os和win双系统时间错误
修改windows注册表，在HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Control/TimeZoneInformation/中加一项DWORD，RealTimeIsUniversal，把值设为1。

原理是这个键值使得windows也像OS一样把bios时间作为GMT+0时间，这样就可以解决两

个系统之间反复调整时区的问题了。
```
