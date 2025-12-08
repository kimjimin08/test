# [고급운영체제 & 실무프로젝트] 완전 정복 요약

## 1. 🚨 [10점 확보] 서술형 족보 (⭐⭐⭐)
**Q. 패스워드 파일에서 현재 사용자 정보를 추출하여 출력하시오.**

### (1) 핵심 로직
`getuid()`(내 ID) ➔ `getpwuid()`(정보 퍼오기)

### (2) 모범 답안 코드
#include <stdio.h>
#include <unistd.h>  // getuid
#include <pwd.h>     // getpwuid, struct passwd
int main() {
    // 1. 현재 사용자 ID 획득
    uid_t uid = getuid();
    // 2. 패스워드 파일에서 정보 획득
    struct passwd *pw = getpwuid(uid);
    // 예외 처리
    if (pw == NULL) return -1;
    // 3. 정보 출력 (멤버변수 pw_ 접두어 주의!)
    printf("계정명: %s\n", pw->pw_name);
    printf("UID: %d\n", pw->pw_uid);
    printf("GID: %d\n", pw->pw_gid);
    printf("홈디렉토리: %s\n", pw->pw_dir);
    printf("쉘: %s\n", pw->pw_shell);
    return 0;
}
[cite_start][cite: 38, 101, 103]

---

## 2. 📂 파일 및 디렉토리 관리

### (1) 파일/디렉토리 조작 (⭐⭐⭐)
* [cite_start]**파일 삭제:** `unlink(path)` [cite: 449][cite_start], `remove(path)` [cite: 452]
* [cite_start]**디렉토리 삭제:** `rmdir(path)` [cite: 462] (비어있어야 함)
* [cite_start]**디렉토리 생성:** `mkdir(path, mode)` [cite: 458] (**mode** 인자 필수!)
* [cite_start]**이름 변경:** `rename(old, new)` [cite: 477]
* **링크 생성:**
    * [cite_start]하드링크: `link(기존, 새)` [cite: 469]
    * [cite_start]심볼릭링크: `symlink(기존, 새)` [cite: 472]
* [cite_start]**경로 변경:** `chdir(path)`, `fchdir(fd)` [cite: 487-488]
* [cite_start]**현재 경로 확인:** `getcwd(buf, size)` [cite: 483]

### (2) 디렉토리 읽기 (⭐⭐⭐)
* [cite_start]**순서:** `opendir()` ➔ `readdir()` ➔ `closedir()` [cite: 503-516]
* **위치 제어(추가됨):**
    * [cite_start]`telldir(dp)`: 현재 위치(오프셋) 반환 [cite: 507]
    * [cite_start]`seekdir(dp, pos)`: 위치 이동 [cite: 509]
    * [cite_start]`rewinddir(dp)`: 맨 처음으로 되감기 [cite: 513]
* [cite_start]**구조체 `struct dirent`:** `d_ino`(번호), `d_name`(이름) [cite: 495]

---

## 3. ⏰ 시간 관리

### (1) 시간 함수 (⭐⭐⭐)
* [cite_start]**현재 시간(초):** `time(NULL)` (1970.1.1 기준 초) [cite: 527]
* **구조체 변환:**
    * [cite_start]`gmtime(time_t*)`: UTC(세계표준시) 기준 [cite: 550]
    * [cite_start]`localtime(time_t*)`: **지역 시간대** 기준 (가장 많이 씀) [cite: 553]
* [cite_start]**반대 변환:** `mktime(struct tm*)` (구조체 ➔ 초) [cite: 556]
* [cite_start]**시간 차이:** `difftime(t1, t0)` (두 시간의 차이 초 계산) [cite: 561]

### (2) 문자열 출력 함수 (⭐)
* [cite_start]`ctime(time_t*)`: "Day Mon dd hh:mm:ss yyyy" 형식 자동 출력 [cite: 567]
* [cite_start]`asctime(struct tm*)`: `ctime`과 동일 형식 [cite: 579]
* [cite_start]`strftime(buf, size, format, tm*)`: 내 맘대로 포맷 지정 [cite: 582]
    * [cite_start]주요 포맷: `%Y`(년), `%m`(월), `%d`(일), `%H`(시), `%M`(분), `%S`(초) [cite: 592-595]

