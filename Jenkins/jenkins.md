# JENKINS
MacOS 启动jenkins： java -jar /Applications/Jenkins/jenkins.war --httpPort=8080

Sec Pass in : /var/root/.jenkins/secrets/initialAdminPassword
 
登录jenkins服务器：  sudo su - jenkins

验证是否ssh设置成功，可以clone代码from git: ssh -T git@github.com:ShirlyFE

查找jenkins服务器的ssh私钥： 登入jenkins服务器后cd /Users/Shared/Jenkins/.ssh

## Install jenkins by Docker
```
docker pull jenkins
// Jenkins 启动
docker run -p 8080:8080 -p 50000:50000 -v /Users/shirlytian/jenkins_home:/var/jenkins_home jenkins

docker run -p 8080:8080 -p 50000:50000 -v /Users/shirlytian/jenkins_home:/var/jenkins_home -e JENKINS_JAVA_OPTIONS='-Djava.awt.headless=true -Dmail.smtp.starttls.enable=true' -e JENKINS_ARGS=-Dmail.smtp.starttls.enable=true jenkins


// Mail config for outlook
Server/smart host: smtp.office365.com
Port 587 (recommended) or port 25
TLS/ StartTLS: Enabled

Need set the 

或者
docker run \
  --rm \
  -u root \
  -p 8080:8080 \
  -v jenkins-data:/var/jenkins_home \ 
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v "$HOME":/home
```

## 重置jenkins的登录用户名密码when foget them
set shirlytian/.jenkins/config.xml 中<useSecurity>true</useSecurity> to <useSecurity>false</useSecurity>

## jenkins容器中安装NodeJs插件
### 下载nodejs插件并配置
1. 系统管理--->管理插件--->下载NodeJS插件
2. 下载的插件在：$JENKINS_HOME/plugins目录下
3. 系统管理--->Global Tool Configuration--->选择需要安装的nodejs版本-(选择自动安装->Install from nodejs.org)
4. 会从nodejs官网下载安装，nodejs安装包在：$JENKINS_HOME/tools目录下

###job配置并下载安装nodejs
#### Create job use freestyle project
构建环境勾选 Provide Node & npm bin/folder to PATH
1. 每次build，都会首先执行环境构建，环境构建无误后，才会开始真正的构建过程
2. 会下载nodejs并安装配置，并把node添加到当前PATH环境变量中，这样就支持node和npm命令啦！

#### Use jenkinsFile pipeline
加入已经安装NodeJS插件，并且配置可NodeJS，如图
[Node Tool Config](./Config NodeJS in the global tool section.png)

那么在执行build之前先需要先执行构建环境，Pipeline JenkinsFile声明如下：
```
pipeline {
  agent any

  tools {
    nodejs 'NodeJS'
  }

  stages {
    stage('Checkout/Prep') {
      
    }
  }
}
```
### 构建中打印$PATH并查看node，npm版本
```
echo $PATH
node -v
npm -v
```
### jenkin容器中查看node
+ 虽然jenkins在线安装了nodejs但是并没有将其永久性添加到PATH中，所以仍然需要进入其安装目录查看。

