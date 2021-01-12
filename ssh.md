# SSH 정리

### permission
```
ssh 관련 파일의 permission 은 아래의 권한을 권장
1. 700
2. 600
3. 644
```

### public key & private key 생성
```
1. [참고 : GIT-SCM](https://git-scm.com/book/ko/v2/Git-%EC%84%9C%EB%B2%84-SSH-%EA%B3%B5%EA%B0%9C%ED%82%A4-%EB%A7%8C%EB%93%A4%EA%B8%B0)
2. ls -al ~ (현재 유저 디렉터리에 .ssh 디렉터리 존재 유무 확인)
3. 없으면 mkdir ~/.ssh && chmod 700 ~/.ssh 실행
4. cd ~/.ssh
5. ls -al (이미 키 존재하는지 확인하기 위함.)
6. *.pub 으로 끝나는 파일이 공개키(public key). 다른 파일은 개인키(private key)
7. 만약 키가 없다면 ssh-keygen -t rsa 명령어 실행 (-t rsa 는 ras 라는 암호화 방식으로 키를 생성한다는 의미. 없어도 상관없음)
8. 비밀번호 입력을 비워두면 키 사용시 암호를 묻지 않음
9. 키의 권한의 권장 권한 사용
``` 

### ssh config file
```
서버 설정
1. ls -al ~ (.ssh directory 존재 유무 확인)
2. 없으면 mkdir .ssh 실행
3. chmod 700 ~/.ssh
4. cd ~/.ssh
5. ls -al (authorized_keys.pub 파일 존재 유무 확인)
6. vi authorized_keys.pub
7. 사용할 public key 내용 입력
8. wq -> 내용 저장 & vi 종료
9. chmod 600 ~/.ssh/authorized_keys.pub
```

```
개인 설정
1. cd ~/.ssh
2. ls -al (config file 있는지 확인하기 위함)
3. vi config
4. Host example                              => alias 라고 생각
    HostName 127.0.0.1                       => 접속하고자 하는 서버의 IP
    User username                            => 접속하고자 하는 서버의 user
    Port 22                                  => ssh port
    IdentityFile ~/.ssh/private_key_name.pem => private key
5. chmod 440 ~/.ssh/config (permission 설정 - 파일 소유자만이 설정 파일을 읽을 수 있게 함. 위의 권장 설정 사용 가능)
```