<div align="center">

## 自用腾讯ubuntu服务器部署指令

</div>



<details>

<summary><font size="4">安装 SSH</font></summary>

更新软件包列表
```
sudo apt update
```
安装远程服务组件
```
sudo apt install openssh-server
```
本地电脑主机终端使用，创建密匙
```
ssh-keygen
```                           
把本地电脑的 C:\Users\用户名\.ssh\id_rsa.pub 内容复制进服务器的 /.ssh/authorized_keys"

</details>



<details>

<summary><font size="4">设置 root 用户</font></summary>

设置 root 密码
```
sudo passwd root
```
编辑 SSH 配置文件，允许用户通过 root 登录 SSH
```
sudo vim /etc/ssh/sshd_config

PermitRootLogin yes
```
取消密码登录，使用公钥验证
```
sudo vim /etc/ssh/sshd_config

PasswordAuthentication no
```
重启 SSH 服务
```
sudo service ssh restart
```
</details>



<details>

<summary><font size="4">设置 swap</font></summary>

查看 swap 信息
```
sudo swapon --show
```
关闭当前 swap
```
sudo swapoff /swap.img
```
删除旧 swap 文件
```
sudo rm /swap.img
```
创建一个新的 4GB 的 swap 文件
```
sudo fallocate -l 4G /swap.img
```
设置正确的权限
```
sudo chmod 600 /swap.img
```
将新的文件格式化为swap格式
```
sudo mkswap /swap.img
```
启用新的 swap 文件
```
sudo swapon /swap.img
```
查看 /etc/fstab 文件,以便在系统重启时自动启用 swap 文件
```
vim /etc/fstab

/swap.img none swap sw 0 0
``` 
</details>



<details>

<summary><font size="4">设置环境变量</font></summary>

换软件源
```
bash <(curl -sSL https://linuxmirrors.cn/main.sh)
```
[安装 redis](https://redis.io/docs/latest/operate/oss_and_stack/install/install-redis/install-redis-on-linux/)

<details>

<summary>安装必要软件包</summary>

| 软件包 | 作用 |
| ---- | ---- |
| lsb-release | 用于显示 Linux Standard Base (LSB) 和特定版本的信息 |
| curl | 一个命令行工具，用于从或向服务器传输数据 |
| gpg | GNU Privacy Guard，用于加密和签名数据 |

</details>

```
apt-get install lsb-release curl gpg
```
从 Redis 官方网站下载 GPG 密钥
```
curl -fsSL https://packages.redis.io/gpg
```
将下载的 GPG 密钥转换为二进制格式并保存到指定位置
```
sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg
```
设置密钥文件权限
```
sudo chmod 644 /usr/share/keyrings/redis-archive-keyring.gpg
```
添加 Redis 存储库
```
echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list
```
更新软件包列表
```
sudo apt-get update
```
安装 Redis
```
sudo apt-get install redis
```

安装 tmux
```
apt-get install tmux
```
安装 docker
```
bash <(curl -sSL https://linuxmirrors.cn/docker.sh)
```

安装 git
```
apt install git
```

[配置 go](https://golang.google.cn/dl/)
```
tar -zxvf go1.20.14.linux-amd64.tar.gz -C /usr/local/
```
[安装 Nvm](https://nodejs.org/zh-cn/download/package-manager)
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash
```
安装 node.js
```
nvm install 20
```
设置环境变量
```
vim /.bashrc

# nvm
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  ## This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  ## This loads nvm bash_completion
# go
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```
使环境变量更改生效
```
source ~/.bashrc
```

</details>



<details>

<summary><font size="4">napcat 相关</font></summary>

启动[napcat](https://napneko.github.io/zh-CN/guide/getting-started)并保持后台运行
```
tmux new-session -d -s napcat_2036 'xvfb-run -a qq --no-sandbox'
```
后台快速登录
```
tmux new-session -d -s napcat_2036 'xvfb-run -a qq --no-sandbox -q 203627006'
```
进入后台进程
```
tmux attach-session -t napcat_2036
```
</details>



<details>

<summary><font size="4">qsign 相关(8963)</font></summary>

```
sudo docker run -d --restart=always --name qsign_2036 -p 8080:8080 -e BASE_PATH=/srv/qsign/qsign/txlib/8.9.63 xzhouqd/qsign:core-1.1.9
```

</details>



<details>

<summary><font size="4">docker 常用指令</font></summary>

查看docker资源使用情况
```
sudo docker stats
```
容器
```
sudo docker ps -a                     # 查看所有容器
sudo docker rm <容器ID或名称>           # 删除容器
sudo docker start <容器ID或名称>        # 启动容器
sudo docker stop <容器ID或名称>         # 停止容器
sudo docker restart <容器ID或名称>      # 重启容器
sudo docker logs -f <容器ID或名称>      # 查看容器日志
sudo docker exec -it <容器ID或名称> /bin/bash  # 进入容器
```
镜像
```
sudo docker images                    # 查看所有镜像
sudo docker pull <镜像ID或名称>         # 拉取镜像
sudo docker rmi <镜像ID或名称>          # 删除镜像
```
配置 docker 代理
```
sudo mkdir /etc/systemd/system/docker.service.d
sudo vim docker.service.d

[Service]
Environment="HTTP_PROXY=http://127.0.0.1:7890"
Environment="HTTPS_PROXY=http://127.0.0.1:7890"
Environment="NO_PROXY=localhost,127.0.0.1"
```

</details>



<details>

<summary><font size="4">云崽相关</font></summary>

后台启动
```
pnpm start
```
停止后台
```
pnpm stop
```
查看日志
```
pnpm log
```

</details>