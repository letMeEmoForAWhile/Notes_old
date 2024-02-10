# 零、windos安装ubuntu双系统

## 相关资料：

[Windows 10 安装ubuntu 18.04 双系统（超详细教程）_安装ubuntu双系统-CSDN博客](https://blog.csdn.net/qq_43106321/article/details/105361644)

[【精选】【Ubuntu18.04分区分配方式推荐与详解】-CSDN博客](https://blog.csdn.net/qq_29960631/article/details/123369207)

## 问题：

##### 1、什么是efi系统分区，应该分配多大空间

用于存储引导加载程序和其他与系统引导相关的文件的地方。它是 UEFI（统一扩展固件接口）引导过程的一部分。

大小为200MB至500MB，也可以更大，具体取决于系统和需求。(ubuntu18.04 可用500MB)

##### 2、什么是swap分区，应该分配多大空间

用于操作系统的虚拟内存。当物理内存(RAM)不足时，操作系统可以将不常用的**内存页面**移动到Swap分区中，从而释放物理内存以供其他程序使用。

大小通常为物理内存大小的1到2倍。如果计算机有足够的物理内存，也可以选择不创建或者使用文件代替。这里16G物理内存选择创建16G的swap分区

##### 3、该选择ubuntu18.04还是ubuntu20.04

根据情况选择

ubuntu18.04：

- 更加稳定，对资源要求更低
- 适合**企业环境**(需要稳定性和长期支持的服务器)和**相对旧的硬件**（对硬件要求较低）

ubuntu20.04：

- 更好的桌面体验和更新的软件包
- 适合最新的硬件和希望体验新的GNOME版本的用户

较新的电脑需要最好选择20.04！！！

电脑硬件过新，18.04内核过低，无法识别显示器、声卡、wifi等驱动，安装20.04一劳永逸

## 遇到的问题

### 1、没有无线驱动

- [双系统装完之后，Ubuntu系统连不上WIFI的问题 - 代码先锋网 (codeleading.com)](https://codeleading.com/article/42485056591/)
- [ubuntu18.04安装后没有wifi - CSDN文库](https://wenku.csdn.net/answer/b74e7077e0d19b293da066a3e293277e)

适用于ubuntu20.04和ubuntu22.04

[Ubuntu20.04 无线网卡驱动（未发现wifi适配器）、Nvidia显卡驱动安装一条龙教程【多坑预警】_ubuntuwifi驱动安装-CSDN博客](https://blog.csdn.net/weixin_52490336/article/details/133139105)

- 首先明确网卡是intel还是realtek，在windows中查看设备管理器可知本机网卡为 intel AX211

适用于ubuntu18.04

https://blog.csdn.net/m0_74397934/article/details/134809876

- 更新linux内核
- 下载驱动

### 2、扩展显示屏无法使用

折腾了很久，最终无法实现扩展屏和内置屏同时使用。

### 3、触摸板失效

![image-20231216192315752](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20231216192315752.png)

触摸板无法使用，设置中无触摸板模块。

1、检查是否存在可用的触摸板驱动程序

```bash
sudo apt update
sudo apt install xserver-xorg-input-synaptics
```

出现问题：

```
下列软件包有未满足的依赖关系：
 xserver-xorg-input-synaptics : 依赖: xserver-xorg-core (>= 2:1.18.99.901)
E: 无法修正错误，因为您要求某些软件包保持现状，就是它们破坏了软件包间的依赖关系。
```

先更新xserver-xorg-core

```
sudo apt-get install xserver-xorg-core
```

重新安装驱动

```
sudo apt install xserver-xorg-input-synaptics
```

失败，且出现登陆成功后无法使用鼠标和键盘的问题。

### 4、浏览器无法播放视频

##### 描述：

浏览器无法在相关网页播放视频，如b站等网页

##### 解决方法1（失败）：

安装相应插件

```
sudo apt install flashplugin-installer
sudo apt install browser-plugin-freshplayer-pepperflash
```

重启浏览器

##### 解决方法2：

在显卡配置文件中修改：https://zhuanlan.zhihu.com/p/348624522?utm_id=0



### 5、无法修改亮度

https://www.cnblogs.com/zl-yang/p/13073356.html



https://blog.csdn.net/afgqwjgfjqwgfg/article/details/121084950

## 删除Ubuntu系统

[【Linux卸载】Win10卸载Ubuntu双系统（不安装任何软件）_怎么卸载ubuntu系统_百里飞洋的博客-CSDN博客](https://blog.csdn.net/qq_51513895/article/details/128614127)

# 一、Ubuntu 配置clash

https://zhuanlan.zhihu.com/p/598337110

\# 官方网站[clash](https://link.zhihu.com/?target=https%3A//github.com/Dreamacro/clash/releases)下载文件：**clash-linux-amd64-v1.12.0.gz**（也可以下载最新的版本，前缀是clash-linux-amd64即可）

进入该文件所在目录，在页面空白处右键，在终端打开

1. 解压

   ```
   gunzip clash-linux-amd64-v1.12.0.gz
   ```

2. 将clash-linux-amd64-v1.12.0文件重命名为clash

   ```
   mv clash-linux-amd64-v1.12.0 clash
   ```

3. 在此目录下创建文件夹（注意这里用大写Clash只是为了和clash区别开）

  ```
  mkdir Clash
  ```

4. 移动clash文件夹到Clash文件夹

   ```
   mv clash ./Clash
   ```

5. 进入Clash文件夹

   ```
   cd Clash
   ```

6. 下载clash 配置文件config.yaml （注意：这个订阅链接是自己的，替代 [订阅链接]，如果失败了说明订阅链接有问题）

   ```
   wget -O config.yaml [订阅链接]
   ```

   这里下载的config.yaml是乱码，推测原因为文件中存在中文字符

   由于之前在手机上配置过clash for android，可在手机中的clash导出配置文件`配置文件.yaml`

   将其弄到Liunx的Clash目录中，并改名`config.yaml`

7. 下载Country.mmdb

   ```
   wget -O Country.mmdb https://www.sub-speeder.com/client-download/Country.mmdb
   ```

   注意：如果步骤7失败了也没关系，直接跳过这一步，后面也会自动下载。也可以在网址[Country.mmdb](https://link.zhihu.com/?target=https%3A//github.com/Loyalsoldier/geoip/releases/download/202212010123/Country.mmdb)下载。

8. 启动clash

   ```
   ./clash -d .
   ```

   注意：如果提示权限不足，先执行 `chmod +x clash` ，再执行 `./clash -d .`  出现如下表示成功，并保持此终端打开。

   ![image-20230314170958315](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314170958315.png)

9. 打开系统设置，选择网络，点击网络右边的⚙按钮，选择手动，填写HTTP和HTTPS
   代理为127.0.0.1:7890，填写Socks主机为127.0.0.1:7891，即可启用系统代理。

   ![image-20230314171334255](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230314171334255.png)

10. 访问  http://clash.razord.top/

    这个页面要求提供，Host,Port两个输入：

    - Host: 127.0.0.1
    - 
      Port: 9090 

# 二、调整分区大小

## Question：

直接用swapfile分配swap空间，为什么安装ubuntu时需要分配swap空间



## 动机：

不小心给swap分区分配太多空间，导致可用磁盘空间过小

https://blog.csdn.net/lt9700/article/details/127979193

1. 下载gparted

   ```
   sudo apt-get install gparted
   ```

2. 启动 

   ```
   sudo gparted
   ```


# 三、双系统home空间不足的解决方法

安装双系统时分配空间过少，复现代码搭载环境，出现home空间内存不足的问题。

解决思路：在windows分出空白卷，并在其中创建共享文件夹，挂在到ubuntu的home路径下

参考链接：https://blog.csdn.net/qq_62517226/article/details/128708656

## 1、 windows下的操作

step1：新建空白卷

1. ```
   win+x，然后打开磁盘管理
   ```

2. 类似于安装双系统的操作，选择磁盘0，右键，压缩卷。

   该过程比较漫长，耐心等待。

3. 将新压缩出来空间设置为新的空白卷E

![image-20230403171714683](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230403171714683.png)

step2：在空白卷下新建一个文件夹，设置属性为共享，并添加**Everyone用户**，以便于使子系统都可以共享该文件夹

![image-20230403173317039](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230403173317039.png)

## 2、linux下的操作

1. 查看磁盘信息，找到新加卷E盘对应的分区

   ```
   sudo fdisk -l
   ```

   ![30f7ea457dfaf04c099735ec2039a45a](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/30f7ea457dfaf04c099735ec2039a45a.png)

   根据内存辨认，这里是60G，对应分区为dev/sda2

2. 查看分区UUID

   ```
   sudo blkid
   ```

   ![3d21188b75b77f4ed43cc360f41c7ebc](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/3d21188b75b77f4ed43cc360f41c7ebc.png)

3. 创建挂载点，在home目录下新建文件夹，用于作挂载点

   ![25090ef8f034a6bce6164fd058b3602a](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/25090ef8f034a6bce6164fd058b3602a.png)

4. 修改fstab文件进行永久挂载

   ```
   sudo vim /etc/fstab
   ```

   ![img](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/A7A_ICD@S{A_6KLM]4Z9HJI.jpg)

   在最后一行加上挂载的信息，第一个是UUID，第二个是新建挂载点的地址，系统文件类型为ntfs

5. 进行挂载操作，执行如下代码实现永久挂载

   ```
   sudo mount -a
   df -kh
   ```

## 3、结语

成功实现共享文件夹在挂载磁盘下的情况，既可以实现软件的迁移使用，又能和windows实现文件的传输。

# 四、Ubuntu安装与卸载软件

## 4.1 安装软件

### A 、使用安装包(dep等)

##### 选择对应的版本

在终端输入

```
uname -m
```

- 若输出为`x86_64`，则系统是基于64位的x86架构，应选择x86或者特别指出位64位的版本
- 若输出为`i686`或`i386`，则系统是基于32位的x86架构，应该选择x86或者32位版本。
  - 32位软件版本
    - x86
  - 64位版本
    - x64
    - x86_64
- 若输出为`armv71`或类似，说明使用的是32位的ARM架构
- 若输出为`aarch64`，则表示系统为64位的ARM架构，选择arm64或者aarch64版本

### B、Ubuntu卸载软件

ubuntu卸载和安装 https://blog.csdn.net/Laney_Midory/article/details/120686618

https://blog.csdn.net/qq_42170079/article/details/130770073

## 4.2 卸载源码安装的软件

在你make install的文件下面输入:

```
sudo make uninstall
```

然后删除源码即可

# 五、NVIDIA驱动

查询推荐安装的驱动版本

```bash
ubuntu-drivers devices
```



## 5.1 安装驱动

三种方法

### A、官方下载

### B、ubuntu自带的“附加驱动”中安装

### C、apt安装

## 5.2 卸载

两种方法

### A、进入系统的路径/usr/bin,然后查看与nvidia相关的命令

```bash
ls nvidia-*
```

若显示结果有`nvidia-uninstall`，则可以使用该命令卸载。

### B、直接使用系统自带的命令卸载

```bash
sudo apt-get remove --purge nvidia*
sudo apt autoremove
```

