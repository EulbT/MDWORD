

“正是生活中那些细微的感受
才让我们生活的这个世界如此真实”
                                       				  -------葵的精神世界







# Mac

打开 “系统偏好设置” – “安全性与隐私” – “允许从以下位置下 载的 App:”，可以看到勾选已经变成了“任何来源”

```
sudo spctl --master-disable
```

查看系统cpu架构

```
uname -a 或者 uname -a|awk -F""'{print$(NF-1)}'
```



### 环境变量

在Mac系统中，你可以通过以下步骤来配置环境变量：

1. 打开终端。

2. 输入以下命令以打开bash配置文件：`open -e ~/.bash_profile`或 `open -e ~/.zshrc`。需要注意的是，bash和zsh是两种常见的shell，你可以根据自己使用的shell选择对应的文件进行编辑。

3. 在打开的文件中添加环境变量，例如：

   ```.zshrc
   export JAVA_HOME=/Users/foo/workapps/jdk12/jdk-11.0.2.jdk/Contents/Home   #指定一个JAVA_HOME的路径
   export PATH=$JAVA_HOME/bin:$PATH    #将刚刚指定的路径追加到环境变量的路径中
   ```

   再执行echo $PATH查看 如下

   ```shell
   /Users/foo/workapps/jdk11/jdk-11.0.2.jdk/Contents/Home/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
   ```

   > 这里，“export”是用于导出环境变量的命令，“PATH”是环境变量的名称，“$PATH”表示当前路径，“：”是用来分隔多个路径的符号，“/usr/local/mysql/bin”则是要添加的新路径。

4. 保存并关闭文件。

5. 输入以下命令使修改生效：`source ~/.bash_profile`或 `source ~/.zshrc`。这样，你的环境变量就配置成功了。

   

~~**另一种方法（软连接）**#不可行，这个只能配置单个软件启动的情况，前面的方法是把bin下面的所有执行文件都配进去了~~

>~~思路：~~
>
>~~echo $PATH展示出所有的环境变量路径，也可以自己添加一个常用路径（export）~~
>
>~~以`/usr/local/bin`为例：sudo ln -s  /opt/apps/java/home/bin  /usr/local/bin/java~~
>~~这样就在`/usr/loacl/bin`创建了一个软连接（这个软连接就在PATH里）~~

### 卸载Mysql

```
执行下列命令
sudo rm /usr/local/mysql
sudo rm -rf /usr/local/mysql*
sudo rm -rf /Library/StartupItems/MySQLCOM
sudo rm -rf /Library/PreferencePanes/My*
rm -rf ~/Library/PreferencePanes/My*
sudo rm -rf /Library/Receipts/mysql*
sudo rm -rf /Library/Receipts/MySQL*
sudo rm -rf /var/db/receipts/com.mysql.*
其实不同的安装方式有些东西的存储位置不一样，删除完检查一下下面这些文件是否删除了，没有的话则删除掉：
/usr/local/Cellar 里的mysql文件
/usr/local/var 里的mysql文件
/tmp 里的mysql.sock, mysql.sock.lock, my.cnf文件
pid文件和err文件都在/usr/local/var/mysql里确保删除了
brew安装的安装包存储在/usr/local/Library/Cache/Homebrew也可以一并删除
执行brew cleanup
```



# Linux

### 系统配置指令

本地host文件

```
sudo vim /etc/hosts

#（举例）在127.0.0.1 localhost下面添加：
199.232.69.194          github.global.ssl.fastly.net
140.82.113.3            github.com
185.199.108.154         github.githubassets.com 

#重启 systemd-resolved 服务，刷新DNS：
sudo systemctl restart systemd-resolved
```



### 一些指令

**ssh**指令

SSH（Secure Shell）是一种用于在网络上安全传输数据的协议，同时也是一种通过加密通道进行网络连接的工具。在命令行中，使用 `ssh` 命令可以建立 SSH 连接，并在远程主机上执行命令。以下是 `ssh` 命令的基本用法：

```
bashCopy code
ssh [选项] [用户名@]主机 [命令]
```

