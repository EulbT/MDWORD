# Kubernetes

![image-20231120145421882](/Users/apple/Library/Application Support/typora-user-images/image-20231120145421882.png)

### 下面是一些资源名的对比

**声明式操作(Declarative)和指令式操作(Imperative)**
是编程中的两种范式，它们在处理问题和编写代码时有着不同的关注点和实现方式。

> 1. **声明式编程**：这种编程方式主要关注“我想要什么”，而不具体关心怎么实现。它告诉计算机系统你想要得到什么结果，但具体如何达到这个结果是计算机自己决定的。例如，声明式编程可能会描述：“我想要所有的数字都加起来”，而具体的加法过程则由计算机系统自行完成。这种方式更接近人类语言的表达方式，因为它描述了最终的目标，而不是具体的步骤。
>
> 2. **指令式编程**：与声明式编程相反，命令式编程关注“让计算机应该如何做”。它明确地告诉计算机每一步的具体操作，包括数据的移动、计算等。计算机会严格遵循这些指令来完成任务。例如，命令式编程可能会详细描述每一个加法步骤，从而得到最终的总和。这种方式更接近于计算机语言，因为它直接给出了每一步的操作指令。
>
> 从另一个角度看，函数式编程（Functional Programming）也被视为一种声明式的编程方式，因为它不使用可变状态，并且不需要指定任何的执行顺序关系。这意味着函数式编程中的函数都是独立的，不依赖于外部的状态或数据。

**StatefulSet和ReplicaSet/Deployment**
都是Kubernetes中的控制器，用于管理Pod的生命周期。然而，它们在处理有状态应用和无状态应用方面有着显著的不同。

>1. **ReplicaSet**：可以视为Replication Controller的增强版，主要协调创建、删除和更新Pod。与Replication Controller的主要区别在于，ReplicaSet支持灵活的标签选择器，而不仅仅是一个标签。这种集合式的标签选择器使得滚动更新成为可能。
>
>2. **StatefulSet**：是在Deployment的基础上扩展出来的控制器，特别是在Kubernetes 1.9版本之后加入的。StatefulSet是为有状态应用设计的，它考虑到了有状态应用的两个关键特性：拓扑状态和存储状态。
>
> - **拓扑状态**：这意味着应用的多个实例之间不是完全对等的关系。这些实例必须按照特定的顺序启动，例如主节点必须先于从节点启动。如果删除了某些Pod，再次创建时也必须保持这种顺序。
> - **存储状态**：这涉及到应用的多个实例绑定到不同的存储数据。
>
> StatefulSet为有状态应用的Pod提供了稳定的网络标识，这使得应用内部通信和数据交换更加简单和可靠。相比之下，Deployment更适合无状态的应用，这些应用的Pod之间没有固定的顺序，所有Pod共享存储，并且Pod的名字通常包含随机数字。
>
>总的来说，
>**有状态**的并且需要保持稳定的网络标识和特定顺序的启动-->**StatefulSet**是更好的选择。
>而对于**无状态**的应用-->**ReplicaSet或Deployment**可能更为合适。

**Replication Controller（RC）**

> 部署和升级Pod，声明某种Pod的副本数量在任意时刻都符合某个预期值；
> Pod期待的副本数；
> 用于筛选目标Pod的Label Selector；
> 当Pod副本数量小于预期数量的时候，用于创建新Pod的Pod模板（template）

**Deployment和ReplicaSet**

Deployment相较于ReplicaSet的主要优势包括以下几点：

1. **声明式更新**：Deployment支持声明式更新，这意味着在进行应用程序升级或扩展等操作时，可以明确地描述期望的状态，而不会丢失任何历史变更。这种方式使得Pod的管理变得更加简单和直观。

2. **管理ReplicaSet**：Deployment控制器不直接管理Pod对象，而是由Deployment管理ReplicaSet，再由ReplicaSet负责管理Pod对象。这种层级结构使得管理更加清晰。

3. **更强大的选择器功能**：相对于ReplicaSet只能支持基于等式的selector，如env=dev或者environment!=qa，Deployment引入了新的基于集合的selector，例如version in (v1.0,v2.0)或者env not in (dev,qa)。这为Pod的选择和管理提供了更大的灵活性。

