
# 360T7刷openwrt 
### **准备**  
| 360T7 | |
| ----- | ----- |
| windows电脑 | 本操作均是基于window端 |
| usb转ttl工具| CH340即可，电商网站很多 |
| 杜邦线+网线 | 
| uboot |[108m的uboot](https://github.com/fqw000/360T7/releases/download/openwrt/mt7981_360t7-fip-fixed-parts-uboot.bin) \ [来源](https://cmi.hanwckf.top/p/360t7-firmware/)|
| openwrt固件 | [固件下载](https://github.com/fqw000/360T7/releases/download/openwrt/360t7.chajian-openwrt.zip) \ [来源](https://www.right.com.cn/forum/thread-8263340-1-1.html)| 
| 串口调试助手 | [MicrosoftStore](#)\ [依赖](https://github.com/fqw000/360T7/releases/download/openwrt/chuankoutiaoshiyilai-Microsoft.NET.Native.Runtime.2.2_2.2.28604.0_x86__.appx) 、 [依赖](https://github.com/fqw000/360T7/releases/download/openwrt/chuankoutiaoshiyilai-Microsoft.NET.Native.Runtime.2.2_2.2.28604.0_x86__.appx) 、 [串口调试助手](https://github.com/fqw000/360T7/releases/download/openwrt/chuankoutiaoshizhushou-lingguang.8.0.1.0.msixbundle) | 
| termius | telnet工具都可以 | 
  
 *部分没有microsoftstore的版本比如企业版，使用抓包的.appx和.msixbundle安装串口调试助手*    

### **[拆机]()**  
网上流传很多拆机教程，图文的、视频的都有自行参考吧  
  以下是拆机的关键点：
- 贴纸覆盖下的两颗十字螺丝拆解。想保护贴纸的就用风枪或者吹风机吹下把贴纸私下里。
- 准备一张废弃的塑料卡片（比如：过期的信用卡、会员卡），从底部中央插入，想一侧滑动，碰到卡口向上撬卡，使上盖向外，下盖向里滑动，依次撬开四个角和两侧的卡口，正上方的两个卡口不好撬，其实不用撬，以顶为轴心从底部上掀上盖，正上方两卡口即可脱落
- 绞和方式为底盖卡牙靠外侧向内扣紧上盖卡扣，上盖卡扣靠内测。
- 基本都能无损不断扣拆解

### **failsafe模式下开启telnet**
- 使用usb转ttl工具+杜邦线接线：路由RXD连接ttl工具的TXD、TXD连接RXD、GND连接GND。
- 打开串口调试助手,串口号选择插入 COM 口设备，波特率选择到 115200，然后选择连接。
- 路由器接通电源，调试助手右侧开始日志开始显示，不停的输入 f+回车 发送，直至显示进入failsafe模式。
- 依次执行以下命令  
&darr; 开启uboot控制台菜单  
  ```
  fw_setenv bootmenu_delay 3
  ```
  
  &darr;挂载rootfs并开启telnet
  ```
  mount_root
  sed -i 's/.*local debug=.*/\tlocal debug=1/' /etc/init.d/telnet
  ```
  &darr;修改root密码为password
  ```
  passwd root
  password
  password
  ```
  &darr;重启
  ```
  reboot
  ```  
  
### **刷入uboot**
- 路由重启后，进入并配置路由，确保路由能够联网（如果需要从网络下载uboot文件，你也可以将uboot文件使用HSF、SCP等方式直接上传到路由使用），原厂的uboot只能使用36M，hanwckf的可以使用108M。
- 使用termius对路由器进行telnet连接，默认的后台地址是 192.168.2.1，用户名是 root，密码是 password（这就是上一步修改的密码）
- 将uboot写入 /tmp/ 路径，将360t7-fip-fixed-parts.bin文件中的数据写入到Linux内核中名为fip的MTD设备分区中。当然你也可以使用mtd命令写入刚下载好的uboot。  

```
cd /tmp
wget https://sebs.oss-cn-shanghai.aliyuncs.com/360t7-fip-fixed-parts.bin
mtd write 360t7-fip-fixed-parts.bin fip
```  
###  **刷入openwrt**
 - 进入uboot  
  摁住路由reset按键，通电，持续8秒以上松开即可进入reboot
 - uboot没有DHCP，所有需要使用网线将路由的lan口和电脑连接。  
  电脑端：  
  IP 手动设置为 192.168.1.2    
  子网掩码 255.255.255.0  
  网关设置 192.168.1.1  
  dns 也设置 192.168.1.1
 - 浏览器打开192.168.1.1 进入uboot管理页面，选择下载好的openwrt固件，注意文件格式需要为.bin不能使压缩包
 - 刷入完成后，自动重启进入openwrt  
 默认后台是192.168.6.1，用户名是 root，密码 password，默认的 Wi-Fi 名是 mtk 开头的，默认 Wi-Fi 没有密码   
 
### 最后附上360t7的 [ 原生固件备份 ](https://github.com/fqw000/360T7/releases/download/openwrt/360T7-yuanshengrom-bak.7z) 
 
 
 
