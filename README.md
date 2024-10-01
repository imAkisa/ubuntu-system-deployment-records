<div align="center">

## 自用腾讯ubuntu服务器部署指令

</div>

### 基础设置

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

</details>



<details>

<summary><font size="4">设置 root 用户</font></summary>

设置 root 密码
```
passwd root
```
编辑 SSH 配置文件，允许用户通过 root 登录 SSH
```
sudo vim /etc/ssh/sshd_config

PermitRootLogin yes
```
重启 SSH 服务
```
sudo service ssh restart
```
本地电脑终端使用 ```ssh-keygen``` ，创建密匙，把本地电脑的 C:\Users\用户名\.ssh\id_rsa.pub 内容复制进服务器的 /.ssh/authorized_keys"

取消密码登录，使用公钥验证
```
sudo vim /etc/ssh/sshd_config

PermitRootLogin prohibit-password
PubkeyAuthentication yes
AuthorizedKeysFile /root/.ssh/authorized_keys
PasswordAuthentication no
MaxAuthTries 6
LoginGraceTime 2m
PasswordAuthentication no
Banner none
ChallengeResponseAuthentication no
PermitRootLogin prohibit-password
PubkeyAuthentication yes
AuthorizedKeysFile /root/.ssh/authorized_keys
```
</details>



<details>

<summary><font size="4">设置 swap</font></summary>

查看 swap 信息
```
swapon --show
```
关闭当前 swap
```
swapoff /swap.img
```
删除旧 swap 文件
```
rm /swap.img
```
创建一个新的 4GB 的 swap 文件
```
fallocate -l 4G /swap.img
```
设置正确的权限
```
chmod 600 /swap.img
```
将新的文件格式化为swap格式
```
mkswap /swap.img
```
启用新的 swap 文件
```
swapon /swap.img
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
curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg
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
apt-get update
```
安装 Redis
```
apt-get install redis
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
安装 ffmpeg
```
apt install ffmpeg
```

[配置 go](https://golang.google.cn/dl/)
```
tar -zxvf go1.20.14.linux-amd64.tar.gz -C /usr/local/
```
[安装 Nvm](https://nodejs.org/zh-cn/download/package-manager)
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash
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
安装 [node.js](https://nodejs.org/zh-cn/download/prebuilt-binaries)
```
nvm install 20
```
手动设置环境
```
mkdir -p ~/.nvm/versions/node/v20.17.0 && tar -xvf /root/downloads/node-v20.17.0-linux-x64.tar.xz -C /root/downloads/ && mv /root/downloads/node-v20.17.0-linux-x64 ~/.nvm/versions/node/v20.17.0
nvm use v20.17.0  # 使用 nvm 激活这个版本
node -v
npm -v
```
安装 [chrome](https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb)
```
apt install deb文件路径
```

</details>


### 常用工具指令

<details>

<summary><font size="4">软件包常用指令</font></summary>

更新软件包列表
```
apt-get update
```
更新软件包
```
apt-get upgrade
```
安装软件包
```
dpkg -i 软件包名
```
重找已安装的软件包
```
dpkg -l | grep 软件包名
```
卸载软件包
```
apt-get remove 软件包名
```
清除残留文件
```
apt-get purge 软件包名
```
自动删除不在线顺的依赖包
```
apt-get autoremove
```

</details>

<details>

<summary><font size="4">docker 常用指令</font></summary>

查看docker资源使用情况
```
docker stats
```
容器
```
docker ps -a                     # 查看所有容器
docker rm <容器ID或名称>           # 删除容器
docker start <容器ID或名称>        # 启动容器
docker stop <容器ID或名称>         # 停止容器
docker restart <容器ID或名称>      # 重启容器
docker logs -f <容器ID或名称>      # 查看容器日志
docker exec -it <容器ID或名称> /bin/bash  # 进入容器
```
镜像
```
docker images                    # 查看所有镜像
docker pull <镜像ID或名称>         # 拉取镜像
docker rmi <镜像ID或名称>          # 删除镜像
```
配置 docker 代理
```
mkdir /etc/systemd/system/docker.service.d
vim docker.service.d

