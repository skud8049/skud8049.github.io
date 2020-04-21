---
title: "[시스템 프로그래밍] 저수준 파일 입출력"
excerpt: "저수준 파일 입출력"

toc: true
toc_sticky: true

categories:
  - System Programming
tags:
  - 
last_modified_at: 2020-04-20T20:17:00
---

# 1. 저수준 파일 처리

- 유닉스 계열은 C언어 표준 라이브러리에서 저수준, 고수준 파일을 지원한다.
- 파일 디스크립터 번호를 사용해 입, 출력
- Socket, Device handling에는 저수준 파일 IO 사용한다.

|저수준|고수준|
|:-:|:-:|
|비직관적|직관적|
|빠름|비교적느림|
|open, close, read, write 등 | fopen, fclose, fread, fwrite 등|



# 2. File descriptor

- 현재 열려있는 파일을 구분하는 정수값
- 저수준 파일 입출력에서 열린 파일을 참조하는데 사용 됨
- 0번 : stdin / 1번 : stdout / 2번 : stderr 은 미리 할당되어 있다.
    - 따라서, File을 가져오면 3번부터 할당된다.
    - 실패 시 -1 리턴



# 3. open() : 파일 열기, 생성, 닫기

```c
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

// 열기
int open(const char *pathname, int oflag);
int open(const char *pathname, int oflag, mode_t mode);

// 생성
int creat(const char *pathname, int oflag);
int creat(const char *pathname, int oflag, mode_t mode);

// 닫기
int close(int fd);
```
    path : 파일 지정
    oflag : 기능 설정
    mode : 권할 설정

|<center>oflag의 종류</center>||
|:-|:-|
|<center>종류|<center>기능|
|O_RDONLY|읽기 전용으로 연다.|
|O_WRONLY|쓰기 전용으로 연다.|
|O_RDWR|읽기/쓰기 가능하게 연다.|
|O_CREAT|파일이 없다면 파일 생성|
|O_EXCL|O_CREAT 옵션과 같이 사용할 경우, 기존에 파일이 없었다면 생성 / 있었다면 오류 출력|
|O_APPEND|파일의 맨 끝에 추가|
|O_TRUNC|파일이 이미 있고 쓰기 옵션이라면 모든 내용 삭제하고 처음부터 쓴다.|
|O_NONBLOCK / O_NDELAY|비블로킹(Non-blocking) 입출력|
|O_SYNC / O_DSYNC|저장장치에 쓰기가 끝나아 쓰기 동작을 완료|

|<center>mode의 종류|||
|:-|:-|:-|
|<center>플래그|<center>모드|<center>기능|
|S_IRWXU|0700|소유자 읽기/쓰기/실행 권한|
|S_IRUSR|0400|소유자 읽기 권한|
|S_IWUSR|0200|소유자 쓰기 권한|
|S_IXUSR|0100|소유자 실행 권한|
|S_IRWXG|0070|그룹 읽기/쓰기/실행 권한|
|S_IRGRP|0040|그룹 읽기 권한|
|S_IWGRP|0020|그룹 쓰기 권한|
|S_IXGRP|0010|그룹 실행 권한|
|S_IRWXO|0007|기타 사용자 읽기/쓰기/실행 권한|
|S_IROTH|0004|기타 사용자 읽기 권한|
|S_IWOTH|0002|기타 사용자 쓰기 권한|
|S_IXOTH|0001|기타 사용자 실행 권한|

## 3-1 파일 입력 예제

```c
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>

int main()
{
    int fd;
    mode_t mode;

    mode = S_IRUSR | S_IWUSR | S_IRGRP | S_IROTH;

    fd = open("test.txt", O_CREAT, mode);

    if(fd == -1)
    {
        perror("Creat error!");
        exit(1);
    }
    else
    {
        printf("fd = %d\n", fd);
    }

    close(fd);

    return 0;
}
```
    [출력]
        fd = 3

    [코드설명]
    
    - mode 플래그 = 소유자 읽기/쓰기, 그룹 읽기, 기타사용자 읽기 권한으로 설정
    
    - test.txt 파일을 열고 File descriptor 번호를 fd에 할당
        - 만약 test.txt 파일이 없다면 O_CREAT에 의해 파일이 생성됨
    
    - 만약, 파일을 열지 못했다면 fd = -1 이므로 에러 메세지 표시


# 작성중.......