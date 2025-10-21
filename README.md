# 파일시스템(File System)

## 1️ 개념  
운영체제가 **파일이나 자료를 쉽게 찾고 접근할 수 있도록 보관·조직하는 체제**입니다.

---

## 2️ 파일시스템의 종류  

### 🔹 (1) 수평적 파일시스템 (Flat File System)
- 디스크의 빈 공간에 파일들을 **구분 없이 저장** (디렉토리 X)  
- 구현이 간단하며, 파일 위치 계산이나 유지에 많은 메모리가 필요하지 않음  
- **초기 컴퓨터 환경**(하드웨어 자원 제한 시)에 사용  

### 🔹 (2) 계층적 파일시스템 (Hierarchical File System)
- 파일을 **논리적 디렉토리 구조로 구분 저장**  
- 디렉토리 / 서브디렉토리로 관리 가능  
- **현재 대부분의 OS에서 사용**

---

# 🐧 유닉스 파일시스템 (UNIX File System)

## 1 특징
- **계층적 파일시스템** 사용  
- **하드웨어 장치를 추상화**하여 파일로 관리 (장치 파일)  
- 모든 파일이 **하나의 루트(/)** 아래 구성됨  
- 이동식 파일시스템(mount, unmount) 지원  

---

## 2 파일의 종류

| 구분 | 설명 |
|------|------|
| **일반 파일 (Regular File)** | 디스크에 저장된 데이터의 집합 |
| **특수 파일 (Special File)** | 하드웨어 장치를 추상화한 파일 |
| **문자장치파일 (Character Device File)** | 키보드, 모니터, 프린터 등 바이트 단위 입출력 (비버퍼링) |
| **블록장치파일 (Block Device File)** | 디스크, DVD 등 블록 단위 입출력 (버퍼링 지원) |
| **디렉토리 (Directory)** | 파일명과 파일 간의 매핑 정보 관리 |
| **FIFO (Named Pipe)** | 프로세스 간 통신용 파이프 파일 |
| **유닉스 도메인 소켓 (UNIX Domain Socket)** | 같은 서버 내 프로세스 간 통신용 특수 파일 |

---

## 3 이동식 파일시스템 (Removable File System)
- 하나의 루트 디렉토리 아래 새 디스크를 연결 가능  
- `mount`, `unmount` 명령으로 관리  
- 일관된 파일시스템 유지 가능  

---

## 4 장치번호 (Device Number)
- **특수 파일**에는 두 가지 번호 존재  
  - **주장치번호 (major number)**: 장치의 대표 분류 (예: 디스크, I/O 장치 등)  
  - **부장치번호 (minor number)**: 주장치 내부의 세부 장치 식별 (예: 모니터 등)

---

## 5 I-노드 (i-node) 구조

| 구분 | 설명 |
|------|------|
| **I-노드** | 파일/디렉토리의 모든 정보를 가진 구조체 (64B) |
| **I-리스트 (I-list)** | 파일시스템 내의 모든 i-node의 리스트 |
| **I-번호 (I-number)** | 파일 식별용 고유 번호 (주소 포인터 역할) |

### i-node가 저장하는 정보
- 파일 소유자 ID / 그룹 ID  
- 접근 권한  
- 데이터 블록의 실제 디스크 주소  
- 파일 크기  
- 접근 / 변경 / i-node 변경 시간  
- 파일 형식  
- (특수파일의 경우) 주/부 장치번호  

---

## 6 링크(Link)

| 종류 | 설명 |
|------|------|
| **하드 링크 (Hard Link)** | 동일 파일을 여러 이름으로 접근 가능 (같은 파일시스템 내에서만) |
| **심볼릭 링크 (Symbolic Link)** | 원본 파일의 **경로명**만 저장 (서로 다른 파일시스템에서도 가능) |

---

# 파일 속성 (File Attributes)

## 1 i-node 정보 구조체 (struct stat)
```c
struct stat {
    dev_t     st_dev;      // i-node가 위치한 장치 번호
    ino_t     st_ino;      // i-node 번호
    mode_t    st_mode;     // 파일 형식 + 권한 비트
    nlink_t   st_nlink;    // 하드 링크 수
    uid_t     st_uid;      // 소유자 ID
    gid_t     st_gid;      // 그룹 ID
    dev_t     st_rdev;     // 특수 파일의 장치 번호
    off_t     st_size;     // 파일 크기(Byte)
    timestruc_t st_atim;   // 마지막 접근 시간
    timestruc_t st_mtim;   // 마지막 수정 시간
    timestruc_t st_ctim;   // i-node 변경 시간
    blksize_t st_blksize;  // I/O 버퍼 크기
    blkcnt_t  st_blocks;   // 사용 블록 수
};
```

---

## 2 파일 형식 매크로

