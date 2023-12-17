FROM debian:12.2

# 更新apt源
RUN tee /etc/apt/sources.list <<-'EOF'
deb http://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm main contrib non-free non-free-firmware
deb http://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-updates main contrib non-free non-free-firmware
deb http://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-backports main contrib non-free non-free-firmware
deb http://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
EOF

# 安装必要的包
RUN <<EOF
apt-get update
apt-get install --reinstall -y ca-certificates
sed -i 's/http:/https:/g' /etc/apt/sources.list
apt-get upgrade -y
apt-get install -y --no-install-recommends \
    curl \
    zip \
    unzip \
    git \
    vim \
    openssh-server
EOF    

# 将本地的 Java 和 Maven 压缩包复制到镜像中
COPY jdk-8u361-linux-x64.tar.gz /tmp/jdk.tar.gz
COPY apache-maven-3.9.6.zip /tmp/maven.zip

# 解压并安装 Java Maven
RUN <<EOF
tar -xzvf /tmp/jdk.tar.gz -C /
rm /tmp/jdk.tar.gz
unzip /tmp/maven.zip -d /
rm /tmp/maven.zip
chmod +x /apache-maven-3.9.6/bin/mvn
EOF       

# 配置 ssh git vim 环境变量
RUN <<EOF
sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config
git config --global user.name "antares"
git config --global user.email "1716607668@qq.com"
EOF    

RUN tee ~/.vimrc <<-'EOF'
set tabstop=4
syntax enable
if has('mouse')
    set mouse-=a
endif
EOF

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
RUN <<EOF
apt-get clean
rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
rm /var/log/lastlog /var/log/faillog
EOF   

# 设置工作目录
WORKDIR /root/workplace

EXPOSE 22