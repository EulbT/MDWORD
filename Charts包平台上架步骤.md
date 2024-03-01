# Charts包平台上架步骤

1. 做好charts包
2. 上传压缩包到140环境利用docker将二进制包构建成镜像包

```
[root@10 fone]# docker load -i aas.gz 
f1b5933fe4b5: Loading layer  5.796MB/5.796MB
9b9b7f3d56a0: Loading layer  3.584kB/3.584kB
ceaf9e1ebef5: Loading layer  100.2MB/100.2MB
b11719eb1776: Loading layer  107.8MB/107.8MB
90d4f68853db: Loading layer  12.29kB/12.29kB
5f70bf18a086: Loading layer  1.024kB/1.024kB
1529c14e45d2: Loading layer  469.1MB/469.1MB
ef2a209e3867: Loading layer  578.1MB/578.1MB
Loaded image: harbor.apusic.com/apusic/aas-server:v10.0.0-rc2
```

3.对镜像包进行重命名

```
docker tag harbor.apusic.com/apusic/aas-server:v10.0.0-rc2  10.10.102.124/middleware/aas-server:v10.0.0-rc2
```

4.推送包到镜像仓库

```
docker push 10.10.102.124/middleware/aas-server:v10.0.0-rc2
```

5.打包做好的charts包

```
helm package ass/
Successfully packaged chart and saved it to: /Users/apple/Desktop/aas-1.1.1-rc2.tgz

```

6.去平台市场管理上架打包好的charts包



区域集群对应表

| id(主键，自增长) | area_id(区域id) | area_name(区域名称) | cluster_id(集群id) | description(区域描述) |
| ---------------- | --------------- | ------------------- | ------------------ | --------------------- |
| 1                | 234234231       | 华北地区            | 123123             | 地区描述              |
| 2                | 353452342       | 东北地区            | 123123             | 地区描述              |
|                  |                 |                     |                    |                       |

