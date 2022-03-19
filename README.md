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
