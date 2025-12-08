# [고급운영체제 & 실무프로젝트] 완전 정복 요약 (PDF 원문 기준)

## 1. 🚨 [10점 확보] 서술형 족보 (⭐⭐⭐)
**Q. 패스워드 파일에서 현재 사용자 정보를 추출하여 출력하는 프로그램을 작성하시오.**

### (1) 핵심 로직
`getuid()`(내 ID) ➔ `getpwuid()`(정보 퍼오기)

### (2) 모범 답안 코드
```c
#include <stdio.h>
#include <unistd.h>      // getuid
#include <pwd.h>         // getpwuid, struct passwd

int main() {
    // 1. 현재 사용자 ID 획득
    uid_t uid = getuid();

    // 2. 패스워드 파일에서 uid와 일치하는 사용자 정보 획득
    struct passwd *pw = getpwuid(uid);

    // 예외 처리
    if (pw == NULL) return -1;

    // 3. 구조체 정보 출력 (멤버변수명 pw_ 주의)
    printf("Login Name: %s\n", pw->pw_name);
    printf("UID: %d\n", pw->pw_uid);
    printf("GID: %d\n", pw->pw_gid);
    printf("Home Dir: %s\n", pw->pw_dir);
    printf("Shell: %s\n", pw->pw_shell);

    return 0;
}
```
---

## 2. 📂 파일 및 디렉토리 관리 (매개변수 암기)

### (1) 파일/디렉토리 조작 (⭐⭐⭐)
* **파일 삭제:**
  * `int unlink(const char *path)`
  * `int remove(const char *path)`
* **디렉토리 생성:** `int mkdir(const char *path, mode_t mode)`
* **디렉토리 삭제:** `int rmdir(const char *path)`
* **링크 생성:** (⚠️변수명 `existing`, `new` 주의)
  * 하드링크: `int link(const char *existing, const char *new)`
  * 심볼릭링크: `int symlink(const char *existing, const char *new)`
* **이름 변경:** (⚠️변수명 `old`, `new` 주의)
  * `int rename(const char *old, const char *new)`
* **경로 변경:**
  * `int chdir(const char *path)`
  * `int fchdir(int fd)`
* **현재 경로 확인:** `char *getcwd(char *buf, size_t size)`

### (2) 디렉토리 스트림 읽기 (⭐⭐⭐)
* **열기:** `DIR *opendir(const char *path)`
* **읽기:** `struct dirent *readdir(DIR *dp)`
* **위치 확인:** `long telldir(DIR *dp)`
* **위치 이동:** `long seekdir(DIR *dp, long pos)`
* **되감기:** `long rewinddir(DIR *dp)`
* **닫기:** `long closedir(DIR *dp)`
* **구조체 `struct dirent`:** `d_ino`(번호), `d_name`(이름)

---

## 3. ⏰ 시간 관리 (매개변수 암기)

### (1) 시간 함수 (⭐⭐⭐)
* **현재 시간(초):** `time_t time(time_t *tloc)`
* **구조체 변환:**
  * `struct tm *gmtime(const time_t *clock)` (UTC 기준)
  * `struct tm *localtime(const time_t *clock)` (지역 시간 기준, **중요**)
* **반대 변환:** `time_t mktime(struct tm *tp)`
* **시간 차이:** `double difftime(time_t t1, time_t to)`

### (2) 문자열 출력 함수 (⭐)
* **기본 출력:** `char *ctime(const time_t *clock)`
* **구조체 기반 출력:** `char *asctime(const struct tm *tm)`
* **포맷 지정 출력:** `size_t strftime(char *s, size_t maxsize, const char *format, const struct tm *tm)`
  * 주요 포맷: `%Y`(년), `%m`(월), `%d`(일), `%H`(시), `%M`(분), `%S`(초)

### (3) `struct tm` 멤버 (⭐⭐)
* `tm_year`: 1900년 이후 년도
* `tm_mon`: 0~11 (1월이 0)
* `tm_mday`: 1~31

---

## 4. 👥 사용자 & 그룹 & 시스템 정보

### (1) 사용자 ID 구분 (⭐⭐⭐)
* **로그인명 확인:** `char *getlogin(void)`
* **실제 사용자 ID (RUID):** `uid_t getuid(void)`
* **유효 사용자 ID (EUID):** `uid_t geteuid(void)`
* **ID 설정:** `int setuid(uid_t uid)`

### (2) 정보 파일 및 함수 (⭐⭐)
* **패스워드 파일 (`/etc/passwd`)**:
  * `struct passwd *getpwnam(const char *name)`
  * `struct passwd *getpwuid(uid_t uid)`
* **Shadow 파일 (`/etc/shadow`)**: (관리자 전용)
  * `struct spwd *getspnam(const char *name)`
  * 구조체 `spwd`: `sp_pwdp`(암호), `sp_lstchg`(마지막 변경일)
* **그룹 파일 (`/etc/group`)**:
  * `struct group *getgrnam(const char *name)`
  * `struct group *getgrgid(gid_t gid)`
  * 구조체 `group`: `gr_name`(그룹명), `gr_gid`(그룹ID), `gr_mem`(멤버들)

### (3) 로그인 기록 및 시스템 정보 (⭐)
* **로그인 기록 (`/var/adm/utmp` 등):** `struct utmpx`, `ut_type` 상수(BOOT_TIME 등)
* **시스템 정보:**
  * `int uname(struct utsname *name)`
  * `struct utsname`: `sysname`, `nodename`, `release`, `version`, `machine`

---

## 5. 🚦 신호 (Signal)

### (1) 주요 신호 종류 (⭐⭐⭐)
* **SIGINT:** Ctrl+C (인터럽트)
* **SIGQUIT:** Ctrl+\ (종료 및 코어덤프)
* **SIGKILL:** **강제 종료** (무시/블록 불가)
* **SIGSTOP:** 실행 중단 (무시/블록 불가)
* **SIGALRM:** `alarm()` 시간 만료 시 발생
* **SIGCHLD:** 자식 프로세스 상태 변경(종료 등) 시 부모에게 알림
* **SIGSEGV:** 잘못된 메모리 접근 (Segmentation Fault)
* **SIGPIPE:** 끊어진 파이프에 쓰기 시도 시

### (2) 신호 처리 함수 (⭐⭐)
* **보내기:** `int kill(pid_t pid, int sig)`
* **대기:** `int pause(void)`
* **핸들러 등록:** `void (*signal(int sig, void (*disp) (int))) (int)`
  * `SIG_IGN`: 신호 무시
  * `SIG_DFL`: 기본 동작 수행
* **정보 출력:**
  * `void psignal(int sig, const char *s)`
  * `char *strsignal(int sig)`
