# Lotus 本地测试网一键部署 Ansible 剧本
## 概述
用Ansible实现一条命令自动部署 Lotus 本地测试网，部署的节点包含一下模块：
1. Lotus Genesis Daemon (端口 1234）
2. Lotus Genesis Miner（端口 2345）
3. 一个正常的 daemon 节点（端口1235），并连接到 Genesis Daemon
4. 一个正常的 Miner 节点（端口2346）
5. 使用 tmux 管理进程
6. 可启用 512MiB 扇区支持


## 系统需求：
1. 一台 Linux 主机，物理机、虚拟机、云主机均可，配置至少2核8G内存
2. 一个全新安装并更新到最新的 Ubuntu 20.04 系统，可以使用下面的命令更新系统
    ```
    sudo apt update -y && sudo apt upgrade -y
    ```

3. 当前用户支持 nopasswd sudo，可以使用下面的命令设置 nopasswd sudo 权限
    ```
    # 请使用当前用户的用户名替换<username>
    echo "<username> 	ALL=(ALL)	NOPASSWD: ALL" | sudo tee /etc/sudoers.d/lotus
    ```
    修改后退出当前用户并重新登录
4. 安装 ansible 软件包
    ```
    sudo apt install -y ansible git
    ```
5. (可选)一个能够正常访问github/rust/go软件包的http代理，如果在编译过程中出现无法访问各种软件包的问题，请准备一个能够正常访问各种软件包的http代理，中国大陆用户，强烈建议使用http代理部署。


## 部署:
使用下面的命令完成部署

    ```
    git clone https://github.com/denniszou/lotus-localdev-deploy.git
    cd lotus-localdev-deploy
    ansible-playbook deploy.yml
    ```
如果准备了http代理，请使用下面的命令替代上面的命令：
    ```
    https_proxy=http://<proxy_ip>:<proxy_port> git clone https://github.com/denniszou/lotus-localdev-deploy.git
    cd lotus-localdev-deploy
    ansible-playbook deploy.yml -e proxy_url=http://<proxy_ip>:<proxy_port>
    ```

## 使用说明：
1. 运行 ```tmux a``` 进入tmux 管理界面，使用下面的快捷键切换终端：

    * Ctrl+B 0 切换到 lotus genesis daemon 日志终端
    * Ctrl+B 1 切换到 lotus genesis miner 日志终端
    * Ctrl+B 2 切换到 lotus regular daemon 日志终端
    * Ctrl+B 3 切换到 lotus regular daemon 日志终端
    * Ctrl+B 4 切换到 lotus genesis daemon / miner 操作终端
    * Ctrl+B 5 切换到 lotus regular daemon / miner 操作终端
    * Ctrl+B d 退出管理终端（节点进程在后台运行）

2. 在lotus-localdev-deploy目录下执行下面的命令关闭所有进程
   ```
   ansible-playbook stop.yml
   ``` 

3. 在lotus-localdev-deploy目录下执行下面的命令重新启动所有进程
   ```
   ansible-playbook start.yml
   ``` 

4. 如果在部署过程出现问题，请使用下面的命令输出更多的信息，以排查问题
   ```
   ansible-playbook -vv deploy.yml
   ```

5. 如果需要支持512MiB扇区，请取消 host-config.yml 文件中下面这一样的注释符
    ```
    # sector_512MiB_support: true
    ```
