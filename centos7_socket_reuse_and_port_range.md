# CentOS 7 socket reuse & port range

### 소켓 재사용 & port 범위 지정

```shell
vi /etc/sysctl.conf

# TCP TIME_WAIT socket 재사용 여부
net.ipv4.tcp_tw_reuse=1

# port 범위 수정 (대량의 TIME_WAIT 발생시 연결을 시도하려는 port 의 부족이 발생할 수 있음)
net.ipv4.ip_local_port_range="1024 65535"

# vi 종료

# /etc/sysctl.conf reload
sudo sysctl -p

systemctl restart network
```