### (3) `struct tm` 멤버 (⭐⭐)
* `tm_year`: 1900년 이후 년도 (예: 2023년이면 123)
* `tm_mon`: 0~11 (1월이 0)
* [cite_start]`tm_mday`: 1~31 [cite: 546]

---

## 4. 👥 사용자 & 그룹 & 시스템 정보

### (1) 사용자 ID 구분 (⭐⭐⭐)
* [cite_start]**RUID (실제):** `getuid()` - 프로그램을 실행한 찐 사용자 [cite: 38]
* [cite_start]**EUID (유효):** `geteuid()` - 파일 접근 권한을 결정하는 ID [cite: 38]
* [cite_start]**ID 변경:** `setuid(uid)` [cite: 40][cite_start], `seteuid(uid)` (EUID만 변경) [cite: 42]

### (2) 정보 파일 및 함수 (⭐⭐)
* [cite_start]**`/etc/passwd` (사용자 정보):** `getpwuid(uid)`, `getpwnam(name)` [cite: 101]
* [cite_start]**`/etc/shadow` (암호화 비번):** `getspnam(name)` (관리자만 가능) [cite: 145]
    * [cite_start]구조체 `spwd`: `sp_pwdp`(암호), `sp_lstchg`(마지막 변경일) [cite: 130]
* [cite_start]**`/etc/group` (그룹 정보):** `getgrgid(gid)`, `getgrnam(name)` [cite: 169-170]
    * [cite_start]구조체 `group`: `gr_name`(그룹명), `gr_gid`(그룹ID), `gr_mem`(멤버들) [cite: 160]

### (3) 로그인 기록 (utmp/wtmp) (⭐)
* [cite_start]파일 위치: `/var/adm/utmp` (현재 로그인), `/var/adm/wtmp` (히스토리) [cite: 183]
* [cite_start]구조체 `utmpx`: `ut_user`(유저명), `ut_type`(로그인 타입) [cite: 226]
* [cite_start]`ut_type` 상수: `BOOT_TIME`(부팅), `USER_PROCESS`(로그인 중), `DEAD_PROCESS`(종료됨) [cite: 209-219]

### (4) 시스템 정보 (⭐)
* [cite_start]**`uname(struct utsname*)`:** OS 이름, 버전, 하드웨어 정보 확인 [cite: 252]
    * [cite_start]멤버: `sysname`(OS명), `nodename`(호스트명), `release`, `version`, `machine` [cite: 255]
* [cite_start]**`sysinfo()`:** 시스템 설정 정보 확인 [cite: 265]

---

## 5. 🚦 신호 (Signal)

### (1) 주요 신호 종류 (⭐⭐⭐)
* [cite_start]**SIGINT:** Ctrl+C (인터럽트) [cite: 318]
* [cite_start]**SIGQUIT:** Ctrl+\ (종료 및 코어덤프) [cite: 322]
* [cite_start]**SIGKILL:** **강제 종료** (무시/블록 불가) [cite: 344]
* [cite_start]**SIGSTOP:** 실행 중단 (무시/블록 불가) [cite: 401]
* [cite_start]**SIGALRM:** `alarm()` 시간 만료 시 발생 [cite: 363]
* [cite_start]**SIGCHLD:** 자식 프로세스 상태 변경(종료 등) 시 부모에게 알림 [cite: 377]
* [cite_start]**SIGSEGV:** 잘못된 메모리 접근 (Segmentation Fault) [cite: 353]
* [cite_start]**SIGPIPE:** 끊어진 파이프에 쓰기 시도 시 [cite: 359]

### (2) 신호 처리 함수 (⭐⭐)
* [cite_start]**보내기:** `kill(pid, sig)` [cite: 413]
* [cite_start]**대기:** `pause()` (신호 올 때까지 멈춤) [cite: 420]
* [cite_start]**핸들러 등록:** `signal(sig, handler)` [cite: 437]
    * [cite_start]`SIG_IGN`: 신호 무시 [cite: 442]
    * [cite_start]`SIG_DFL`: 기본 동작 수행 [cite: 443]
* [cite_start]**정보 출력:** `psignal(sig, msg)`, `strsignal(sig)` (신호 이름 문자열 출력) [cite: 429-432]
