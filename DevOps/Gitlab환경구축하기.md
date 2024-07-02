# Gitlab 환경 구축하기

* Gitlab 은 docker compose 로 진행
  * 기존 사내 서버의 DB가 이미 docker compose 로 관리되고 있었고 쉬운 유지보수 
* Gitlab Tier 는 Gitlab Community Edition (CE) 가 아닌 Enterprise Edidiont (EE) 로 선택
  * EE 의 Core Tier 를 이용하면 무료로 사용할 수 있고 CE -> EE 보다 EE core -> EE standard 가 더 쉽기 때문에 굳이 CE를 이용할 필요가 없음. (즉, 추후 유료버전 사용이 쉽도록 EE core 선택)
  * Gitlab CE 를 직접 커스텀 해서 사용할 일이 없을 것이라 판단.

단점
- GitLab 자체 릴리즈 및 보안패치 주기적으로 확인 필요

장점
- 프로젝트 (레포) 마다 고유의 git 브랜치 전략 사용 가능
- 노션 대신 워크플로우 구성 가능
- 배포 파이프라인 구성 및 봇 사용 가능 

[추가]
- SMTP 설정
  - https://www.bearpooh.com/116

docker-compose.yaml
```yaml
services:
  gitlab:
    image: gitlab/gitlab-ee:16.6.8-ee.0
    platform: linux/amd64
    container_name: gitlab
    restart: always
    hostname: ${GITLAB_HOSTNAME}
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://${GITLAB_HOSTNAME}/gitlab/'
    ports:
      - '80:80'
      - '443:443'
      - '2222:22' ## 22 는 해당 서버에서 ssh 접속을 위해 사용중이므로 2222 로 gitlab ssh 접속 포트 변경
    volumes:
      - '${GITLAB_HOME}/config:/etc/gitlab'
      - '${GITLAB_HOME}/logs:/var/log/gitlab'
      - '${GITLAB_HOME}/data:/var/opt/gitlab'
    shm_size: '256m'
```

.env
```yaml
GITLAB_HOSTNAME=
GITLAB_HOME=
```