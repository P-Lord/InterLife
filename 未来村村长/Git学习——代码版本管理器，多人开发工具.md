## Git与GitLab，B站视频太多理论，理论太多容易忘。我只教你怎么使用！

### 一、git软件下载

打开 [git官网] https://git-scm.com/，下载git对应操作系统的版本

官网下载太慢可以使用淘宝镜像下载：http://npm.taobao.org/mirrors/git-for-windows/


### 二、基础linux命令（git bash相当于在windows系统的linux命令）

1）、cd : 改变目录。

2）、cd . . 回退到上一个目录，直接cd进入默认目录

3）、pwd : 显示当前所在的目录路径。

4）、ls(ll):  都是列出当前目录中的所有文件，只不过ll(两个ll)列出的内容更为详细。

5）、touch : 新建一个文件 如 touch index.js 就会在当前目录下新建一个index.js文件。

6）、rm:  删除一个文件, rm index.js 就会把index.js文件删除。

7）、mkdir:  新建一个目录,就是新建一个文件夹。

8）、rm -r :  删除一个文件夹, rm -r src 删除src目录

```vi
rm -rf /  切勿在Linux中尝试！删除电脑中全部文件
```

9）、mv 移动文件, mv index.html src index.html 是我们要移动的文件, src 是目标文件夹,当然, 这样写,必须保证文件和目标文件夹在同一目录下。

10）、reset 重新初始化终端/清屏。

11）、clear 清屏。

12）、history 查看命令历史。

13）、help 帮助。

14）、exit 退出。

15）、#表示注释



### 三、Git配置

查看git配置：

```cmd
git config -l
```

设置用户名与邮箱（用户标识，必要）
当你安装Git后首先要做的事情是设置你的用户名称和e-mail地址。这是非常重要的，因为每次Git提交都会使用该信息。它被永远的嵌入到了你的提交中：

```cmd
git config --global user.name "plord"  #名称
git config --global user.email 1538581962@qq.com   #邮箱
```

只需要做一次这个设置，如果你传递了--global 选项，因为Git将总是会使用该信息来处理你在系统中所做的一切操作。如果你希望在一个特定的项目中使用不同的名称或e-mail地址，你可以在该项目中运行该命令而不要--global选项。总之--global为全局配置，不加为某个项目的特定配置。



### 四、git基本理论

##### ① 工作区域：

Git本地有三个工作区域：工作目录（Working Directory）、暂存区(Stage/Index)、资源库(Repository或Git Directory)。如果在加上远程的git仓库(Remote Directory)就可以分为四个工作区域。文件在这四个区域之间的转换关系如下：

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy91SkRBVUtyR0M3S3N1OFVsSVR3TWxiWDNrTUd0WjlwME5KNEw5T1BJOWlhMU1taWJwdkRkNmNTZGRCZHZybGJkRXR5RU9yaDRDS25XVmlieWZDSGEzbHpYdy82NDA?x-oss-process=image/format,png)

Workspace：工作区，就是你平时存放项目代码的地方

Index / Stage：暂存区，用于临时存放你的改动，事实上它只是一个文件，保存即将提交到文件列表信息

Repository：仓库区（或本地仓库），就是安全存放数据的位置，这里面有你提交到所有版本的数据。其中HEAD指向最新放入仓库的版本

Remote：远程仓库，托管代码的服务器，可以简单的认为是你项目组中的一台电脑用于远程数据交换


##### ② 工作流程

git的工作流程一般是这样的：

１、在工作目录中添加、修改文件；

２、将需要进行版本管理的文件放入暂存区域；

３、将暂存区域的文件提交到git仓库。

因此，git管理的文件有三种状态：已修改（modified）,已暂存（staged）,已提交(committed)

##### ③ 文件的四种状态

Untracked: 未跟踪, 此文件在文件夹中, 但并没有加入到git库, 不参与版本控制. 通过git add 状态变为Staged.

Unmodify: 文件已经入库, 未修改, 即版本库中的文件快照内容与文件夹中完全一致. 这种类型的文件有两种去处, 如果它被修改, 而变为Modified. 如果使用git rm移出版本库, 则成为Untracked文件

