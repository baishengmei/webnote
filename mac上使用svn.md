## mac上安装并运行svn，连接有道svn步骤
##### 说明： mac自带svn服务器端和客户端，采用命令行完成
#### 配置svn的用户权限
+ [参考链接](http://blog.csdn.net/q199109106q/article/details/8655204)

>  只参考配置svn用户权限部分；  
>  简要描述下步骤：  
> 1. 输入指令svnadmin create /Users/apple/svn/mycode，意思是在路径下创建mycode文件夹
> 
> a. 主要修改/svn/mycode/conf目录下的三个文件：
> 
> b. vim svnserve.conf,按下i键，进入可编辑状态，将配置项前面的#和空格都去掉，然后按下esc，进入不可编辑状态，输入:wq保存并推出，输入:q!不保存推出
> 
```
# anon-access =read
# auth-access =write
# password-db = passwd
# authz-db = authz
```
>
> c. 打开authz，配置用户组和权限
> 
```
[group]
topgroup=mj
```
>
```
[/]
@topgroup =rw
```
> 其中topgroup组名，可自定义，mj为用户名；[/]表示svn服务器中的所有资源库，rw表示读写权限，即：该组用户都具有读写权限
> 
> d. 启动svn
> 
```
svnserve -d -r /Users/apple/svn或者svnserve -d -r /Users/apple/svn/mycode
没有任何提示说明启动成功
```

#### 操作svn与本地代码常用命令
1. [参考链接](http://www.cnblogs.com/luckythan/p/4478706.html)
2. 举例说明：将新智选远程库内容拉到本地：

```
利用svn checkout path其中path是服务器上的目录；
svn checkout https://corp.youdao.com/svn/ydstatic/dsp_website/NewZhiXuan/
```


