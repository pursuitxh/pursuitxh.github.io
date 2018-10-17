---
title: sdio设备枚举问题
date: 2018-10-17 16:07:08
tags: 问题定位
categories: 
- 问题定位
- SDIO
---
##### 问题现象

对于同一款SDIO设备，在4.10的内核上可以枚举成功，但是在3.4.35的内核上却无法枚举成功

##### 问题定位

1. 通过对sdio core层加相关log，发现枚举失败的原因是sdio clock设置为0，导致后面sdio所有的通信都失败，最后枚举也失败；

2. 让我们来看一下设置sdio clock的函数：

   ```
   mmc_set_clock(host, mmc_sdio_get_max_clock(card));
   ```

   它调用mmc_sdio_get_max_clock（）来获取sdio所支持的最大clock，其实现如下：

   ```
   static unsigned mmc_sdio_get_max_clock(struct mmc_card *card)
   {
   	unsigned max_dtr;
   
   	if (mmc_card_hs(card)) {
   		/*
   		 * The SDIO specification doesn't mention how
   		 * the CIS transfer speed register relates to
   		 * high-speed, but it seems that 50 MHz is
   		 * mandatory.
   		 */
   		max_dtr = 50000000;
   	} else {
   		max_dtr = card->cis.max_dtr;
   	}
   
   	if (card->type == MMC_TYPE_SD_COMBO)
   		max_dtr = min(max_dtr, mmc_sd_get_max_clock(card));
   
   	return max_dtr;
   }
   ```

3. 问题就出在if(mmc_card_hs(card))的判断上，所有sdio设备枚举通过的内核版本，都走到第一个分支，将max_dtr hardcode成50M，所有枚举失败的的内核版本，都走到了第二个分支，通过cis中的max_dtr来设置，恰巧我们的CIS区域只设置了VID/PID信息，max_ptr和blk_size信息都没有设置，所以最终max_dtr的值为0.

4. mmc_hard_hs(card)实现有两种版本, 见patch（https://patchwork.kernel.org/patch/3492031/）

5. 最终的解决方法，修改sdio ip cis区域，将max_ptr和blk_size信息也配上。