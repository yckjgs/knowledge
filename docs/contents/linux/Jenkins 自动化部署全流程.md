

> 环境:腾讯云轻量服务器：CentOS CPU - 2核 内存 - 4GB（原来使用的内存是2GB构建总是卡死，升级为4GB）
>
> 目标:实现推送代码后，自动进行构建部署，网站刷新即可展示最新效果.

内容较长，且看且耐心！💪

# 一、前言

Jenkins是一个开源软件项目，是基于Java开发的一种持续集成工具，用于监控持续重复的工作，旨在提供一个开放易用的软件平台，使软件的持续集成变成可能。

![jenkins基础工作原理](https://s2.51cto.com/images/blog/202405/31170601_665992f967abc35071.png)

一、项目部署和DevOps
1.1、传统的开发模式
在传统的开发模式中，开发的整个过程是按部就班就行：

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/8e50ede67de54e2a8ea220da4b400c34.png#pic_center)

但是这种模式存在很大的弊端：

工作的不协调：开发人员在开发阶段，测试和运维人员其实是处于等待的状态。等到测试阶段，开发人员等待测试反馈bug，也会处于等待状态。
线上bug的隐患：项目准备交付时，突然出现了bug，所有人员需要加班、等待问题的处理；
1.2、DevOps开发模式
DevOps是Development和Operations两个词的结合，将开发和运维结合起来的模式：

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9b7a25c6b9c64e4fafa5a2998073b2e0.png#pic_center)



1.3、持续集成和持续交付
伴随着DevOps一起出现的两个词就是持续集成和持续交付(部署)：

CI是Continuous Integration（持续集成）；
CD是两种翻译：Continuous Delivery（持续交付）或Continuous Deployment（持续部署）；
持续集成CI：

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b21445fded894a33b837a4abcf236d31.png#pic_center#pic_center)

持续交付和持续部署：

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/6082d6fe09bd4e118780f427a235ecdb.png#pic_center)

![](https://i-blog.csdnimg.cn/direct/393aa0c64c6b4a60a0acd364ac714d41.png#pic_center)

1.4、自动化部署流程

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/5e586b04a9514c989a8f03dccf72775d.png#pic_center)

一.Docker安装Jenkins插件

1.启动docker，下载Jenkins镜像文件

>docker -v
>docker pull jenkins/jenkins

![image-20241023174415285](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20241023174415285.png)

2.创建Jenkins挂载目录并授权权限

>mkdir -p /var/jenkins_mount
>chmod 777 /var/jenkins_mount

3.创建并启动Jenkins容器

> docker run -d -p 10240:8080 -p 10241:50000 -v /var/jenkins_mount:/var/jenkins_home -v /etc/localtime:/etc/localtime --name jenkins jenkins/jenkins:lts

参数说明:

　-d 后台运行镜像

　-p 10240:8080 将镜像的8080端口映射到服务器的10240端口。

　-p 10241:50000 将镜像的50000端口映射到服务器的10241端口

　-v /var/jenkins_mount:/var/jenkins_mount /var/jenkins_home目录为容器jenkins工作目录，我们将硬盘上的一个目录挂载到这个位置，方便后续更新镜像后继续使用原来的工作目录。这里我们设置的就是上面我们创建的 /var/jenkins_mount目录

　-v /etc/localtime:/etc/localtime让容器使用和服务器同样的时间设置。

　--name myjenkins 给容器起一个别名

![image-20241023174900589](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20241023174900589.png)

4.查看docker容器日志。

>docker ps -l #查看jenkins是否启动成功，如下图出现端口号，就为启动成功了
>docker logs myjenkins

5.配置镜像加速，进入cd /var/jenkins_mount/目录,修改hudson.model.UpdateCenter.xml文件内容。

![docker 部署jenkins 稳定版本 jenkins docker安装_Jenkins_09](https://s2.51cto.com/images/blog/202405/31170602_665992fa9f33448386.png)

注:将url修改为清华大学官方镜像:https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

6.访问Jenkins页面，输入你的ip:10240

![docker 部署jenkins 稳定版本 jenkins docker安装_docker_10](https://s2.51cto.com/images/blog/202405/31170602_665992fad115f96785.png)

7.查看管理员密码

> cat /var/jenkins_mount/secrets/initialAdminPassword

![docker 部署jenkins 稳定版本 jenkins docker安装_Jenkins_12](https://s2.51cto.com/images/blog/202405/31170603_665992fb33ef273543.png?)

到此以全部安装成功，尽情的使用吧！

宝塔安装补充:

在`软件商店`搜索安装Jenkins插件

![image-20241023112020208](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20241023111920103.png)

点击进入设置，`选择不使用域名`，`允许外部访问`，安装应用

 ![image-20241023112020208](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20241023112020208.png)

<img src="C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20241023142520035.png" alt="image-20241023112020208" style="zoom:50%;" />

提示成功后打开访问连接，登录密码在应用信息中获取，

 ![image-20241023142855470](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20241023142855470.png)

二、部署项目前-安装插件及相关配置

项目的部署主要参考了网友linux中使用jenkins自动部署前端工程 的博客，在此表达感谢！

![image-20241025234537143](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20241025234537143.png)

安装之前先确认服务已启动。

SpringBoot前后端额外安装插件如下：

Locale Plugin(Jenkins环境语言汉化)

NodeJS Plugin（nodejs环境-->用于前端构建打包）

Maven Integration（java环境-->用于后端构建打包）

Publish over SSH （用于执行构建后的操作-->用于远程传输文件，`切记docker环境cp不要用，只能SSH传输部署`）

Git Plugin(用于拉取前后端代码到本地)

Coding Webhook (Coding代码库须装)

Gitee Plugin（gitee代码库须装）

1、点击系统管理 -> 插件管理

![jenkins安装部署前后端简版 jenkins 前端部署_jenkins安装部署前后端简版_04](https://s2.51cto.com/images/blog/202406/28021053_667dab2d7958089883.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)

 2、搜索插件并安装

![jenkins安装部署前后端简版 jenkins 前端部署_java_05](https://s2.51cto.com/images/blog/202406/28021053_667dab2dd988132761.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)

`安装插件之后切记重启Jenkins。`

 3、全局工具配置

 点击系统管理->全局工具配置，进行配置git和node

![jenkins安装部署前后端简版 jenkins 前端部署_jenkins安装部署前后端简版_06](https://s2.51cto.com/images/blog/202406/28021054_667dab2e34d0c6480.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)

![image-20241025235726868](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20241025235726868.png)

配置信息填完后保存即可。

4、系统配置(Publish over SSH)

![jenkins安装部署前后端简版 jenkins 前端部署_jenkins安装部署前后端简版_07](https://s2.51cto.com/images/blog/202406/28021055_667dab2f4995e80199.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)

![image-20241026000100416](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20241026000100416.png)

 写入服务器密码或key，写完信息点击右下角Test Configuration确认是否配置成功测试成功！

5.配置Git代码库账户凭证

![image-20241026000559180](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20241026000559180.png)

三、部署前后端项目

1）

![image-20241026002233676](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20241026002233676.png)

```
#!/bin/bash
#默认根目录:/var/jenkins_mount/workspace/mes-ui
#查看版本号
node -v
npm -v
#加载依赖
npm install --force --registry=https://registry.npmmirror.com
#构建代码
npm run build:prod
#进入dist目录
cd dist
#移除之前的压缩包
rm -f dist.tar.gz
#把当前目录的所有文件打包成dist.tar.gz
tar -zcvf dist.tar.gz *
```

```
cd /www/wwwroot/java/dist
tar -zxvf dist.tar.gz
rm -f dist.tar.gz
#重启Nginx
sudo systemctl restart nginx
echo "构建部署完成"
```

```
# !bin/bash
#查看nginx进程
ps -ef | grep nginx
```

2)后台配置

![image-20241026002656010](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20241026002656010.png)

```
#默认目录:/var/jenkins_mount/workspace/mes
#查看版本号
java -version
mvn -v
#切换了编译后的目录
cd t3rik-erp/target
#移除之前的压缩包
rm -f mes.tar.gz
#把当前目录的所有文件打包成mes.tar.gz
tar -zcvf mes.tar.gz t3rik-erp.jar
```

```
clean install -Dmaven.test.skip=true -U
```

```
#默认目录:/var/jenkins_mount/workspace/mes
#查看版本号
java -version
mvn -v
#切换了编译后的目录
cd t3rik-erp/target
#移除之前的压缩包
rm -f mes.tar.gz
#把当前目录的所有文件打包成mes.tar.gz
tar -zcvf mes.tar.gz t3rik-erp.jar
```

#查看版本
java -version
cd /www/wwwroot/java
tar -zxvf mes.tar.gz
rm -f mes.tar.gz
```
# 获取Java进程ID
PID=$(ps -ef | grep 't3rik-erp.jar' | grep -v grep | awk '{print $2}')
if [ -n "$PID" ]; then
    kill -15 $PID
    kill -9 $PID
fi
# 等待1秒
sleep 1
/www/server/java/jdk-20.0.2/bin/java  -jar -Xmx1024M -Xms256M  /www/wwwroot/java/t3rik-erp.jar --server.port=8762 > release_out.log
echo "构建部署完成"
```

```
#查看java进程
ps -ef | grep java
```



2、配置 源码管理

 选择git，补充红框的信息

 gitee的凭证需要先点击添加，写入登录的用户名和密码，然后在下拉选择即可

 分支默认master，可根据实际情况修改

![jenkins安装部署前后端简版 jenkins 前端部署_笔记_10](https://s2.51cto.com/images/blog/202406/28021057_667dab31d83c142711.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)









3、配置 构建触发器

 选择 Gitee webhook触发构建。。。

![jenkins安装部署前后端简版 jenkins 前端部署_jenkins_11](https://s2.51cto.com/images/blog/202406/28021058_667dab323471a94418.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)

 修改 允许触发构建的分支 ，并生成 webhook密码

![jenkins安装部署前后端简版 jenkins 前端部署_jenkins_12](https://s2.51cto.com/images/blog/202406/28021058_667dab329315277979.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)

 去gitee仓库配置webhook

![jenkins安装部署前后端简版 jenkins 前端部署_java_13](https://s2.51cto.com/images/blog/202406/28021058_667dab32ee07424274.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)

补充下面信息:

URL即为勾选构建触发器时，提示URL

密码是上面生成的

 添加完成后，会进行请求测试，今天测试结构都失败了呢，试了一个之前的也是404但构建没问题，姑且放一放，看后来构建是否有影响



 5、配置 Build Steps

增加构建步骤->选择 执行shell

```
node -v
npm -v

#判断-start 如果存在/usr/local/nginx/html/hello
if [-d /usr/local/nginx/html/hello]
    echo '当前存在/usr/local/nginx/html/hello目录'
    #时间戳
    TIMESTAMP=$(date+"%y%m%d%H%M%S")
    #备份
    sudo mv /usr/local/nginx/html/vues '/usr/local/nginx/html/hello_'TIMESTAMP
else
   echo '当前存在/usr/local/nginx/html/hello目录'
fi
#判断-end
npm install
npm run build
```

 这里主要是将nginx的代码进行备份移除，并进行install和build

增加构建步骤->选择 Send files or execute commands over SSH

 通过ssh讲打包后的文件发送到指定目录，及nginx配置的包位置

 jenkins生成的文件在/var/lib/jenkins/workspace

 至此 完成了项目的配置，

四、构建

点击【立即构建】或提交代码都能进行自动构建部署

![jenkins安装部署前后端简版 jenkins 前端部署_jenkins安装部署前后端简版_14](https://s2.51cto.com/images/blog/202406/28021101_667dab356634124577.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)

![jenkins安装部署前后端简版 jenkins 前端部署_前端_15](https://s2.51cto.com/images/blog/202406/28021101_667dab35b5f0d40354.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)

 如果成功，则

![jenkins安装部署前后端简版 jenkins 前端部署_笔记_16](https://s2.51cto.com/images/blog/202406/28021102_667dab361d89879125.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)

 如果失败，看看log，错哪里了进行修改

![jenkins安装部署前后端简版 jenkins 前端部署_java_17](https://s2.51cto.com/images/blog/202406/28021102_667dab3675e214445.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)

美好的一天结束了！