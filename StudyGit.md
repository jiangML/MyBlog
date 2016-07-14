##Git 学习笔记

#### 昨天在公司准备把本地的代码push到github的时候出现错误，居然push不上去。
<br>
<img src="https://github.com/jiangML/MyBlog/blob/master/img/git_error.png" width=400 height=150/>

#####然后google了一下，这个原因是公司的ssh的端口22禁用了。所以要改一下端口。
#####打开git bash命令界面。然后输入命令 cd ~/.ssh然后就会切换路径到放置ssh密钥和公钥的目录下。
#####然后用ls命令查看该目录下是否有config文件，如果没有就输入touch config命令创建config文件，然后在用vim config命令进入config文件，输入如下内容。
host github.com<br>
hostname ssh.github.com<br>
port 443<br>
#####然后在git里面输入命令 ssh -T  git@github.com,执行命令后，会出现“Are you sure you want to continue connecting(yes/no)? ”然后输入yes后出现成功提示即可。
#####现在就修改了端口号为443，现在也就可以pus项目到github了。


