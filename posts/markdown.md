---
title: Ubuntu 从入门到精通
date: 2026-03-15
tags: [linux, ubuntu]
head:
  - - meta
    - name: description
      content: vitepress-theme-bluearchive Ubuntu从入门到精通
  - - meta
    - name: keywords
      content: vitepress theme bluearchive Ubuntu从入门到精通
---

Ubuntu 从入门到精通

---

# Ubuntu 从入门到精通

> 本指南涵盖从系统安装到高级运维的完整知识体系，适合 Linux 初学者及希望深入掌握 Ubuntu 系统的用户。

---

## 目录

1. [准备工作与环境选择](#一准备工作与环境选择)
2. [系统安装与初始配置](#二系统安装与初始配置)
3. [基础命令与文件系统](#三基础命令与文件系统)
4. [软件包管理](#四软件包管理)
5. [用户与权限管理](#五用户与权限管理)
6. [网络配置与远程访问](#六网络配置与远程访问)
7. [存储管理与文件系统](#七存储管理与文件系统)
8. [系统服务与进程管理](#八系统服务与进程管理)
9. [Shell 脚本编程](#九shell-脚本编程)
10. [系统监控与性能调优](#十系统监控与性能调优)
11. [安全加固与防火墙](#十一安全加固与防火墙)
12. [故障排查与恢复](#十二故障排查与恢复)

---

## 一、准备工作与环境选择

### 1.1 Ubuntu 版本选择

Ubuntu 提供多个官方版本，根据使用场景选择：

| 版本 | 适用场景 | 支持周期 |
|------|----------|----------|
| **Ubuntu Desktop** | 个人电脑、开发工作站 | 9个月（LTS为5年）|
| **Ubuntu Server** | 服务器、云计算 | 5年（LTS）|
| **Ubuntu LTS** | 生产环境、企业部署 | 5年（可扩展至10年）|

**推荐策略：**
- 个人学习：选择最新的 LTS 版本（如 22.04 LTS 或 24.04 LTS）
- 生产服务器：必须使用 LTS 版本
- 开发测试：可使用非 LTS 版本体验新特性

### 1.2 硬件要求

**最低配置：**
- CPU：2 GHz 双核处理器
- 内存：4 GB RAM
- 存储：25 GB 可用空间
- 网络：互联网连接（用于更新）

**推荐配置：**
- CPU：64 位四核处理器
- 内存：8 GB 或更高
- 存储：SSD，50 GB 以上
- 显卡：支持 OpenGL 的 GPU（桌面版）

### 1.3 安装介质准备

下载官方 ISO 镜像：

```bash
# 通过命令行下载（示例为 22.04 LTS）
wget https://releases.ubuntu.com/22.04/ubuntu-22.04.4-desktop-amd64.iso

# 验证校验和
sha256sum ubuntu-22.04.4-desktop-amd64.iso


制作启动盘（在 Linux 上）：

```bash
# 插入 USB 设备，确认设备名（如 /dev/sdb，务必确认正确）
sudo fdisk -l

# 卸载设备
sudo umount /dev/sdb*

# 写入镜像（谨慎操作，会清空 U 盘数据）
sudo dd if=ubuntu-22.04.4-desktop-amd64.iso of=/dev/sdb bs=4M status=progress
```

---

## 二、系统安装与初始配置

### 2.1 安装过程关键步骤

1. **启动方式选择**
   - UEFI 模式（推荐）：支持 Secure Boot，启动更快
   - Legacy BIOS：兼容旧硬件

2. **分区方案**

   **推荐方案（桌面版）：**
   ```
   /boot/efi    512 MB    EFI 系统分区
   /boot        1 GB      引导分区（可选）
   /            50 GB+    根分区（ext4）
   /home        剩余空间  用户数据分区
   swap         4-8 GB    交换分区（或 swapfile）
   ```

   **服务器推荐方案：**
   ```
   /boot        1 GB
   /            100 GB    系统与应用程序
   /var         50 GB     日志与数据
   /home        单独分区  用户数据隔离
   /tmp         10 GB     临时文件（noexec 挂载）
   ```

3. **安装类型选择**
   - 清除整个磁盘并安装：适合新电脑
   - 与其他系统共存：双系统方案
   - 手动分区：高级用户自定义

### 2.2 首次启动配置

更新系统至最新状态：

```bash
sudo apt update && sudo apt upgrade -y
```

安装必要工具：

```bash
sudo apt install -y \
    build-essential \
    curl \
    wget \
    git \
    vim \
    htop \
    net-tools \
    software-properties-common \
    apt-transport-https \
    ca-certificates \
    gnupg \
    lsb-release
```

配置时区与区域设置：

```bash
# 查看当前时区
timedatectl status

# 列出可用时区
timedatectl list-timezones | grep Shanghai

# 设置时区
sudo timedatectl set-timezone Asia/Shanghai

# 启用 NTP 时间同步
sudo timedatectl set-ntp true
```

---

## 三、基础命令与文件系统

### 3.1 Linux 文件系统层次标准（FHS）

```
/               根目录
├── /bin        基本用户命令（二进制文件）
├── /boot       引导加载程序文件
├── /dev        设备文件
├── /etc        系统配置文件
├── /home       用户主目录
├── /lib        共享库文件
├── /media      可移动媒体挂载点
├── /mnt        临时挂载点
├── /opt        可选应用软件包
├── /proc       进程信息虚拟文件系统
├── /root       root 用户主目录
├── /run        运行时变量数据
├── /sbin       系统管理命令
├── /srv        服务数据
├── /sys        系统信息虚拟文件系统
├── /tmp        临时文件
├── /usr        用户程序
│   ├── /bin    非基本用户命令
│   ├── /lib    非基本共享库
│   └── /local  本地安装软件
├── /var        可变数据文件
│   ├── /log    日志文件
│   └── /spool  任务队列
```

### 3.2 文件与目录操作

**基础导航：**

```bash
# 查看当前目录
pwd

# 切换目录
cd /path/to/directory
cd ~          # 回到家目录
cd -          # 回到上次目录
cd ..         # 上级目录

# 列出目录内容
ls -la        # 详细列表，包含隐藏文件
ls -lh        # 人类可读的文件大小
ls -lt        # 按时间排序
ls -ltr       # 反向时间排序
```

**文件操作：**

```bash
# 创建文件
touch filename.txt
> filename.txt        # 清空或创建空文件

# 查看文件内容
cat file.txt          # 显示全部内容
less file.txt         # 分页查看（可向前翻页）
more file.txt         # 分页查看
head -n 20 file.txt   # 前 20 行
tail -n 20 file.txt   # 后 20 行
tail -f /var/log/syslog  # 实时跟踪日志

# 复制、移动、删除
cp source.txt dest.txt           # 复制文件
cp -r source_dir/ dest_dir/      # 递归复制目录
mv oldname.txt newname.txt       # 重命名/移动
rm file.txt                      # 删除文件
rm -r directory/                 # 递归删除目录
rm -rf directory/                # 强制递归删除（谨慎使用）

# 创建目录
mkdir newdir
mkdir -p path/to/nested/dir      # 递归创建父目录

# 查找文件
find /path -name "*.txt"         # 按名称查找
find /path -type f -size +100M   # 查找大于 100MB 的文件
find /path -mtime -7             # 7 天内修改的文件
locate filename                  # 快速定位（需 updatedb）
```

### 3.3 文本处理工具

**grep：文本搜索**

```bash
# 基础搜索
grep "pattern" file.txt

# 常用选项
grep -i "pattern" file.txt       # 忽略大小写
grep -r "pattern" /path/         # 递归搜索
grep -n "pattern" file.txt       # 显示行号
grep -v "pattern" file.txt       # 反向匹配（排除）
grep -E "pattern1|pattern2"      # 扩展正则表达式
grep -C 3 "pattern" file.txt     # 显示上下文 3 行

# 管道组合
ps aux | grep nginx
cat /var/log/syslog | grep error | tail -20
```

**sed：流编辑器**

```bash
# 替换文本
sed 's/old/new/' file.txt        # 每行首次替换
sed 's/old/new/g' file.txt       # 全局替换
sed -i 's/old/new/g' file.txt    # 直接修改文件（原地编辑）

# 删除行
sed '/pattern/d' file.txt        # 删除匹配行
sed '2,5d' file.txt              # 删除 2-5 行

# 高级用法
sed -n '10,20p' file.txt         # 打印 10-20 行
sed 's/^/# /' file.txt           # 行首添加注释
```

**awk：文本处理语言**

```bash
# 基础用法（按空格分割，打印第 1、3 列）
awk '{print $1, $3}' file.txt

# 指定分隔符
awk -F: '{print $1}' /etc/passwd

# 条件过滤
awk '$3 > 100 {print $1}' file.txt

# 计算总和
awk '{sum += $1} END {print sum}' numbers.txt

# 复杂处理
ps aux | awk '/nginx/ && !/awk/ {print $2, $11}'
```

---

## 四、软件包管理

### 4.1 APT 包管理系统

APT（Advanced Package Tool）是 Ubuntu 的核心包管理工具。

**基础命令：**

```bash
# 更新包列表
sudo apt update

# 升级已安装包
sudo apt upgrade              # 安全升级
sudo apt full-upgrade         # 完整升级（可能删除依赖包）
sudo apt dist-upgrade         # 发行版升级

# 安装软件
sudo apt install package_name
sudo apt install pkg1 pkg2 pkg3    # 批量安装

# 删除软件
sudo apt remove package_name       # 保留配置文件
sudo apt purge package_name        # 完全删除（包括配置）

# 清理系统
sudo apt autoremove                # 删除未使用的依赖
sudo apt clean                     # 清理下载的包缓存

# 搜索与信息
apt search keyword                 # 搜索包
apt show package_name              # 显示包信息
apt list --installed               # 列出已安装包
apt list --upgradeable             # 列出可升级包
```

**软件源管理：**

```bash
# 查看当前源
cat /etc/apt/sources.list
ls /etc/apt/sources.list.d/

# 更换国内镜像源（以阿里云为例）
sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup

# 编辑源列表（Ubuntu 22.04 Jammy 示例）
sudo tee /etc/apt/sources.list << 'EOF'
deb http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
EOF

sudo apt update
```

### 4.2 Snap 与 Flatpak

**Snap（Ubuntu 主推）：**

```bash
# 基础命令
sudo snap install package_name
sudo snap remove package_name
snap list                          # 列出已安装
snap find keyword                  # 搜索包
sudo snap refresh                  # 更新所有
sudo snap revert package_name      # 回滚版本

# 常用软件安装
sudo snap install code --classic   # VS Code
sudo snap install chromium
sudo snap install docker
```

**Flatpak（跨发行版）：**

```bash
# 安装 Flatpak
sudo apt install flatpak

# 添加 Flathub 仓库
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

# 安装软件
sudo flatpak install flathub com.spotify.Client
flatpak run com.spotify.Client
```

### 4.3 从源码编译安装

当需要特定版本或自定义编译选项时：

```bash
# 1. 安装编译依赖
sudo apt build-dep package_name

# 2. 下载源码
wget https://example.com/software-1.0.tar.gz
tar -xzf software-1.0.tar.gz
cd software-1.0

# 3. 编译安装（经典三步）
./configure --prefix=/usr/local
make
sudo make install

# 或使用 CMake
mkdir build && cd build
cmake ..
make
sudo make install
```

---

## 五、用户与权限管理

### 5.1 用户管理基础

**用户类型：**
- root：超级管理员（UID 0）
- 系统用户：运行服务（UID 1-999）
- 普通用户：交互式登录（UID 1000+）

**用户管理命令：**

```bash
# 创建用户
sudo adduser username              # 交互式（推荐）
sudo useradd -m -s /bin/bash username    # 非交互式
sudo passwd username               # 设置密码

# 修改用户
sudo usermod -aG sudo username     # 添加到 sudo 组
sudo usermod -l newname oldname    # 重命名
sudo usermod -d /new/home -m username    # 修改主目录

# 删除用户
sudo deluser username              # 删除用户
sudo deluser --remove-home username      # 同时删除主目录

# 查看用户信息
id username
whoami
who                                # 当前登录用户
w                                  # 登录用户及活动
last                               # 登录历史
```

### 5.2 组管理

```bash
# 创建组
sudo groupadd groupname

# 管理组成员
sudo usermod -aG groupname username    # 添加用户到组
sudo gpasswd -d username groupname     # 从组移除用户

# 查看组信息
groups username
getent group groupname
cat /etc/group | grep groupname
```

### 5.3 文件权限系统

**权限表示：**

```
-rwxr-xr-x  1 user group  1234 Jan 1 12:00 file.sh
│└┬┘└┬┘└┬┘
│ │  │  └── 其他用户权限 (rwx)
│ │  └───── 所属组权限 (rwx)
│ └──────── 所有者权限 (rwx)
└────────── 文件类型 (- 文件, d 目录, l 链接)
```

**权限数值：**

| 权限 | 数值 | 说明 |
|------|------|------|
| r（读） | 4 | 查看内容 |
| w（写） | 2 | 修改内容 |
| x（执行） | 1 | 执行文件/进入目录 |

**常用权限组合：**
- 755（rwxr-xr-x）：可执行文件、目录
- 644（rw-r--r--）：普通文件
- 700（rwx------）：私有文件
- 777（rwxrwxrwx）：开放权限（尽量避免）

**权限管理命令：**

```bash
# 修改权限
chmod 755 file.sh
chmod u+x file.sh                  # 给所有者添加执行权限
chmod go-w file.txt                # 移除组和其他人的写权限
chmod -R 755 directory/            # 递归修改

# 修改所有者
sudo chown user:group file.txt
sudo chown -R user:group directory/
sudo chgrp groupname file.txt      # 仅修改组

# 特殊权限
chmod u+s /usr/bin/passwd          # SUID（以所有者权限执行）
chmod g+s /shared/dir              # SGID（新建文件继承组）
chmod +t /tmp                      # Sticky Bit（仅删除自己的文件）
```

### 5.4 sudo 与权限委派

**配置 sudo 权限：**

```bash
# 编辑 sudoers 文件（必须使用 visudo）
sudo visudo

# 添加自定义规则
username ALL=(ALL:ALL) ALL         # 完全权限
%admin ALL=(ALL) NOPASSWD: ALL     # 免密码 sudo
username ALL=(root) /usr/bin/apt, /usr/bin/systemctl   # 限定命令
```

**sudo 使用技巧：**

```bash
sudo -i                            # 切换到 root 环境
sudo -u www-data whoami            # 以特定用户执行
sudo !!                            # 以 sudo 执行上条命令
sudo -l                            # 查看当前用户 sudo 权限
```

---

## 六、网络配置与远程访问

### 6.1 网络基础配置

**查看网络信息：**

```bash
# IP 地址
ip addr show
ip -4 addr show eth0

# 路由表
ip route show

# DNS 配置
cat /etc/resolv.conf
systemd-resolve --status

# 网络统计
ss -tuln                           # 监听端口
netstat -tulpn                     # 类似功能（需安装 net-tools）
```

**Netplan 配置（Ubuntu 18.04+）：**

```bash
# 配置文件位置
/etc/netplan/00-installer-config.yaml

# 静态 IP 配置示例
sudo tee /etc/netplan/01-static.yaml << 'EOF'
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.1.100/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 114.114.114.114
EOF

sudo netplan apply
```

### 6.2 SSH 远程管理

**安装与基础配置：**

```bash
sudo apt install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
```

**安全配置（/etc/ssh/sshd_config）：**

```bash
# 关键安全设置
Port 2222                          # 修改默认端口
PermitRootLogin no                 # 禁止 root 登录
PasswordAuthentication no          # 禁用密码认证（使用密钥）
PubkeyAuthentication yes           # 启用密钥认证
MaxAuthTries 3                     # 最大尝试次数
ClientAliveInterval 300            # 心跳检测
ClientAliveCountMax 2
AllowUsers user1 user2             # 白名单用户

# 重启服务
sudo systemctl restart sshd
```

**密钥认证配置：**

```bash
# 生成密钥对
ssh-keygen -t ed25519 -C "your_email@example.com"
# 或传统 RSA
ssh-keygen -t rsa -b 4096

# 复制公钥到服务器
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@server_ip

# 手动添加公钥
cat ~/.ssh/id_ed25519.pub | ssh user@server "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"

# 设置权限（服务器端）
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

**SSH 客户端技巧：**

```bash
# 配置文件 ~/.ssh/config
Host myserver
    HostName 192.168.1.100
    User admin
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
    ServerAliveInterval 60

# 使用别名连接
ssh myserver

# 代理转发
ssh -A user@jump-server

# 本地端口转发
ssh -L 8080:localhost:80 user@remote-server

# 远程端口转发
ssh -R 9090:localhost:3000 user@remote-server
```

### 6.3 防火墙配置（UFW）

```bash
# 基础操作
sudo ufw enable                    # 启用
sudo ufw disable                   # 禁用
sudo ufw status verbose            # 查看状态

# 规则管理
sudo ufw default deny incoming     # 默认拒绝入站
sudo ufw default allow outgoing    # 默认允许出站

# 添加规则
sudo ufw allow 22/tcp              # 允许 SSH
sudo ufw allow 80/tcp              # HTTP
sudo ufw allow 443/tcp             # HTTPS
sudo ufw allow from 192.168.1.0/24 # 允许特定网段

# 删除规则
sudo ufw delete allow 80/tcp
sudo ufw reset                     # 重置所有规则

# 高级规则
sudo ufw allow proto tcp from any to any port 10000:20000
```

---

## 七、存储管理与文件系统

### 7.1 磁盘分区与格式化

**查看磁盘信息：**

```bash
lsblk                              # 列出块设备
fdisk -l                           # 详细分区信息
df -h                              # 磁盘使用情况
du -sh /path                       # 目录大小
```

**分区操作（fdisk/gdisk）：**

```bash
# MBR 分区（fdisk，最大 2TB）
sudo fdisk /dev/sdb
# 常用命令：n(新建), d(删除), p(打印), w(写入), q(退出)

# GPT 分区（gdisk，推荐用于大磁盘）
sudo gdisk /dev/sdb

# 非交互式分区（parted）
sudo parted /dev/sdb mklabel gpt
sudo parted -a optimal /dev/sdb mkpart primary ext4 0% 100%
```

**文件系统创建：**

```bash
# 创建 ext4 文件系统
sudo mkfs.ext4 /dev/sdb1
sudo mkfs.ext4 -L "DataDisk" /dev/sdb1    # 添加标签

# 创建 XFS（大文件性能更好）
sudo apt install xfsprogs
sudo mkfs.xfs /dev/sdb1

# 检查文件系统
sudo fsck /dev/sdb1
sudo e2fsck -f /dev/sdb1             # 强制检查 ext 系列
```

### 7.2 挂载与自动挂载

**手动挂载：**

```bash
sudo mkdir /mnt/data
sudo mount /dev/sdb1 /mnt/data
sudo mount -o remount,ro /mnt/data   # 重新挂载为只读
```

**配置 /etc/fstab 实现开机挂载：**

```bash
# 获取 UUID
sudo blkid /dev/sdb1

# 编辑 fstab
sudo vim /etc/fstab

# 添加条目（使用 UUID 更可靠）
UUID=xxxxx-xxxx-xxxx-xxxx-xxxxxxxx /mnt/data ext4 defaults,noatime,nofail 0 2

# 字段说明：
# <file system> <mount point> <type> <options> <dump> <pass>
# noatime: 减少写操作，提升性能
# nofail: 启动时若磁盘不存在不报错
# 0 2: 不备份，开机检查顺序（根分区为 1，其他为 2，0 为不检查）
```

**验证配置：**

```bash
sudo mount -a                        # 挂载所有条目
sudo systemctl daemon-reload         # 重新加载 systemd
```

### 7.3 LVM 逻辑卷管理

LVM 提供灵活的磁盘空间管理，支持动态调整大小。

**基础概念：**
- PV（Physical Volume）：物理卷
- VG（Volume Group）：卷组
- LV（Logical Volume）：逻辑卷

**操作步骤：**

```bash
# 1. 创建物理卷
sudo pvcreate /dev/sdb /dev/sdc

# 2. 创建卷组
sudo vgcreate vg_data /dev/sdb /dev/sdc

# 3. 创建逻辑卷
sudo lvcreate -L 50G -n lv_www vg_data      # 固定大小
sudo lvcreate -l 100%FREE -n lv_backup vg_data   # 使用全部剩余空间

# 4. 创建文件系统
sudo mkfs.ext4 /dev/vg_data/lv_www

# 5. 挂载使用
sudo mkdir /var/www
sudo mount /dev/vg_data/lv_www /var/www

# 扩展逻辑卷（在线扩容）
sudo lvextend -L +20G /dev/vg_data/lv_www
sudo resize2fs /dev/vg_data/lv_www           # ext4
# 或 xfs_growfs /mnt/point                   # XFS

# 查看信息
sudo pvs, vgs, lvs                           # 简要信息
sudo pvdisplay, vgdisplay, lvdisplay         # 详细信息
```

### 7.4 RAID 配置

**软件 RAID（mdadm）：**

```bash
sudo apt install mdadm

# 创建 RAID 1（镜像）
sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1

# 创建 RAID 5（至少需要 3 块盘）
sudo mdadm --create /dev/md0 --level=5 --raid-devices=3 /dev/sd[bcd]1

# 查看状态
cat /proc/mdstat
sudo mdadm --detail /dev/md0

# 保存配置
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
sudo update-initramfs -u

# 创建文件系统并挂载
sudo mkfs.ext4 /dev/md0
```

---

## 八、系统服务与进程管理

### 8.1 systemd 系统管理

systemd 是 Ubuntu 15.04+ 的初始化系统和服务管理器。

**服务管理：**

```bash
# 服务控制
sudo systemctl start service_name
sudo systemctl stop service_name
sudo systemctl restart service_name
sudo systemctl reload service_name       # 重新加载配置（不中断服务）
sudo systemctl status service_name

# 开机自启
sudo systemctl enable service_name
sudo systemctl disable service_name
sudo systemctl is-enabled service_name

# 查看所有服务
systemctl list-units --type=service --state=running
systemctl list-unit-files --type=service

# 查看启动耗时
systemd-analyze
systemd-analyze blame                  # 各服务耗时
```

**日志管理（journald）：**

```bash
# 查看日志
sudo journalctl                        # 所有日志
sudo journalctl -u nginx               # 特定服务
sudo journalctl -f                     # 实时跟踪
sudo journalctl --since "1 hour ago"
sudo journalctl --since "2024-01-01" --until "2024-01-31"
sudo journalctl -p err                 # 错误级别日志

# 日志维护
sudo journalctl --vacuum-time=7d       # 保留 7 天
sudo journalctl --vacuum-size=500M     # 限制大小
```

### 8.2 进程管理

**查看进程：**

```bash
ps aux                                 # 详细进程列表
ps aux | grep nginx
ps -ef --forest                        # 显示进程树
pstree                                 # 树形显示

# 动态监控
top
htop                                   # 需安装，更友好

# 进程信息
pidof nginx                            # 获取进程 ID
pgrep nginx                            # 类似功能
```

**进程控制：**

```bash
# 终止进程
kill PID
kill -9 PID                            # 强制终止
killall process_name                   # 按名称终止
pkill process_name

# 调整优先级（nice 值 -20 到 19，越小优先级越高）
nice -n 10 command                     # 以低优先级启动
sudo renice -n -5 -p PID               # 调整运行中进程

# 后台与前台
command &                              # 后台运行
Ctrl+Z                                 # 暂停当前进程
bg                                     # 后台运行暂停的进程
fg                                     # 前台运行
jobs                                   # 查看后台任务
```

### 8.3 定时任务（cron）

**系统级定时任务：**

```bash
# 编辑 crontab
sudo crontab -e

# 格式：分 时 日 月 周 命令
# 每天凌晨 3 点备份
0 3 * * * /usr/local/bin/backup.sh

# 每 5 分钟检查服务
*/5 * * * * /usr/local/bin/check_service.sh

# 每周日清理日志
0 0 * * 0 /usr/local/bin/cleanup.sh >> /var/log/cleanup.log 2>&1
```

**特殊时间字符串：**
- `@reboot`：开机时
- `@yearly`：每年 1 月 1 日 0:00
- `@monthly`：每月 1 日 0:00
- `@weekly`：每周日 0:00
- `@daily`：每天 0:00
- `@hourly`：每小时 0 分

**用户级定时任务：**

```bash
crontab -e                             # 编辑当前用户任务
crontab -l                             # 列出任务
crontab -r                             # 删除所有任务
```

**anacron（适合非 24 小时运行的机器）：**

```bash
sudo apt install anacron
# 配置文件 /etc/anacrontab
# 格式：周期 延迟 标识 命令
7       5       cron.weekly      run-parts /etc/cron.weekly
```

---

## 九、Shell 脚本编程

### 9.1 Bash 基础语法

**脚本结构：**

```bash
#!/bin/bash
# 脚本名称: backup.sh
# 描述: 系统备份脚本
# 作者: Your Name
# 日期: 2024-01-01

set -euo pipefail                      # 严格模式
# -e: 命令失败立即退出
# -u: 使用未定义变量报错
# -o pipefail: 管道中任一命令失败则整体失败

# 变量定义
BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d)
LOG_FILE="/var/log/backup_${DATE}.log"

# 函数定义
log_message() {
    local level="$1"
    local message="$2"
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] [$level] $message" | tee -a "$LOG_FILE"
}

# 主逻辑
main() {
    log_message "INFO" "开始备份..."
    
    if [[ ! -d "$BACKUP_DIR" ]]; then
        mkdir -p "$BACKUP_DIR"
        log_message "INFO" "创建备份目录: $BACKUP_DIR"
    fi
    
    # 备份操作
    tar -czf "${BACKUP_DIR}/backup_${DATE}.tar.gz" /etc /home 2>/dev/null
    
    if [[ $? -eq 0 ]]; then
        log_message "SUCCESS" "备份完成"
    else
        log_message "ERROR" "备份失败"
        exit 1
    fi
}

# 执行主函数
main "$@"
```

**条件判断：**

```bash
# 文件测试
[[ -e file.txt ]]                      # 存在
[[ -f file.txt ]]                      # 是普通文件
[[ -d directory ]]                     # 是目录
[[ -r file.txt ]]                      # 可读
[[ -w file.txt ]]                      # 可写
[[ -x script.sh ]]                     # 可执行
[[ -s file.txt ]]                      # 非空
[[ file1 -nt file2 ]]                  # file1 比 file2 新

# 字符串比较
[[ "$str1" == "$str2" ]]
[[ "$str1" != "$str2" ]]
[[ -z "$str" ]]                        # 空字符串
[[ -n "$str" ]]                        # 非空字符串
[[ "$str" =~ regex ]]                  # 正则匹配

# 数值比较（使用 (( )) 或 -eq 系列）
[[ $num1 -eq $num2 ]]                  # 等于
[[ $num1 -ne $num2 ]]                  # 不等于
[[ $num1 -gt $num2 ]]                  # 大于
[[ $num1 -lt $num2 ]]                  # 小于
[[ $num1 -ge $num2 ]]                  # 大于等于
[[ $num1 -le $num2 ]]                  # 小于等于

# 逻辑运算
[[ condition1 && condition2 ]]
[[ condition1 || condition2 ]]
[[ ! condition ]]
```

**流程控制：**

```bash
# if 语句
if [[ condition ]]; then
    # ...
elif [[ condition ]]; then
    # ...
else
    # ...
fi

# case 语句
case "$VAR" in
    start)
        echo "Starting..."
        ;;
    stop)
        echo "Stopping..."
        ;;
    restart)
        echo "Restarting..."
        ;;
    *)
        echo "Usage: $0 {start|stop|restart}"
        exit 1
        ;;
esac

# for 循环
for i in {1..10}; do
    echo $i
done

for file in /var/log/*.log; do
    echo "Processing: $file"
done

# while 循环
counter=0
while [[ $counter -lt 10 ]]; do
    echo $counter
    ((counter++))
done

# until 循环
until [[ -f /tmp/signal ]]; do
    sleep 1
done

# 读取文件
while IFS= read -r line; do
    echo "$line"
done < file.txt
```

### 9.2 高级脚本技巧

**参数处理：**

```bash
#!/bin/bash

# 获取参数
echo "脚本名: $0"
echo "第一个参数: $1"
echo "参数个数: $#"
echo "所有参数: $@"
echo "所有参数（单字符串）: $*"

# getopts 处理选项
while getopts ":u:p:h" opt; do
    case $opt in
        u) USERNAME="$OPTARG" ;;
        p) PASSWORD="$OPTARG" ;;
        h) show_help; exit 0 ;;
        \?) echo "无效选项: -$OPTARG" >&2; exit 1 ;;
        :) echo "选项 -$OPTARG 需要参数" >&2; exit 1 ;;
    esac
done
shift $((OPTIND-1))

# 使用：./script.sh -u admin -p secret arg1 arg2
```

**数组操作：**

```bash
# 定义数组
fruits=("apple" "banana" "cherry")

# 访问元素
echo "${fruits[0]}"                    # 第一个元素
echo "${fruits[-1]}"                   # 最后一个元素
echo "${fruits[@]}"                    # 所有元素
echo "${#fruits[@]}"                   # 数组长度

# 遍历数组
for fruit in "${fruits[@]}"; do
    echo "$fruit"
done

# 关联数组（Bash 4.0+）
declare -A user
user[name]="John"
user[age]=30
echo "${user[name]}"
for key in "${!user[@]}"; do
    echo "$key: ${user[$key]}"
done
```

**错误处理与调试：**

```bash
#!/bin/bash

# 错误处理函数
error_handler() {
    local line_no=$1
    local error_code=$2
    echo "错误发生在第 $line_no 行，退出码: $error_code" >&2
    # 清理操作
    cleanup
    exit $error_code
}

trap 'error_handler ${LINENO} $?' ERR

# 调试模式
set -x                                 # 开启调试（显示执行的每条命令）
set +x                                 # 关闭调试

# 或在脚本头部指定
#!/bin/bash -x

# 更精细的调试
[[ "${DEBUG:-0}" == "1" ]] && set -x
# 运行：DEBUG=1 ./script.sh
```

---

## 十、系统监控与性能调优

### 10.1 系统监控工具

**综合监控：**

```bash
# htop（交互式进程查看器）
sudo apt install htop
htop

# glances（全能系统监控）
sudo apt install glances
glances

# vmstat（系统整体状态）
vmstat 1 10                          # 每秒采样，共 10 次

# iostat（IO 统计）
sudo apt install sysstat
iostat -x 1                          # 扩展统计，每秒刷新

# sar（系统活动报告）
sar -u 1 5                           # CPU 使用率
sar -r 1 5                           # 内存使用率
sar -d 1 5                           # 块设备活动
```

**网络监控：**

```bash
# iftop（实时带宽监控）
sudo apt install iftop
sudo iftop -i eth0

# nethogs（按进程查看带宽）
sudo apt install nethogs
sudo nethogs eth0

# netstat/ss
ss -s                                # 套接字统计
ss -tuln                             # 监听端口
ss -tp                               # 显示进程

# tcpdump（抓包分析）
sudo tcpdump -i eth0 -w capture.pcap
sudo tcpdump -i eth0 port 80
```

### 10.2 性能分析

**CPU 分析：**

```bash
# 查看 CPU 信息
lscpu
cat /proc/cpuinfo

# 负载分析
uptime
cat /proc/loadavg

# perf 性能分析
sudo apt install linux-tools-common
sudo perf top                        # 实时热点函数
sudo perf record -a -g -- sleep 10   # 记录 10 秒
sudo perf report                     # 查看报告
```

**内存分析：**

```bash
# 内存使用情况
free -h
cat /proc/meminfo

# 查看内存占用最高的进程
ps aux --sort=-%mem | head -10

# slab 分配器信息
cat /proc/slabinfo
slabtop

# 查找内存泄漏（valgrind）
sudo apt install valgrind
valgrind --leak-check=full ./program
```

**磁盘 IO 分析：**

```bash
# IO 延迟
ioping -c 10 /path

# 实时 IO 监控
iotop                              # 需 root 权限

# blktrace（块层跟踪）
sudo apt install blktrace
sudo blktrace -d /dev/sda -o - | blkparse -i -
```

### 10.3 内核参数调优

**sysctl 配置：**

```bash
# 查看当前参数
sysctl -a | grep net.core
sysctl vm.swappiness

# 临时修改
sudo sysctl vm.swappiness=10

# 永久修改（/etc/sysctl.conf 或 /etc/sysctl.d/99-custom.conf）
sudo tee /etc/sysctl.d/99-performance.conf << 'EOF'
# 网络优化
net.core.somaxconn = 65535
net.core.netdev_max_backlog = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.tcp_fin_timeout = 10
net.ipv4.tcp_keepalive_time = 1200
net.ipv4.tcp_tw_reuse = 1

# 内存优化
vm.swappiness = 10
vm.dirty_ratio = 15
vm.dirty_background_ratio = 5

# 文件描述符
fs.file-max = 2097152
fs.nr_open = 2097152
EOF

sudo sysctl --system                 # 重新加载
```

**资源限制（ulimit）：**

```bash
# 查看限制
ulimit -a

# 临时修改（当前会话）
ulimit -n 65535                      # 文件描述符
ulimit -u 4096                       # 最大进程数

# 永久修改（/etc/security/limits.conf）
*    soft    nofile    65535
*    hard    nofile    65535
*    soft    nproc     4096
*    hard    nproc     4096
root soft    nofile    65535
root hard    nofile    65535
```

---

## 十一、安全加固与防火墙

### 11.1 基础安全加固

**账户安全：**

```bash
# 删除无用账户
sudo deluser games
sudo deluser irc

# 锁定 root 密码（强制使用 sudo）
sudo passwd -l root

# 设置密码策略（/etc/security/pwquality.conf）
minlen = 12
minclass = 3
maxrepeat = 2

# 密码过期策略（/etc/login.defs）
PASS_MAX_DAYS   90
PASS_MIN_DAYS   7
PASS_WARN_AGE   14
```

**文件权限检查：**

```bash
# 查找 SUID/SGID 文件
find / -perm -4000 -type f 2>/dev/null

# 查找无属主文件
find / -nouser -o -nogroup 2>/dev/null

# 查找全局可写文件
find / -type f -perm -002 2>/dev/null

# 修复权限
sudo chmod u-s /path/to/suspicious/file
sudo chown root:root /path/to/file
```

### 11.2 Fail2ban 入侵防御

```bash
sudo apt install fail2ban

# 配置（/etc/fail2ban/jail.local）
sudo tee /etc/fail2ban/jail.local << 'EOF'
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 3
backend = systemd

[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 86400

[nginx-http-auth]
enabled = true
filter = nginx-http-auth
port = http,https
logpath = /var/log/nginx/error.log
EOF

sudo systemctl restart fail2ban
sudo fail2ban-client status sshd
```

### 11.3 审计与日志

**auditd 系统审计：**

```bash
sudo apt install auditd

# 监控文件变更
sudo auditctl -w /etc/passwd -p wa -k identity_changes
sudo auditctl -w /etc/nginx/nginx.conf -p wa -k nginx_config

# 查看审计日志
sudo ausearch -k identity_changes
sudo aureport --login --summary -i

# 永久规则（/etc/audit/rules.d/audit.rules）
-w /etc/passwd -p wa -k identity_changes
-w /etc/group -p wa -k identity_changes
-a always,exit -F arch=b64 -S setuid -S setgid -k privilege_escalation
```

**日志集中管理（rsyslog）：**

```bash
# 客户端配置（发送日志到中央服务器）
sudo tee -a /etc/rsyslog.d/99-forward.conf << 'EOF'
*.* @log-server.example.com:514
EOF

sudo systemctl restart rsyslog

# 服务器配置（接收日志）
sudo tee -a /etc/rsyslog.d/10-server.conf << 'EOF'
$ModLoad imudp
$UDPServerRun 514
$ModLoad imtcp
$InputTCPServerRun 514

$template RemoteLogs,"/var/log/remote/%fromhost-ip%/%programname%.log"
*.* ?RemoteLogs
& stop
EOF
```

---

## 十二、故障排查与恢复

### 12.1 启动故障修复

**GRUB 修复：**

```bash
# 使用 Live USB 启动，选择 "Try Ubuntu"
sudo add-apt-repository ppa:yannubuntu/boot-repair
sudo apt update
sudo apt install boot-repair
sudo boot-repair

# 或手动修复
sudo mount /dev/sda2 /mnt              # 根分区
sudo mount /dev/sda1 /mnt/boot/efi     # EFI 分区（如果是 UEFI）
sudo mount --bind /dev /mnt/dev
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys /mnt/sys
sudo chroot /mnt
grub-install /dev/sda
update-grub
exit
sudo reboot
```

**单用户模式（恢复模式）：**

1. 启动时按住 Shift 或 Esc 进入 GRUB 菜单
2. 选择 "Advanced options for Ubuntu"
3. 选择 "Recovery mode"
4. 选择 root 进入命令行

**文件系统修复：**

```bash
# 卸载分区（或从 Live USB 启动）
sudo umount /dev/sda1

# 检查并修复
sudo fsck -y /dev/sda1                 # 自动修复
sudo fsck -n /dev/sda1                 # 只检查不修复

# 针对 ext 系列
sudo e2fsck -f /dev/sda1
sudo e2fsck -p /dev/sda1               # 自动修复

# 针对 XFS
sudo xfs_repair /dev/sda1              # 必须卸载
sudo xfs_repair -L /dev/sda1           # 强制修复（可能丢失数据）
```

### 12.2 网络故障排查

**诊断流程：**

```bash
# 1. 检查物理连接
ethtool eth0
dmesg | grep eth

# 2. 检查 IP 配置
ip addr show
cat /etc/netplan/*.yaml

# 3. 检查路由
ip route show
ping -c 4 8.8.8.8                      # 测试外网连通性
traceroute 8.8.8.8

# 4. 检查 DNS
cat /etc/resolv.conf
nslookup google.com
dig google.com

# 5. 检查端口
ss -tuln
telnet host port
nc -zv host port

# 6. 抓包分析
sudo tcpdump -i any -w debug.pcap
# 使用 Wireshark 分析
```

### 12.3 数据恢复

**误删除文件恢复：**

```bash
# 立即停止写入，卸载分区
sudo umount /dev/sda1

# 安装工具
sudo apt install testdisk photorec

# TestDisk（分区表恢复）
sudo testdisk

# PhotoRec（文件恢复，按签名）
sudo photorec /dev/sda1

# ext4 专用（extundelete，需未覆盖）
sudo apt install extundelete
sudo extundelete /dev/sda1 --restore-file /path/to/deleted/file
sudo extundelete /dev/sda1 --restore-all
```

**备份策略：**

```bash
# rsync 增量备份
rsync -avz --delete --exclude='*.tmp' /source/ /backup/

# 使用 borgbackup（重复数据删除）
sudo apt install borgbackup
borg init --encryption=repokey /path/to/repo
borg create /path/to/repo::backup-{now} /home /etc
borg list /path/to/repo
borg extract /path/to/repo::backup-2024-01-01

# 自动化备份脚本（结合 cron）
#!/bin/bash
# /usr/local/bin/backup.sh
DATE=$(date +%Y%m%d_%H%M%S)
borg create --stats --progress \
    /backup/borg::auto-${DATE} \
    /home \
    /etc \
    /var/www \
    --exclude '/home/*/.cache'
borg prune --keep-daily=7 --keep-weekly=4 --keep-monthly=6 /backup/borg
```

---

## 附录：常用速查表

### A. 快捷键

| 快捷键 | 功能 |
|--------|------|
| `Ctrl + C` | 终止当前进程 |
| `Ctrl + Z` | 暂停当前进程 |
| `Ctrl + D` | 退出当前 shell（EOF）|
| `Ctrl + L` | 清屏（类似 clear）|
| `Ctrl + A` | 光标移到行首 |
| `Ctrl + E` | 光标移到行尾 |
| `Ctrl + R` | 反向搜索历史命令 |
| `Ctrl + U` | 删除光标前所有字符 |
| `Ctrl + K` | 删除光标后所有字符 |
| `Ctrl + W` | 删除前一个单词 |
| `Tab` | 自动补全 |
| `!!` | 执行上一条命令 |
| `!$` | 上一条命令的最后一个参数 |
| `!n` | 执行历史记录第 n 条命令 |
| `!string` | 执行最近以 string 开头的命令 |

### B. 重要文件路径

| 路径 | 说明 |
|------|------|
| `/etc/passwd` | 用户账户信息 |
| `/etc/shadow` | 用户密码（加密）|
| `/etc/group` | 组信息 |
| `/etc/hosts` | 静态主机名解析 |
| `/etc/resolv.conf` | DNS 配置 |
| `/etc/ssh/sshd_config` | SSH 服务配置 |
| `/etc/crontab` | 系统级定时任务 |
| `/var/log/syslog` | 系统日志（Ubuntu）|
| `/var/log/auth.log` | 认证日志 |
| `/var/log/nginx/` | Nginx 日志 |
| `/proc/cpuinfo` | CPU 信息 |
| `/proc/meminfo` | 内存信息 |
| `/proc/loadavg` | 系统负载 |

### C. 推荐学习资源

1. **官方文档**
   - [Ubuntu 官方文档](https://help.ubuntu.com/)
   - [Ubuntu Server Guide](https://ubuntu.com/server/docs)

2. **在线资源**
   - [Linux Foundation](https://www.linuxfoundation.org/)
   - [Arch Wiki](https://wiki.archlinux.org/)（技术细节极佳）

3. **认证考试**
   - Linux Foundation Certified System Administrator (LFCS)
   - CompTIA Linux+
   - Red Hat Certified System Administrator (RHCSA)

---

## 结语

掌握 Ubuntu 需要理论与实践相结合。建议：

1. **搭建实验环境**：使用虚拟机（VirtualBox/VMware）或云服务器（AWS/Azure/阿里云）进行练习
2. **从桌面版开始**：熟悉图形界面后逐步转向命令行和服务器版
3. **阅读 man 手册**：`man command` 是最权威的帮助文档
4. **参与社区**：Ubuntu 中文论坛、Stack Overflow、GitHub 开源项目
5. **建立知识库**：使用笔记软件记录遇到的问题和解决方案

Linux 系统的学习是一个持续的过程，保持好奇心和动手能力，你将逐步从入门走向精通。

---

*本文最后更新：2024 年*
*许可协议：CC BY-SA 4.0*
```

这篇指南涵盖了 Ubuntu 系统的完整知识体系，从基础安装到高级运维，适合作为博客的技术参考文档。内容结构清晰，包含大量实用的命令示例和配置说明，可以直接复制到你的博客系统中使用。
