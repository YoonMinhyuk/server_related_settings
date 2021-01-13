# Linux group 관련

### Group 목록
```shell
1. groups

# 계정이 포함된 그룹 목록 나열
2. groups [계정명]

3. cat /etc/group
```

### Group 생성
```shell
groupadd [그룹명]

# -g 옵션 : 그룹 GID 지정. 등록된 마지막 GID 확인 후 그 다음 번호로 지정해야함
```

### Group 정보 수정
```shell
groupmod

# -n 옵션 : 그룹명 변경. groupmod -n [변경하고자 하는 그룹명] [변경전 그룹명] 
# -g 옵션 : 그룹 GID 변경. groupmod -g [변하고자 하는 GID] [변경전 GID]
```

### Group 삭제
```shell
groupdel [그룹명]
```

### Group password
```shell
# 패스워드 설정
gpasswd [그룹명]

# 패스워드 제거
gpasswd -r [그룹명]
```

### Group 사용 금지
```shell
gpasswd -R [그룹명]
```

### Group 사용자 추가
```shell
gpasswd -a [계정명] [그룹명]
```

### Group 사용자 삭제
```shell
gpasswd -d [사용자명] [그룹명]
```

### Group 사용자 초기화
```shell
gpasswd -M [사용자명] [그룹명]
```