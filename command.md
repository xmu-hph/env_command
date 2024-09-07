# ssh检验
```
ssh -F ~/.ssh/config -T git@github.com
```

# git clone
```
GIT_SSH_COMMAND='ssh -F ~/.ssh/config' git clone git@github.com:xmu-hph/repo.git
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
