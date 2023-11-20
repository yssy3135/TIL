
### missing signature key.

**실행 명령어를 입력했을때**
docker run --name pqsql -d -p 5432:5432 -e POSTGRES_USER=postgresql -e POSTGRES_PASSWORD=postgrespassword postgres

Unable to find image 'postgres:latest' locally
Trying to pull repository docker.io/library/postgres ...
/usr/bin/docker-current: missing signature key.
요런 에러가 보일 수 있다.

**sudo yum install을 이용해서 docker를 설치하면 엄청 이전 버전이 설치가 되어버린다** ( 1.0.32 )

- sudo yum remove docker* 명령어를 이용해 docker를 삭제
- sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    - docker repo 추가
-  yum list docker-ce --showduplicates | sort -r updates: mirror01.idc.hinet.net
    - 설치가능한 docker 버전 확인
- sudo yum install -y docker-ce-24.0.7 docker-ce-cli-24.0.7 containerd.io docker-buildx-plugin docker-compose-plugin
    - 버전 확인 후 설치


**이때 yum-config-manager를 찾을수 없다고 에러가 발생 할 수 있다.**
- yum install yum-utils



