# **Kubernetes environment setup**
###### lijiahao - January 5, 2019
---
1. ***安装Docker***
> sudo apt-get install -y docker.io
 - Docker版本：18.06.1-ce

2. ***安装Kubectl***
> sudo snap install kubectl --classic
- 将kubectl作为一个snap app安装
> 查看kubectl version：kubectl version
- Client Version: version.Info{Major:"1", Minor:"13", GitVersion:"v1.13.1", GitCommit:"eec55b9ba98609a46fee712359c7b5b365bdd920", GitTreeState:"clean", BuildDate:"2018-12-13T10:39:04Z", GoVersion:"go1.11.2", Compiler:"gc", Platform:"linux/amd64"}
- Server Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.0", GitCommit:"fc32d2f3698e36b93322a3465f63a14e9f0eaead", GitTreeState:"clean", BuildDate:"2018-03-26T16:44:10Z", GoVersion:"go1.9.3", Compiler:"gc", Platform:"linux/amd64"}
  
3. ***安装Minikube***
> 通过阿里云镜像下载：curl -Lo minikube http://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/releases/v0.28.0/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
- 注：不通过镜像可能下载速度很慢或无法下载
> 检验minikube是否安装成功：minikube -h

4. ***启动Minikube***
     1. 加入一个阿里云镜像:
        > minikube start --registry-mirror=https://registry.docker-cn.com

     2. 若出现这样的错误,则需要先安装VirtualBox：
        > E0106 11:10:00.035369   10474 start.go:150] Error starting host: Error creating host: Error executing step: Running precreate checks.
: VBoxManage not found. Make sure VirtualBox is installed and VBoxManage is in the path.
 Retrying.
E0106 14:10:00.035780   10474 start.go:156] Error starting host:  Error creating host: Error executing step: Running precreate checks.
: VBoxManage not found. Make sure VirtualBox is installed and VBoxManage is in the path
================================================================================
An error has occurred. Would you like to opt in to sending anonymized crash
information to minikube to help prevent future errors?
To opt out of these messages, run the command:
    minikube config set WantReportErrorPrompt false
================================================================================
Please enter your response [Y/n]:

     3. 安装VirtualBox：
        > sudo apt-get install virtualbox
     4. 不设置虚拟机
        > sudo minikube delete
        > sudo minikube start --vm-driver=none
     5. 若在Linux下成功执行sudo minikube start --vm-driver=none，terminal会出现类似以下提示
        > tarting local Kubernetes v1.10.0 cluster...
Starting VM...
Getting VM IP address...
Moving files into cluster...
Setting up certs...
Connecting to cluster...
Setting up kubeconfig...
Starting cluster components...
Kubectl is now configured to use the cluster.
===================
WARNING: IT IS RECOMMENDED NOT TO RUN THE NONE DRIVER ON PERSONAL WORKSTATIONS
	The 'none' driver will run an insecure kubernetes apiserver as root that may leave the host vulnerable to CSRF attacks
When using the none driver, the kubectl config and credentials generated will be root owned and will appear in the root home directory.
You will need to move the files to the appropriate location and then set the correct permissions.  An example of this is below:
	sudo mv /root/.kube $HOME/.kube # this will write over any previous configuration
	sudo chown -R $USER $HOME/.kube
	sudo chgrp -R $USER $HOME/.kube
	sudo mv /root/.minikube $HOME/.minikube # this will write over any previous configuration
	sudo chown -R $USER $HOME/.minikube
	sudo chgrp -R $USER $HOME/.minikube
This can also be done automatically by setting the env var CHANGE_MINIKUBE_NONE_USER=true
Loading cached images from config file.

     6. 启动容器服务
        > sudo kubectl run kube-nginx999 --image=nginx:latest --port=80 --image-pull-policy=IfNotPresent
        - 若成功启动，terminal提示：deployment "kube-nginx999" created
         ![image][https://github.com/TactfulYuu/SE_homework4/blob/master/Part2-Kubernetes/Kubernetes_src/1.png]
     7. 检查状态
        > sudo kubectl get pods
         ![image][https://github.com/TactfulYuu/SE_homework4/blob/master/Part2-Kubernetes/Kubernetes_src/2.png]
     8. 发布服务
        > sudo kubectl expose deployment kube-nginx999 --type=NodePort
        - 若成功发布，terminal会提示：service "kube-nginx999" exposed
     9. 服务地址
        > sudo minikube service kube-nginx999 --url
        - 若容器服务尚未准备完，terminal会提示：Waiting, endpoint for service is not ready yet...，此时耐心等待
        - 准备完成后terminal会显示服务地址：http://10.0.2.15:30414
         ![image][https://github.com/TactfulYuu/SE_homework4/blob/master/Part2-Kubernetes/Kubernetes_src/3.png]
     10. 访问step9中得到的地址，出现了nginx的首页，代表service成功启动
         ![image][https://github.com/TactfulYuu/SE_homework4/blob/master/Part2-Kubernetes/Kubernetes_src/4.png]

     11. 启动dashboard
        > sudo minikube dashboard --url
        - terminal会显示dashboard的地址
        - 在浏览器中访问该地址即可打开dashboard
         ![image][https://github.com/TactfulYuu/SE_homework4/blob/master/Part2-Kubernetes/Kubernetes_src/5.png]
1. ***通过kubectl进行控制查看***
    1. 查看所有的节点
        > sudo kubectl get pods --all-namespaces
         ![image][https://github.com/TactfulYuu/SE_homework4/blob/master/Part2-Kubernetes/Kubernetes_src/6.png]
    2. 查看client和server的version
        > sudo kubectl version
         ![image][https://github.com/TactfulYuu/SE_homework4/blob/master/Part2-Kubernetes/Kubernetes_src/7.png]
    3. 查看所有的cs的状态
        > sudo kubectl get cs
         ![image][https://github.com/TactfulYuu/SE_homework4/blob/master/Part2-Kubernetes/Kubernetes_src/8.png]