[Service]
Environment="HTTP_PROXY=http://127.0.0.1:7890"
Environment="HTTPS_PROXY=http://127.0.0.1:7890"
Environment="NO_PROXY=localhost,127.0.0.1"
```
docker 代理 ```docker pull dockerpull.com/```项目人/项目名
</details>



<details>

<summary><font size="4">tmux 常用指令</font></summary>

```
tmux new -s xx                  # 新建终端
tmux attach-session -t xx       # 返回xx窗口
tmux kill-session -t xx         # 删除xx窗口
tmux ls                         # 查看所有终端
Ctrl+B D                        # 退出窗口
```

</details>



### Bot 相关

<details>

<summary><font size="4">napcat 相关</font></summary>

[napcat](https://napneko.github.io/zh-CN/guide/getting-started) 一键脚本
```
curl -o napcat.sh https://nclatest.znin.net/NapNeko/NapCat-Installer/main/script/install.sh && sudo bash napcat.sh
```
启动并保持后台运行
```
tmux new-session -d -s napcat_2036 'xvfb-run -a qq --no-sandbox'
tmux new-session -d -s napcat_2959 'xvfb-run -a qq --no-sandbox'
```
后台快速登录
```
tmux new-session -d -s napcat_2036 'xvfb-run -a qq --no-sandbox -q 203627006'
tmux new-session -d -s napcat_2959 'xvfb-run -a qq --no-sandbox -q 2959738664'
```
进入后台进程
```
tmux attach-session -t napcat_2036
tmux attach-session -t napcat_2959
```
</details>



<details>

<summary><font size="4">qsign 相关 (8963)</font></summary>

创建 qsign docker
```
docker run -d --restart=always --name qsign_2036 -p 8080:8080 -e BASE_PATH=/srv/qsign/qsign/txlib/8.9.63 xzhouqd/qsign:core-1.1.9
```

</details>

<details>

<summary><font size="4">meme 相关</font></summary>

拉取 memes 镜像
```
docker pull meetwq/meme-generator:0.0.20
```
创建 memes docker
```
docker run -d --name=memes -p 2233:2233 --restart always -v /tools/data:/data -e MEME_DIRS='["/data/memes"]' -e GIF_MAX_SIZE=10.0 -e GIF_MAX_FRAMES=60 -e LOG_LEVEL='INFO' meetwq/meme-generator
```

</details>


<details>

<summary><font size="4">云崽相关</font></summary>

[TRSS-Yunzai](https://github.com/TimeRainStarSky/Yunzai)
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

<details>

<summary><font size="4">koishi 相关</font></summary>

[koishi](https://koishi.chat/zh-CN/manual/starter/boilerplate.html)
安装 yarn
```
npm i -g yarn
```
创建 koishi 项目
```
yarn create koishi
```

</details>

### 其它

<details>

<summary><font size="4">修复依赖</font></summary>

修复系统软件包依赖
```
apt-get install -f
```

</details>

<details>

<summary><font size="4">中文字体包</font></summary>

安装中文字体包(思源黑体)
```
apt-get install fonts-noto
```
更新字体缓存
```
sudo fc-cache -fv
```
[鸿蒙字体](https://github.com/uniartisan/fonts-harmonyos-sans-cn)

</details>

<details>

<summary><font size="4">查看端口</font></summary>

查看端口占用
```
lsof -i:端口
```
强制结束进程
```
kill -9 进程pid
```

</details>

<details>

<summary><font size="4">代理</font></summary>

安装 [v2raya](https://v2raya.org/docs/prologue/installation/docker/) docker
```
docker pull mzz2017/v2raya
```
仅端口代理
```
docker run -d \
  -p 2017:2017 \
  -p 20170-20172:20170-20172 \
  --restart=always \
  --name v2raya \
  -e V2RAYA_V2RAY_BIN=/usr/local/bin/v2ray \
  -e V2RAYA_LOG_FILE=/tmp/v2raya.log \
  -v /etc/v2raya:/etc/v2raya \
  mzz2017/v2raya
```

<details>

<summary><font size="4">npm 代理</font></summary>

```
vim .npmrc

http-proxy=http://127.0.0.1:7890
https-proxy=http://127.0.0.1:7890
```

</details>

yarn 命令行代理
```
export HTTP_PROXY=http://127.0.0.1:7890
export HTTPS_PROXY=http://127.0.0.1:7890
```
.yarnrc.yml 添加代理
```
httpProxy: "http://127.0.0.1:7890"
httpsProxy: "http://127.0.0.1:7890"
```

</details>


<details>

<summary><font size="4">nginx 编写中</font></summary>

https://github.com/nginx/nginx

</details>
