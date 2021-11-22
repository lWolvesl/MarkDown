# Git

## 一、简介

-   git是一个分布式版本控制系统

### 1.1 版本控制

-   记录文件变化，以便将来查阅特定版本修订情况的系统
-   版本控制最重要的是可以记录文件修改历史记录，从而让用户能够查看历史版本方便版本切换

### 1.2 分布式

-   本地的每台电脑都是主控，都有版本控制，另外还有一个远程库进行统一管理。
-   

### 1.3 发展历程

![821637305417_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/821637305417_.pic_hd.jpg)

### 1.4 git工作机制

![831637305646_.pic](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/831637305646_.pic.jpg)

-   工作区指代码存在的磁盘的位置
-   暂存区
-   本地库
    -   一旦生成历史版本，就无法删除了

### 1.6 Git和代码托管中心

-   托管中心就是远程库
    -   git push
-   局域网
    -   GitLab
-   互联网
    -   GitHub
    -   Gitee码云

## 二、Git常用命令

| 命令                                | 作用             |
| :---------------------------------- | ---------------- |
| `git config --global user.name xxx` | 设置用户名       |
| `git config --global user.email`    | 设置邮箱         |
| `git init`                          | 初始化本地库     |
| `git status`                        | 查看本地库状态   |
| `git add xxx`                       | 添加到暂存区     |
| `git commit -m "日志信息" 文件名`   | 提交到本地库     |
| `git reflog`                        | 查看历史记录     |
| `git reset --hard 版本号`           | 版本穿梭         |
| `git rm --cached file`              | 删暂存区中的文件 |
| `git log`                           | 详细日志         |

-   修改文件后，git status会提示需要上传

## 三、版本穿梭

-   `git reset --hard`   更换版本

## 四、分支操作

-   ![841637309121_.pic](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/841637309121_.pic.jpg)

### 4.1 什么是分支

-   在版本控制的过程中，同时推进多个任务，为每个任务创建每个任务的单独分支。
-   分支意味着程序员可以吧自己的工作从开发主线上分离开，开发自己分支的时候，不会影响主线分支的运行。
-   分支底层也是指针的引用

![851637309289_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/851637309289_.pic_hd.jpg)

### 4.2 分支的好处

-   同时并行推进多个功能开发，提高开发效率
-   各个分支在开发的过程中，如果某一个分支开发失败，不会对其他分支造成影响。

### 4.3 分支操作

| 命令                  | 作用                         |
| --------------------- | ---------------------------- |
| `git branch 分支名`   | 创建分支                     |
| `git branch -v`       | 查看分支                     |
| `git checkout 分支名` | 切换分支                     |
| `git merge 分支名`    | 把指定的分支合并到当前分支上 |

### 4.4 解决冲突

-   合并分支的时候，两个分支在同一个文件同一个位置有两条完全不同的修改。
-   git无法替人选择，需要认为决定新代码内容

![截屏2021-11-19 16.31.49](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-19%2016.31.49.png)

![截屏2021-11-19 16.31.58](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-19%2016.31.58.png)

-   删除相关符号，选择保存哪个
-   再使用add和commit，此时commit不能附带文件名

### 4.6 分支原理

-   指针

![861637310943_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/861637310943_.pic_hd.jpg)

-   分支都是指向具体版本的指针。
-   当前所在的分支是有head决定的
-   切换分支本质上是移动Head指针

## 五、Git团队协作机制

-   此时用到代码托管中心

-   团队内协作

![871637311478_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/871637311478_.pic_hd.jpg)

-   跨团队协作

![881637311656_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/881637311656_.pic_hd.jpg)

## 六、GitHub操作

### 6.1 新建库

### 6.2 创建远程仓库别名

-   `git remote -v`查看所有远程地址的别名
-   `git remote add 别名 远程地址`

### 6.3 推送代码

-   `git push 别名 分支`

-   由于Github弃用了账户密码登陆，可以使用 token

![截屏2021-11-19 19.14.22](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-19%2019.14.22.png)

-   生成token后在原项目处依旧使用https，账户不变，密码改为token

### 6.4 拉取库

`git pull 仓库 分支`

![截屏2021-11-19 19.51.02](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-19%2019.51.02.png)

-   此时本地库代码会直接获取远程代码

### 6.5 克隆仓库

`git clone 仓库`



### 6.6 跨团队合作

-   修改后，使用拉取请求
-   此时会有聊天室功能
-   此后可以更新自己的代码

## 七、IDEA集成git

### 7.1 忽略文件

-   某些文件与实际项目无关。不参与服务器运行，还能屏蔽IDE之间的差异
-   使用`xxx.ignore`可以识别为屏蔽目录

### 7.2 合并文件(冲突)

![截屏2021-11-21 14.58.44](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-21%2014.58.44.png)

![截屏2021-11-21 15.00.11](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-21%2015.00.11.png)

### 7.3 连接GitHub

![截屏2021-11-21 15.05.29](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-21%2015.05.29.png)

-   如果本地库代码和远程库代码版本不一致的话，push操作将会被拒绝，因此push前先pull再修改后再push

### 7.4 gitee克隆github

![截屏2021-11-21 15.36.13](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-21%2015.36.13.png)

### 7.5 GitLab

-   gitlab，使用MIT的基于网络的仓库管理工具
-   自动配置gitlab
    -   gitlab-ctl reconfigure

-   启动gitlab
    -   gitlab-ctl start
-   关闭gitlab
    -   gitlab-ctl stop

-   修改密码
    -   sudo gitlab-rake "gitlab:password:reset"