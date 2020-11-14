---
title: 一致性协议(raft)
date: 2020-11-12 13:15:45
tags: 算法
categories: 算法
mp3:
cover:
---
## 起因

## 线型一致性

## 比较

## 实现

## 验证

### jepsen
[https://github.com/jepsen-io/jepsen](https://github.com/jepsen-io/jepsen)

### sofa-jraft-jepsen
代码现成的[https://github.com/sofastack/sofa-jraft-jepsen](https://github.com/sofastack/sofa-jraft-jepsen) 实际操作一下

使用docer部署5个节点 和 一个控制端

Dockerfile

```Dockerfile
FROM ubuntu:18.04
LABEL Description="This image is used to test jepsen for jraft"
WORKDIR /root
RUN apt update && \
	apt install -y openssh-server && \
	apt install -y openjdk-8-jre && \
	apt install -y curl
RUN	curl -O https://download.clojure.org/install/linux-install-1.10.1.727.sh &&\
	chmod +x linux-install-1.10.1.727.sh && \
	./linux-install-1.10.1.727.sh && \
	useradd -d /home/admin -g root -s /bin/bash -m admin && \
	mkdir /home/admin/.ssh 
COPY --chown=admin:root authorized_keys /home/admin/.ssh/authorized_keys
COPY --chown=root:root id_rsa /root/.ssh/
COPY --chown=root:root id_rsa.pub /root/.ssh/
COPY sofa-jraft-jepsen-master/ /root/
COPY /bin /usr/bin
ENTRYPOINT service ssh start && \
	export PATH=/root/bin:$PATH && \
	tail -f /var/log/dpkg.log
CMD ["bash"]
```

启动容器
```shell
#! /bin/sh
docker run -d --network jepsen --hostname jraft.host1 --name jraft.host1 --rm jepsen:0.0.1
docker run -d --network jepsen --hostname jraft.host2 --name jraft.host2 --rm jepsen:0.0.1
docker run -d --network jepsen --hostname jraft.host3 --name jraft.host3 --rm jepsen:0.0.1
docker run -d --network jepsen --hostname jraft.host4 --name jraft.host4 --rm jepsen:0.0.1
docker run -d --network jepsen --hostname jraft.host5 --name jraft.host5 --rm jepsen:0.0.1
docker run -d --network jepsen --hostname client --name client --rm jepsen:0.0.1
```

* 容器的名字要跟 nodes 和 project.cli 记录的一致
* 注意本地 authorized_keys id_rsa id_rsa.pub 的权限是0600

问题
1. apache-codec/commons-codec/1.2/commons-codec-1.2.jar 拉不下来
project.cli 增加 :repositories [["alibaba" "http://maven.aliyun.com/nexus/content/groups/public"]]
2. error='Not enough space' (errno=12) 内存不够
project.cli 修改 :jvm-opts ["-Xms1g" "-Xmx1g" "-server" "-XX:+UseG1GC"]
3. java.lang.ClassNotFoundException: javax.xml.bind.DatatypeConverter 还是用openjdk-8吧
4. Unrecognized option: -client control 脚本里-client参数 去除
5. 准备好http_proxy
6. jepsen不支持新的openssh生成的key [https://stackoverflow.com/questions/53134212/invalid-privatekey-when-using-jsch](https://stackoverflow.com/questions/53134212/invalid-privatekey-when-using-jsch)




#### 后记




> 参考:
[https://raft.github.io/raft.pdf](https://raft.github.io/raft.pdf)
[http://thesecretlivesofdata.com/raft/](http://thesecretlivesofdata.com/raft/)

