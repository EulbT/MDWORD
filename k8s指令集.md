## k8s指令集

> + Kubeadm:
>
> ```
> kubeadm init: 初始化一个新的Kubernetes集群。
> kubeadm join: 将新的节点加入到现有的Kubernetes集群中。
> kubeadm reset: 重置Kubernetes集群，删除所有相关数据并准备重新部署。
> kubeadm upgrade: 升级Kubernetes集群中的控制平面组件。
> ```
>
> + Kubelet:
>
> ```
> kubelet --help: 显示kubelet的用法信息。
> kubelet --version: 显示kubelet的版本信息。
> kubelet --config=: 指定kubelet配置文件的路径。
> kubelet --pod-cidr=: 设置Pod使用的CIDR地址范围。
> kubelet --resolv-conf=: 指定kubelet使用的DNS配置文件路径。
> ```



**获取全部节点信息**

```
kubectl get notes/no
```

**获取 集群中所有组件的状态信息**

```
kubectl get componentstatus/cs
```

**获取pods**

```
kubectl get pods
```

**获得pods的详细信息**

```
kubectl get po -o wide 	#-o 按指定格式输出，将以宽格式显示当前命名空间中的所有Pod及其详细信息
```

>$ kubectl get services                          # 列出所有 namespace 中的所有 service
>$ kubectl get pods --all-namespaces             # 列出所有 namespace 中的所有 pod
>$ kubectl get pods -o wide                      # 列出所有 pod 并显示详细信息
>$ kubectl get deployment my-dep                 # 列出指定 deployment
>$ kubectl get pods --include-uninitialized      # 列出该 namespace 中的所有 pod 包括未初始化的
>
>格式化输出
>
>- 输出 json 格式
>
> - -o json
>
>- 仅打印资源名称
>
> - -o name
>
>- 以纯文本格式输出所有信息
>
> - -o wide
>
>- 输出 yaml 格式
>
> - -o yaml

**获取 集群中名为 "kube-system" 命名空间下的所有 Pod**

```
kubectl  get pods/po -n kube-system	 #-n表示名字为“kube-system”的集群
```

**聚合文件pull**

```
kubectl apply -f calico.yaml
#calico.yaml (配置文件)文件应该包含 Calico 网络插件的配置信息，以便正确设置网络策略和路由规则。
```

**查看cpu和内存的都使用量**

```
kubectl top -h    #通常 -h 选项用于显示帮助信息。
```

**查看节点详细信息**

```
kubectl describe pods/po lvm-lvm-csi-plugin-provisioner-788584dc6c-82x4w -n kube-sysytem
```

**获得pod和serice的信息**

```
kubectl get pod,svc -n zeus 	#获得名为zeus的命名空间的
```

**创建一个Nginx服务**

```
kubectl create deployment nginx --image=nginx
```

**为上一步的nginx创建一个service暴露他的80端口**

```
kubectl expose deployment nginx --port=80 --type=NodePort
```

**查询k8s集群的信息（比如：IP地址或主机名）**

```
kubectl cluster-info
```

复制deployment控制的nginx到3个

```
scale deploy nginx --replicas=3 
```

查看ReplicaSet状态

```
kubectl get replicaset/rs
```

更新原来的yaml文件配置

```
kubectl replace -f nginx-replicaset.yml
```

临时对pod进行伸缩处理

```
kubectl scale --replicas=5 -f nginx-replicaset.ymal		#-f 应用到那个配置文件上 
```

获取Pod策略列表

```
kubectl get psp 	#psp 是 Pod Security Policy（Pod安全策略）的缩写
```

修改Pod策略列表

```
kubectl edit psp permissive
```

删除pod安全策略

```
kubectl delete psp permissive
```

通过筛选label搜索pod

```
kubectl get pods -l app=logging  #--selector=app=logging
```

通过筛选搜索node

```
kubectl get pods -o=spec.nodeName=<Node名称>   #--field-selector spec.nodeName=<Node名称>
```

更新资源

```
kebectl replace -f nginx-deploy.yaml
```

获取pod的helm信息

```
helm get values -n middleware pg-test3 -a | less   #-a就是all 获取所有release的配置    
```

进入容器