4. **保证运行状态**：只有在容器能保证自己始终是Running状态的前提下，ReplicaSet调整Pod的个数才有意义。因此，Deployment只允许容器的restartPolicy设置为Always。

5. **水平扩展**：通过“控制器模式”，Deployment可以操作ReplicaSet的个数和属性，进而实现应用的水平扩展。

> 在Kubernetes中，Deployment和ReplicaSet是两种核心的资源对象，它们之间存在密切的关系。Deployment是一种声明式的定义方法，**用于描述期望的运行状态**，如Pod的副本数等信息。当用户定义了一个Deployment后，Kubernetes的Deployment控制器会按照一定的控制速率将实际状态改成期望状态。
>
> 而ReplicaSet则是一个控制器模式的对象，它的主要功能是确保系统中指定数量的Pod副本始终在运行，并处理Pod的创建、删除和更新等操作。更具体的说，每一个由Deployment管理的Pod都会被一个ReplicaSet管理，以保证Pod的正常运行。
>
> 因此，我们可以将Deployment和ReplicaSet看作是一种“层层控制”的关系。通过Deployment，用户可以方便地管理多个ReplicaSets和Pods，使得整个系统的管理和运维变得更加简单和高效。
>
> **Replica Set**
>
> > 下一代的Replication Controlle，Replication Controlle只支持基于等式的selector（env=dev或environment!=qa）但Replica Set还支持新的、基于集合的selector（version in (v1.0, v2.0)或env notin (dev, qa)），这对复杂的运维管理带来很大方便。
>
> **Deployment**
>
> > 拥有更加灵活强大的升级、回滚功能。在新的版本中，**官方推荐使用Replica Set和Deployment来代替RC，**两者相似度>90%，相对于RC一个最大升级是我们随时指导当前Pod“部署”的进度。Deployment使用了Replica Set，除非需要自定义升级功能或根本不需要升级Pod，一般情况下，我们推荐使用**Deployment而不直接使用Replica Set**；
> > 典型使用场景：
> >
> > + 创建一个Deployment对象来生成对应的Replica Set并完成Pod副本的创建过程；
> > + 检查更新Deployment的状态来查看部署动作是否完成（Pod副本的数量是否达到预期的值）；
> > + 更新Deployment以创建新的Pod；（比如镜像升级）
> >   如果当前Deployment不稳定，则回滚到一个早先的Deployment版本；
> >   挂起或者回复一个Deployment；

**Horizontal Pod Autoscaler（HPA）**
意思是Pod横向自动扩容，目标是实现自动化、智能化扩容或缩容。

> Pod负载度量指标：
> CPUUtilizationPercentage
> 通常使用一分钟内的平均值，可以通过Heapster扩展组件获取这个值。一个Pod自身的CPU利用率是该Pod当前CPU的使用量除以它的Pod Request的值。例如Pod Request定义值为0.4，当前Pod使用量为0.2，则它的CPU使用率为50%。但如果没有定义Pod Request值，则无法使用CPUUtilizationP

**静态pod和普通pod**

> 静态Pod与普通Pod的主要区别体现在定义位置和生命周期管理上。静态Pod的定义文件位于kubelet所在节点的本地文件系统中，而普通Pod的定义文件一般是通过API Server提交到Kubernetes集群中。在生命周期管理方面，静态Pod由kubelet直接管理，不受Kubernetes控制平面的影响，只在其部署节点上运行；而普通Pod由Kubernetes控制平面管理，包括调度、监控和重启等。另外，值得注意的是，kubelet会为每个它管理的静态Pod，调用api-server在 Kubernetes 的 apiserver 上创建一个镜像Pod（Mirror Pod），因此我们可以在 apiserver 中查询到该 Pod。静态Pod始终绑定在某个kubelet，并且始终在同一个节点上运行。

**kubelet**

> Kubelet是在每个节点上运行的主要“节点代理”，它的主要功能是处理Master节点下发到本节点的任务，按照PodSpec描述来管理Pod和其中的容器。Kubelet可以通过API服务器或主机名进行注册。
> 在架构上，Kubelet由多个内部组件构成，包括Kubelet API、syncLoop等。具体来说，Kubelet API包括10250认证API、4194端口的cAdvisor API、10255端口的只读API以及10248端口的健康检查API。syncLoop则负责从API或者manifest目录接收Pod更新，并发送到podWorkers进行处理。

