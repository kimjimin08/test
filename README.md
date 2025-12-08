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

### (3
