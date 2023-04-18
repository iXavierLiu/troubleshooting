### 禁用内存文件
vmware在默认情况下会将内存映射到磁盘的文件中，以更快的实现挂起操作。但由于内存映射(shared memory | file mapping)会在虚拟机运行时创建一个大小等同于内存容量的文件，当分配的内存较大时会占用较多的磁盘空间及io占用，为了避免这一现象我们可以关闭内存映射。
在`C:\ProgramData\VMware\VMware Workstation`目录中编辑`settings.ini`，增加一项配置——这将会在全局禁用内存映射。如果只想对某个虚拟机设置，可单独编辑对应的虚拟机配置文件(\*.vmx)
```ini
mainMem.useNamedFile = "FALSE"
```

### 无法识别网卡(原生openwrt)
原生openwrt x86_64镜像会存在无法识别网卡的现象，可通过指定网络适配器解决，在对应的虚拟机配置文件(\*.vmx)中添加一项配置。[参考连接][openwrt-network-adapters]
```ini
ethernet0.virtualDev = "e1000"
```



[^_^]:
    下面是非常nice的引用型连接的识别符描述语法哦，不会在正文中显示的
    
[openwrt-network-adapters]: <https://openwrt.org/docs/guide-user/virtualization/vmware#things_you_need> (openwrt)
