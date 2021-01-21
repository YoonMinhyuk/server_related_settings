# CentOS 7 SELinux

- root 권한 필요
- selinux 의 주요 기본 개념 : **Label(보안문맥), policy(정책)**
- selinux 관점에서 리눅스 시스템은 수많은 객체로 분리되어있다.  
  (객체 : 모든 파일, 프로세스, 사용자, 소켓, 파이프)

- selinux 문맥에서 모든 객체는 특별한 레이블을 가진다.
- selinux 정책은 객체에 정의된 레이블을 사용해 **객체에 대한 접근을 제어할 수 있는 규칙**이다.
- 최소한의 권한 모델을 따른다. 즉, 모든 객체에 대한 최소한의 기능만 활성화한다는 의미이며 시스템 서비스와 같이 해당 기능만 수행하고 그 이상은 수행하지 않는다.
- /var/log/audit/audit.log 에 selinux 의 모든 로그 정보가 저장되며 읽기 편한 에러 메세지는 /var/log/messages 에 저장된다.

```shell
#selinux 관련 커맨드 메뉴얼
man -k selinux

# 자주 사용하는 명령어
sestatus
setenforce
semanage fcontext
restorecon
setsebool
sesearch
sealert
```

### selinux mode

- Enforcing
    - 서버의 보안을 높이고 시스템을 보호
    - 모든 정책이 로드된 상태로 실행될 때 시스템에 보안 규칙이 사용된다

- permissive
    - SELinux 사용을 의미
    - SELinux 보안 상태에서 시뮬레이션하고 실제로 실행되면서 모든 SELinux 정책 위반이 발생할 때마다 시스템 록가 생성됌
    - 시스템을 디버그하거나 시스템에서 정상 실행, 강제 실행 결과를 분석할 수 있다.
    - SELinux 에 대한 영향을 확인하지 못할 때 사용
    - permissive 모드는 추가적인 보안을 제공하지 않기 때문에 보안의 강화가 필요할 때는 반드시 Enforcing 모드로 변경해야한다.

- disabled : SELinux 사용하지 않음

### selinux 현재 mode 확인

```shell
sestatus
```

### selinux mode 변경

임시 변경

```shell
setenforce [mode]

# 변경된 모드 확인
sestatus
```

영구 변경

```shell
vi /etc/selinux/config
# SELINUX 값 변경
# SELINUX=[mode]
# example SELINUX=enforcing
# vi 종료
```

### semanage 설치

```shell
yum -y update

# selinux 작업을 위해 기본 툴킷 설치
yum -y install policycoreutils-python
yum install -y setools setools-console setroubleshoot*

# selinux manual 설치. 
# 특정 정책, 보안 문맥, SELinux boolean 에 대한 상세한 정보를 얻기위해 반드시 필요.
yum install -y policycoreutils-devel

# 시스템에서 사용 가능한 모든 SELinux 의 보안 문맥 정책에 대한 manual page 를 생성하고
# manual 페이지 데이터베이스를 변경
sepolicy manpage -a -p /usr/share/man/man8; mandb
```

### selinux label(보안 문맥) 작업

- 모든 selinux 객체는 하나의 보안 문맥만 가질 수 있다.

- selinux 보안 문맥을 출력하기 위해 표준 리눅스 커맨드라인에서 -Z 옵션 사용

- 객체의 보안 문맥
    - *_u : 사용자 (id -Z 로 확인 가능)
    - *_r : 역할  (ls -Z 로 확인 가능)
    - *_t : 타입  (ls -Z 로 확인 가능)
        - 표준 selinux 타입(targeted 라 불림)의 모든 접근 제어 걸정을 진행하기 위한 주요 메커니즘
        - selinux 의 모든 접근 제어 프로세스 타입 정책( SELinux access control process type enforcement) 또는 TE 라 불림

- 파일과 디렉토리 객체는 서로 연관된 객체의 그룹으로 묶기 위해 쓰이고   
  특정 정책 규칙을 파일과 디렉토리 객체에 정의할 수 있도록 동일하게 보호되거나 취급되어야 한다.   
  예를 들어 표준 메일 spool 디렉토리( /var/spool/mail) 의 모든 파일을   
  mail_spool_t 로 할당할 수 있으며 특정 접근을 허용할 수 있는 타입을 사용하는 접근 규칙 정책을 생성한다.

- 프로세스 문맥에서 타입 값은 도메인이라 불린다. 해당 타입은 프로세스를 격리하기 위해 사용된다.  
  특정 도메인 이름을 가진 모든 프로세스는 동일 도메인에서 다른 프로세스와 통신하고 상호 작용 할 수 있다. 도메인으로 프로세스를 분리하는 부분은 보안 위험을 크게 줄일 수 있다.  
  격리된 프로세스가 손상되면 해당 프로세스는 다른 프로세스에 영향을 주지 않고 해당 프로세스에만 영향을 준다.

