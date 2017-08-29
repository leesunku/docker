docker 설치 ( ubuntu 14.04 )
 - sudo apt-get remove docker docker-engine docker.io // 설치되어 있는 도커 삭제
 - sudo rm -rf /var/lib/docker

 - curl -fsSL https://download.docker.com.linux/ubuntu/gpg | sudo apt-key add -
 - sudo apt-get install docker-ce // 도커 설치
 - docker version // 버전 확인

docker 이미지 생성
 - 1) docker hub에서 받는 방법
 --- sudo docker pull <image>
ex)
    sudo docker pull centos
(xe

 - 2) Dockerfile 생성
 --- Dockerfile 명령어 모음
 > FROM : 이미지 기반 설정
 > MAINTAINER : 이미지 작성자 정보
 > RUN : 이미지 내에서 스크립트나 명령 실행
 > CMD : 컨테이너가 시작될 때 스크립트나 명령 실행
 > ENTRYPOINT : 컨테이너가 시작되었을 때 스크립트나 명령 실행(RUN과 처리방식 다름)
 > EXPOSE : 호스트와 연결할 포트 설정
 > ENV : 환경 변수 설정
 > ADD, COPY : 이미지에 파일 추가
 > VOLUME : 데이터를 호스트에 저장하도록 설정
 > USER : 명령을 실행할 사용자 계정 설정
 > WORKDIR : 명령을 실행할 디렉터리 설정 //?cd?
 > ONBUILD : FROM 으로 이미지가 사용될 때 실행할 명령 설정
ex)
FROM scratch
MAINTAINER https://github.com/CentOS/sig-cloud-instance-images
ADD centos-7-docker.tar.xz /

LABEL name="CentOS Base Image" \
        vendor="CentOS" \
        license="GPLv2" \
        build-date="20161214" \
        create-image-date="20170829" \
CMD ["/bin/bash"]
(xe

docker build // Dockerfile 파일로 빌드
 > docker build <옵션> <Dockerfile 경로>
 --- build option
 > --force-rm=false: 이미지 생성에 실패할때, 때도 임시 컨테이너를 삭제
 > --no-cache=false: 이전 빌드에서 생성된 캐시를 사용안
 > -q, --quiet=false: Dockerfile의 RUN이 실행한 출력 결과를 표시안
 > --rm=true: 이미지 생성에 성공했을 때 임시 컨테이너를 삭제
 > -t, --tag="": 저장소 이름, 이미지 이름, 태그를 설정 <저장소 이름>/<이미지 이름>:<태그>


docker run // 생성된 이미지로 컨테이너를 생성
 > docker run <옵션> <이미지 이름 또는 이미지 아이디> <명령> <매개 변수>
 --- run option
 > -t, --tty=false: TTY 모드(pseudo-TTY)를 사용 Bash를 사용하려면 이 옵션을 설정
 > -i, --interactive=false: 표준 입력(stdin)을 활성화하며 컨테이너와 연결(attach)되어 있지 않더라도 표준 입력을 유지
 > -a, --attach=[] : 컨테이너에 표준 입력, 출력, 에러 연결
    ex) --attach="stdin" // stdout, stderr
 > --add-host=[] : 컨테이너의 /etc/hosts에 호스트 이름과 ip 주소 추가
    ex) --add-host=localshot:192.168.0.1
 > -c, -cpu-shares=0 : CPU 자원 분배 설정, 디폴트는 1024, 상대 적용
    ex) --cpu-shares=512
 > -cap-add=[] : 컨네이너에서 cgroups의 특징 Capability 사용 ALL을 지정하면 모든 Capability 사용
 > -d --detach=false : 컨테이너가 백그라운드에서 실행[데몬 모드]
 > --device=[] : 호스트 장치를 컨테이너에서 사용 <호스트 장치>:<컨테이너 장치>
 > -h, --hostname ="" : 컨테이너의 호스트 이름 설정
 > -m, --memory="" : 최대 메모리 설정, 단위 b,k,m,g
    ex ) --memory="1000k"
 > --name="" : 컨테이너 이름 설정
 > -p, --publish=[] : 컨테이너와 호스트 포트 연결하여 외부에 노출
 > 너무 많다, etc : http://pyrasis.com/book/DockerForTheReallyImpatient/Chapter20/28
ex)
 sudo docker run -p 8888:8080 centos

container 보기
 > docker container ls -a || docker ps -a
container 시작
    docker start <con_id>
재시작
    docker restart <con_id>
컨테이너 접속
    docker attach <con_id>
컨테이너 정지
    docker stop <con_id>
컨테이너 삭제
    docker rm <con_id>

docker 실행과 접속
 > sudo docker run -i -t -p 8888:8080 centos


docker commit 중요


>>>>>>>

sudo docker pull centos:latest // 기본 센토스를 도커허브에서 받는다
Dockerfile create // 기본 센토스를 기반으로, 도커 파일을 생성한다.
sudo docker build --tag server:01 . // 토커 파일로 나의 이미지를 만든다. //.은 현재 경로? 
sudo docker run -t -i --name dev2 server:0.1 /bin/bash // 만든 이미지를 컨테이너 생성
컨테이너가 생성되면서 접속이 된다. exit로 빠져나와
sudo docker container ls -a 로 확인하면, 컨테이너가 종료 되어있다.
sudo docker start dev 로 컨테이너를 시작해보자
다시 sudo docker container ls -a 로 확인하면, status에 up이라고 시작되었다.
sudo docker exec -i -t dev /bin/bash // 실행중인 컨테이너에 접속한다.
