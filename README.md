# Windows 11 & wsl2 & Docker Desktop 설정 가이드

## wsl
-

## Docker Desktop
도커 데스크탑 설치는 기본적으로 C 드라이브에 자동으로 설치가 된다. 필자는 D 드라이브로 이동시켜 작업을 하려한다.
#### > 최신 우분투 다운로드
```
docker pull ubuntu
```
설치 후 이미지를 받으면 자동으로 `C:\Users\<UserName>\AppData\Local\Docker\wsl\` 하위 `data` `distro` 폴더 내 각각 총2개의 `ext4.vhdx`를 생성한다.

#### > PowerShell (admin)
```
wsl --shutdown
wsl -l -v

  NAME                   STATE           VERSION
* docker-desktop         Stopped         2
  docker-desktop-data    Stopped         2
```
다음으로 실행 중인 wsl 을 모두 종료한다. wsl -l -v 명령어로 정지가 된것을 확인 한다.

```
wsl --export docker-desktop "D:\Docker\system\docker-desktop.tar"
wsl --export docker-desktop-data "D:\Docker\system\docker-desktop-data.tar"
```
필자는 `D:\Docker\system` 의 경로에 백업 tar 를 생성 하였다.
```
wsl --unregister docker-desktop
wsl --unregister docker-desktop-data
```
wsl에 등록된 도커 vhd 이미지를 해제 한다.
```
wsl --import docker-desktop "D:\Docker\vms\docker-desktop" "D:\Docker\system\docker-desktop.tar" --version 2
wsl --import docker-desktop-data "D:\Docker\vms\docker-desktop-data" "D:\Docker\system\docker-desktop-data.tar" --version 2
```
`wsl <옵션> <등록이름> "<생성할 vhd 위치>" "<백업 tar 경로>" <wsl 버전>`
백업한 이미지를 위의 명령으로 등록과 동시에 vhd 파일을 생성한다.
