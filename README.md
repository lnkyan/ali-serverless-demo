# serverless-order-dinner（开了个头然后就烂尾了。。 ）
基于无服务架构做的钉钉点餐工具。用来发布和记录公司每天免费晚餐的点餐情况

- 语言是Node.js
- 数据存储在阿里云OSS对象存储（每月有免费额度）
- 部署在阿里云的函数计算（每月有免费额度）
- 推送到钉钉机器人API
- 

# 运行方法
该项目无法在本地运行，只能跑跑测试用例。想运行的话还得丢阿里云上
```
npm install
```

# 部署方法
1. 首先注册一个阿里云账号，可以使用淘宝账号登录
2. 开通OSS对象存储
3. 创建一个`存储空间`，即`Bucket`，就近选择一个区域，其它保留默认配置即可
4. 访问OSS需要accessKeys。鼠标指向阿里云控制台右上角头像，就可以看到了。我们创建一对新的acessId和accessKey
5. 开通Serverless函数计算
6. 就近新建一个服务，服务相当于我们的一个应用。高级配置不用管，反正后面都能改
7. 新建一个函数
    > 函数相当于一个最小执行单元，这里的拆分粒度就见仁见智了。目前我是按照HTTP接口来拆分的，公共的代码通过lib文件的方式复用
    1. 我们需要暴露HTTP接口，所以设置一个HTTP触发器（当然不设置的话后面也能再加），要对外暴露所以认证方式是`anonymous`。需要防刷的话可以设置为`function`，每次访问都需要[签名](https://help.aliyun.com/document_detail/71229.html?spm=5176.8663048.function-trigger.1.48f73edcfaHDTG)
    2. 填写函数名
    3. 代码上传方式选`代码包上传`，把本项目中除了`node_modules`目录之外的部分一起上传。以后修改了代码重新上传千万要记得点保存。。
    4. 代码上传后被放到沙盒的`code`目录下，可以通过`process.env.FC_FUNC_CODE_PATH`获取到这个路径。函数入口我这里需要配置为`functions/query_food.handler`，表示`code/functions/query_food.js`的`handler`方法。上传后`在线编辑`页面就会展示这个入口所在文件的代码
    5. 如果拆分的比较小，运行内存也可以设置到最小，省着点用。超时时间也没必要默认的60秒那么久，5秒足矣
    6. 权限配置页面我一打开就会报错，设置了一个`AliyunOSSFullAccess`权限，好像不设置也可以
8. 把函数`代码执行`页面调试http用的URL复制出来，这就是访问函数的接口了