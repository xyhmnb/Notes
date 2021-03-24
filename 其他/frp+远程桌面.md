> 因为某种原因需要将公司电脑在家里能进行远程，而传统的软件不安全或是被禁用，所以可以使用内网穿透来进行操作

#### 使用场景

被控端：Win10 专业版，支持开启远程桌面

服务端：阿里云ECS，Centos7

访问端：Win10家庭版，不支持开启远程桌面，但是支持访问

使用软件：开源项目frp

#### 操作步骤

**准备服务端**

1. 在GitHub上下载frp包，[release发布地址](https://github.com/fatedier/frp/releases)

2. 将frp_0.34.0_linux_amd64.tar上传到服务器解压

```shell
//解压文件夹
[root@iz0derq2d2e5ubz ~]# tar xf frp_0.34.0_linux_amd64.tar.gz
```

3. 解压完成后进入目录，编辑**frps.ini**进行配置

```ini
[common]
bind_port = 7000 //绑定端口接收数据
token = 123456 //可选，作为被控端进行连接时的校验
```

4. 运行frp

```shell
#使用配置文件运行服务，这里先运行测试，还没有将其放到后台运行
[root@iz0derq2d2e5ubz frp_0.34.0_linux_amd64]# ./frps -c frps.ini
2020/09/24 10:13:45 [I] [service.go:190] frps tcp listen on 0.0.0.0:7000
2020/09/24 10:13:45 [I] [root.go:212] start frps success
```

**准备被控端**

1. 开启远程桌面服务
2. 同样需要下载包，frp_0.34.0_windows_386.zip，解压文件夹
3. 配置**frpc.ini**

```ini
[common]
server_addr = ip  //服务端IP地址，这里指云服务器公网地址
server_port = 7000 //服务端配置的接收端口，7000
token = 123456 //可选，如果服务端配置了这里就需要一致

[RDP] //开启远程桌面，Win自带远程桌面就是RDP协议
type = tcp
local_ip = 127.0.0.1
local_port = 3389 //RDP默认端口
remote_port = 7001 //远程访问转发端口

[ssh] //开启ssh
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 7002

//其他需要的协议按需配置
```

3. 运行服务，**cmd**或**powershell**都可以

```po
 //使用配置文件运行
 .\frpc.exe -c ./frpc.ini
```

==注意：==因为使用的云服务器，所以需要提前打开端口（加入安全组），这里配置的开放了7000,7001和7002，才能使服务与被控端和监控端数据顺利打通。

**frps**代表frp server ，**frpc**代表frp client

#### 测试使用

访问端打开远程桌面，cmd：mstsc ，输入IP地址，云服务器IP:设置端口，如：112.112.112.113:7001，用户名密码输入正确后就能访问。

#### 优化

无论是被控端还是服务端，现在虽然可以测试访问，但是都是开启了命令行窗口，一旦关闭服务就会停掉，所以Centos上需要利用**nohub**加入后台运行，window上也需要写成脚本或服务后台运行，保证使用体验 。

#### 问题说明

* 如果无法访问，检查被控端是否开启远程桌面，而且远程桌面必须设置window的开机账户密码，同时注意防火墙

#### 参考连接

[少数派](https://sspai.com/post/60852)

[window打包服务](https://github.com/winsw/winsw/releases)

[frp中文教程网](https://gofrp.org/docs/setup/)