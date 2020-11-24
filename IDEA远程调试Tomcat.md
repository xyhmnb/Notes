### 准备工作

1. 确定远程服务器IP
2. 关闭服务器防火墙 `service iptables stop`

### IDEA本地配置 

* 添加**Remote Server**

  ![image-20201016113033741](https://xie-cloud-oss.oss-cn-shanghai.aliyuncs.com/images/image-20201016113033741.png)

* 复制Remote Server自动生成的JVM参数，后面使用会需要

  ![image-20201016113429960](https://xie-cloud-oss.oss-cn-shanghai.aliyuncs.com/images/image-20201016113429960.png)

* 修改参数，加前缀
  * Linux（加单引号）：export JAVA_OPTS='-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8000'
  * Window（没有单引号）：set JAVA_OPTS=-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8000

### 服务器Tomcat配置

以Linux环境为例，找到Tomcat安装路径，编辑`catalina.sh`，Windows是编辑：`catalina.bat`

* 在该文件的最上面，添加我们刚刚修改过的参数