
# Docker + Jenkins

## Requirements

- docker-machine version 0.16.1, build cce350d7(```docker-machine -v```)
- docker-compose version 1.23.2, build 1110ad01(```docker-compose -v```)
- Docker version 18.09.2, build 6247962(```docker -v```)
- Virtual Box

## Install Guide

1) Install Docker Machine 

- 建立Virtual Box的虛擬機器 及 IP

```bash
$ docker-machine create jenkins-howtocodewell
```

- 之後可用以下指令重新啟動此 docker-machine

```bash
$ docker-machine start jenkins-howtocodewell
```

- 查看此 VM 的設定檔

```bash
$ docker-machine env jenkins-howtocodewell
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.103:2376"
export DOCKER_CERT_PATH="/Users/neohsu/.docker/machine/machines/jenkins-howtocodewell"
export DOCKER_MACHINE_NAME="jenkins-howtocodewell"
# Run this command to configure your shell: 
# eval $(docker-machine env jenkins-howtocodewell)
```

2) Configure shell

- 登入VM

```bash
eval $(docker-machine env jenkins-howtocodewell)
```

- 確認一下目前該VM中應該沒有任何docker images存在且沒有container在執行
  
```bash
docker ps -a
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker images
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

3) Detach(派遣) machine

```bash
$ docker-compose up --build
Creating network "jenkinsdockerworkspace_default" with the default driver
Creating jenkinsdockerworkspace_jenkins_1 ... done
```

- 此時可查看VM內已啟動的docker container

```bash
$ docker ps -a
```

- 日後再重新下達 docker-compose up --build 所啟動的 jenkins 還會保留之前的資料唷！

4) 設定 static route

- 先查看目前已啟動的 docker-machine 清單

```bash
$ docker-machine active
jenkins-howtocodewell
```

- 再查看指定 docker-machine 的 ip，並將該ip寫入 /etc/hosts

```bash
$ docker-machine ip jenkins-howtocodewell
192.168.99.103
$ sudo nano /etc/hosts 
(新增一筆)
192.168.99.103 jenkins.local
```

5) 使用瀏覽器打開網頁測試

```bash
http://jenkins.local
```

- 應該會出現要求輸入初始化密碼的提示
- 直接登入 jenkins 的 container shell操作，取得初始化密碼

```bash
$ docker exec -it jenkinsdockerworkspace_jenkins_1 bash
$ cat /var/jenkins_home/secrets/initialAdminPassword
83723f3651984d7f9c540091dc4bee00
```

- 於瀏覽器輸入後，jenkins會詢問要安裝哪些plugins，選『suggest』
- 等plugins安裝完後，會要求建立 admin 帳號 (neo/eva-01)
- 之後會要求提供 jenkins 服務URL，此處保留不變
- jenkins設定完成，如此即可啟用！

## Upgrade Guide

1) 『用root權限』登入 jenkins 的 container shell操作

```bash
docker exec -u 0 -it jenkinsdockerworkspace_jenkins_1 bash
```

２) 執行 wget 指令，由網路取得最新的 jenkins.war(網址可由jenkins管理網頁的更新提示取得)

```
wget http://updates.jenkins-ci.org/download/war/2.164.3/jenkins.war
```

3) 將 jenkins.war 檔放到 docker container 的 /usr/share/jenkins/ 目錄下

```bash
mv jenkins.war /usr/share/jenkins
```

4) 修改檔案權限

```bash
chown jenkins:jenkins /usr/share/jenkins/jenkins.war
```

5) 離開 docker container shell

```bash
exit
```

6) 重啟 jenkins docker container

- 先查看 container id

```bash
docker ps -a
```

- 再重新啟動 docker container

```bash
docker container restart 94ac84dab0d4
```