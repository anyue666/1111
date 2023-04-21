使用Android运行Klipper，Moonraker，Mainsail / Fuidd和KlipperScreen
替代版本：https://github.com/gaifeng8864/klipper-on-android（使用流体并简化脚本创建）
免责声明：这是一项正在进行的工作，仍有一些更改待定。会尽可能尝试更新它。
@RyanEwen的原创作品（https://gist.github.com/RyanEwen/ae81fc48ad00397f1026915f0e6beed9)
我拥有的当前设置：带有Klipper固件的Artillery Genius Pro+联想Tab M8运行Klipper+Moonraker+Mainsail+klipperscreen。
要求
安装了以下内容的 Android 设备：

Linux Deploy app： https://play.google.com/store/apps/details?id=ru.meefik.linuxdeploy
XServer app： https://play.google.com/store/apps/details?id=x.org.server
Octo4a 应用程序：https://github.com/feelfreelinux/octo4a
为同一设备启动并运行的OTG + 充电电缆（请查看此视频以供参考：https://www.youtube.com/watch?v=8afFKyIbky0)

使用Klipper固件的已刷新的打印机。

供参考 ： https://3dprintbeginner.com/how-to-install-klipper-on-sidewinder-x2/
Klipper 和 Moonraker 的初始化脚本（脚本文件夹）。

用于 KlipperScreen 的 XTerm 脚本（脚本文件夹）。

设置说明
使用以下设置在 Linux 部署中创建容器：

引导程序：
发行版：（破坏者）Debian
安装类型： 注意：您可以选择“文件”，但请确保它足够大，因为以后无法调整其大小，并且 2 GB 不够。
Directory
安装路径： 注意：您可以选择其他位置，但如果它在 ${EXTERNALDATA} 内，则 SSH 可能无法启动。
/data/local/debian
用户名： 注意：如果您确保相应地更新本教程中的脚本、配置和路径，则可以选择其他内容。
android
初始化：
启用：yes
初始化系统：sysv
SSH：
启用：yes
图形用户界面：
启用：yes
图形子系统：X11
桌面环境：XTerm
通过 SSH 连接到容器。

安装 Git 和 KIAUH：

sudo apt install git
git clone https://github.com/th33xitus/kiauh.git
安装 Klipper、Moonraker、Mainsail（或 Fluidd）和 KlipperScreen：

kiauh/kiauh.sh
注意：特别是KlipperScreen需要很长时间（数十分钟）。

查找打印机的串行设备以在 Klipper 中使用：
它可能是 或 。检查插入/拔出打印机时是否出现/消失其中任何一个。printer.cfg/dev/ttyACM0/dev/ttyUSB0/dev/

如果在 中找不到您的打印机，则可以检查 Octo4a 应用程序，其中包含 CH34x 驱动程序的自定义实现。重要提示：您不需要在其中运行OctoPrint，因此一旦进入应用程序的主屏幕，只需在它正在运行时停止它。为此：/dev/

从 https://github.com/feelfreelinux/octo4a/releases 安装 Octo4a
运行Octo4a并让它安装OctoPrint（可选地在完成安装后点击停止按钮）。
确保Octo4a看到您的打印机（它将在旁边列出一个复选框）。
如果检测到，您的安卓设备中将出现一个提示，询问是否允许连接到您的打印机。
现在，您需要返回到 Linux 部署并编辑容器设置：
坐骑：
启用：yes
安装点：按“+”按钮
源：/data/data/com.octo4a/files
目标：/home/android/octo4a
/home/android/octo4a/serialpipe是您需要在printer.cfg
使串行设备可供 Klipper 访问：

sudo chmod 777 /dev/ttyACM0
# or 
sudo chmod 777 /dev/ttyUSB0
# or 
sudo chmod 777 /home/android/octo4a/serialpipe
从以下要点安装 init 和 xterm 脚本：