**kubectl和kubelet**
在Kubernetes集群中，kubectl和kubelet是两个相互协作的关键组件。

> Kubectl是一个命令行工具，它允许用户通过命令行与Kubernetes集群进行交互，执行诸如创建、删除、更新Pod和Service等操作。而Kubelet则是运行在每个节点上的代理，主要负责保证容器都运行在Pod中并维护其生命周期。Kubelet定期从kube-apiserver组件接收新的或修改的Pod规范，以确保Pod及其容器按照期望的规范运行。同时，作为工作节点的监控组件，Kubelet会向kube-apiserver汇报主机的运行状况。因此，可以说kubectl和kubelet共同构成了Kubernetes集群的控制平面和数据平面，协同工作以实现集群的管理和维护。

**Kubeadm，kubelet和kubectl**
是Kubernetes中三个核心的工具，它们各有不同的职能和作用。

> Kubeadm是Kubernetes官方推出的一个初始化集群的工具，它可以用来安装单节点，多节点，高可用（HA）等各种Kubernetes集群。需要注意的是，Kubeadm不能帮助你安装或者管理kubelet或kubectl，所以你需要确保它们与通过Kubeadm安装的控制平面的版本相匹配。
>
> Kubelet则是运行在Kubernetes集群所有节点上的一个组件，负责启动Pod和容器等。当有新的Pod被创建时，Kubelet会确保该Pod中的所有容器都被正确运行并监控其运行状态。
>
> 而kubectl是Kubernetes的命令行工具，用户可以通过kubectl来部署和管理应用，查看各种资源，创建，删除和更新组件。简单来说，你可以把kubectl看作是操作Kubernetes集群的用户界面。
>
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

**systemd和systemctl**
之间的关系紧密且互补。

> systemd是Linux系统中的一个初始化程序，负责管理和控制系统的启动过程和后台服务。它的作用不仅限于启动操作系统，还包括接管了许多其他任务，如结束进程、状态查询、日志归档、设备管理、电源管理以及定时任务等。
>
> 而systemctl则是一个命令行工具，它是systemd的一部分，专门用于与systemd进行交互，控制服务的状态，查看服务的状态，重启和重新加载服务等。例如，你可以使用systemctl来启动、停止或重启一个服务，或者管理系统的服务和进程。
>
> 总的来说，systemd提供了一个全面的系统和服务管理器，而systemctl则提供了一个易于使用的命令行界面来操作和管理这些服务和进程。这两者共同



### 资源类型和别名

|          | 可调度最小对象 |            |          |           | 节点：服务器等 |
| -------- | -------------- | ---------- | -------- | --------- | -------------- |
| 全名     | pods           | deployment | services | namespace | nodes          |
| **简写** | **po**         | **deploy** | **svc**  | **ns**    | **no**         |

- `Pod`：它是最小的可部署对象，一个Pod中可以包含一个或多个紧密关联的容器，用于运行某个特定的应用程序或一组相同的应用程序实例。Pod是Kubernetes中的最小调度单元，它可以跨节点分布。
- `Service`：它为多个Pod提供统一的访问入口，外部流量可以通过Service访问到后端的Pod。Service提供了一个单一的IP地址和DNS名称，并负责将流量路由到后端的Pod。例如，如果为Deployment的应用创建了一个名为portal的Service，那么集群内想要通过Http访问这个应用，就可以使用http://portal.
- `Deployment`：它是Kubernetes中的一个Replication Controller的升级版，支持更加灵活的部署策略。一个Deployment可以管理一个或多个Pod，通常用于描述期望的应用状态，比如运行哪些副本的Pod以及如何运行这些Pod。在Deployment的Pod模板中，可以添加对应的标签，并在Service的选择器中指定与Deployment中Pod标签匹配的标签。
- `Node`：在Kubernetes集群中，Node是一个工作节点，它代表一个服务器或者计算机。Node上可以运行多个Pod，并且每个Pod都在该Node上运行。
- `Namespace`：它是对物理Cluster的逻辑划分，可以将一个物理Cluster划分为多个Namespace，每个Namespace也可以认为是一个Cluster。不同的Namespace内的资源是相互隔离的。



### Node 的状态

Node 包括如下状态信息：

