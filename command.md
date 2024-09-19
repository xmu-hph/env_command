# ssh检验
```
ssh -F ~/.ssh/config -T git@github.com
ssh -F ~/.ssh/config -o ServerAliveInterval=60 -o ServerAliveCountMax=3 -fNR 服务器ip1的开放映射转发端口:localhost:本地机器上的ssh连接端口22 user@ip1
```

# git clone
```
GIT_SSH_COMMAND='ssh -F ~/.ssh/config' git clone git@github.com:users/repo.git
```
# git push
```
GIT_SSH_COMMAND='ssh -F ~/.ssh/config' git push
```
# 创建新环境
```
# 在指定目录下创建envs文件夹
mkdir envs
# 使用以下命令创建新环境
env_name=$(basename $(pwd))
conda create --prefix ./envs/$env_name python=3.9
nohup pip install -r requirements.txt > pip.log 2>&1 &
# 删除环境
env_name=$(basename $(pwd))
conda remove -p ./envs/$env_name --all
# 设置.env
touch .env
cat <<EOF > ./.env
PYTHONPATH=$(pwd)/envs/${env_name}/bin/python
EOF
# 创建.condarc文件并写入以下内容
touch ./.condarc
cat <<EOF > ./.condarc
envs_dirs:
  - ./envs
auto_activate_base: false
EOF
# 在bashrc中添加代码
# 自动激活 Conda 环境
function auto_activate_conda_env() {
    if [ -f "environment.yml" ]; then
        env_name=$(head -n 1 environment.yml | cut -f2 -d ' ')
        if [[ "$env_name" != "" ]]; then
            # 如果已激活的环境不同于目标环境，则切换
            if [[ "$CONDA_DEFAULT_ENV" != "$env_name" ]]; then
                conda activate $env_name
            fi
        fi
    elif [ -d "./envs" ]; then
        # 如果目录下有环境文件夹，激活该环境
        env_name=$(basename $(pwd))
        conda activate ./envs/$env_name
    fi
}

# 每次进入新目录时自动检查 Conda 环境
cd() {
    builtin cd "$@" || return
    auto_activate_conda_env
}
```
# cityflow实验回放
```
# 设置ssh端口映射
    ProxyCommand nc -X 5 -x localhost:7900 %h %p
    RemoteForward 7890 localhost:7890
    LocalForward 8080 localhost:8080
# 跳转到指定文件夹启动web浏览器服务
cd /path/to/frontend
python3 -m http.server 8080
# 本地通过映射端口访问服务器上的服务
http://localhost:8080/index.html
```

# 校园网服务器反向ssh隧道
```
# 校园网服务器（192.168.1.100）测试能不能访问外部公共ip机器（1.2.3.4）
ssh user@1.2.3.4
# 校园网服务器把本地端口映射到公共ip服务器上
ssh -R [跳板机端口]:localhost:[校园网服务器端口] [跳板机用户]@[跳板机IP]
ssh -R 2222:localhost:22 user@1.2.3.4
# 通过公共ip服务器的跳板机端口连接校园网服务器
ssh user@1.2.3.4 -p 2222
```
# 共享网络环境
```
# 使用动态端口转发
DynamicForward 7890
# 测试
curl --socks5 localhost:7890 http://ifconfig.me
# 使用curl下载文件
curl --socks5 localhost:7890 -O http://example.com/file.zip
# 使用docker代理/etc/systemd/system/docker.service.d/
sudo mkdir -p /etc/systemd/system/docker.service.d/
sudo nano /etc/systemd/system/docker.service.d/http-proxy.conf
## 添加内容
[Service]
Environment="HTTP_PROXY=socks5://localhost:7890/"
Environment="HTTPS_PROXY=socks5://localhost:7890/"
# 使用git pull
git config --global http.proxy 'socks5://localhost:7890'
git config --global https.proxy 'socks5://localhost:7890'
# 使用pip
export ALL_PROXY=socks5://localhost:7890
pip install <package-name>
export HTTP_PROXY=socks5://localhost:7890
export HTTPS_PROXY=socks5://localhost:7890
```

# 安装cityflow-dev环境
```
# git clone 
# 创建环境
# 安装cityflow
GIT_SSH_COMMAND='ssh -F ~/.ssh/config' pip install .
# 升级自定义
# 修改vehicle文件

# 修改cmakfile文件
## 手动设置 Python 的 include 和库文件路径
set(PYTHON_INCLUDE_DIRS "/home/hph/code/dev_cityflow/envs/dev_cityflow/include/python3.9")
set(PYTHON_LIBRARIES "/home/hph/code/dev_cityflow/envs/dev_cityflow/lib/libpython3.9.so")
## 告诉 CMake 使用这些路径
include_directories(${PYTHON_INCLUDE_DIRS})
link_directories(${PYTHON_LIBRARIES})

# 系统上安装pybind11
sudo apt-get install pybind11-dev
# 完成安装
```