| 매크로 | 의미 |
|---------|------|
| S_ISREG | 일반 파일 |
| S_ISDIR | 디렉토리 |
| S_ISCHR | 문자 특수 파일 |
| S_ISBLK | 블록 특수 파일 |
| S_ISLNK | 심볼릭 링크 |
| S_ISFIFO | FIFO 파일 |
| S_ISSOCK | 유닉스 도메인 소켓 |

---

## 3 접근 권한 속성

| 매크로 | 의미 |
|---------|------|
| S_ISUID | set-user-id-on-execution |
| S_ISGID | set-group-id-on-execution |
| S_ISVTX | sticky bit |

| 매크로 | 권한 | 설명 |
|---------|------|------|
| S_IRUSR | 읽기 | 소유자 읽기 |
| S_IWUSR | 쓰기 | 소유자 쓰기 |
| S_IXUSR | 실행 | 소유자 실행 |
| S_IRGRP, S_IWGRP, S_IXGRP | 그룹 권한 |
| S_IROTH, S_IWOTH, S_IXOTH | 기타 사용자 권한 |

---

# 파일 정보 함수

| 함수 | 설명 |
|------|------|
| **int stat(const char *path, struct stat *st)** | 파일의 i-node 정보 추출 |
| **int lstat(const char *path, struct stat *st)** | 링크 자체의 정보 추출 |
| **int readlink(const char *path, void *buf, size_t bufsiz)** | 심볼릭 링크의 경로명 읽기 |
| **char *realpath(const char *filename, char *resolvedname)** | 심볼릭 링크의 실제 경로 반환 |
| **int access(const char *path, int amode)** | 파일 존재/권한 점검 (`R_OK`, `W_OK`, `X_OK`, `F_OK`) |

---

# 파일 권한 비트

| 권한 | 의미 |
|------|------|
| **읽기(Read)** | 파일/디렉토리 내용 읽기 |
| **쓰기(Write)** | 파일 내용 수정, 디렉토리 내 파일 생성·삭제 가능 |
| **실행(Execute)** | 프로그램 실행, 디렉토리 접근 가능 |

---

# 특수 비트 권한

| 비트 | 설명 | 권한 값 |
|------|------|----------|
| **set-user-id (SUID)** | 실행 시 파일 소유자 권한으로 동작 | `04000` |
| **set-group-id (SGID)** | 실행 시 그룹 권한으로 동작 | `02000` |
| **sticky bit** | 디렉토리 내 다른 사용자의 파일 삭제 방지 | `01000` |
| **예시** | `/tmp` 디렉토리에 적용됨 |  |

---

# 파일 속성 변경 함수

| 함수 | 설명 |
|------|------|
| **int chmod(const char *path, mode_t mode)** | 파일 권한 변경 |
| **int fchmod(int fd, mode_t mode)** | 파일 디스크립터로 권한 변경 |
| **int chown(const char *path, uid_t owner, gid_t group)** | 파일 소유자/그룹 변경 |
| **int lchown(const char *path, uid_t owner, gid_t group)** | 링크 자체의 소유자/그룹 변경 |
| **int fchown(int fd, uid_t owner, gid_t group)** | 파일 디스크립터로 변경 |

#include <stdio.h>      // printf, perror
#include <stdlib.h>     // strtol, exit
#include <sys/stat.h>   // chmod, mode_t

int main(int argc, char *argv[]) {
    
    // 1. 인자 개수 확인
    // 사용법: ./my_chmod <mode> <filepath>
    // (argc는 3이어야 함: [0]프로그램이름, [1]모드, [2]파일경로)
    if (argc != 3) {
        fprintf(stderr, "사용법: %s <octal_mode> <file_path>\n", argv[0]);
        exit(1); // 비정상 종료
    }

    // 2. 인자 파싱
    const char *mode_str = argv[1]; // "755" 같은 문자열
    const char *path = argv[2];     // "myfile.txt" 같은 파일 경로

    // 3. 핵심: 권한 문자열을 8진수 숫자로 변환
    // strtol: 문자열을 long 타입 숫자로 변환하는 함수 [cite: 51]
    // NULL: 변환 후 남은 문자열을 저장할 포인터 (여기선 필요 없음)
    // 8: 입력된 문자열(mode_str)을 8진수로 해석하라는 의미
    mode_t mode = (mode_t)strtol(mode_str, NULL, 8);

    // 4. chmod 함수 호출로 권한 변경
    // int chmod(const char *path, mode_t mode); 
    if (chmod(path, mode) == -1) {
        // 실패 시 -1 반환
        perror("chmod error"); // 오류 원인 출력
        exit(1); // 비정상 종료
    }

    // 5. 성공
    printf("'%s'의 권한이 %s(으)로 변경되었습니다.\n", path, mode_str);
    
    return 0; // 정상 종료
}
