FROM debian:12.2

# 更新apt源
RUN tee /etc/apt/sources.list <<-'EOF'
deb http://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm main contrib non-free non-free-firmware
deb http://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-updates main contrib non-free non-free-firmware
deb http://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-backports main contrib non-free non-free-firmware
deb http://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
EOF

RUN apt-get update
RUN apt-get install --reinstall -y ca-certificates
RUN sed -i 's/http:/https:/g' /etc/apt/sources.list

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

# 配置vim
RUN tee ~/.vimrc <<-'EOF'
set tabstop=4
syntax enable
if has('mouse')
    set mouse-=a
endif
EOF

# 将本地的 Java 和 Maven 压缩包复制到镜像中
COPY jdk-8u361-linux-x64.tar.gz /tmp/jdk.tar.gz
COPY apache-maven-3.9.6.zip /tmp/maven.zip

# 解压并安装 Java
RUN tar -xzvf /tmp/jdk.tar.gz -C / \
    && rm /tmp/jdk.tar.gz

# 解压并安装 Maven
RUN unzip /tmp/maven.zip -d / \
    && rm /tmp/maven.zip \
    && chmod +x /apache-maven-3.9.6/bin/mvn

# 设置环境变量
RUN tee -a /etc/profile <<-'EOF'
export JAVA_HOME=/jdk1.8.0_361
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export MAVEN_HOME=/apache-maven-3.9.6
export PATH=$PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin
EOF

ENV JAVA_HOME /jdk1.8.0_361
ENV JRE_HOME $JAVA_HOME/jre
ENV CLASSPATH .:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV MAVEN_HOME /apache-maven-3.9.6
ENV PATH $PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin

# 清理垃圾
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

# 设置工作目录
WORKDIR /root/workplace

EXPOSE 22