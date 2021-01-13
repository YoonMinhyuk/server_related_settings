# Linux user 관련

**root** 권한이 있는 사용자만 계정 생성 및 삭제를 진행할 수 있다.

### user 확인
```shell
1. cat /etc/passwd
2. cat /etc/passwd | grep {계정명}
```

### user 생성
1. ***useradd***
```shell
sudo useradd -m {계정명} -G {그룹명} -p {비밀번호}

-m 옵션 : 사용자 home directory 까지 함께 생성
-G 옵션 : 그룹 지정 (해당 그룹이 없을 경우 생성)
-g 옵션 : 이미 존재하고 있는 그룹을 사용하여 그룹지정
-p 옵션 : 비밀번호 지정 (sudo passwd {계정명} 으로 비밀번호 변경 가능)
```

2. ***adduser***
    - **password** 및 **home directory** 설정을 알아서 수행해줌

```shell
sudo adduser {계정명}
```

### 사용자 비밀번호 설정
```shell
passwd {계정명}
```

### 사용자 변경
```shell
1. su {계정명}   : 사용자만 변경
2. su - {계정명} : 사용자 변경 및 home directory 로 이동
```

### 사용자 삭제
```shell
sudo userdel -r {계정명}

-r 옵션 : home directory 등 사용자와 관련된 모든 정보를 삭제하기 위해 사용
```

### sudoers 추가

**/etc/sudoers** 직접 수정해도 됌

```shell
1. {계정명} 에게 sudo 권한 추가
echo "{계정명} ALL=(ALL)" >> /etc/sudoers

2. {계정명} 에게 sudo 권한 추가 & 패스워드 확인 X
echo "{계정명} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
```