mkdir -p daily

cat > daily/day04.md <<'EOF'
# Day04 - 프로세스와 서비스 관리

## 오늘 실습 목표

- 실행 중인 프로세스를 확인한다.
- PID의 의미를 이해한다.
- 백그라운드 작업을 실행한다.
- kill 명령어로 프로세스를 종료한다.
- systemctl로 서비스 상태를 확인한다.
- journalctl로 로그를 확인한다.

---

## 1. 프로세스 확인

사용한 명령어:

```bash
ps
ps -ef | head
ps aux | head
```

### 정리

프로세스는 실행 중인 프로그램이다.

리눅스는 실행 중인 프로세스마다 PID라는 번호를 붙여서 관리한다.

- PID: 프로세스 번호
- PPID: 부모 프로세스 번호
- CMD 또는 COMMAND: 실행된 명령어
- USER 또는 UID: 프로세스를 실행한 사용자

`ps`는 현재 터미널과 관련된 프로세스를 보여준다.

`ps -ef`는 전체 프로세스를 자세히 보여준다.

`ps aux`는 CPU, 메모리 사용량을 포함해서 프로세스를 보여준다.

---

## 2. 파이프 기호

사용한 명령어:

```bash
ps -ef | head
ps aux | head
```

### 정리

`|` 기호는 파이프라고 부른다.

앞 명령어의 결과를 뒤 명령어로 넘길 때 사용한다.

예시:

```bash
ps -ef | head
```

뜻:

`ps -ef` 결과를 `head` 명령어로 넘겨서 위 10줄만 출력한다.

---

## 3. 백그라운드 작업 실행

사용한 명령어:

```bash
sleep 300 &
jobs
ps -ef | grep sleep
```

### 정리

`sleep 300`은 300초 동안 기다리는 명령어다.

명령어 뒤에 `&`를 붙이면 백그라운드에서 실행된다.

백그라운드로 실행하면 터미널을 계속 사용할 수 있다.

`jobs` 명령어는 현재 터미널에서 실행 중인 백그라운드 작업을 보여준다.

---

## 4. grep으로 프로세스 찾기

사용한 명령어:

```bash
ps -ef | grep sleep
```

### 정리

`grep`은 특정 글자가 들어간 줄을 찾는 명령어다.

`ps -ef | grep sleep`은 전체 프로세스 목록 중에서 `sleep`이 들어간 줄만 찾는다.

실행 결과에서 `grep sleep` 명령어 자체가 같이 보일 수 있다.

이것은 오류가 아니라, 방금 실행한 grep 명령어도 프로세스이기 때문에 같이 검색되는 것이다.

---

## 5. 프로세스 종료

사용한 명령어:

```bash
kill PID번호
```

예시:

```bash
kill 1911
```

### 정리

`kill`은 PID를 이용해서 프로세스를 종료하는 명령어다.

실습에서는 백그라운드에서 실행한 `sleep 300` 프로세스를 종료했다.

종료 후 `jobs` 명령어에서 `Terminated`가 표시되었다.

---

## 6. 서비스 상태 확인

사용한 명령어:

```bash
systemctl status cron --no-pager
systemctl is-active cron
systemctl list-units --type=service --state=running --no-pager | head
```

### 정리

서비스는 서버에서 계속 실행되는 프로그램이다.

예를 들어 웹 서버, SSH 서버, 데이터베이스 서버, cron 같은 것이 서비스에 해당한다.

`cron`은 정해진 시간에 명령어를 자동 실행하는 서비스다.

예를 들어 매일 새벽 3시에 백업을 실행하는 작업에 사용할 수 있다.

`systemctl status cron` 결과에서 확인한 내용:

- active (running): 현재 실행 중
- enabled: 부팅 시 자동 실행 설정됨
- Main PID: 서비스의 대표 프로세스 번호

---

## 7. 로그 확인

사용한 명령어:

```bash
journalctl -u cron --no-pager | tail -n 10
journalctl -n 20 --no-pager
```

### 정리

`journalctl`은 systemd가 관리하는 로그를 확인하는 명령어다.

`-u cron` 옵션을 사용하면 cron 서비스 관련 로그만 확인한다.

`-n 20` 옵션을 사용하면 최근 로그 20줄을 확인한다.

WSL 환경에서는 cron 로그가 없어서 `No entries`가 나올 수 있다.

또한 WSL에서는 시간 동기화 때문에 다음과 같은 로그가 보일 수 있다.

```text
Clock change detected. Flushing caches.
Time jumped backwards, rotating.
```

오늘 실습에서는 문제로 보지 않아도 된다.

---

## 8. Git 수동 업로드 흐름 정리

오늘 공부 기록을 정리하면서 GitHub에 올리는 방식도 다시 확인했다.

처음에는 자동 업로드 스크립트를 만들려고 했지만, 지금 단계에서는 Git의 기본 흐름을 직접 익히는 것이 더 중요하다고 느꼈다.

그래서 당분간은 자동화보다 수동으로 진행하기로 했다.

GitHub에 올릴 때 기본 흐름은 다음과 같다.

```bash
git status
git add .
git status
git commit -m "커밋 메시지"
git push
git status
```

---

## 오늘 느낀 점

프로세스는 실행 중인 프로그램이고, PID는 그 프로그램을 구분하는 번호라는 것을 알게 되었다.

백그라운드 실행을 할 때 명령어 뒤에 `&`를 붙인다는 것을 배웠다.

`kill` 명령어는 프로세스 이름이 아니라 PID 번호를 이용해서 종료한다는 점이 중요했다.

`ps -ef | grep 이름` 형태로 원하는 프로세스를 찾을 수 있다는 것도 기억해야겠다.

`systemctl`로 서비스 상태를 확인하고, `journalctl`로 로그를 확인하는 흐름이 서버 관리에서 중요하다고 느꼈다.

오늘도 `>`와 `>>` 차이를 주의해야 한다고 느꼈다.

처음 파일을 만들 때는 `>`를 사용할 수 있지만, 기존 파일에 내용을 추가할 때는 `>>`를 사용해야 한다.

`>`를 잘못 사용하면 기존 내용이 덮어쓰기 될 수 있다.
