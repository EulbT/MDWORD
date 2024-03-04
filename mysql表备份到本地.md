### 在 Kubernetes（K8s）中，要从 MySQL 容器中提取文件，可以使用以下几种常见的方法：

mysql服务器->k8s集群空间->本地

#### 1.  mysql服务器->k8s集群空间

从mysql中备份表文件,会保存在当前文件夹中 ls

```
mysqldump -uroot -pZeuS@Middleware01 -h127.0.0.1 -P3306  middleware_platform operation_audit --set-gtid-pu
rged=OFF --result-file=/zeus-2024_01_08_11_22_04-dump.sql
```

再将文件移动到k8s集群环境中

方法1  kubectl cp

```
kubectl cp default/mysql-pod:/var/lib/mysql/data/myfile.txt /tmp/myfile.txt -c mysql    #-c mysql指定容器
```

方法2。**使用 `kubectl exec` 命令和 `tar` 命令**

```
kubectl exec -it 命名空间/pod名字 -- tar cf - -C /var/lib/mysql/data . | tar xf - -C /tmp   

#-it终端打开   
#tar xf - -C /local/path: 在本地机器上使用 tar 命令解压标准输入流中的 tar 存档内容，-C 用于指定解压缩目标路径。
```

#### 2.  k8s集群空间->本地

在服务器上下载文件通常可以使用 `curl` 或 `wget` 命令。以下是这两个命令的用法示例：

使用 `curl`

```
curl [options] URL
```

这将从指定的 URL 下载文件并保存到当前工作目录。例如：

```
curl -# -O https://example.com/file.txt  #使用 -O 选项表示将文件保存为与远程文件名相同的本地文件名。-#下载速度 
```

**使用 `wget`**

```
wget URL
```

这也将从指定的 URL 下载文件并保存到当前工作目录。例如：

```
wget https://example.com/file.txt
```

如果你需要指定文件的保存路径，可以在命令后面添加保存路径：

```
wget -O /path/to/save/file.txt https://example.com/file.txt
```

**需要登入用scp**

```
scp username@remote_server:/path/to/remote/file /path/to/local/directory
```

3. ####  数据库表导入本地

```
mysql -u username -p -h hostname database_name < filename.sql
```