- **选项**：`ssh` 命令有许多选项，以下是一些常用选项的简要说明：
  - `-l 用户名`：指定登录的用户名。
  - `-p 端口号`：指定 SSH 服务的端口号，默认是 22。
  - `-i 密钥文件`：指定用于身份验证的私钥文件。
  - `-A`：启用身份验证代理连接。
- **用户名@主机**：指定远程主机的用户名和主机名（或IP地址）。
- **命令**：如果提供了命令，`ssh` 将在远程主机上执行该命令而不是交互式登录。

以下是一些示例：

1. **基本的 SSH 登录**：

   ```
   bashCopy code
   ssh 用户名@主机
   ```

   例如：

   ```
   bashCopy code
   ssh john@example.com
   ```

2. **指定端口号**：

   ```
   bashCopy code
   ssh -p 2222 用户名@主机
   ```

3. **使用身份验证代理**：

   ```
   bashCopy code
   ssh -A 用户名@主机
   ```

4. **执行远程命令**：

   ```
   bashCopy code
   ssh 用户名@主机 "ls -l"
   ```

   这将连接到远程主机并执行 `ls -l` 命令。

5. **使用私钥文件**：

   ```
   bashCopy code
   ssh -i /path/to/private-key 用户名@主机
   ```

**网络** 

查看网络统计信息

```
netstat -ntlp | grep <port>

-n: 表示以数字形式显示地址和端口号，而不进行域名解析。
-t: 表示显示 TCP 协议的连接。
-l: 表示显示监听状态的连接。
-p: 表示显示与连接关联的进程/程序的名称。
```

进程中包含特定项目名的进程信息

```
ps aux | grep <项目名>

a: 显示终端上的所有进程，而不仅仅是与当前用户关联的进程。
u: 以用户为主的格式显示进程详细信息，包括用户、CPU 占用等。
x: 显示没有控制终端的进程
```

192.168.113.0/24

```
在 IPv4 地址中，每个 IP 地址由 32 位二进制数字组成，而 /24 表示网络前缀的长度为 24 位，剩余的 8 位用于主机部分。这意味着 192.168.113.0/24 包含了 192.168.113.0 到 192.168.113.255 这个范围内的所有 IP 地址。
```



**查看磁盘**

```
df -h
```



**解压tar**

```
tar -zxvf file.tar.gz 	#-z表示使用gzip压缩算法进行解压缩，-x表示解压，-v表示显示详细信息，-f表示指定要解压的文件名。
```

**远程数据传输**

```
curl https://511.org/511.yaml -0 	#-0 是一个命令行选项，用于在传输数据时禁用进度条。它通常与 -s（静默模式）选项一起使用，以便在执行 curl 命令时不显示任何输出。
```

**访问地址**

```
curl 10.10.101.140:31050
```

**文本替换**

```
sed -i 's/old-text/new-text/g' <filename>
```

>其中，"s/old-text/new-text/g" 是要执行的替换操作的模式。它将查找文件中的所有匹配项，并将它们替换为 "new-text"。"g" 表示全局搜索和替换，即在整个文件中进行替换操作。
>
>请注意，在使用 `sed -i` 命令时要小心，因为它会直接修改文件内容而不会创建备份。如果您想保留原始文件的副本，请先创建一个备份或使用其他方法来保护文件。

复制命令scp&cp

```
cp  [OPTIONS]  SOURCE  DEST

cp [OPTIONS]  SOURCE... DIRECTORY

scp [参数] [原路径] [目标路径]``
```

> `scp`和`cp`都是用于文件复制的命令，但是他们的作用和使用场景有所不同。`cp`命令主要用于在同一台电脑上，在不同的目录之间来回copy文件。而`scp`命令主要用于在不同的Linux系统之间来回copy文件，它是基于ssh登陆进行安全的远程文件拷贝命令，其传输过程是加密的。
>
> 在使用上，`cp`的基本格式为：`cp [OPTIONS] SOURCE DEST`，即从源路径复制文件到目的路径；`cp [OPTIONS] SOURCE... DIRECTORY`则是将多个源文件复制到指定的目录（多个源文件用空格分隔）。而`scp`的使用格式为：`scp [参数] [原路径] [目标路径]`。

 查找正在运行的进程

