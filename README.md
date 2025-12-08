# [고급운영체제 & 실무프로젝트] 완전 정복 요약

## 1. 🚨 [10점 확보] 서술형 족보 (⭐⭐⭐)
**Q. 패스워드 파일에서 현재 사용자 정보를 추출하여 출력하시오.**

### (1) 핵심 로직
`getuid()`(내 ID) ➔ `getpwuid()`(정보 퍼오기)

### (2) 모범 답안 코드
```c
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
```
---

## 2. 📂 파일 및 디렉토리 관리

### (1) 파일/디렉토리 조작 (⭐⭐⭐)
* **파일 삭제:** `unlink(path)`, `remove(path)`
* **디렉토리 삭제:** `rmdir(path)` (비어있어야 함)
* **디렉토리 생성:** `mkdir(path, mode)` (**mode** 인자 필수!)
* **이름 변경:** `rename(old, new)`
* **링크 생성:**
    * 하드링크: `link(기존, 새)`
    * 심볼릭링크: `symlink(기존, 새)`
* **경로 변경:** `chdir(path)`, `fchdir(fd)`
* **현재 경로 확인:** `getcwd(buf, size)`

### (2) 디렉토리 읽기 (⭐⭐⭐)
* **순서:** `opendir()` ➔ `readdir()` ➔ `closedir()`
* **위치 제어:**
    * `telldir(dp)`: 현재 위치(오프셋) 반환
    * `seekdir(dp, pos)`: 위치 이동
    * `rewinddir(dp)`: 맨 처음으로 되감기
* **구조체 `struct dirent`:** `d_ino`(번호), `d_name`(이름)

---

## 3. ⏰ 시간 관리

### (1) 시간 함수 (⭐⭐⭐)
* **현재 시간(초):** `time(NULL)` (1970.1.1 기준 초)
* **구조체 변환:**
    * `gmtime(time_t*)`: UTC(세계표준시) 기준
    * `localtime(time_t*)`: **지역 시간대** 기준 (가장 많이 씀)
* **반대 변환:** `mktime(struct tm*)` (구조체 ➔ 초)
* **시간 차이:** `difftime(t1, t0)` (두 시간의 차이 초 계산)

### (2) 문자열 출력 함수 (⭐)
* `ctime(time_t*)`: "Day Mon dd hh:mm:ss yyyy" 형식 자동 출력
* `asctime(struct tm*)`: `ctime`과 동일 형식
* `strftime(buf, size, format, tm*)`: 내 맘대로 포맷 지정
    * 주요 포맷: `%Y`(년), `%m`(월), `%d`(일), `%H`(시), `%M`(분), `%S`(초)

### (3) `struct tm` 멤버 (⭐⭐)
* `tm_year`: 1900년 이후 년도 (예: 2023년이면 123)
* `tm_mon`: 0~11 (1월이 0)
* `tm_mday`: 1~31

---

## 4. 👥 사용자 & 그룹 & 시스템 정보

### (1) 사용자 ID 구분 (⭐⭐⭐)
* **RUID (실제):** `getuid()` - 프로그램을 실행한 찐 사용자
* **EUID (유효):** `geteuid()` - 파일 접근 권한을 결정하는 ID
* **ID 변경:** `setuid(uid)`, `seteuid(uid)` (EUID만 변경)

### (2) 정보 파일 및 함수 (⭐⭐)
* **`/etc/passwd` (사용자 정보):** `getpwuid(uid)`, `getpwnam(name)`
* **`/etc/shadow` (암호화 비번):** `getspnam(name)` (관리자만 가능)
    * 구조체 `spwd`: `sp_pwdp`(암호), `sp_lstchg`(마지막 변경일)
* **`/etc/group` (그룹 정보):** `getgrgid(gid)`, `getgrnam(name)`
    * 구조체 `group`: `gr_name`(그룹명), `gr_gid`(그룹ID), `gr_mem`(멤버들)

### (3) 로그인 기록 (utmp/wtmp) (⭐)
* 파일 위치: `/var/adm/utmp` (현재 로그인), `/var/adm/wtmp` (히스토리)
* 구조체 `utmpx`: `ut_user`(유저명), `ut_type`(로그인 타입)
* `ut_type` 상수: `BOOT_TIME`(부팅), `USER_PROCESS`(로그인 중), `DEAD_PROCESS`(종료됨)

### (4) 시스템 정보 (⭐)
* **`uname(struct utsname*)`:** OS 이름, 버전, 하드웨어 정보 확인
    * 멤버: `sysname`(OS명), `nodename`(호스트명), `release`, `version`, `machine`
* **`sysinfo()`:** 시스템 설정 정보 확인

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
* **보내기:** `kill(pid, sig)`
* **대기:** `pause()` (신호 올 때까지 멈춤)
* **핸들러 등록:** `signal(sig, handler)`
    * `SIG_IGN`: 신호 무시
    * `SIG_DFL`: 기본 동작 수행
* **정보 출력:** `psignal(sig, msg)`, `strsignal(sig)` (신호 이름 문자열 출력)
