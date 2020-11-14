---
title: 一致性协议(jraft)
date: 2020-11-12 13:15:45
tags: 算法
categories: 算法
mp3:
cover:
---
#### 起因

##### 线型一致性

#### 比较

#### 实现

#### 验证

##### jepsen
使用docer部署5个节点 和 一个控制端

Dockerfile

```Dockerfile
FROM debian
LABEL Description="This image is used to test jepsen for jraft"
WORKDIR /root
RUN apt update && \
	apt install -y openssh-server && \
	apt install -y openjdk-11-jre && \
	apt install -y curl && \
	curl -O https://download.clojure.org/install/linux-install-1.10.1.727.sh &&\
	chmod +x linux-install-1.10.1.727.sh && \
	./linux-install-1.10.1.727.sh && \
	useradd -d /home/admin -g root -s /bin/bash -m admin && \
	mkdir /home/admin/.ssh 
COPY authorized_keys /home/admin/.ssh/authorized_keys
COPY bin/ /root
COPY id_rsa /root/.ssh/
COPY id_rsa.pub /root/.ssh/
COPY sofa-jraft-jepsen-master/ /root/
ENTRYPOINT service ssh start && \
    export PATH=/root/bin:$PATH && \
    tail -f /var/log/dpkg.log
CMD ["bash"]
```

* 容器的名字要跟 nodes 和 project.cli 记录的一致


问题
1. apache-codec/commons-codec/1.2/commons-codec-1.2.jar 拉不下来
project.cli 增加 :repositories [["alibaba" "http://maven.aliyun.com/nexus/content/groups/public"]]
2. error='Not enough space' (errno=12) 内存不够
project.cli 修改 :jvm-opts ["-Xms1g" "-Xmx1g" "-server" "-XX:+UseG1GC"]
3. java.lang.ClassNotFoundException: javax.xml.bind.DatatypeConverter 还是用openjdk-8吧




#### 后记




> 参考:
[https://raft.github.io/raft.pdf](https://raft.github.io/raft.pdf)
[http://thesecretlivesofdata.com/raft/](http://thesecretlivesofdata.com/raft/)

