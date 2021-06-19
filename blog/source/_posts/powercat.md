# powercat

- powercat是netcat的powershell版本，可以在windows系统上使用

## 一、安装powercat

- [GitHub下载链接](git clone https://github.com/besimorhino/powercat.git)
- 管理员权限打开powershell，进入powercat下载目录，打开README.md文档，按照上面的步骤进行操作，如果显示系统无法加载这种情况：

<!--more-->
```
: 无法加载文件 C:\Users\herol\Documents\powercat\powercat.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 https:/go.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies。
```

- 在powershell输入以下命令（注意要以管理员权限）：

```
Set-ExecutionPolicy RemoteSigned
```

- 输入y即可，接着可以输入powercat -h测试是否安装成功



## 二、使用powercat

- powercat -h 或 vim README.md查看帮助文件 



