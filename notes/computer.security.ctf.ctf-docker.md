---
id: xjuuqf0xybll9epclbiqy5s
title: Ctf Docker
desc: ''
updated: 1672142423475
created: 1672142284036
---


## docker file

```docker
# Dockerfile
# FROM phusion/baseimage:master-amd64
# FROM ubuntu:16.04
FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive


# apt setup
ENV UBUNTU_MIRROR=mirrors.tuna.tsinghua.edu.cn
RUN  sed -i s@/archive.ubuntu.com/@/${UBUNTU_MIRROR}/@g /etc/apt/sources.list
RUN  sed -i s@/security.ubuntu.com/@/${UBUNTU_MIRROR}/@g /etc/apt/sources.list
RUN  apt-get clean  
RUN  apt-get update

# RUN  apt-get install \
#         python python3 \
#         python-pip python3-pip \
#         python-dev libffi-dev libssl-dev \
#         vim \
#         lib32ncurses5 lib32z1 \
#         ruby gem git \
#          -y

# install some lib using apt
RUN dpkg --add-architecture i386 && \
    apt-get -y update && \
    apt install -y \
    libc6:i386 \
    libc6-dbg:i386 \
    libc6-dbg \
    lib32stdc++6 \
    g++-multilib \
    cmake \
    ipython3 \
    vim \
    net-tools \
    iputils-ping \
    libffi-dev \
    libssl-dev \
    python3-dev \
    python3-pip \
    build-essential \
    ruby \
    ruby-dev \
    tmux \
    strace \
    ltrace \
    nasm \
    wget \
    gdb \
    gdb-multiarch \
    netcat \
    socat \
    git \
    patchelf \
    gawk \
    file \
    python3-distutils \
    bison \
    rpm2cpio cpio \
    zstd \
    tzdata --fix-missing && \
    rm -rf /var/lib/apt/list/*

# timezone
ENV TZ=Asia/Shanghai
RUN ln -fs /usr/share/zoneinfo/$TZ /etc/localtime && \
    dpkg-reconfigure -f noninteractive tzdata


# python setup
ENV PIP_MIRROR=https://pypi.tuna.tsinghua.edu.cn/simple
# RUN pip install -i ${PIP_MIRROR} "pip < 21.0" -U && pip3 install -i ${PIP_MIRROR}  "pip < 21.0" -U 
RUN python3 -m pip install -U pip -i ${PIP_MIRROR}
RUN pip config set global.index-url ${PIP_MIRROR}
RUN python3 -m pip install --no-cache-dir \
    ropgadget \
    z3-solver \
    smmap2 \
    apscheduler \
    ropper \
    unicorn \
    keystone-engine \
    capstone \
    angr \
    pebble \
    r2pipe \
    requests \
    pwntools

RUN gem sources --remove https://rubygems.org/ && \
    gem sources -a http://gems.ruby-china.com/ && \
    gem install one_gadget seccomp-tools && \
    rm -rf /var/lib/gems/2.*/cache/*



# git setup
ENV GIT_PROXY="http://192.168.8.1:10811" 
RUN git config --global http.proxy ${GIT_PROXY}
RUN git clone --depth 1 https://github.com/pwndbg/pwndbg && \
    cd pwndbg && chmod +x setup.sh && ./setup.sh

RUN git clone --depth 1 https://github.com/scwuaptx/Pwngdb.git ~/Pwngdb && \
    cd ~/Pwngdb && mv .gdbinit .gdbinit-pwngdb && \
    sed -i "s?source ~/peda/peda.py?# source ~/peda/peda.py?g" .gdbinit-pwngdb && \
    echo "source ~/Pwngdb/.gdbinit-pwngdb" >> ~/.gdbinit

RUN git clone https://github.com/lieanu/LibcSearcher.git && \
    cd LibcSearcher && \
    python3 setup.py develop
ENV CMD_PROXY="http://192.168.8.1:10811" 
RUN echo http_proxy=${CMD_PROXY}  >> ~/.bashrc 


RUN git clone --depth 1 https://github.com/niklasb/libc-database.git libc-database && \
    cd libc-database && ./get ubuntu debian || echo "/libc-database/" > ~/.libcdb_path && \
    rm -rf /tmp/*

# wget 

RUN http_proxy=${CMD_PROXY}  wget -O ~/.gdbinit-gef.py -q http://gef.blah.cat/py



RUN mkdir /root/workspace

```

## run script

```sh
#!/bin/bash

# ctf-docker.sh
OWNERNAME="b1babo"
IMAGENAME="ctf-docker"
TAGNAME="v.1.0"
CONTAINERNAME="ctf"
PORTSTRING=""
if [ "${PORTFWD}" = "" ]
then
   PORTFWD="20080:80,20443:443,28080:8080,24433:4433,9999:9999"
fi
PORTFWD=$(echo ${PORTFWD} | tr -cd [0-9,:])
IFS=',' read -ra PORTLIST <<< "${PORTFWD}"
for PORTPAIR in "${PORTLIST[@]}"
do
   SPORT=$(echo ${PORTPAIR} | cut -d':' -f1)
   PORTSTRING="${PORTSTRING} -p ${SPORT}:${SPORT}"
done




function build_image(){
    echo [+] building $OWNERNAME/$IMAGENAME:$TAGNAME
    DOCKER_BUILDKIT=1 docker build -t $OWNERNAME/$IMAGENAME:$TAGNAME -f Dockerfile .
}

function run_docker(){
    echo [+] runing $OWNERNAME/$IMAGENAME:$TAGNAME ${PORTSTRING}
    docker run -it  ${PORTSTRING} --name $CONTAINERNAME --mount "type=bind,src=$(pwd)/workspace,dst=/root/workspace" $OWNERNAME/$IMAGENAME:$TAGNAME 
        -e PORTFWD="${PORTFWD}" \
        # --rm \
        --cap-add=NET_ADMIN \
        --cap-add=SYS_ADMIN \
        --security-opt apparmor=unconfined \
        --device=/dev/net/tun \
        --network=host \
        
}

function rm_image(){
    docker container rm $IMAGENAME
}


# Handle arguments
function show_help {
    echo "Usage: $0 [-b] [-r]"
    echo "-b - build ctf docker image"
    echo "-r - run ctf docker"
}


if [[ $# < 1 ]]; then
    show_help
    exit 1
fi


# 选项后面的冒号表示需要参数
while getopts "brh" optname
do
    case "$optname" in
      "b")
        build_image
        ;;
      "r")
        run_docker 
        ;;
      "h")
        echo "Usage: $0 [-b] [-r]"
        ;;
      *)
        echo "Unknown options"
        ;;
    esac
    #echo "option index is $OPTIND"
done

```