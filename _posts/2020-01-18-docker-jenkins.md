---
  layout: post
  title: docker+jenkins+GitLab
  categories: [docker]
  tags: [docker]
  description: docker环境下部署jenkins+GitLab前端自动化部署
---


>### 背景

由于开发环境和测试环境的分离，代码需要同时部署到两台服务器上，人工部署有工作量和风险，所以想使用jenkins来做自动化部署，刚好服务器上也有docker环境

>### 环境

1.linux

2.docker

3.jenkins镜像：[<a title='下载镜像'>jenkins/jenkins:lts</a>](/images/files/jenkins.tar) <label style='color: red;margin: 0 20px;'>(坑点:使用jenkins镜像没有最新的版本，很多插件都不能安装，所以使用jenkins/jenkins:lts，点击即可下载)</label><br>


>### 安装jenkins

1.导入镜像

<code>docker pull jenkins/jenkins:lts </code>

如果下载太慢了的话，可以下载我上面准备好的镜像使用本地导入

<code>cat jenkins.tar | docker import - 容器名称</code>

导入成功后使用<code>docker images </code>命令可以看到
![images](/images/docker/images.png)<br>

2.运行镜像，启动容器
<code>docker run -d --name czr-jenkins -p 8777:8080 -v /root/jenkins_home/:/var/jenkins_home -v /root/docker/tomcat/webapps/ROOT/:/usr/front_dist -v /var/run/docker.sock:/var/run/docker.sock -v $(which docker):/usr/bin/docker  jenkins/jenkins:lts</code>

命令解析
```
  -d --name czr-jenkins // 命名容器别名
  -p 8777:8080 // 把8777端口映射给jenkins使用（默认8080）
  -v /root/jenkins_home/:/var/jenkins_home // 把宿主主机的/root/jenkins_home挂载到容器的/var/jenkins_home 这里主要为了jenkins的迁移，插件配置都是在这个文件夹下面
  -v /root/docker/tomcat/webapps/ROOT/:/usr/front_dist // 把前端静态文件路径映射到容器内，方便后面构建文件后的部署
  -v /var/run/docker.sock:/var/run/docker.sock -v $(which docker):/usr/bin/docker // 把docker映射进去，可以在容器里面使用docker命令（DooD）;
```
启动成功使用<code>docker ps -a</code>查看所有的容器
![images](/images/docker/container.png)<br>

3.登录配置
启动容器成功，访问<a>http://localhost:8777</a>可以看到
![images](/images/docker/password.png)<br>
根据提示，到目标文件夹查看密码<code>docker exec -it czr-jenkins tail :url </code>
选择下面左边推荐安装
![images](/images/docker/plus.png)<br>
设置管理员和密码
![images](/images/docker/admin.png)<br>

4.下载插件
下载完推荐插件后还要下载我们构建部署需要的插件
![images](/images/docker/cumston.png)<br>
在插件中心进行搜索下载如下插件

<code>Gitlab Hook Plugin</code> // 触发gitlab钩子

<code>GitLab Plugin</code> // 配置gitlab钩子

<code>NodeJS Plugin</code> // 运行node脚本

<code>Publish Over SSH</code> // ssh连接服务

>### 配置jenkins和GitLab
在gitlab上已建好了bd-ci-test的项目，且Jenkins用户拥有该项目的相关读写权限。
在jenkins新建bd-ci-test项目
![images](/images/docker/project1.png)<br>
源码管理选择git，添加jenkins用户在gitlab上的凭据(即用户名密码)，这里选择打包的分支为release分支，这里根据需求自己填写（默认为master分支）
![images](/images/docker/project2.png)<br>
构建触发器，勾选gitlab-ci，记住后面的GitLab CI Service URL后面要填在gitlab的webhooks中：
![images](/images/docker/project3.png)<br>
现在有develop分支和release分支，如果不做这一步，开发只要向gitlab中提交代码（develop分支或者release分支），那么jenkins就会进行构建打包，下面我们设置判断过滤只有向release分支push代码时，才会触发构建打包。

点开高级，填写根据正则过滤branch，写法如下，并generate一个token，不然后面webhooks会报403：
![images](/images/docker/project4.png)<br>
在gitlab项目配置界面设置链接和token。这里要注意路径，根据部署jenkins的路径填写，不然会报404错误，并填写刚刚对应的token信息，保存：
![images](/images/docker/project5.png)<br>
点击测试，返回200的话就表示成功了。其他错误可以根据gitlab日志来排除原因gitlab/gitlab-rails/production.log：
![images](/images/docker/project6.png)<br>

### 配置构建
先配置构建环境，这里构建环境选择nodejs
![images](/images/docker/product1.png)<br>
构建脚本根据项目情况来自定义，这里仅供参考：
```
  #!/bin/bash
  echo $PATH
  node -v
  echo 'set npm registry'
  npm install
  npm run build:micro
  cp -rf ./build/. /usr/front_dist/innerssmp-app/
```
由于我的项目需要同步到其他的服务器，这里配置了构建后操作：
首先在全局配置下面添加Publish over SSH,填入ssh名称，ssh地址和用户
![images](/images/docker/product3.png)<br>
点击高级设置ssh 密码和端口号
![images](/images/docker/product4.png)<br>
选择配置好的publish ssh<br>
Source files：需要上传的文件地址<br>
Remove prefix：需要删除的目录<br>
Remote directory：远端目录<br>
Exec command：执行命令<br>
![images](/images/docker/product2.png)<br>
点击构建测试一下
构建成功后，连接到ssh成功，并且同步了构建文件
![images](/images/docker/product5.png)<br>

### 总结
整个配置过程还是相对比较简单的，但是里面有比较多的小细节需要注意，比如linux区分大小写，项目里面的打包代码有这个问题，会一直导致构建失败，这些问题需要自己去手动发现，这个配置比较简单，用于测试环境还是可以的，后面再研究一下，单元测试和版本回退的一些功能，配置过程中也对docker的试用多了一些了解










