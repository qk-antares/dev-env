FROM node:18

# 设置工作目录
WORKDIR /root/workplace

# 更新apt源
RUN tee /etc/apt/sources.list <<-'EOF'
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm main contrib non-free non-free-firmware
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-updates main contrib non-free non-free-firmware
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-backports main contrib non-free non-free-firmware
deb https://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
EOF

# 安装必要的包
RUN <<EOF
apt-get update
apt-get upgrade -y
apt-get install -y --no-install-recommends \
    curl \
    zip \
    unzip \
    git \
    vim \
    openssh-server
EOF

# 配置ssh git npm vim 
RUN <<EOF
sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config
git config --global user.name "antares"
git config --global user.email "1716607668@qq.com"
npm config set registry https://registry.npm.taobao.org
echo 'fs.inotify.max_user_watches=524288' >> /etc/sysctl.conf
EOF

RUN tee ~/.vimrc <<-'EOF'
set tabstop=4
syntax enable
if has('mouse')
    set mouse-=a
endif
EOF

# 清理垃圾
RUN <<EOF
apt-get clean
rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
rm /var/log/lastlog /var/log/faillog
EOF

EXPOSE 22