- ps -auxZ 를 실행하면 unconfined_t 라는 도메인으로 실행되는 프로세스를 볼 수 있다.  
  unconfined_t 레이블로 실행되는 프로세스는 SELinux 정책으로 보호받지 못한다.  
  즉, 프로세스가 손상되면 selinux 는 시스템 자원과 데이터에 접근 권한을 얻으려는 공격자를 방어할 수 없다.  
  그리고 보안은 표준 DAC(Discretionary Access Control) 규칙으로  
  후퇴해서 시스템에서는 유일한 보안 정책이 DAC 규칙이 될 것이며 DAC 규칙만 적용될 것이다.

```shell
# 시스템에서 사용할 수 있는 모든 문맥 타입을 출력
seinfo -t

# 모든 객체에 SELinux label 출력 방법(-Z 옵션 사용)
id -Z
ls -Z
ps -auxZ
netstat-tulpenZ

# semanage fcontext manual
man semanage-fcontext

# 시스템에서 파일과 디렉토리에 사용 가능한 모든 보안 문맥 이름을 출력
semanage fcontext -l

# httpd label 만 필터링
semanage fcontext -l | grep httpd

# 보안 문맥 변경
semanage fcontext -a -t [label] [object]

# 파일 시스템에 정책 레이블 동기화
restorecon -v [object]

# 변경된 내용이 제대로 반영됐는지 확인
ls -Z 

# 보안 문맥 변경 example
touch /tmp/test.txt
ls -Z /tmp/test.txt
semanage fcontext -a -t httpd_sys_content_t /tmp/text.txt
restorecon -v /tmp/test.txt
# 변경된 내용이 제대로 반영됐는지 확인
ls -Z 
```

### selinux 정책(policy) 작업

- selinux 시스템의 핵심은 정책이다.
- 정책은 모든 객체 간의 접근 권한과 관계를 정의하는 정확한 규칙이다.
- 모든 시스템 객체는 레이블이 있고 그중 하나는 정책에 의해 정의된 구칙이 적용될 수 있는 타입 식별자이다.
- selinux 가 적용된 모든 시스템에서는 기본적으로 정책 규칙이 다르게 정의되지 않는 한 모든 객체에 대한 모든 접근이 금지된다.
- Boolean 값을 사용해 런타임에 해당 정책 기능을 제어(활성화 | 비활성화) 할 수 있다.
- 정책을 영구적으로 변경하려면 -P 옵션을 사용. 리부팅 후에도 값이 적용되어있음.
- 파일 복사시 해당 파일의 보안 문맥 타입은 부모 디렉토리의 보안 문맥 타입을 상속한다. 만약 원본 파일의 보안 문맥을 그대로 유지하기 원한다면 cp -preserve=context 를 사용한다.

```shell
# 모든 selinux boolean 정책 출력
semanage boolean -l

# httpd 데몬에만 적용된 모든 selinux boolean 정책 설정 출력
semanage boolean -l | grep httpd

# 특정 정책 활성화
setsebool [selinux boolean] [on 또는 off]
#example
setsebool httpd_can_network_connect on
setsebool httpd_can_network_connect off

# 특정 정책 영구적으로 활성화
setsebool -P [selinux boolean] [on 또는 off]
#example
setsebool -P httpd_can_network_connect on
setsebool -P httpd_can_network_connect off

# 사용할 수 있는 모든 httpd 정책 규칙 조회
sesearch --allow | grep httpd_t
```

### selinux 문제 해결
- sealert 명령어 사용
  - 사람이 읽을 수 있는 형태로 selinux 문제에 대한 상세 설명을 얻을 수 있고, 문제 해결을 위한 추천 솔루션을 제공한다.
  - 감사 로그 파일(/var/log/audit/audit.log) 과 메세지 로그 파일(/var/log/messages) 을 분석하여  
    사람이 읽을 수 있는 형태의 의미있는 AVC(access vector cache) 컨텐트를 만들어낼 수 있는 편리한 프로그램이다.
  - 모든 경고 메세지의 끝에 문제 해결에 도움이 될만한 내용이 출력된다.  
    하지만 자동으로 생성된 메세지이기 때문에 적용하기 전에 항상 의문을 갖고 조심스럽게 처리해야한다. 
  
```shell
# /var/log/audit/audit.log 를 사람이 읽을 수 있는 최신 로그 파일로 생성
sealert -a /var/log/audit/audit.log
```