```
cd /var/jenkins_home/tools/jenkins.plugins.nodejs.tools.NodeJSInstallation/nodejs_9.5.0

./bin/node -v     # 可以正常的输出node版本

maybe you can
export PATH=$PATH:/home/jenkins/.jenkins/tools/jenkins.plugins.nodejs.tools.NodeJSInstallation/node_js/bin/
```
#### 参考链接
[Jenkins---jenkins中安装nodejs](https://www.jianshu.com/p/0a865a321d78)

## Shirly account config
User: Shirly
Password: pH@18092687239
Full Name: Shirly Tian
E-mail address: 840987538@qq.com

My API Access Token: UpZ4ynj283bcPaZnCxP4

## Fix issue of "Failed to connect to repository : Command "git -c core.askpass=true ls-remote -h git@XXXXX:XXXX/XXXX.git HEAD" returned status code 128:"

issue when create job and excute:
```
stderr: Permission denied, please try again.

Permission denied, please try again.

Permission denied (publickey,password).

fatal: Could not read from remote repository.

Please make sure you have the correct access rights

and the repository exists.
```
it is related with SSH config.

其实在Jenkins系统使用的都是Jenkins这个系统的帐号，因此需要换到jenkins这个帐号下生成个新的ssh密钥，在上传到git的服务器就好了

1. sudo su - jenkins
# 切换到 jenkins HOME 目录
2. ssh-keygen -t rsa  // 生成 ssh key, 复制 xxx.pub 公钥到 git 服务器上.
3. 在 jenkins 上设置 Credentials，然后再新建job的时候使用设置的 Credentials 即可

在jenkins界面，依次点击： Credentials -> System -> Add domain： 
Domain Name: 填写你git服务器的地址，如 github.xxx.com 
Description: 随便写一点描述，如 This is the Credential for github

点击 ok 后，在点击 “adding some credentials?”

进入页面后，可以选择 Username with password 或者 SSH Username with private key, 根据你的情况选择，这里我们选择 Username with private key：

Username: 随便起一个名字，以便在创建 Job 的时候使用该 Credential 
Private Key：可以指定文件，也可以使用默认的 ~/.ssh，当然也可以直接将私钥复制粘贴到此处。 
Passphrase: 如果你在创建 ssh key 的时候输入了 Passphrase 那就填写相应的Passphrase，为空就不填写 
ID: 空 
Description： 空

如图：[./jenkins ssh config for git.png](jenkins ssh config for git.png)

点击 ok 后 Credential 就创建好了。

如果你再新建 Job 就可以看到我们的 Credential 选项了

## Jenkins 安装
最小推荐配置：

Java 8（JRE或JDK）
256MB可用内存
1GB +可用磁盘空间

### Docker 
您必须在您的机器上正确安装Docker。有关详细信息，请参阅Docker安装指南。

首先，从Docker存储库中取出官方的jenkins图像。

docker pull jenkins/jenkins
接下来，使用此映像运行容器并将数据目录从容器映射到主机; 例如在下面的示例中/var/jenkins_home，容器jenkins/从主机上的当前路径映射到目录。jenkins8080接口也显示在主机49001。

docker run -d -p 49001:8080 -v $PWD/jenkins:/var/jenkins_home -t jenkins/jenkins


### MACOS
要从网站安装，使用一个包：

下载最新的软件包
打开包装并按照说明进行操作
jenkins也可以使用brew进行安装：

安装最新版本
brew install jenkins

## Jenkins slack plugin

Need get the integration token from the slack, and need know the jenkins workspace our team use, also the chanel we need send message to

[](./slack plugin global config.png)

## [安装在Linux下的jenkins执行windows下的bat命令](https://blog.csdn.net/songjiaping/article/details/49587297)
1、打开Jenkins->系统管理->管理节点

2、点击新建节点，取名字，如果是第一次创建节点，需选择第一个Dumb slave

3、我的配置
!Jenkins Window Node Config!

4、保存后会出现如下内容：
!jenkins window node config result.png!

5、在目标windows中创建“远程工作目录”中的路径，然后下载保存slave.jar，在同级路径下创建bat命令，添加上图中“Run from slave command line:”下的命令，双击bat命令就连通了。

6、然后就可以创建一个job，勾选“Restrict where this project can be run”，输入创建节点时保存的标签名，再添加一个构建步骤（execute windows batch command）,在里面输入bat命令就可以了 

注：有时候连接成功了但是jenkins上却是失败的，需要在连接的bat命令前输入以下代码，并重新双击bat命令建立连接

javaws http://xxxx/jenkins/computer/windows_37/slave-agent.jnlp

### Questions
1. The option to “Launch slave agents via Java Web Start” is missing from new node configuration, how can I add it to the options menu?

!Jenkins add window node no java launch method.png!
*Answers:*
You have to enable the TCP port of JNLP agents to enable this option for slaves.
```
Manage Jenkins > Configure Global Security > TCP port for JNLP agents
```

2. Error cloning remote repo 'origin' when use the window slave in jenkins which located in the linux
[Error cloning remote repo 'origin'](https://stackoverflow.com/questions/37155321/error-error-cloning-remote-repo-origin)

## 如何获取Jenkins MultiJob 下游Job（子Job）的状态并且发送邮件

第一种方式适用于：

1.具有Jenkins 插件安装权限

2.不发送Email，只用于查看子Job状态 ，并且自己做进一步处理

3.没有任何基础也能懂，因为只是装个插件，并且使用

想简便并且发送邮件，请直接查看第二种方式。

第二种方式适用于：

1.没有Jenkins安装权限，但是现有Jenkins已经安装了 Email-ext plugin （Email Extension）

2.需要将子Job状态整理发送邮件

第一种方式：

首先，如果只是想获取子Job的状态，参考链接：[Jenkins MultiJob - Send Results e-mail with data from inner Jobs](https://stackoverflow.com/questions/17172927/jenkins-multijob-send-results-e-mail-with-data-from-inner-jobs)

本人尝试了下，具体步骤如下：

1.创建 MultiJob （已有请跳过）

2.安装Groovy Postbuild Plugin 

3.部分Jenkins在安装Groovy Postbuild插件的时候会失败，并且提示更新其他插件，按照提示更新即可

4.配置MultiJob的相关内容

MultiJob --> Configure --> Post-build Actions --> add Post-build Actions -->Groovy Postbuild

!Groovy Postbuild Action.webp!

!Groovy postBuild Action InputBox.webp!

接下来在Groovy Script的输入框内输入
```
subBuilds = manager.build.getSubBuilds()

//print the each sub build instance to the console log and its result

subBuilds.each{

    manager.listener.logger.println "${it}, ${it.getResult()}"

}
```

!Result of get the sub job information.webp!


第二种方式：

1.创建 MultiJob （已有请跳过）

2.安装 Email-ext plugin （Email Extension）（已安装请跳过）.

3.MultiJob配置

MultiJob --> Configure --> Post-build Actions --> add Post-build Actions --> Editable Email Notification （添加发送邮件的组件）-->Advanced Settings（高级设置）-->Pre-send Script

在Pre-send Script里面加上

import groovy.json.JsonSlurper; //msg使用的类需要先引用包

def EmailContent = msg.getContent().getBodyPart(0).getContent() //logger.print("$EmailContent")

String newContent = """$EmailContent Test change content"""

def subBuilds = build.getSubBuilds()

subBuilds.each{

// logger.print("${it.jobName}")

logger.print("${it}, ${it.getResult()}")

newContent +="""${it} ${it.getResult()} """ } //获取每个子job的状态

//logger.print("$subBuilds")

//logger.print("$newContent")

msg.setContent(newContent, "text/html; charset=utf-8"); //设置邮件内容

这部分代码是学习了Andrew's Blog 里面关于配置Jnekins发送邮件，访问github获取最新Commit信息 [这篇文章之后](https://liuhongjiang.github.io/hexotech/2015/12/04/jenkins-send-email-after-build/)，改的比较简单的版本，如果对相关内容感信息，可以去这篇博客学习。

暂时还没进行美化，不过邮件也已经达到了效果。

!email.webp!

如果想要之获取JobName，buildNumber，和result的等相关参数

修改下面这句话就好了

newContent +="""${it} ${it.getResult()} """ } //获取每个子job的状态，并且加到邮件内容

${it.getJobName()} // 获取每个Job的名字

${it.getBuildNumber()}//获取每个Job的Build Number


## Resource
[Pipeline As Code With Jenkins2.0](http://www.spring4all.com/article/909)

[Jenkins Pipeline 插件 Pipeline Doc 中文版合集](https://testerhome.com/topics/11265)

[Jenkins 教程](https://www.w3cschool.cn/jenkins/jenkins-vpgf28qp.html)

[基于shipit-deploy实现的多服务器自动化部署方案](https://alili.tech/2017/12/17/Nodejs/%E5%9F%BA%E4%BA%8Eshipit-deploy%E5%AE%9E%E7%8E%B0%E7%9A%84%E5%A4%9A%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%87%AA%E5%8A%A8%E5%8C%96%E9%83%A8%E7%BD%B2%E6%96%B9%E6%A1%88/)

[End-to-End Multibranch Pipeline Project Creation](https://jenkins.io/doc/tutorials/build-a-multibranch-pipeline-project/#on-macos-and-linux)

[使用jenkins进行项目的自动构建部署,回滚](https://www.jianshu.com/p/dceaa1c7bb49)

[PM2 一次前后端分离项目部署实践](https://www.breezymelon.com/2018/06/14/%E4%B8%80%E6%AC%A1%E5%89%8D%E5%90%8E%E7%AB%AF%E5%88%86%E7%A6%BB%E9%A1%B9%E7%9B%AE%E9%83%A8%E7%BD%B2%E5%AE%9E%E8%B7%B5/)

[https://cloud.tencent.com/developer/article/1027557](Jenkins迁移及日常操作的一点总结)

[如何获取Jenkins MultiJob 下游Job（子Job）的状态并且发送邮件](https://www.jianshu.com/p/b50162a5144a)

[jenkins multijob 插件使用-让jenkins任务按照分组、顺序/并行执行](https://www.cnblogs.com/cocoat/p/7606447.html)

[Jenkins不同job之间传递参数](https://blog.csdn.net/itfootball/article/details/45060331)

[[Jenkins]Job中如何传递自定义变量](https://www.cnblogs.com/junneyang/p/5239480.html)

[Jenkins 任务构建](https://blog.csdn.net/u014084065/article/details/77460403)
