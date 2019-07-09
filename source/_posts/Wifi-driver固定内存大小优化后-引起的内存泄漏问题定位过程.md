---
title: Wifi driver固定内存大小优化后.引起的内存泄漏问题定位过程
date: 2019-01-19 15:28:41
tags: 问题定位
categories: 
- 问题定位
- WIFI
---
## 问题背景
客户的产品是MIFI，目前客户openwrt平台起来之后，内存剩余11MB，我们的driver目前支持10个station，
driver加载成功之后，内存直接耗掉10MB，后经定位发现，每支持一个station，就需要想Linux net core申请
8个netdev_queue，而8个netdev_queue就需要耗掉600KB左右的内存，由此可知，如果客户要求支持32个
station，那内存更加不足，于是，需要优化driver，减少静态内存消耗。

## driver静态内存消耗优化方法


## 内存优化后内存泄漏问题原因定位