```
#pod单容器
kubectl exec -it -n namespace pod-name bash
#pod多容器
kubectl exec -it -n namespace pod-name -c pod-container-1 --bash
```

进入容器将容器内的值输出

```
kubectl exec -it pod-name --sh -c 'cat /etc/nginx/conf/nginx.conf'
```

启动容器时执行的命令是停止 Nginx（以非守护进程方式）并让容器休眠 3600 秒

```
command: ["/bin/sh", "-c", "nginx -g 'daemon off;' && sleep 3600"]
```

如果想看系统支持哪些 GVK

```
k api-resources
```

打印pod日志

```
#添加 -f 选项可以实时跟踪（follow）日志输出，类似于 tail -f 命令
kubectl logs -f <pod-name> 

#通过标签选择器
kubectl logs -l <label-selector>    

｜ tail/head -n 100  #后/前100行
```







**Node**

禁止 Pod 调度到该节点上。

```bash
kubectl cordon <node>
```

驱逐该节点上的所有 Pod。

```bash
kubectl drain <node>
```

>该命令会删除该节点上的所有 Pod（DaemonSet 除外），在其他 node 上重新启动它们，通常该节点需要维护时使用该命令。直接使用该命令会自动调用`kubectl cordon <node>`命令。当该节点维护完成，启动了 kubelet 后，再使用`kubectl uncordon <node>` 即可将该节点添加到 kubernetes 集群中。

创建一个容器在终端运行，重命名为dns-test 重启策略为Never 用完之后rm。 创建好直接进入目录/bin/sh

```
kubectl run -it --image busybox dns-test --restart=Never --rm /bin/sh
```

为node上添加一个标签

```
kubectl label node k8s-node1 type=microservices
```

查看node上标签

```
kubectl get nodes --show-labels
```

给node上添加污点

```
kubectl taint node k8s-node2 memory=low:NoSchedule
#内存低的不会调度到该节点上，NoSchedule原来已经在的不会驱逐; NoExecute原来节点也会驱逐走
```

给node去掉污点

```
kubectl taint node k8s-node2 memory=low:NoSchedule-
```



**configmap**

创建一个configmap

```
#一个目录下全部加入
kubectl create configmap name --from-file=/opt/abc/

#单个文件，可添加多个
kubectl create configmap name --from-file=/opt/abc.yaml

#单独添加字段
kubectl create configmap(cm) name --from-literal=username=root --from-literal=password=admin
```

使用--dry--run更新configmap

```
kubectl create configmap name(现有的cm) --from-file=./test/ --dry-run -o ymal | kubectl replace -f-   #管道命令前会输出一个ymal文件 再给后面的replace进行更新，以达到更新cm的作用
#‘-f-’的第二个‘-’是指将前面的输出作为这里的输入
```







### 在 Kubernetes（K8s）中，要从 MySQL 容器中提取文件，可以使用以下几种常见的方法：

### 1. **使用 `kubectl cp` 命令：**

`kubectl cp` 命令允许你将文件复制到或从容器中。以下是一个示例：

```

kubectl cp <namespace>/<pod_name>:/path/to/file /local/path
```

- `<namespace>`：MySQL 所在的命名空间。
- `<pod_name>`：MySQL 容器所在的 Pod 名称。
- `/path/to/file`：MySQL 容器中的文件路径。
- `/local/path`：将文件复制到本地的目标路径。

示例：

```

kubectl cp default/mysql-pod:/var/lib/mysql/data/myfile.txt /tmp/myfile.txt
```

### 2. **使用 `kubectl exec` 命令和 `tar` 命令：**

如果要从容器中提取多个文件或整个目录，可以使用 `tar` 命令打包文件，然后通过 `kubectl exec` 将打包文件的内容输出到本地。以下是一个示例：

```

kubectl exec -it <namespace>/<pod_name> -- tar cf - -C /path/to/source . | tar xf - -C /local/path
```

示例：

```

kubectl exec -it default/mysql-pod -- tar cf - -C /var/lib/mysql/data . | tar xf - -C /tmp
```

这将在 MySQL 容器中创建一个 tar 存档，然后将其解压到本地目录。

请确保你有足够的权限访问 MySQL 容器中的文件，并替换示例中的命名空间、Pod 名称、路径等为你实际使用的值。

