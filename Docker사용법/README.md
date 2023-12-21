# Docker
\
우분투 다운로드

```
docker pull ubuntu:20.04
```

\
컨테이너 생성

```
docker run -it --storage-opt size=20G --name {컨테이너명} ubuntu:20.04 /bin/bash
```

> -it: docker 컨테이너에 표준 입력(stdin)을 열어두고(-i), 가상 터미널을 열어(-t) 키보드의 입력을 표준 입력으로 전달할 수 있도록 하는 옵션
> --storage-opt size=20G: 컨테이너의 디스크용량을 미리 정해주고 시작 Default는 10G
> /bin/bash: 컨테이너의 /bin/bash를 실행하여 터미널이 열리도록함

\
컨테이너 나가기

```
exit
```

\
컨테이너 확인

```
docker ps -a
```

> -a이 없으면 종료된 컨테이너는 표시되지 않음

\
컨테이너 삭제

```
docker rm -f {컨테이너명}
```

> -f로 꺼지지 않은 컨테이너도 강제로 삭제함

\
컨테이너 실행

```
docker start {컨테이너명}
```

\
컨테이너 접속

```
docker exec -it {컨테이너명} /bin/bash
```

\
sudo 설치

```
apt-get update && apt-get -y install sudo
```

\
apt 업데이트

```
sudo apt update -y
sudo apt upgrade -y
```

\
git 설치

```
sudo apt-get install git -y
```