```
ps aux
```

> 其中，“ps” 是 "Process Status" 的缩写，用于显示当前进程的状态，类似于 Windows 系统的任务管理器。这个命令提供了一次性的查看，它所提供的查看结果并不动态连续的。
>
> 具体来说，"ps aux" 会显示所有用户的进程信息，包括 PID（进程 ID）、TTY（终端类型）、TIME（CPU 时间）和 CMD（命令名）等。而 "grep" 是一个文本搜索工具，用于在文本中查找包含指定字符串的行。通过将这两个命令组合在一起，可以搜索包含特定字符串的进程信息。例如，要查找名为 "python" 的进程，可以使用以下命令：`ps aux | grep python`。



### ubuntu安装软件

+ **情况1：DEB**

下载 ".deb" 格式的文件(官网下载)后可以直接用安装

```
sudo dpkg -i <filename>.deb		#-i为指定需要安装的软件名
```

卸载

```
// 只是卸载，保留配置
sudo dpkg --remove code
// 彻底卸载，包括配置也一起卸载
sudo dpkg --purge code
```

+ **情况2：PPA**

添加到repository再进行安装

```
sudo add-apt-repository ppa:ubuntu-desktop/ubuntu-make
sudo apt-get update
sudo apt-get install ubuntu-make
umake ide visual-studio-code	 #该步需要指定安装路径choose installation path,再根据提示输入a即可安装完成
```

+ **情况3：APT**

安装

```
sudo apt-get update
sudo apt-get -y install meshlab 	#用于在 Linux 系统中安装 MeshLab，-y表示默认全yes
```

卸载

```
sudo apt-get remove 软件包名	 #卸载软件
sudo apt-get autoremove 软件包名 	#卸载软件并移除软件依赖
sudo apt-get remove 软件包名 --purge 	#卸载软件并 --purge删除配置文件
```

+ **情况4：MSI**

安装wine

```
sudo dpkg --add-architecture i386
sudo apt update
sudo apt install wine64 wine32
wine --version
```

安装msi软件

```
wine msiexec /i 软件名.msi
```



---



### **桌面图标创建**

**(以创建v2ray为例)**

1.设置可执行程序

```
sudo chmod +x Qv2ray-v2.7.0-linux-x64.AppImage 	#这个是需要运行的文件
```

2.查看系统变量

    echo $PATH
    # 输出如下
    /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
    #这个输出表示当前用户的 PATH 环境变量的值。PATH 是一个环境变量，用于指定系统在查找可执行文件时应该搜索的目录列表。在这个例子中，PATH 包含了多个目录，这些目录按照顺序排列，系统会在这些目录中查找可执行文件。

3.在其中一个常用的环境路径下创建软连接 “/usr/local/bin/”

    sudo ln -s /opt/apps/Qv2ray/Qv2ray-v2.7.0-linux-x64.AppImage /usr/local/bin/qv2ray

>另一种做法：
>也可以不创建这个环境变量，直接在下面的.desktop文件中的Exec=qv2ray 修改为 Exec=/opt/apps/Qv2ray/Qv2ray-v2.7.0-linux-x64.AppImage #具体路径，避开你软连接的方式

4.创建快捷图标

+ 自行下载桌面图标，图片格式任意，如：png，jpg等

    sudo vim /usr/share/applications/qv2ray.desktop

添加内容：

>[Desktop Entry]
>Encoding=UTF-8  	# 编码格式为 UTF-8
>Name=Qv2ray 	 # 应用程序的名称为 Qv2ray
>Exec=qv2ray   	# 执行命令为 qv2ray
>Icon=/opt/apps/Qv2ray/v2ray.png  	# 图标文件路径为 /opt/apps/Qv2ray/v2ray.png
>Terminal=false  	# 不使用终端窗口运行应用程序
>Type=Application  	# 应用程序类型为 Application
>Categories=Internet 	 # 应用程序属于 Internet 类别

