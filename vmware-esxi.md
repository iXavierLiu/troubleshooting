### crc error (code:28)
出现该问题是由于文件校验错误，请检查启动镜像刻录是否完整，尝试更换工具刻录镜像或更换镜像刻录的存储设备后重试

### No Network Adapters
出现该问题是由于无法找到合适的网卡驱动程序，可以在[vmware社区][vmware-drivers]下载驱动程序集合(或手动下载对应的驱动程序)
**如果是realtek的网卡就别挣扎了，建议直接再上个intel的网卡**

1. 先安装vmware官方的powercli的docker镜像，启动镜像并将目录`~/esxi/`映射到容器中
```
docker pull vmware/powerclicore
mkdir ~/esxi/
docker run -v ~/esxi/:/esxi/:z --rm -it vmware/powerclicore
```



2. 下载合适的[网卡驱动程序(.vib)][vmware-drivers]及[Offline Bundle zip][esxi-download]包，并放到刚刚创建的`~/esxi/`目录
4. 在powercli容器中执行
```
PS> cd /esxi/
PS> Add-EsxSoftwareDepot -DepotUrl /esxi/VMware-ESXi-8.0U1-21495797-depot.zip
PS> Add-EsxSoftwareDepot -DepotUrl /esxi/Net-Community-Driver_1.2.7.0-1vmw.700.1.0.15843807_19480755.zip
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
PS> New-EsxImageProfile -CloneProfile ESXi-8.0U1-21495797-standard -name MyEsxi -Vendor errcodex
```

将驱动添加到配置文件中，其中MyEsxi是刚刚创建的配置名称，net-community是驱动名称
```
PS> Add-EsxSoftwarePackage -ImageProfile MyEsxi -SoftwarePackage net-community
```

导出以MyProfile为配置的镜像
```
PS> Export-EsxImageProfile -ImageProfile MyEsxi -ExportToISO -filepath ./MyEsxi.iso
```

`exit`退出容器，在`~/esxi/`目录中MyEsxi.iso即为构建好的安装镜像

参考:
[添加驱动][add-drivers]
[powercli官网][powercli]

[^_^]:
    参考地址
    
[vmware-drivers]: <https://flings.vmware.com/community-networking-driver-for-esxi#instructions> (点击打开)
[esxi-download]: <https://customerconnect.vmware.com/en/evalcenter?p=free-esxi8> (点击打开)
[powercli]: <https://developer.vmware.com/powercli>
[add-drivers]: <https://kb.vmware.com/s/article/2005205?lang=zh_cn#Update_Manager>

