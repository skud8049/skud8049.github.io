---
title: "[시스템 프로그래밍] 저수준 파일 입출력-1"
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
- **0번** : stdin / **1번** : stdout / **2번** : stderr 은 미리 할당되어 있다.
    - 따라서, File을 가져오면 3번부터 할당된다.
    - 실패 시 -1 리턴



# 3. 파일 열기, 생성, 닫기 (open, close)

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
    mode : 권한 설정 (O_CREAT 생성 시)

|<center>**oflag의 종류**</center>||
|:-|:-|
|<center>종류|<center>기능|
|O_RDONLY|읽기 전용으로 연다.|
|O_WRONLY|쓰기 전용으로 연다.|
|O_RDWR|읽기/쓰기 가능하게 연다.|
|O_CREAT|파일이 없다면 파일 생성|
|O_EXCL|O_CREAT 옵션과 같이 사용할 경우, 기존에 파일이 없었다면 생성 / 있었다면 오류 출력|
|O_APPEND|파일의 맨 끝에 추가|
|O_TRUNC|파일이 이미 존재하고, 쓰기 옵션이라면 모든 내용 삭제하고 처음부터 쓴다.|
|O_NONBLOCK / O_NDELAY|비블로킹(Non-blocking) 입출력|
|O_SYNC / O_DSYNC|저장장치에 쓰기가 끝나아 쓰기 동작을 완료|

|<center>**mode의 종류**|||
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

## 3-1. 예제

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

    // mode 설정
    // 소유자 RW, 그룹 R, 기타 R
    mode = S_IRUSR | S_IWUSR | S_IRGRP | S_IROTH;

    // 파일이 없다면 O_CREAT에 의해 생성됨
    fd = open("test.txt", O_CREAT, mode);

    // 에러 발생 시
    if(fd == -1)
    {
        perror("Creat error!");
        exit(1);
    }
    // 정상적으로 열었다면 FD번호를 출력한다.
    else
    {
        printf("fd = %d\n", fd);
    }

    // 파일 닫기
    close(fd);

    return 0;
}
```
    [출력]
        fd = 3
    "test.txt" 파일 생성

# 4. 파일 읽기 / 쓰기 (read, write)

```c
//읽기
#include <unistd.h>
ssize_t read(int fd, void *buf, size_t count);
```
- 파일에서 count로 지정한 크기만큼 읽어서 buf에 저장
- 바이트 단위로 읽는다
- 읽은 바이트를 리턴, 파일의 끝이면 0을 리턴

```c
//쓰기
#include <unistd.h>
ssize_t write(int fd, const void *buf, size_t count);
```
- buf에 담긴 메모리에서 count 바이트만큼 파일에 기록
- 바이트 단위로 쓴다
- 쓰기를 수행한 바이트 수를 리턴

## 4-1. 예제

```c
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>
#include <fcntl.h>
#include <stdio.h>

int main()
{
    int rfd, wfd, n;
    char buf[10]; //버퍼

    // test.txt 파일을 가져온다
    rfd = open("test.txt", O_RDONLY);
    if(rfd == -1){
        perror("ERROR");
        return 1;
    }

    // test2.txt 파일을 가져온다 (없다면 생성)
    // flag 와 permission 설정
    wfd = open("test2.txt", O_WRONLY | O_CREAT | O_TRUNC, S_IRWXU);
    if(wfd == -1){
        perror("ERROR");
        return 1;
    }
    
    // 6 바이트 단위로 읽는다
    // n에는 읽어온 바이트가 저장
    while((n = read(rfd, buf, 6)) > 0){
        // 읽어온 바이트와 쓴 바이트가 다르면 에러
        if(write(wfd, buf, n) != n)
            perror("write error");
    }

    // read 에러 발생 시
    if(n == -1) perror("Read");

    close(rfd);
    close(wfd);

    return 0;
}
```
```
[test.txt] Lee's Life
[test2.txt] Lee's Life

test.txt의 내용이 test2.txt에 복사되었다.
```

# 5. 파일 오프셋 (lseek)

```c
#include <sys/types.h>
#include <unistd.h>

off_t lseek(int fd, off_t offset, int whence);
```
- **fd** : 파일 지정
- **offset** : 새롭게 지정할 포인터의 위치, 음수도 될 수 있다.
- **whence** : 오프셋의 기준
  - **SEEK_SET** : 파일의 맨 처음
  - **SEEK_CUR** : 현재 포인터의 위치
  - **SEEK_END** : 파일의 맨 마지막

<center><img src="ss"></center>

## 5-1. 예제

```c
off_t newpos;
newpos = lseek(fd, (off_t)0, SEEK_SET);
// 첫 번째 바이트로 이동
newpos = lseek(fd, (off_t)2, SEEK_CUR);
// 현재 포인터에서 뒤로 2바이트 이동
newpos = lseek(fd, (off_t)0, SEEK_END);
// 마지막 바이트로 이동
newpos = lseek(fd, (off_t)-1, SEEK_END);
// 마지막 바로 전(EOF)로 이동
newpos = lseek(fd, (off_t)0, SEEK_CUR);
// 현 위지
```


```c
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>

int main(void)
{
    int fd, n;
    off_t start, cur;
    char buf[256];

    // test.txt 열기
    fd = open("test.txt", O_RDONLY);
    if(fd==-1){
        perror("Open error");
        return 1;
    }

    // fd의 현재 포인터를 start에 저장
    start = lseek(fd, 0, SEEK_CUR);

    // 버퍼에 파일 읽기 (255바이트 이하)
    n = read(fd, buf, 255);

    // 출력을 위해 문자열 끝(\0)을 저장
    buf[n] = '\0';

    // 현재 포인터의 위치, 읽은 문자열, 읽은 문자 바이트 수 출력
    printf("Offset start=%d, Read str=%s, n=%d\n",
            (int)start, buf, n);

    // fd의 현재 포인터를 cur에 저장
    cur = lseek(fd, 0, SEEK_CUR);
    
    // 현재 포인터의 위치 출력
    printf("Offset cur=%d\n", (int)cur);

    close(fd);

    return 0;
}
```
```
[test.txt] Lee's Life

[출력]
Offset start=0, Read str=Lee's life, n=11
Offest cur=11
```
