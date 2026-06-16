cd ~/linux-study

cat > day03/day03.md <<'EOF'
# Day 03 - 파일 탐색, 검색, 로그 확인

## 오늘의 목표

- 리눅스에서 파일과 디렉터리 위치를 확인한다.
- `ls`, `pwd`, `cat` 명령어를 복습한다.
- `grep`으로 파일 내용에서 원하는 문자열을 검색한다.
- `find`로 파일 이름을 기준으로 검색한다.
- `/var/log` 디렉터리에서 로그 파일을 확인한다.
- `tail`로 로그 파일의 마지막 부분을 확인한다.

## 1. 파일 탐색 명령어

### 현재 위치 확인

```bash
pwd
```

현재 내가 어느 디렉터리에 있는지 확인하는 명령어다.

### 파일 목록 확인

```bash
ls
```

현재 디렉터리 안의 파일과 디렉터리를 보여준다.

### 자세히 보기

```bash
ls -l
```

파일 권한, 소유자, 크기, 수정 시간까지 자세히 보여준다.

### 숨김 파일 보기

```bash
ls -a
```

`.`으로 시작하는 숨김 파일까지 보여준다.

### 숨김 파일까지 자세히 보기

```bash
ls -al
```

숨김 파일을 포함해서 권한, 소유자, 크기, 수정 시간까지 자세히 보여준다.

## 2. 실습용 파일 만들기

```bash
echo "Linux study day 3" > memo.txt
echo "error: permission denied" >> memo.txt
echo "warning: disk space low" >> memo.txt
echo "info: login success" >> memo.txt
```

`>`는 파일을 새로 쓰거나 기존 내용을 덮어쓴다.

`>>`는 기존 내용 뒤에 새로운 내용을 추가한다.

## 3. 파일 내용 확인

```bash
cat memo.txt
```

파일 내용을 터미널에 출력한다.

## 4. grep으로 문자열 검색

```bash
grep "error" memo.txt
grep "warning" memo.txt
grep "login" memo.txt
```

`grep`은 파일 안에서 원하는 문자열을 찾을 때 사용한다.

### 대소문자 무시 검색

```bash
grep -i "ERROR" memo.txt
```

`-i` 옵션은 대소문자를 구분하지 않고 검색한다.

### 줄 번호와 함께 검색

```bash
grep -n "error" memo.txt
```

`-n` 옵션은 검색 결과가 몇 번째 줄에 있는지 보여준다.

## 5. find로 파일 찾기

```bash
find . -name "memo.txt"
```

현재 디렉터리 아래에서 `memo.txt` 파일을 찾는다.

```bash
find . -name "*.txt"
```

현재 디렉터리 아래에서 `.txt`로 끝나는 파일을 찾는다.

```bash
find . -name "day03"
```

현재 디렉터리 아래에서 `day03`이라는 이름의 파일이나 디렉터리를 찾는다.

## 6. 로그 확인

```bash
ls /var/log
```

리눅스 로그 파일들이 모여 있는 `/var/log` 디렉터리의 내용을 확인한다.

```bash
tail /var/log/syslog
```

`syslog` 파일의 마지막 부분을 확인한다.

## 오늘 확인한 로그 예시

```text
Time jumped backwards, rotating.
Clock change detected.
PackageKit daemon quit.
```

WSL 환경에서는 시간 동기화나 서비스 동작 때문에 이런 로그가 보일 수 있다.

## 핵심 정리

- `pwd`: 현재 위치 확인
- `ls`: 파일 목록 확인
- `ls -l`: 자세한 파일 목록 확인
- `ls -a`: 숨김 파일 확인
- `ls -al`: 숨김 파일까지 자세히 확인
- `cat`: 파일 내용 출력
- `grep`: 파일 내용에서 문자열 검색
- `grep -i`: 대소문자 무시 검색
- `grep -n`: 줄 번호 표시
- `find`: 파일이나 디렉터리 검색
- `tail`: 파일의 마지막 부분 확인
- `/var/log`: 리눅스 로그 파일들이 있는 위치

## 오늘 느낀 점

파일을 찾는 `find`와 파일 내용에서 검색하는 `grep`의 차이를 알게 되었다.

로그 파일은 `/var/log`에 있고, `tail` 명령어로 최근 기록을 확인할 수 있다는 것을 배웠다.

특히 `echo`로 파일에 내용을 추가할 때 `>`와 `>>`의 차이를 확실히 기억해야겠다고 느꼈다.  
`>`를 사용하면 기존 내용이 덮어써지고, `>>`를 사용해야 기존 내용 뒤에 새로운 내용이 추가된다.  
실습 중에 실수로 `>`를 사용해서 내용이 덮어써지는 경험을 했기 때문에, 앞으로 기록을 추가할 때는 `>>`를 더 주의해서 사용해야겠다.


GitHub 자동 업로드 명령어 save를 만들고 테스트 진행
