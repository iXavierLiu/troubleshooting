### crc error (code:28)
出现该问题是由于文件校验错误，请检查启动镜像刻录是否完整，尝试更换工具刻录镜像或更换镜像刻录的存储设备后重试

### No Network Adapters (该解决办法未验证官方性)
出现该问题是由于无法找到合适的网卡驱动程序，可以在[vmware社区][vmware-drivers]下载驱动程序集合(或手动下载对应的驱动程序)

1. 下载vib网卡驱动程序
2. 下载[Offline Bundle zip][exsi-download]包
3. 管理员身份启动powershell，安装vmware powercli
(注：下载过程较慢，若要在虚拟机中安装，请先配好系统代理)
```
Install-Module -Name VMware.PowerCLI
```

(下列命令需要python环境，windows store中安装Python3.7可以轻松识别，自行下载安装的python可能会导致各种识别问题)
添加offline bundle和vib驱动
```
PS> Add-EsxSoftwareDepot .\VMware-ESXi-8.0U1-21495797-depot.zip
PS> Add-EsxSoftwareDepot .\Net-Community-Driver_1.2.7.0-1vmw.700.1.0.15843807_19480755.zip
```
查看预定义配置
```
PS> Get-EsxImageProfile
Name                           Vendor          Last Modified   Acceptance Level
----                           ------          -------------   ----------------
ESXi-8.0U1-21495797-standard   VMware, Inc.    2023/4/18 0:... PartnerSupported
ESXi-8.0U1-21495797-no-tools   VMware, Inc.    2023/3/25 2:... PartnerSupported
```
选择标准的配置
```
New-EsxImageProfile -CloneProfile ESXi-8.0U1-21495797-standard -name MyProfile -Vendor MyExsi
```
将驱动添加到配置文件中，其中MyProfile是刚刚创建的配置名称，net-community是驱动名称
```
PS> Add-EsxSoftwarePackage -ImageProfile MyProfile -SoftwarePackage net-community
```

导出以MyProfile为配置的镜像
```
PS> Export-EsxImageProfile -ImageProfile MyProfile -ExportToISO -filepath .\MyExsi.iso
```


[^_^]:
    参考
    
[vmware-drivers]: <https://flings.vmware.com/community-networking-driver-for-esxi#instructions> (点击打开)
[exsi-download]: https://customerconnect.vmware.com/en/evalcenter?p=free-esxi8 (点击打开)