- Address
  - HostName：可以被 kubelet 中的 `--hostname-override` 参数替代。
  - ExternalIP：可以被集群外部路由到的 IP 地址。
  - InternalIP：集群内部使用的 IP，集群外部无法访问。
- Condition
  - OutOfDisk：磁盘空间不足时为 `True`
  - Ready：Node controller 40 秒内没有收到 node 的状态报告为 `Unknown`，健康为 `True`，否则为 `False`。
  - MemoryPressure：当 node 有内存压力时为 `True`，否则为 `False`。
  - DiskPressure：当 node 有磁盘压力时为 `True`，否则为 `False`。
- Capacity
  - CPU
  - 内存
  - 可运行的最大 Pod 个数
- Info：节点的一些版本信息，如 OS、kubernetes、docker 等

### **文件目录地址**

**用户认证信息地址**

```
cat ~/.kube/config
```

**在任意节点使用kubectl**

```
#拷贝master节点的admin.conf 到 需要运行的服务器的目录下
scp /etc/kubernetes/admin.conf root@ip地址:/etc/kubernetes/
#在对应服务器上配置环境变量
echo "export KUBECONFIG=/etc/kubernetes/admin.conf" >> ~/.bush_profile
#配置生效
source~/.bash_profile
```

>**`echo`命令用于在终端输出文本或变量的值。在这个例子中，使用`echo`命令将字符串"export KUBECONFIG=/etc/kubernetes/admin.conf"输出到终端，并将其追加到`~/.bush_profile`文件中。这样，每次打开新的终端窗口时，都会自动执行该命令，从而设置环境变量`KUBECONFIG`的值为`/etc/kubernetes/admin.conf`。**









查看log

···

在Kubernetes中，namespace是集群级别的资源，主要用于将集群划分为多个逻辑隔离的区域，以供不同用户、租户、环境或项目使用。例如，可以为开发、QA和生产应用环境分别创建各自的名称空间。同时，Namespace还提供了一种机制，将同一集群中的资源划分为相互隔离的组。

属于Namespace的资源包括：Pods、Services、Replication Controllers、Deployments、ConfigMaps和Secrets以及Network Policies。这些资源的层级关系如下：

- Network Policies：位于层级结构的最上层，用于控制Pod之间的通信。
- Secrets：位于Network Policies之下，用于存储敏感信息。
- ConfigMaps：位于Secrets之下，用于存储配置信息。
- Replication Controllers和Deployments：这两者位于ConfigMaps之下，用于管理Pod的复制数量和版本。其中，RC是早期的资源对象，而Deployment则是后来引入的更为强大的资源对象。
- Services：位于Deployments之下，用于将一组Pod暴露给其他应用程序或服务。
- Pods：位于Services之下，每个Pod都属于一个特定的Namespace。

需要注意的是，Kubernetes的层级命名空间控制器（Hierarchical Namespace Controller，HNC）负责管理这种层级关系，包含两个组件：控制器和策略对象。控制器运行在集群中，用来管理子命名空间，传递策略对象，确保层次结构的合理性，并管理扩展点。



### 配置文件spec

```
apiVersion: v1 	#api 文档版本
kind：pod 	#资源对象类型，也可以配置为像 Deployment、StatefulSet 这类对象
metadata： 	#Pod相关的元数据，用于描述 Pod 的数据
	name：nginx-demo 	# Pod 的名称
	labels： 	#定义Pod的标签
		type：app 	#自定义 lable 标签，名字为 type ，值为 app
		test：1.0.0 	#自定义 lable 标签，描述 Pod 的版本号
	namespace: 'default' 	#命名空间的配置
spec: 	#期望 Pod 按照这里面的描述进行创建
	containers: 	#对于 Pod 中的容器描述景象
	-name: nginx 	#容器的名称
	 image： nginx:1.7.9 	# 指定容器的镜像
	 imagePullPolicy: IfNotPresent 	#镜像拉取策略，
	 command: 	#指定容器启动时执行的命令
	 -nginx
	 -g 	#后台运行
	 -'daemon off;' #nginx -g 'daemon off;'
	 workingDir: /usr/share/nginx/
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

```bash
kubectl apply -f mysql-deployment.yaml
kubectl apply -f mysql-service.yaml

```





---

