# cli-test

# proot-debian-virgl

## termux 설치파일

- [termux]
  (https://f-droid.org/en/packages/com.termux/)

- [termux-x11]
  (https://github.com/termux/termux-x11/actions/workflows/debug_build.yml)

- [termux-api]
  https://f-droid.org/ko/packages/com.termux.api/

- [termux-widget]
  (https://github.com/termux/termux-widget/actions/workflows/debug_build.yml)

## 설치 사전 작업

termux
안드로이드 12 이상 사용자
Phantom Process Killer 해제

    adb shell "/system/bin/device_config put activity_manager max_phantom_processes 2147483647"

termux-setup-storage 명령어 입력
팝업창 열림 저장소 권한 허용
pkg update && upgrade
pkg install openssh ifconfig

passwd (비밀번호 설정)
sshd (ssh 실행)
ifconfig(기기의 IP주소 확인)

ssh 접속 port 8022

    pkg install -y ifconfig
    pkg install termux-api
    pkg install -y xwayland pkg install -y x11-repo pkg update -y pkg install -y pulseaudio virglrenderer-android proot-distro

[termux-x11.deb]
(https://github.com/termux/termux-x11/actions)

    dpkg -i ~/storage/downloads/[다운로드위치]*.deb
    sed '/allow-external-apps/s/^# //' -i ~/.termux/termux.properties termux-reload-settings

## Proot-distro debian 설치

    Proot-distro install debian

Proot-distro debian 접속

    Proot-distro login ubuntu --user root --shared-tmp

debconf 오류 해결위해 설치

    apt update -y && apt upgrade -y apt install -y dialog apt-utils

    groupadd storage
    groupadd wheel
    groupadd video

    apt install -y sudo nano psmisc htop software-properties-common wget mesa-utils dbus-x11 xfce4 xfce4-terminal onboard ibus ibus-hangul fonts-nanum

root 비밀번호 설정

    passwd

user 생성

    user [사용자이름] _user=[사용자이름] echo $_user ALL=\(root\) ALL > /etc/sudoers.d/$_user;chmod 0440 /etc/sudoers.d/$_user

user 로그인 sudo user 추가

    login [사용자이름]
    sudo nano /etc/sudoers

![sshd](https://github.com/maskelog/termux-proot-debian-virgl/blob/main/img/user.png)
이미지에서는 user 사용
ctrl+x, Enter 저장

## Termux:Widget 설정

Termux 실행 위젯

    mkdir .shortcuts
    echo '#!/bin/sh
    killall -9 termux-x11 Xwayland pulseaudio virgl_test_server_android
    termux-wake-lock; termux-toast "Starting X11"
    am start --user 1 -n com.termux.x11/com.termux.x11.MainActivity
    XDG_RUNTIME_DIR=${TMPDIR} termux-x11 :0 -ac &
    sleep 3
    pulseaudio --start --load="module-native-protocol-tcp auth-ip-acl=127.0.0.1 auth-anonymous=1" --exit-idle-time=-1
    pacmd load-module module-native-protocol-tcp auth-ip-acl=127.0.0.1 auth-anonymous=1
    virgl_test_server_android &
    proot-distro login debian --user [유저이름] --shared-tmp --no-sysvipc -- bash -c "export DISPLAY=:0 PULSE_SERVER=tcp:127.0.0.1:4713; GALLIUM_DRIVER=virpipe dbus-launch --exit-with-session startxfce4"' > ~/.shortcuts/LaunchXFCE

Termux 종료 위젯

    echo '#!/bin/sh
    killall -9 termux-x11 Xwayland pulseaudio virgl_test_server_android
    termux-wake-unlock; termux-toast "Stopping X11, virgl, etc."' > ~/.shortcuts/KillXFCE

## Termux llvmpipe 가속 바로가기 아이콘 사용

    [Desktop Entry]
    Name=[app] llvmpipe
    Exec=bash -c "env GALLIUM_DRIVER=llvmpipe [app]"
    Icon=[app icon]
    Terminal=false
    Type=Application

바로가기 키를 만들고 수정하는 방식으로 사용
Exec 부분만 수정
