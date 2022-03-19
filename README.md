# Windows 11 & wsl2 & Docker Desktop 설정 가이드
필자의 기록용으로 남기려 깃을 작성하였다.


## Windows 11 & wsl2 설정
wsl 를 사용하려는 경우 굳이 windows 기능에서 `Hyper-V` 를 설치 하지 않아도 사용이 가능했다.

`Linux용 Windows 하위시스템` 기능 설치를 하여 wsl 를 활성화 후 
#### > PowerShell (admin)
```
> dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
배포 이미지 서비스 및 관리 도구
버전: 10.0.22000.1

이미지 버전: 10.0.22000.556

기능을 사용하도록 설정하는 중
[==========================100.0%==========================]
작업을 완료했습니다.
```
`가상머신 플랫폼 옵션 기능 사용` 명령을 한 후 재부팅 한다.
```
> wsl --set-default-version 2
WSL 2와의 주요 차이점에 대한 자세한 내용은 https://aka.ms/wsl2를 참조하세요
작업을 완료했습니다.
```
이후 wsl 버전을 기본값으로 2 를 지정하고 나면 설정은 끝난다.


## Docker Desktop 설정
도커 데스크탑 설치는 기본적으로 C 드라이브에 자동으로 설치가 된다. 필자는 D 드라이브로 이동시켜 작업을 하려한다.

만약 기본 드라이브로 작업하려 한다면 이과정은 하지 않아도 된다.
#### > console / 최신 우분투 다운로드
```
> docker pull ubuntu
```
설치 후 이미지를 받으면 자동으로 `C:\Users\<UserName>\AppData\Local\Docker\wsl\` 하위 `data` `distro` 폴더 내 각각 총2개의 `ext4.vhdx`를 생성한다.

#### > PowerShell (admin)
```
> wsl --shutdown
> wsl -l -v

  NAME                   STATE           VERSION
* docker-desktop         Stopped         2
  docker-desktop-data    Stopped         2
```
다음으로 실행 중인 wsl 을 모두 종료한다. wsl -l -v 명령어로 정지가 된것을 확인 한다.

```
> wsl --export docker-desktop "D:\Docker\system\docker-desktop.tar"
> wsl --export docker-desktop-data "D:\Docker\system\docker-desktop-data.tar"
```
필자는 `D:\Docker\system` 의 경로에 백업 tar 를 생성 하였다.
```
> wsl --unregister docker-desktop
> wsl --unregister docker-desktop-data
```
wsl에 등록된 도커 vhd 이미지를 해제 한다. 이미지를 생성함과 동시에 기존의 `ext4.vhdx` 파일은 삭제된다.
```
> wsl --import docker-desktop "D:\Docker\vms\docker-desktop" "D:\Docker\system\docker-desktop.tar" --version 2
> wsl --import docker-desktop-data "D:\Docker\vms\docker-desktop-data" "D:\Docker\system\docker-desktop-data.tar" --version 2
```
`wsl <옵션> <등록이름> "<생성할 vhd 위치>" "<백업 tar 경로>" <wsl 버전>`

백업한 이미지를 위의 명령으로 등록과 동시에 vhd 파일을 생성한다. 필자는 `D:\Docker\vms\` 의 경로로 지정하였으며, 주의할 점은 동일 폴더에 2개 이상의 vhd는 생성할 수 없다.

이후 도커를 재시작하면 이동된 경로로 사용이 가능해진다.


## Dokcer 기본 셋팅
도커 이미지를 받고 컨테이너를 만들고 생성을 해보려 한다. 먼저 Docker Desktop 을 실행 시켜 Docker service를 활성화 한다.
#### > console
```
> docker pull ubuntu
```
도커 이미지를 받는 방법은 아까와 동일하다. 첫 셋팅때 이미 받았으므로 이 단계는 넘어가도 된다.
```
> docker create -it --name test ubuntu
```
`docker <run> or <create> -it --name <표시 이름> <이미지명>` 이름을 지정하지 않으면 랜덤으로 컨테이너 이름이 지정된다.
```
> docker start test
```
`docker start <컨테이너 명>` 으로 컨테이너를 실행 시켜 준다.
```
> docker attach test
```
`docker attach <컨테이너 명>` 으로 컨테이너에 접속이 가능하다.
#### > 도커 기본 명령어
```
> docker ps -a  / 도커 상태 출력
CONTAINER ID   IMAGE     COMMAND   CREATED         STATUS                        PORTS     NAMES
972129234ee8   ubuntu    "bash"    5 minutes ago   Exited (130) 11 seconds ago             test

> docker stop test / 생행된 컨테이너 정지
```
#### > 컨테이너 접속 후
```
> docker attach test
root@972129234ee8:/#
```
접속에 성공한다면 콘솔창의 상태가 위와같이 변하게 된다.
```
> apt-get update && apt-get upgrade

> passwd root
New password:
Retype new password:
passwd: password updated successfully

> apt-get install sudo vim wget -y

> cd root
> wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
> bash Miniconda3-latest-Linux-x86_64.sh
> enter
> q
> yes
> enter
> yes
> export PATH="/root/miniconda3/bin:$PATH"
> source ~/miniconda3/etc/profile.d/conda.sh

> conda create -n test python=3.7.5
> conda activate test
> pip install django 
```
필자가 원하는 기본적인 셋팅은 위와 같다.

위과정은 도커에 접속에서 간단한 사용법이며 일반적으로 도커파일(스크립트)을 생성하여 위 작업을 자동화 한다.
