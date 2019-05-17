# Docker + Jenkins
## Requirements
- docker-machine version 0.16.1, build cce350d7(```docker-machine -v```)
- docker-compose version 1.23.2, build 1110ad01(```docker-compose -v```)
- Docker version 18.09.2, build 6247962(```docker -v```)
- Virtual Box

## Install Guide

1) Install Docker Machine 
- 建立Virtual Box的虛擬機器 及 IP
```
docker-machine create jenkins-howtocodewell
```
- 查看此 VM 的設定檔
```
docker-machine env jenkins-howtocodewell
```

2) Configure shell
- 登入VM
```
eval $(docker-machine env jenkins-howtocodewell)
```
- 確認一下目前該VM中應該沒有任何docker images存在且沒有container在執行
```
docker ps -a
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker images
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
3) Detach(派遣) machine
```
$ docker-compose up --build
Creating network "jenkinsdockerworkspace_default" with the default driver
Creating jenkinsdockerworkspace_jenkins_1 ... done
```
- 此時可查看VM內已啟動的docker container
```
$ docker ps -a
```
- 日後再重新下達 docker-compose up --build 所啟動的 jenkins 還會保留之前的資料唷！

4) 設定 static route
```
$ docker-machine active
jenkins-howtocodewell
$ docker-machine ip jenkins-howtocodewell
192.168.99.102
$ sudo nano /etc/hosts 
(新增一筆)
192.168.99.102 jenkins.local
```

5) 使用瀏覽器打開網頁測試
```
http://jenkins.local
```
- 應該會出現要求輸入初始化密碼的提示
- 直接登入 jenkins 的 container shell操作，取得初始化密碼
```
$ docker exec -it jenkinsdockerworkspace_jenkins_1 bash
# cat /var/jenkins_home/secrets/initialAdminPassword
83723f3651984d7f9c540091dc4bee00
```
- 於瀏覽器輸入後，jenkins會詢問要安裝哪些plugins，選『suggest』
- 等plugins安裝完後，會要求建立 admin 帳號 (neo/eva-01)
- 之後會要求提供 jenkins 服務URL，此處保留不變
- jenkins設定完成，如此即可啟用！

7) 