java -jar 可以直接运行jar包，前提是里面已经包含了tomcat服务器。

```shell
java -jar raybox.jar //运行rayboxjar包
nohup java -jar raybox-1.0.jar >RayboxJmeter.log 2>&1 &
后台运行jar包，并将错误信息转为标准信息输出到 RayboxJmeter.log日志
nohup ... & 后台挂起
2>&1 是将标准错误信息转变为标准输出
```

