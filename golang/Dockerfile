FROM golang:1.21

ENV GOPROXY https://goproxy.cn,direct

ENV PATH $GOPATH/bin:$PATH

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

# 配置ssh git vim GOPATH GOPROXY
RUN <<EOF
sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config
git config --global user.name "antares"
git config --global user.email "1716607668@qq.com"
go env -w GOPROXY=https://goproxy.cn,direct
EOF

RUN tee ~/.vimrc <<-'EOF'
set tabstop=4
syntax enable
if has('mouse')
    set mouse-=a
endif
EOF

RUN tee /etc/profile <<-'EOF'
export GOPATH=/usr/local/go
export PATH=$PATH:$GOPATH/bin
EOF

# 安装 goctl protoc protoc-gen-go protoc-gen-go-grpc
RUN <<EOF
GOPROXY=https://goproxy.cn/,direct go install github.com/zeromicro/go-zero/tools/goctl@latest
curl -L -o /tmp/protoc.zip https://github.com/protocolbuffers/protobuf/releases/download/v3.19.1/protoc-3.19.1-linux-x86_64.zip
unzip -d /tmp/protoc /tmp/protoc.zip
mv /tmp/protoc/bin/protoc $GOPATH/bin
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
EOF

# 清理垃圾
RUN <<EOF
apt-get clean
rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
rm /var/log/lastlog /var/log/faillog
EOF

# 设置工作目录
WORKDIR /root/workplace

EXPOSE 22