Modified: 文件已修改, 仅仅是修改, 并没有进行其他的操作. 这个文件也有两个去处, 通过git add可进入暂存staged状态, 使用git checkout 则丢弃修改过, 返回到unmodify状态, 这个git checkout即从库中取出文件, 覆盖当前修改 !

Staged: 暂存状态. 执行git commit则将修改同步到库中, 这时库中的文件和本地文件又变为一致, 文件为Unmodify状态. 执行git reset HEAD filename取消暂存, 文件状态为Modified

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy91SkRBVUtyR0M3S3N1OFVsSVR3TWxiWDNrTUd0WjlwMDlpYU9obDBkQUNmTHJNd05iRHp1Y0dRMzBzM0huc2lhY3pmY1I2ZEM5T2VoaWN1d2liS3VIalJsemcvNjQw?x-oss-process=image/format,png)

##### ③ git命令

```bash
#查看指定文件状态
git status [filename]

#查看所有文件状态
git status

#提交到暂存区
git add .

#提交到本地仓库
git commit -m

#提交到远程服务器
git push

#拉取服务器上最新资源
git pull
```



### 五、Git本地仓库的使用

#### 1、建立一个工作目录

##### ① 新建一个文件夹

##### ② 打开git bash命令终端

##### ③ 输入 `git init`

![image-20211004111009603](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20211004111009603.png)

这样就可以看到文件夹中多了一个.git文件，代表这个文件夹已经成为了工作目录。

#### 2、将文件添加到暂存区

##### ① 查看暂存区的状态

```bash
git status
```



② 将文件提交到暂存区

```bash
#提交当前文件夹指定文件
git add [filename]

#提交当前目录下文件
git add .

#提交指定路径下文件
git add [path][./filename]
```



若添加错误，可以使用下列命令将文件移除暂存区。

```bash
#restore只能移除暂存区文件
git restore --staged [filename]

#reset命令可以移除所有区域文件
git reset HEAD [filename]
```



![image-20211004111513877](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20211004111513877.png)



#### 3、将文件从暂存区提交到本地仓库

通过命令将暂存区文件提交到本地仓库

```bash
git commit -m '备注信息'
```

![image-20211004112310012](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20211004112310012.png)

可以通过

```bash
git log
```

查看提交的记录

通过

```bash
git checkout [path/./filename]
```

更新当前目录/文件



#### 4、本地仓库文件修改与提交

```bash

#添加同名文件到暂存区
git add [exsitFileName]

#提交到本地仓库
git commit -m "备注信息"

#查看缓存区状态
git status

#查看区别
git diff [filename]

#删除操作

#--查看本地仓库文件目录
git ls-files

#通过rm删除本地仓库文件
git rm [filename]

```





#### 5、版本回退

```bash
#查看版本记录
$ git log --pretty=oneline

#查看s所有记录
$ git reflog

#回退一个版本
git reset --hard HEAD^

#回退n个版本
git reset --hard HEAD~n

#版本回归
git reset --hard [commit的唯一标识]
```





### 六、GitHub远程仓库的使用

#### 1、通过SSH下载项目

##### ① 通过命令找到SSH Key

```bash
ssh-keygen -t rsa -C 'github注册邮箱账号'
```

![image-20211004151859594](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20211004151859594.png)

找到id_rsa.pub文件，打开拿到SSH Key。



##### ② 到github-setting中设置添加

![image-20211004152633636](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20211004152633636.png)

通过命令查看即可是否生效

```bash
ssh -T git@github.com
```

![image-20211004155658010](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20211004155658010.png)

##### ③ 通过SSH下载项目

```bash
git clone [SSH地址]
```

![image-20211004155916521](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20211004155916521.png)

2、将项目传到github











### 七、GitLab远程仓库的使用

GitLab 是一个用于仓库管理系统的开源项目，使用Git作为代码管理工具，并在此基础上搭建起来的web服务。

它拥有与Github类似的功能，能够浏览源代码，管理缺陷和注释。可以管理团队对仓库的访问，它非常易于浏览提交过的版本并提供一个文件历史库。它还提供一个代码片段收集功能可以轻松实现代码复用，便于日后有需要的时候进行查找。

GitLab和Github区别是，一个设置权限要收费，一个不用收费。



















