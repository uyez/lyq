# 小米Xiaomi路由器 BE6500 Pro 解锁SSH和科学上网教程
本教程一共分为两个视频，第一个是解锁SSH，第二个是安装ShellCrash科学上网。在小米原来的固件上安装科学上网软件，不影响路由器原有的功能，并且支持全屋科学上网。

## 小米路由器 BE6500 Pro 解锁SSH教程
视频教程：▶ https://youtu.be/OqTVuJC-TIo

### 第一步：下载固件和SSH工具
- 小米路由器 BE6500 Pro 固件 1.0.46 版本，<a href="https://github.com/uyez/lyq/releases/download/rom/miwifi_rd08_firmware_076b5_1.0.46.bin" target="_blank">点击下载>></a>
- 工具下载：<a href="https://github.com/uyez/lyq/releases/download/rom/putty.zip" target="_blank">Putty下载>></a> ，MAC使用Termius，<a href="https://termius.com/download" target="_blank">点击下载>></a>
- 固件版本需要是1.0.46，如果不是这个版本，请使用 <a href="https://bigota.miwifi.com/xiaoqiang/tools/MIWIFIRepairTool.x86.zip" target="_blank">小米路由器修复工具</a> 完成固件降级，<a href="https://youtu.be/noBqKNq2MTk" target="_blank">降级教程</a>。

### 第二步：解锁SSH
Windows用户可使用 <code>命令提示符</code> 、MacOS用户可使用 <code>终端</code> ，输入下列代码开启小米路由器 BE6500Pro 的SSH功能。<br>
请将 <STOK> 替换成 stok 码，注意：每次登录路由器后台 stok 码会改变。

    curl -X POST http://192.168.31.1/cgi-bin/luci/;stok=<STOK>/api/misystem/arn_switch -d "open=1&model=1&level=%0Anvram%20set%20ssh_en%3D1%0A"
    
    curl -X POST http://192.168.31.1/cgi-bin/luci/;stok=<STOK>/api/misystem/arn_switch -d "open=1&model=1&level=%0Anvram%20commit%0A"
    
    curl -X POST http://192.168.31.1/cgi-bin/luci/;stok=<STOK>/api/misystem/arn_switch -d "open=1&model=1&level=%0Ased%20-i%20's%2Fchannel%3D.*%2Fchannel%3D%22debug%22%2Fg'%20%2Fetc%2Finit.d%2Fdropbear%0A"
    
    curl -X POST http://192.168.31.1/cgi-bin/luci/;stok=<STOK>/api/misystem/arn_switch -d "open=1&model=1&level=%0A%2Fetc%2Finit.d%2Fdropbear%20start%0A"

### 第三步：登录路由器
- 通过SSH登录，用户名是：root，密码通过网站计算，<a href="https://miwifi.dev/ssh" target="_blank">密码计算网站>></a>
- 更改SSH登录密码，执行以下指令：（修改之后的用户名是：root，密码是：admin）

      nvram set ssh_en=1
      nvram set telnet_en=1
      nvram set uart_en=1
      nvram set boot_wait=on
      nvram commit
      sed -i 's/channel=.*/channel="debug"/g' /etc/init.d/dropbear
      /etc/init.d/dropbear restart
      echo -e 'admin\nadmin' | passwd root

- 重启后自动开启SSH

      mkdir /data/auto_ssh && cd /data/auto_ssh
      curl -O https://fastly.jsdelivr.net/gh/lemoeo/AX6S@main/auto_ssh.sh
      chmod +x auto_ssh.sh
      ./auto_ssh.sh install
  

### 第四步：固化SSH
- 用SSH工具登录路由器后分别执行以下指令，每次执行指令后路由器会重启。

      zz=$(dd if=/dev/zero bs=1 count=2 2>/dev/null) ; printf '\xA5\x5A%c%c' $zz $zz | mtd write - crash
      reboot

- 执行指令后路由器会重启

      nvram set ssh_en=1
      nvram set telnet_en=1
      nvram set uart_en=1
      nvram set boot_wait=on
      nvram commit
      bdata set ssh_en=1
      bdata set telnet_en=1
      bdata set uart_en=1
      bdata set boot_wait=on
      bdata commit
      reboot

- 执行指令后路由器会重启，重启后固化完成。

      mtd erase crash
      reboot

### 第五步：升级固件
- 完成固化SSH后就可以升级固件了
- 固件升级完之后，如果SSH无法登录，请使用SSH工具进行 Telnet 登录，用户名为：root，密码为：admin，如果路由器进行了恢复出厂设置，密码为通过网站计算的密码。
- 通过Telnet登录，开启SSH，并修改root密码为admin：

      sed -i '/flg_ssh=`nvram get ssh_en`/{:loop; N; /\n.*channel=`\/sbin\/uci get \/usr\/share\/xiaoqiang\/xiaoqiang_version.version.CHANNEL`\n.*return 0\n.*fi/!b loop; d}' /etc/init.d/dropbear
      /etc/init.d/dropbear restart
      echo -e 'admin\nadmin' | passwd root

- 重启后自动开启SSH

      mkdir /data/auto_ssh && cd /data/auto_ssh
      curl -O https://fastly.jsdelivr.net/gh/lemoeo/AX6S@main/auto_ssh.sh
      chmod +x auto_ssh.sh
    
      uci set firewall.auto_ssh=include
      uci set firewall.auto_ssh.type='script'
      uci set firewall.auto_ssh.path='/data/auto_ssh/auto_ssh.sh'
      uci set firewall.auto_ssh.enabled='1'
      uci commit firewall


## 小米路由器 BE6500 Pro 安装 ShellCrash 科学上网教程
视频教程：▶ https://youtu.be/ES12KA1FN9A

**Clash安装源：**

    export url='https://fastly.jsdelivr.net/gh/juewuy/ShellCrash@master' && sh -c "$(curl -kfsSl $url/install.sh)" && source /etc/profile &> /dev/null

**备用安装源：**

    export url='https://gh.jwsc.eu.org/master' && sh -c "$(curl -kfsSl $url/install.sh)" && source /etc/profile &> /dev/null

**Clash管理地址：** http://192.168.31.1:9999/ui/ (如果打不开请按Ctrl+F5 刷新)
