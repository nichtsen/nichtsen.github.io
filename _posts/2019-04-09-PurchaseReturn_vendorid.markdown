---
layout: post
title:  "供应商字段获取逻辑@Caihua"
date:   2019-04-10 16==14:40:44 +0000
categories: jekyll update
---
## 1. 获取采购员id（写死）：
```sql
declare @PurchaseSellTeamUserID int = 1779 /*系统自动采购员*/
```
## 2. 获取供应商代码
2.1 输入：平台代码和组织代码：
```sql
declare @platformcode varchar(10),
        @fsrcorg varchar(5)
```
2.2 输出：查询供应商代码
```sql
declare @SettleAccountVendorID int
select @SettleAccountVendorID = v.VendorID 
from tbPlatformFSRCORG p
join tbVendor(nolock) v on v.VendorCode = p.VendorCode
where p.PlatFormCode = @platformcode 
and p.FSRCORG = @fsrcorg

/* TODO: insert into .... */
```