sudo wget -O /etc/default/klipper https://raw.githubusercontent.com/d4rk50ul1/klipper-on-android/main/scripts/etc_default_klipper
sudo wget -O /etc/init.d/klipper https://raw.githubusercontent.com/d4rk50ul1/klipper-on-android/main/scripts/etc_init.d_klipper
sudo wget -O /etc/default/moonraker https://raw.githubusercontent.com/d4rk50ul1/klipper-on-android/main/scripts/etc_default_moonraker
sudo wget -O /etc/init.d/moonraker https://raw.githubusercontent.com/d4rk50ul1/klipper-on-android/main/scripts/etc_init.d_moonraker
sudo wget -O /usr/local/bin/xterm https://raw.githubusercontent.com/d4rk50ul1/klipper-on-android/main/scripts/usr_local_bin_xterm

sudo chmod +x /etc/init.d/klipper 
sudo chmod +x /etc/init.d/moonraker 
sudo chmod +x /usr/local/bin/xterm

sudo update-rc.d klipper defaults
sudo update-rc.d moonraker defaults
停止 Debian 容器。

启动 XServer XSDL。

一次性设置：
点击“更改设备配置”
将鼠标仿真模式更改为桌面，无仿真
启动 Debian 容器。

KlipperScreen应该出现在XServer XSDL中，Mainsail和/或Fluidd应该可以在浏览器中使用Android设备的IP地址访问。

杂项
您可以使用以下命令手动启动/停止Klipper和Moonraker（例如：）。
日志可在 中找到。servicesudo service start klipper/home/android/klipper_logs

电报机器人
您可以在此处找到如何设置电报机器人的说明

故障排除（基于注释的持续部分）
可能存在这样一种情况，即通过浏览器访问 Mainsail 时，您会收到一条错误消息，并且没有连接到 moonraker：mainsail 在连接到上游时权限被拒绝。要解决此问题，您必须更改文件 ，更改为klipper_logs/mainsail_error.log/etc/nginx/nginx.confuser www-data;user android;

如果几分钟后有人以非 root 用户身份在容器中遇到网络问题，则需要禁用深度睡眠/空闲。您可以通过在 shell 中使用此命令来做到这一点（termux 或 adb 无关紧要）：.您可能还需要此应用程序： [唤醒锁定 - CPU 唤醒] （https://play.google.com/store/apps/details?id=com.dambara.wakelockerdumpsys deviceidle disable)

根据 ZerGo0 评论 - 最新的 moonraker 更新似乎破坏了一些东西。目录和文件位置有一些更改，但您可以使用包含的脚本将新目录符号链接到旧目录：

sudo /etc/init.d/moonraker stop
cd ~/moonraker
scripts/data-path-fix.sh
sudo /etc/init.d/moonraker start
显然，您也可以手动执行此操作。 您还必须将以下部分添加到您的 ：moonraker.conf

[machine]
validate_service: False
validate_config: False
provider: none
现在还有一些不推荐使用的设置，请查看通知或 moonraker 文档以了解您需要删除的内容。

检查OTG + Charge电缆时，每部手机都会“识别”不同的电阻器，我的建议是，一旦构建电缆，尽量不要将电阻器直接焊接到5针插头上。相反，使用临时试验板并使用不同的电阻进行测试。我的方法如下：

无需将任何东西连接到微型USB端口（未使用Type-c进行测试） 在手机中打开Octo4a应用程序
将打印机连接到USB修改后的电缆（假设您已经构建了一根;)）
将充电器连接到微型 USB 公头端口
将修改后的电缆连接到手机。
如果手机检测到充电并且Octo4a显示一个弹出窗口，请求访问串行设备，那么您就完成了！电阻器工作正常。
如果手机仅检测到充电，而 Octo4a 未显示请求串行访问的弹出窗口，则必须从试验板上卸下电阻器并尝试使用其他电阻器。
重复此过程，直到弄清楚为止。
我买了一个电阻器套件，其值从 1k 欧姆到 1M（大约 20 种可能性）。我逐一更改它们，直到它起作用。
以防有人拥有相同的设备，作为参考。我使用了联想标签M8（TB 8505F），它与10k欧姆电阻器一起工作！
