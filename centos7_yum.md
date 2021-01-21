# Cent OS 7 package manager

## yum update
```shell
yum -y update

-y 옵션 : 패키지의 업데이트 진행 여부를 묻지 않고 모두 업데이트 수행
```

## package 검색
```shell
yum search [package name]
```

## package 설치

```shell
yum install [pacakge_name1 package_name2 package_name3...]

# -y 옵션 : 설치 여부 묻지않고 무조건 설치 
```

## package 삭제

```shell
yum remove [package_name1 package_name2 pakage_name3...]
```

# yum cache 

```shell

# 캐시된 패키지 정보 모두 삭제
yum clean packages

# XML 기반 파일의 메타데이터 캐시 삭제
yum clean metadata

# 캐시된 모든 데이터베이스 파일을 삭제
yum clean dbcache

# 사용하지 않는 불필요한 디스크 공간 정리 & 캐시된 모든 파일 삭제
yum clean all

# yum cache 재구성
yum makecache
```

