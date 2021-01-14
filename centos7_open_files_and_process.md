# CentOS 7 open files & process

### 최대 열 수 있는 파일 개 수 수정

kernel 제한 수정

```shell
vi /etc/sysctl.conf

fs.file-max=65535

# vi 종료

# /etc/sysctl.conf reload
sudo sysctl -p

# 최대 open files 개 수 조회
cat /proc/sys/fs/file-max
```

사용자별 제한 수정

```shell
# soft & hard open files 최대 개 수 변경
ulimit -SHn 65535

# soft & hard 프로세스 최대 개 수 변경
ulimit -SHu 65535


# ulimit 명령어에 의한 변경은 재부팅시 명령어를 입력한 세션에만 적용됌
# 따라서 재부팅 후에도 모든 사용자에 대해 위의 값이 적용되도록 아래 수행
vi /etc/security/limits.conf

# * (모든 사용자)  
* soft nofile 65565
* hard nofile 65535
* soft nproc 65535
* hard nproc 65535

# vi 종료

#sshd 재시작하여 재접속 또는 centos 재시작
systemctl restart sshd
#reboot

# soft 제한 확인
ulimit -a

# hard 제한 확인
ulimit -aH
```