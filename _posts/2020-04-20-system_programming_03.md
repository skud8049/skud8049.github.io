---
title: "[시스템 프로그래밍] 고수준 파일 입출력"
excerpt: "고수준 파일 입출력"

toc: true
toc_sticky: true

categories:
  - System Programming
tags:
  - 
last_modified_at: 2020-04-21T20:17:00
---

# 1. 고수준 파일 처리

- 파일
    - 관련 있는 데이터들의 집합, 하디디스크와 같은 저장장치에 일정한 형태로 저장
    - 유닉스에서의 파일은 데이터 저장 뿐만 아니라 데이터 전송, 장치 접근을 위해서도 사용

|저수준 파일 입출력|고수준 파일 입출력|
|:-:|:-:|
|비직관적|직관적(사용이 쉽다)|
|고수준 보다 빠름|저수준 보다 느림|
|바이트 단위로 읽고 쓴다|버퍼 단위로 읽고 쓴다|
|특수 파일에 대한 접근 가능|여러 가지 형식을 지원|
|open, close, read, write 등 | fopen, fclose, fread, fwrite 등|

## 1-1. 파일 포인터

- 고수준 파일 입출력에서 열린 파일을 가리키는 포인터
- 자료형으로 FILE * 형을 사용 (구조체에 대한 포인터)
- **0번** : stdin / **1번** : stdout / **2번** : stderr 은 미리 할당되어 있다.
    - 따라서, File을 가져오면 3번부터 할당된다.
    - 실패 시 NULL 리턴

# 2. 파일 열기, 닫기 (fopen, fclose)

```c
#include <stdio.h>

// 열기
FILE *fopen(const char *pathname, const char *mode);
FILE *fdopen(int fd, const char *mode);
FILE *freopen(const char *pathname, const char *mode, FILE *stream);

// 닫기
int fclose(FILE *stream);

// 문자 기반 입력
int fgetc(FILE *stream);
int getc(FILE *stream);
int getchar(void);
int getw(FILE *stream);

// 문자 기반 출력
int fputc(int c, FILE *stream);
int putc(int c, FILE *stream);
int putchar(int c);
int putw(int w, FILE *stream);
```
    pathname : 파일 지정
    mode : 모드 설정
    fd : file descriptor
    stream : 파일 포인터

|<center>모드</center>|<center>기능</center>|
|:--|:--|
|r|읽기 전용으로 텍스트 파일을 연다|
|w|새로 쓰기용으로 텍스트 파일을 연다. 기존 내용 삭제|
|a|추가용으로 텍스트 파일을 연다(append)|
|rb|읽기 전용으로 바이너리 파일을 연다|
|wb|새로 쓰기용으로 바이너리 파일을 연다. 기존 내용 삭제|
|ab|추가용으로 바이너리 파일을 연다|
|r+|읽기와 쓰기용으로 텍스트 파일을 연다|
|w+|쓰기와 읽기용으로 텍스트 파일을 연다|
|a+|추가와 읽기용으로 텍스트 파일을 연다|
|rb+|읽기와 쓰기용으로 바이너리 파일을 연다|
|wb+|쓰기와 읽기용으로 바이너리 파일을 연다|
|ab+|추가와 읽기용으로 바이너리 파일을 연다|

## 2-1. 파일 열기, 닫기 예제1


## 2-2. 파일 열기, 닫기 예제2