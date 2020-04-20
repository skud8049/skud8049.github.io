---
title: "[시스템 프로그래밍] 시스템 호출, 라이브러리 함수의 오류 처리"
excerpt: "오류 처리 함수"

toc: true
toc_sticky: true

categories:
  - System Programming
tags:
  - 
last_modified_at: 2020-04-20T20:17:00
---

전역변수 `errno`에 마지막 오류 코드가 저장된다.

# 1. 시스템 호출의 오류 처리

시스템 호출 함수 `access`는 성공 시 0, 실패 시 -1을 리턴한다.

```c
#include <stdio.h>
#include <unistd.h>
#include <errno.h>

extern int errno;

int main(void)
{
    // test.txt가 존재하지 않는다고 가정
    if(access("test.txt", F_OK) == -1){
        printf("errno = %d\n", errno);
    }

    return 0;
}
```
    [출력]
    errno = 2

# 2. 라이브러리 함수의 오류 처리

라이브러리 함수 `fopen`는 오류 발생 시 NULL, 리턴값이 int형이면 -1을 리턴한다.

```c
#include <stdlib.h>
#include <stdio.h>
#include <errno.h>
extern int errno;

int main(){
    FILE *fp;

    if((fp = fopen("test.txt", "r")) == NULL){
        printf("errno = %d\n", errno);
        exit(1);
    }

    fclose(fp);

    return 0;
}
```
    [출력]
    errno = 2

## 2-1. 오류 메세지 출력 perror()

```c
#include <stdio.h>
void perror(const char *str)
```
발생 오류 번호에 대한 메세지를 사용자의 메세지와 함께 출력
```c
    ...

    if((fp = fopen("test.txt", "r")) == NULL){
        perror("ERROR!");
        exit(1);
    }

    ...
```
    [출력]
    ERROR!: No such file or directory

## 2-2. 오류 메세지 출력 strerror()

```c
#include <string.h>
char *strerror(int errnum)
```
에러 번호에 대한 메세지를 리턴
```c
#include <string.h>

    ...

    if(access("test.txt", R_OK) == -1){
        printf("%s\n", strerror(errno));
        exit(1);
    }

    ...
```
    [출력]
    ERROR!: No such file or directory
