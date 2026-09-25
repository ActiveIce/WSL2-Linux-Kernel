# WSL2-Linux-Kernel

[![auto build engine](https://github.com/ActiveIce/WSL2-Linux-Kernel/workflows/auto%20build%20engine/badge.svg)](https://github.com/ActiveIce/WSL2-Linux-Kernel/actions)
![GitHub tag (latest by date)](https://img.shields.io/github/v/tag/torvalds/linux?color=informational&label=verison)
![GPU Compute](https://img.shields.io/badge/CUDA-Support-brightgreen)

## 系统要求

Windows 10 版本 2004 (OS 内部版本 20145) 或更高版本
``` PowerShell
winver.exe
```

启用 “适用于 Linux 的 Windows 子系统” “虚拟机平台” (需要重启)
``` PowerShell
wsl.exe --install
```

## 下载

下载系统镜像、引导程序、内核至同一文件夹

[Ubuntu 24.04 (Noble)](https://cloud-images.ubuntu.com/wsl/noble/current/ubuntu-noble-wsl-amd64-wsl.rootfs.tar.gz)

[Ubuntu Loader](https://raw.githubusercontent.com/ActiveIce/WSL2-Linux-Kernel/master/ubuntu.exe)

[WSL2-Linux-Kernel](https://raw.githubusercontent.com/ActiveIce/WSL2-Linux-Kernel/master/bzImage)


将 `ubuntu-noble-wsl-amd64-wsl.rootfs.tar.gz` 重命名为 `install.tar.gz`

## 配置
在用户文件夹的根目录 `C:\Users\<yourUserName>` 新建文件 `.wslconfig`

在 `.wslconfig` 文件中按格式填入内核路径、分配CPU核心数、分配内存数量

``` PowerShell
[wsl2]
kernel=D:\\Ubuntu\\bzImage
processors=4
memory=4GB
```

## 安装和使用
``` PowerShell
.\ubuntu.exe --ui=tui
```

首次使用按提示设置用户名和密码即可


#### 清理 VHDX 磁盘
VHDX 磁盘文件不会随着 Ubuntu 系统内文件的删除而变小，若磁盘文件太大，可以手动缩小

##### 方法一

清理命令 ( `select vdisk file=` 后面参数是 VHDX 文件的路径)
``` PowerShell
wsl.exe --exec sudo fstrim /
wsl.exe --shutdown
diskpart
select vdisk file="D:\Ubuntu\ext4.vhdx"
attach vdisk readonly
compact vdisk
detach vdisk
exit
```

##### 方法二

启用 “Windows Powershell 的 Hyper-V 模块” (需要重启)
``` PowerShell
dism.exe /online /enable-feature /featurename:Microsoft-Hyper-V-Management-PowerShell /all /norestart
```
清理命令 ( `-Path` 后面参数是 VHDX 文件的路径)
``` PowerShell
wsl.exe --shutdown
Mount-VHD -Path "D:\Ubuntu\ext4.vhdx" -ReadOnly
Optimize-VHD -Path "D:\Ubuntu\ext4.vhdx" -Mode Full
Dismount-VHD -Path "D:\Ubuntu\ext4.vhdx"
```
