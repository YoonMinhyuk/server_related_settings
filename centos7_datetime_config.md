# CentOS 7 시간 동기화

### 하드웨어 시간 확인
```shell
# -r, --show option 사용해도 됌. hwclock 과 동일한 결과를 나타냄
hwclock
```

### 시스템 시간 확인
```shell
date
```

### 하드웨어 & 시스템 시간 동기화
```shell
# 시스템 시간과 동일하게 하드웨어 시간 변경
hwclock --systohc
# or
hwclock -w

# 하드웨어 시간과 동일하게 시스템 시간 변경
hwclock --hctosys
# or
hwclock -s
```

### NTP
```shell
# NTP 가 설치되어있지 않을 경우 설치 & 활설화
yum -y update
yum -y install ntp ntpdate ntp-doc
systemctl enable ntp
systemctl start ntp
systemctl status ntp
```

### NTP & Chrony 
```shell
# package update
yum -y update

# chrony 설치
yum install -y chrony

# chrony 를 서버 부팅 후 재시작 될 수 있도록 활성화
systemctl enable chronyd

# chronyd 실행
systemctl start chronyd

# chronyd 상태 확인
systemctl status chronyd

# 유효한 서버 타임존 리스트 확인
timedatectl list-timezones

# 서버 타임존 UTC 로 변경
timedatectl set-timezone [timezone]

# 네트워크를 통해 system clock 을 동기화하기 위해 NTP 를 이미 사용중인지 확인
# timedatectl 명령어 입력후 NTP synchronized 목록 찾아서 확인해도 됌
timedatectl | grep "NTP synchronized"

# 이전 command 의 결과가 NO 라면 NTP 활성화를 위해 아래의 커맨드 입력
timedatectl set-ntp yes 또는 true
timedatectl | grep "NTP synchronized"

# chrony 는 기본적으로 원자시계에 접근할 수 있는 공개 서버를 사용하지만 
# 서비스 최적화를 위해 서버가 사용될 시간대를 간소화하고 최적화 하기 위해 일부 내용을 수정해야한다
# NTP 동기화 활성화 타임 서버 설정
vi /etc/chrony.conf

# 아래의 내용을 포함하는 라인을 찾는다
# server 0.centos.pool.ntp.org iburst 
# server 1.centos.pool.ntp.org iburst 
# server 2.centos.pool.ntp.org iburst 
# server 3.centos.pool.ntp.org iburst

# 지리적으로 현재 위치 근처에 있는 내부 서버 목록을 얻기 위해 
# https://www.ntppool.org 를 방문 
# 3개 이상의 NTP 서버를 사용하면 NTP 서비스의 정확성을 높일 수 있다
# 원하는 내부 시간 서버의 목록으로 변경한다
# server 0.asia.pool.ntp.org iburst 
# server 1.asia.pool.ntp.org iburst 
# server 2.asia.pool.ntp.org iburst 
# server 3.asia.pool.ntp.org iburst
# vi 종료

# chronyd 재시작
systemctl restart chronyd
systemctl status chronyd
# 현재 날짜 확인
date

# 시스템 시간으로 하드웨어 시간 변경
hwclock --systohc
hwclock -r
```

