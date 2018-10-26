---
title: hostapd启动失败问题定位
date: 2018-10-26 14:07:36
tags: 问题定位
categories: 
- 问题定位
- WIFI
---
## 问题背景
客户Linux内核版本3.4.35，我们的内核版本4.10，FAE将wifi drv从4.10 porting到3.4.35之后，遇到两个问题：
> * sdio 枚举失败
> * hostapd启动失败

本文主要记录hostapd启动失败的原因
## 问题现象
通过查看hostapd的log，发现如下错误：

    1540362998.766921: nl80211: Failed to set channel (freq=2437): -95 (Operation not supported)

那么，-95错误是怎么来的呢？通过定位发现是由于set_channel未实现导致：

    nl80211_set_channel
     	.cmd = NL80211_CMD_SET_WIPHY,
		.doit = nl80211_set_wiphy,
		    result = __nl80211_set_channel(rdev, wdev, info);
		        result = cfg80211_set_freq(rdev, wdev, freq, channel_type);
		            	if (!rdev->ops->set_channel)
		                    return -EOPNOTSUPP;
而4.10的版本不实现set_channel就没有问题，可见新版本的内核相对于老版本的内核，对cfg80211的相关实现进行了改动。
## 问题解决：
正常解决办法是将freq通过`set_channel`信息实现，但是为了保证fw的统一性，在`set_channel`中只是将freq相关信息保存下来，在`apm_start`中将信息取出，通过消息发送给fw，进行channel设定。