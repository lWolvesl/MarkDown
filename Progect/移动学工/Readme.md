## 移动学工自动打卡

-   简介：河南师范大学移动学工打卡

    >   实现自动打卡
    >
    >   可以自定义打卡区间
    >
    >   可自定义邮件

## 使用教程-源码编码启动

### 一、修改 resources 包下的 config.properties

```properties
#域名-默认即可，无需更改
host=htu.banjimofang.com

#个人cookiee
name=yxktmf
cookiee=xxx

#打卡时间（单位为小时,程序会在此期间打卡,24小时制）
time=11-13

#接收打卡信息的邮箱
mail=xxx

#发送打卡信息的邮箱
#此处163邮箱为smtp.163.com,QQ邮箱为smtp.qq.com
mail.host=smtp.163.com
mailServer=xxx
mail.smtp.auth=true
passwd=xxx

#个人打卡页面地址
url=xxx

#位置信息，可以自行复制定位再修改
location=河南省,新乡市,牧野区,求知路河南师范大学(东区)|35.32802,113.92183

#是否在校（是/否）
isInschool=是

#学生身份（本科生=1，研究生=2，博士生=3，新联学院=4，预科生=5，成教生=6，留学生=7）
studentIdentity=1

#是否接触过特殊人员（是/否）
pecialPersonnel=否

#当前位置是否为中高风险区
locationDenger=否

#是否接种过疫苗（是/否）
vaccines=是
#接种针数（1/2/3）(若未接种则无需设置）
vaccinesCount=2

#体温（建议36.5-37之间）
temperature=36.5

#是否有症状
symptom=否

#是否发热（37.3度及以上）
fever=否

#是否被确诊为新型冠状病毒肺炎病例
diagnosis=否

#是否是高度疑似新型冠状病毒肺炎人员
suspected=否

#假期（近14天）是否去过中高风险区
been=否

#假期（近14天）中是否与确诊的新型冠状病毒人员接触
closeContact=否

#假期（近14天）中是否途径/中转/停留中高风险区
stay=否

#近期您的家人朋友，是否有发热、咳嗽、乏力、呼吸困难等症状
parents=否

#近14天是否与来自中高风险区其他地市的亲朋好友或有发热、咳嗽、呼吸困难、感冒等症状的亲友接触过
relatives=否

#今日心理健康状况（健康=1，偶有情绪波动=2，但能自我调节较差，需要心理协助=3）
dayState=1

#本人电话
telephone=xxx
#紧急联系人姓名
parentName=xxx
#紧急联系人手机号
parentPhone=xxx
```

-   所有`xxx`为必须修改项
    -   个人cookiee栏，数据由浏览器获取，本例使用chrome浏览器
        -   首先打开微信中的移动学工，进入并复制链接拷贝至chrome
        -   ![1031638105610_.pic](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1031638105610_.pic.jpg)
        -   ![截屏2021-11-28 21.23.27](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-28%2021.23.27.png)
        -   按照页面登陆
        -   ![截屏2021-11-28 21.23.43](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-28%2021.23.43.png)
        -   进入每日健康打卡
        -   ![截屏2021-11-28 21.25.05](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-28%2021.25.05.png)
        -   顶栏的地址即为个人打卡页面地址（url）
        -   点击不安全
        -   ![截屏2021-11-28 21.26.01](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-28%2021.26.01.png)
        -   再点击Cookie
        -   ![截屏2021-11-28 21.26.37](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-28%2021.26.37.png)
        -   本软件使用的cookie为yxktmf
        -   名称即为cookie的name，内容为cookiee，依次填入
    -   邮箱
        -   发送打卡的邮箱需要开启 IMAP/SMTP 和 POP3/SMTP服务
        -   此处以163邮箱演示,进入上边栏的设置即可看到，注意：开启IMAP后的授权码只会出现一次
        -   ![截屏2021-11-28 21.30.13](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-28%2021.30.13.png)
        -   配置文件中的mailServer为发送邮件的邮箱
        -   passwd为得到的IMAP授权码

### 二、Maven打包

![截屏2021-11-28 21.34.20](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-28%2021.34.20.png)

-   完整jar包为target下的 `worker-1.0-SNAPSHOT-jar-with-dependencies.jar`

### 三、部署

-   将此jar包复制到需要运行到机器上，注意此程序需要长时运行

#### 1、前台运行，如windows

![截屏2021-11-28 21.56.03](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-28%2021.56.03.png)

#### 2、后台运行，如linux、mac及云服务器

-   此处以云服务器为例子
-   首先将文件传入服务器或运行的位置
-   使用`nohup`进行后台运行

`nohup java -jar worker-1.0-SNAPSHOT-jar-with-dependencies.jar > ydxg.out &`

![截屏2021-11-28 22.00.49](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-28%2022.00.49.png)

-   可以使用 cat查看输出文件
-   关闭
    -   使用  `ps -ef|grep worker-1.0` 找到改进程
    -   ![截屏2021-11-28 22.02.14](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-28%2022.02.14.png)
    -   使用`kill -9 24428`即可关闭此程序，24428为我的进程号，注意更改

### 四、常见错误

-   检查cookie是否过期，若过期就及时更换

![截屏2021-11-28 21.41.45](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-28%2021.41.45.png)

## 使用教程Release-jar包模式

-   下载好release的jar包后
-   使用360等压缩工具打开
-   ![截屏2021-11-29 14.28.16](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-29%2014.28.16.png)
-   ![截屏2021-11-29 14.28.46](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-29%2014.28.46.png)
-   直接修改config.properties文件
-   压缩成功后可以直接启动（跳转源码方式-部署）