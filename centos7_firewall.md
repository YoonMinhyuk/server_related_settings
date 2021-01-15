# CentOS 7 Firewall

- 기본적으로 centos 7 에 설치되어있고 활성화 되어있음
- root 권한 필요

### firewall 실행 
```shell
# 이미 firewall 실행중인지 확인
systemctl status firewalld

# 재부팅 후에도 firewall 실행되도록 설정
systemctl enable firewalld

# firewalld 실행
systemctl start firewalld
```

### firewall
- firewalld 는 런타임 설정과 영구 설정이 분리되어 저장될 수 있도록 설계되었다.   
  런타임 설정을 변경하면 즉시 효과를 발휘할 수 있지만 효과는 사라질 수 있다.  
  영구 설정을 적용하면(--permanent 옵션 사용) firewalld 서비스의 재로드 또는 재시작이 호출되어도 변경된 내용이 사라지지 않는다.  
  영구 설정 변경은 즉시 적용되지 않으며 firewalld 서비스의 재로드 또는 재시작을 반드시 필요로 한다.  
  보통은 재로드를 많이 한다.(firewall-cmd --reload)
- `firewall` 는 임시로 적용된 것들은 즉시 반영됌. 하지만 `reload` 시 원래대로 돌아감
- `--permanent` option 사용시 영구적으로 반영되지만 반드시 `reload` 해야함. `reload` 하지 않을시 적용 안됌

```shell
# 지역(zone) 관련 정보 목록 확인
firewall-cmd --get-zones
firewall-cmd --list-all-zones
firewall-cmd --get-default-zone
firewall-cmd --list-all

# default zone  변경
firewall-cmd --set-default-zone=[zone]

# 특정 zone 에 임시로 네트워크 인터페이스 추가
firewall-cmd --zone=[zone] --add-interface=enp0s8

# 특정 zone 에 임시로 서비스 추가
firewall-cmd --zone=[zone] --add-service=[service(ftp)]

# 특정 zone 에 임시로 port 추가
firewall-cmd --zone=[zone] --add-port=[port]/[protocol] (1024/[tcp,udp,sctp,dccp])

# 특정 zone 에 영구적으로 네트워크 인터페이스 추가
firewall-cmd --permanent --zone=[zone] --add-interface=enp0s8

# 특정 zone 에 영구적으로 서비스 추가
firewall-cmd --zone=[zone] --add-service=[service(ftp,http, https)]

# 특정 zone 에 영구적으로 port 추가
firewall-cmd --zone=[zone] --add-port=[port]/[protocol] (1024/[tcp,udp,sctp,dccp])

# firewall 리로드. --permanent option 사용시 반드시 실행
firewall-cmd --reload
```