which

# 刚安装后

## 换源

```shell
sudo gedit /etc/apt/sources.list
```

- 内核

    - focal：ubuntu20
    - bionic：ubuntu18
    - buster：debian

- 添加内容

    复制下面内容，`focal`修改为自己对应的内核

    ```
    deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
    deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
    deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
    deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
    deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
    ```

## 一些问题

### 文件夹名改英文

```shell
export LANG=en_US
xdg-user-dirs-gtk-update
```

### 关机时间过长

```shell
sudo vim /etc/systemd/system.conf
# 找到这两条语句，取消注释，把时间改小（原先默认时90s）
DefaultTimeoutStartSec=1s
DefaultTimeoutStopSec=1s
# 执行
systemctl daemon-reload #使其生效
```

### 双系统时间

```shell
# 更新一下时间
sudo apt-get install ntpdate
sudo ntpdate time.windows.com
# 把时间更新到硬件上
sudo hwclock --localtime --systohc
```

## 安装输入法

[参考网址](https://zhuanlan.zhihu.com/p/142206571)

-   换`fcitx`输入法框架

    ```shell
    sudo apt-get remove ibus #卸载ibus
    sudo apt-get install fcitx fcitx-config-gtk
    # 然后注销重启
    ```

-   安装搜狗输入法

    ```shell
    curl -sL 'https://keyserver.ubuntu.com/pks/lookup?&op=get&search=0x73BC8FBCF5DE40C6ADFCFFFA9C949F2093F565FF' | sudo apt-key add
    sudo apt-add-repository 'deb http://archive.ubuntukylin.com/ukui focal main'
    sudo apt update
    sudo apt install sogouimebs
    ```

-   输入法设置

    ```shell
    sogouIme-configtool
    ```

## MS字体

-   法1

    [参考网址](https://blog.csdn.net/qq_25834839/article/details/105456636)

    ```shell
    # 安装 MS TrueType 字体
    sudo apt install ttf-mscorefonts-installer
    
    # 更新字体缓存
    sudo fc-cache -f -v
    ```

-   法2: 复制windows系统下字体文件

    ```shell
    cd /usr/local/share/fonts/
    mkdir windows && cd windows
    
    cp -r <Windowsdrive>/Windows/Fonts windows/
    
    cd ..
    mkfontdir
    mkfontscale
    fc-cache -fv
    ```

## 美化

```shell
sudo apt install gnome-tweaks gnome-tweak-tool chrome-gnome-shell
# dock
sudo apt install gnome-shell-extension-dashtodock
```

-   打开 [https://extensions.gnome.org/](https://extensions.gnome.org/)，安装`dash to dock`, `user themes`

-   主题/壁纸路径：`~/.local/share/themes或backgrands或icons`

    [https://wallhaven.cc/](https://wallhaven.cc/)

## 触摸板fusuma

-   安装

    ```shell
    sudo apt-get install libinput-tools
    sudo apt-get install xdotool
    sudo apt-get install ruby
    sudo gem install fusuma
    ```

-   配置

    ```shell
    vim .config/fusuma/config.yml
    ```

    写入以下内容，根据需求修改

    ```shell
    swipe:
      3: 
        left: 
          command: 'xdotool key super+Left'
        right: 
          command: 'xdotool key super+Right'
        up:
          command: 'xdotool key super'
        down: 
          command: 'xdotool key ctrl+super+d'
      4:
        left:
          command: 'xdotool key alt+Tab'
        right:
          command: 'xdotool key super+Tab'
        up: 
          command: 'xdotool key super+Up'
        down: 
          command: 'xdotool key super+Down'
    pinch:
      2:
        in:
          command: 'xdotool key ctrl+plus'
          threshold: 0.1
        out:
          command: 'xdotool key ctrl+minus'
          threshold: 0.1
     
    threshold:
      swipe: 0.1
      pinch: 0.1
     
    interval:
      swipe: 0,1
      pinch: 0.1
    ```

## 双系统蓝牙key共用

由于蓝牙连接除了`MAC`地址外，还有一个`Key`来验证设备，所以双系统的`MAC`地址尽管相同，但是由于`Key`不同，所以在同一个蓝牙设备的使用上仍然比较麻烦。

下面的方法是将windows下的`Key`修改为和ubuntu下一致

-   先在windows下配对，生成注册表门之后用

-   换ubuntu配对

-   在ubuntu下查看key

    ```shell
    cd /var/lib/bluetooeh/<电脑蓝牙MAC地址>/<耳机蓝牙MAC地址>
    cat info
    ```

    -   `LinkKey`下面的`key`就是

-   换windows修改注册表，改成相同的key

    -   下载PSTools，[https://technet.microsoft.com/en-us/sysinternals/bb897553](https://technet.microsoft.com/en-us/sysinternals/bb897553)

    -   用管理员身份打开cmd，在PSTools安装路径下：

        ```shell
        PsExec.exe -s -i regedit
        ```

    -   找到`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\BTHPORT\Parameters\Keys\`

    -   把数值数据写成ubuntu下看到的key

# 显卡驱动 + cuda

## 显卡驱动

```shell
# 禁用nouveau驱动
gedit /etc/modprobe.d/blacklist-nouveau.conf
# 添加下面两行:
# blacklist nouveau
# options nouveau modeset = 0
reboot

# 查看是否禁用成功，没有输出则成功
lsmod | grep nouveau

# 查看推荐N卡驱动版本
ubuntu-drivers devices

# 自动选择版本安装
udo ubuntu-drivers autoinstall
# 选择版本安装
sudo apt install <nvidia-driver-435>

# 查看是否安装成功，以及最高支持的CUDA版本
nvidia-smi
```

## cuda

[https://developer.nvidia.cn/cuda-toolkit-archive](https://developer.nvidia.cn/cuda-toolkit-archive)

# 文件系统

- `/`：根目录

- `/home`

  `~`：当前用户home

  `~cr`：cr的home

- `/bin`：二进制文件目录，eg：命令

- `/usr`

  - `/usr/bin`：二进制文件目录

- `/boot`

  - `/boot/vmlinuz`：内核
  - `/boot/grub`：引导

- `/dev`：设备文件

- `/etc`：系统配置文件

- `/lib`

## 打开文件数量限制

-   默认1024
-   查看: `ulimit -a`
-   修改: `ulimit -n 4096`
-   查看系统可以设置的最大值: `cat /proc/sys/fs/file-max`

## 开机挂载

-   查看UUID

    ```shell
    # 先挂载，然后查看UUID
    sudo blkid /dev/sda2
    # 返回: /dev/sda2: LABEL="Data" UUID="88069947069936E2" TYPE="ntfs" PARTLABEL="Basic data partition" PARTUUID="7170f9a7-9c9f-43d8-9916-da47aa910
    # UUID: 88069947069936E2
    ```

-   修改文件

    ```shell
    sudo gedit /etc/fstab
    # 在文档末尾添加
    [UUID=************] [挂载磁盘分区]  [挂载磁盘格式]  0  2
    UUID=88069947069936E2 /mnt/data ntfs defaults   0  2
    # 第一个数字：0表示开机不检查磁盘，1表示开机检查磁盘；
    # 第二个数字：0表示交换分区，1代表启动分区（Linux），2表示普通分区 
    ```
