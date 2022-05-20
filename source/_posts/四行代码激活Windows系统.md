---
title: 四行代码激活Windows系统
date: 2022-03-10 20:13:45
tags:
---

> 鉴于网上激活工具鱼龙混杂，支持激活的版本也不一样，这里直接白嫖一手[果核](https://www.ghxi.com/)的KMS服务器，虽说自己搭建也可以，但一是服务器需要费用，二是需要花费时间维护，哪有白嫖来得香。

## 激活Windows

打开 CMD 输入以下命令：

### 1.卸载

```bash
slmgr.vbs /upk
```

会提示成功卸载密钥，没有密钥的会提示找不到产品密钥。

### 2.安装密钥

1. **Windows 11**

```bash
VK7JG-NPHTM-C97JM-9MPGT-3V66T
```

win11直接复制密钥到设置里粘贴激活就行。

2. **Windows 10**

```bash
slmgr /ipk W269N-WFGWX-YVC9B-4J6C9-T83GX
```

3. **Windows 7**

```bash
slmgr /ipk FJ82H-XT6CR-J8D7P-XQJJ2-GPDD4
```

上面的密钥都是对应专业版，如果你是其他版本请在下面密钥列表里找到你自己对应的版本。

### 3.设置激活服务器

```bash
slmgr /skms kms.ghpym.com
```

提示设置成功后进行下一步

### 4.开始激活

```bash
slmgr /ato
```

输入后回车，稍等一会，会提示激活成功。

顺便贴上office的激活方法，有需要自取。

## 激活office

OFFICE必须是VOL版本，否则无法激活。一般下载的都是VOL版本，MSDN上有下载。

果核提供的 Office 2019 离线安装包：

[Microsoft Office 2019 2021年10月批量许可版 - 果核剥壳](https://www.ghxi.com/office2019iso.html)

### 运行CMD，切换到office安装目录

拿office2016为例

找到你的office安装目录，比如：

32位：C:\Program Files (x86)\Microsoft Office\Office16

64位：C:\Program Files\Microsoft Office\Office16

Office2019只是2016的一个小小的升级，所以目录和2016一致

Office2019目录是office2016，Office16是office2016，Office15就是2013，Office14就是2010

然后目录对的话，该目录下面应该有个 OSPP.VBS

接下来我们就进入到这个目录下面，例如（请更改为自己的实际安装目录）

```bash
cd “C:\Program Files\Microsoft Office\Office16”
```

运行激活指令

查看已设置的激活码

```bash
cscript ospp.vbs /dstatus
```

卸载原本的激活码【xxxxx为密钥后五位】

```bash
cscript ospp.vbs /unpkey:xxxxx
```

设置激活码[到下面找]

```bash
cscript ospp.vbs /inpkey:xxxxx-xxxxx-xxxxx-xxxxx
```

设置激活服务器：

```bash
cscript ospp.vbs /sethst:kms.ghpym.com
```

开始激活：

```bash
cscript ospp.vbs /act
```

等待激活成功即可。

另外，激活office密钥不包含Visio，如果电脑同时安装Visio，需要再次操作上面的方式，把激活码换成Visio的即可。

## 密钥列表

1. **Windows 11**
   
   > 专业版安装密钥：VK7JG-NPHTM-C97JM-9MPGT-3V66T

2. **Windows Server 2019**  
   
   > Windows Server 2019 Datacenter WMDGN-G9PQG-XVVXX-R3X43-63DFG  
   > Windows Server 2019 Standard N69G4-B89J2-4G8F4-WWYCC-J464C  
   > Windows Server 2019 Essentials WVDHN-86M7X-466P6-VHXV7-YY726

3. **Windows Server 2016**  
   
   > Windows Server 2016 Datacenter CB7KF-BWN84-R7R2Y-793K2-8XDDG  
   > Windows Server 2016 Standard WC2BQ-8NRM3-FDDYY-2BFGV-KHKQY  
   > Windows Server 2016 Essentials JCKRF-N37P4-C2D82-9YXRT-4M63B

4. **Windows 10, all supported Semi-Annual Channel versions**  
   
   > Windows 10 Pro W269N-WFGWX-YVC9B-4J6C9-T83GX  
   > Windows 10 Pro N MH37W-N47XK-V7XM9-C7227-GCQG9  
   > Windows 10 Pro for Workstations NRG8B-VKK3Q-CXVCJ-9G2XF-6Q84J  
   > Windows 10 Pro for Workstations N 9FNHH-K3HBT-3W4TD-6383H-6XYWF 
   > Windows 10 Pro Education 6TP4R-GNPTD-KYYHQ-7B7DP-J447Y  
   > Windows 10 Pro Education N YVWGF-BXNMC-HTQYQ-CPQ99-66QFC  
   > Windows 10 Education NW6C2-QMPVW-D7KKK-3GKT6-VCFB2  
   > Windows 10 Education N 2WH4N-8QGBV-H22JP-CT43Q-MDWWJ  
   > Windows 10 Enterprise NPPR9-FWDCX-D2C8J-H872K-2YT43  
   > Windows 10 Enterprise N DPH2V-TTNVB-4X9Q3-TJR4H-KHJW4  
   > Windows 10 Enterprise G YYVX9-NTFWV-6MDM3-9PT4T-4M68B  
   > Windows 10 Enterprise G N 44RPN-FTY23-9VTTB-MP9BX-T84FV

5. **Windows 10 LTSC/LTSB versions**  
   
   > Windows 10 Enterprise LTSC 2019 M7XTQ-FN8P6-TTKYV-9D4CC-J462D  
   > Windows 10 Enterprise N LTSC 2019 92NFX-8DJQP-P6BBQ-THF9C-7CG2H 
   > Windows 10 Enterprise LTSB 2016 DCPHK-NFMTC-H88MJ-PFHPY-QJ4BJ  
   > Windows 10 Enterprise N LTSB 2016 QFFDN-GRT3P-VKWWX-X7T3R-8B639 
   > Windows 10 Enterprise 2015 LTSB WNMTR-4C88C-JK8YV-HQ7T2-76DF9  
   > Windows 10 Enterprise 2015 LTSB N 2F77B-TNFGY-69QQF-B8YKP-D69TJ

6. **Windows Server 2012 R2**  
   
   > Windows Server 2012 R2 Server Standard D2N9P-3P6X9-2R39C-7RTCD-MDVJX
   > Windows Server 2012 R2 Datacenter W3GGN-FT8W3-Y4M27-J84CP-Q3VJ9 
   > Windows Server 2012 R2 Essentials KNC87-3J2TX-XB4WP-VCPJV-M4FWM

7. **Windows Server 2012**  
   
   > Windows Server 2012 BN3D2-R7TKB-3YPBD-8DRP2-27GG4  
   > Windows Server 2012 N 8N2M2-HWPGY-7PGT9-HGDD8-GVGGY  
   > Windows Server 2012 Single Language 2WN2H-YGCQR-KFX6K-CD6TF-84YXQ  
   > Windows Server 2012 Country Specific 4K36P-JN4VD-GDC6V-KDT89-DYFKP
   > Windows Server 2012 Server Standard XC9B7-NBPP2-83J2H-RHMBY-92BT4
   > Windows Server 2012 MultiPoint Standard HM7DN-YVMH3-46JC3-XYTG7-CYQJJ  
   > Windows Server 2012 MultiPoint Premium XNH6W-2V9GX-RGJ4K-Y8X6F-QGJ2G  
   > Windows Server 2012 Datacenter 48HP8-DN98B-MYWDG-T2DCC-8W83P

8. **Windows Server 2008 R2**  
   
   > Windows Server 2008 R2 Web 6TPJF-RBVHG-WBW2R-86QPH-6RTM4  
   > Windows Server 2008 R2 HPC edition TT8MH-CG224-D3D7Q-498W2-9QCTX  
   > Windows Server 2008 R2 Standard YC6KT-GKW9T-YTKYR-T4X34-R7VHC  
   > Windows Server 2008 R2 Enterprise 489J6-VHDMP-X63PK-3K798-CPX3Y  
   > Windows Server 2008 R2 Datacenter 74YFP-3QFB3-KQT8W-PMXWJ-7M648
   > Windows Server 2008 R2 for Itanium-based Systems GT63C-RJFQ3-4GMB6-BRFB9-CB83V

9. **Windows Server 2008**  
   
   > Windows Web Server 2008 WYR28-R7TFJ-3X2YQ-YCY4H-M249D  
   > Windows Server 2008 Standard TM24T-X9RMF-VWXK6-X8JC9-BFGM2  
   > Windows Server 2008 Standard without Hyper-V W7VD6-7JFBR-RX26B-YKQ3Y-6FFFJ  
   > Windows Server 2008 Enterprise YQGMW-MPWTJ-34KDK-48M3W-X4Q6V  
   > Windows Server 2008 Enterprise without Hyper-V 39BXF-X8Q23-P2WWT-38T2F-G3FPG  
   > Windows Server 2008 HPC RCTX3-KWVHP-BR6TB-RB6DM-6X7HP  
   > Windows Server 2008 Datacenter 7M67G-PC374-GR742-YH8V4-TCBY3  
   > Windows Server 2008 Datacenter without Hyper-V 22XQ2-VRXRG-P8D42-K34TD-G3QQC  
   > Windows Server 2008 for Itanium-Based Systems 4DWFP-JF3DJ-B7DTH-78FJB-PDRHK

10. **Windows 8.1**  
    
    > Windows 8.1 Pro GCRJD-8NW9H-F2CDX-CCM8D-9D6T9  
    > Windows 8.1 Pro N HMCNV-VVBFX-7HMBH-CTY9B-B4FXY  
    > Windows 8.1 Enterprise MHF9N-XY6XB-WVXMC-BTDCT-MKKG7  
    > Windows 8.1 Enterprise N TT4HM-HN7YT-62K67-RGRQJ-JFFXW

11. **Windows 8**  
    
    > Windows 8 Pro NG4HW-VH26C-733KW-K6F98-J8CK4  
    > Windows 8 Pro N XCVCF-2NXM9-723PB-MHCB7-2RYQQ  
    > Windows 8 Enterprise 32JNW-9KQ84-P47T8-D8GGY-CWCK7  
    > Windows 8 Enterprise N JMNMF-RHW7P-DMY6X-RF3DR-X2BQT

12. **Windows 7**  
    
    > Windows 7 Professional FJ82H-XT6CR-J8D7P-XQJJ2-GPDD4  
    > Windows 7 Professional N MRPKT-YTG23-K7D7T-X2JMM-QY7MG  
    > Windows 7 Professional E W82YF-2Q76Y-63HXB-FGJG9-GF7QX  
    > Windows 7 Enterprise 33PXH-7Y6KF-2VJC9-XBBR8-HVTHH  
    > Windows 7 Enterprise N YDRBP-3D83W-TY26F-D46B2-XCKRJ  
    > Windows 7 Enterprise E C29WB-22CC8-VJ326-GHFJW-H9DH4

13. **Office 2019**  
    
    > Office 专业增强版 2019
    > NMMKJ-6RK4F-KMJVX-8D9MJ-6MWKP  
    > 
    > Office 标准版 2019
    > 6NWWJ-YQWMR-QKGCB-6TMB3-9D9HK
    > 
    > Project 专业版 2019 
    > B4NPR-3FKK7-T2MBV-FRQ4W-PKD2B
    > 
    > Project 标准版 2019  
    > C4F7P-NCP8C-6CQPT-MQHV9-JXD2M  
    > 
    > Visio 专业版 2019  
    > 9BGNQ-K37YR-RQHF2-38RQ3-7VCBB  
    > 
    > Visio 标准版 2019  
    > 7TQNQ-K3YQQ-3PFH7-CCPPM-X4VQ2  
    > 
    > Access 2019  
    > 9N9PT-27V4Y-VJ2PD-YXFMF-YTFQT  
    > 
    > Outlook 2019  
    > 7HD7K-N4PVK-BHBCQ-YWQRW-XW4VK  
    > 
    > Publisher 2019  
    > G2KWX-3NW6P-PY93R-JXK2T-C9Y9V  
    > 
    > Skype for Business 2019  
    > NCJ33-JHBBY-HTK98-MYCV8-HMKHJ

14. **Office 2016**  
    
    > Office 专业增强版 2016
    > XQNVK-8JYDB-WJ9W3-YJ8YR-WFG99  
    > 
    > Office 标准版2016
    > JNRGM-WHDWX-FJJG3-K47QV-DRTFM  
    > 
    > Project 专业版2016
    > YG9NW-3K39V-2T3HJ-93F3Q-G83KT
    > 
    > Visio 专业版2016  
    > PD3PC-RHNGV-FXJ29-8JK7D-RJRJK  
    > 
    > Visio 标准版2016  
    > 7WHWN-4T7MP-G96JF-G33KR-W8GF4

15. **Office 2013**  
    
    > Office 2013 Professional Plus YC7DK-G2NP3-2QQC3-J6H88-GVGXT  
    > Office 2013 Standard KBKQT-2NMXY-JJWGP-M62JB-92CD4  
    > Project 2013 Professional FN8TT-7WMH6-2D4X9-M337T-2342K  
    > Project 2013 Standard 6NTH3-CW976-3G3Y2-JK3TX-8QHTT  
    > Visio 2013 Professional C2FG9-N6J68-H8BTJ-BW3QX-RM3B3  
    > Visio 2013 Standard J484Y-4NKBF-W2HMG-DBMJC-PGWR7

16. **Office 2010**  
    
    > 套件：  
    > Office Professional Plus 2010 VYBBJ-TRJPB-QFQRF-QFT4D-H3GVB  
    > Office Standard 2010 V7QKV-4XVVR-XYV4D-F7DFM-8R6BM  
    > Office Home and Business 2010 D6QFG-VBYP2-XQHM7-J97RH-VVRCK
    > 
    > 独立产品：  
    > Access 2010 V7Y44-9T38C-R2VJK-666HK-T7DDX  
    > Excel 2010 H62QG-HXVKF-PP4HP-66KMR-CW9BM  
    > SharePoint Workspace 2010 QYYW6-QP4CB-MBV6G-HYMCJ-4T3J4  
    > InfoPath 2010 K96W8-67RPQ-62T9Y-J8FQJ-BT37T  
    > OneNote 2010 Q4Y4M-RHWJM-PY37F-MTKWH-D3XHX  
    > Outlook 2010 7YDC2-CWM8M-RRTJC-8MDVC-X3DWQ  
    > PowerPoint 2010 RC8FX-88JRY-3PF7C-X8P67-P4VTT  
    > Project Professional 2010 YGX6F-PGV49-PGW3J-9BTGG-VHKC6  
    > Project Standard 2010 4HP3K-88W3F-W2K3D-6677X-F9PGB  
    > Publisher 2010 BFK7F-9MYHM-V68C7-DRQ66-83YTP  
    > Word 2010 HVHB3-C6FV7-KQX9W-YQG79-CRY7T
