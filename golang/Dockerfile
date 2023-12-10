FROM golang:1.21

ENV GOPROXY https://goproxy.cn,direct

# 更新apt源
RUN tee /etc/apt/sources.list <<-'EOF'
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm main contrib non-free non-free-firmware
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-updates main contrib non-free non-free-firmware
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-backports main contrib non-free non-free-firmware
deb https://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
EOF

RUN apt-get update
RUN apt-get upgrade -y

# 安装必要的包
RUN apt-get install -y --no-install-recommends \
    curl \
    zip \
    unzip \
    git \
    vim \
    openssh-server

# 允许 root 用户通过 SSH 登录
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
# 允许密码登录
RUN sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config

# 配置git的用户名和密码
RUN git config --global user.name "antares"
RUN git config --global user.email "1716607668@qq.com"

# 安装 goctl
RUN GOPROXY=https://goproxy.cn/,direct go install github.com/zeromicro/go-zero/tools/goctl@latest

# 安装 protoc
RUN curl -L -o /tmp/protoc.zip https://github.com/protocolbuffers/protobuf/releases/download/v3.19.1/protoc-3.19.1-linux-x86_64.zip && \
    unzip -d /tmp/protoc /tmp/protoc.zip && \
    mv /tmp/protoc/bin/protoc $GOPATH/bin

# 安装 protoc-gen-go
RUN go install google.golang.org/protobuf/cmd/protoc-gen-go@latest

# 安装 protoc-gen-go-grpc
RUN go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest

# $GOPATH/bin添加到环境变量中
ENV PATH $GOPATH/bin:$PATH

# 配置vim
RUN tee ~/.vimrc <<-'EOF'
set tabstop=4
syntax enable
if has('mouse')
    set mouse-=a
endif
EOF

# 设置$GOPath环境变量
RUN tee /etc/profile <<-'EOF'
export GOPATH=/usr/local/go
export PATH=$PATH:$GOPATH/bin
EOF

# 配置GOPROXY
RUN go env -w GOPROXY=https://goproxy.cn,direct

# 清理垃圾
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

# 设置工作目录
WORKDIR /root/workplace

EXPOSE 22