# Day 02 - Linux 파일 조작과 권한 기초

## 오늘 배운 명령어

- mkdir: 디렉터리 생성
- touch: 빈 파일 생성
- echo: 문자열 출력 및 파일에 저장
- cat: 파일 내용 출력
- cp: 파일 복사
- mv: 파일 이동 또는 이름 변경
- rm: 파일 삭제
- ls -l: 파일 상세 정보와 권한 확인
- chmod: 파일 권한 변경

## 실습 내용

```bash
mkdir day02
cd day02
touch memo.txt
echo "Linux day02 study" > memo.txt
cat memo.txt

cp memo.txt memo_backup.txt
mv memo.txt memo_old.txt
mv memo_backup.txt memo.txt
rm memo_old.txt

mkdir logs
mv memo.txt logs/
cat logs/memo.txt
ls -l logs/memo.txt

echo 'echo "Hello Linux"' > hello.sh
chmod +x hello.sh
./hello.sh

## 리눅스 권한 정리

리눅스 권한은 읽기, 쓰기, 실행으로 나뉜다.

- r: read, 읽기, 숫자 4
- w: write, 쓰기, 숫자 2
- x: execute, 실행, 숫자 1

권한은 소유자, 그룹, 기타 사용자 순서로 표시된다.

예시:

```text
-rw-r--r--
```

뜻:

```text
-   일반 파일
rw- 소유자 권한: 읽기, 쓰기 가능
r-- 그룹 권한: 읽기만 가능
r-- 기타 사용자 권한: 읽기만 가능
```

숫자로 계산하면 다음과 같다.

```text
r = 4
w = 2
x = 1
```

```text
rw- = 4 + 2 = 6
r-- = 4
r-- = 4
```

따라서 다음 권한은 644이다.

```text
rw-r--r-- = 644
```

또 다른 예시:

```text
-rwxr-xr-x
```

뜻:

```text
rwx 소유자 권한: 읽기, 쓰기, 실행 가능
r-x 그룹 권한: 읽기, 실행 가능
r-x 기타 사용자 권한: 읽기, 실행 가능
```

숫자로 계산하면 다음과 같다.

```text
rwx = 4 + 2 + 1 = 7
r-x = 4 + 1 = 5
r-x = 4 + 1 = 5
```

따라서 다음 권한은 755이다.

```text
rwxr-xr-x = 755
```

오늘 실습에서는 처음 `hello.sh` 파일에 실행 권한이 없었다.

```bash
./hello.sh
```

그래서 다음과 같은 오류가 발생했다.

```text
Permission denied
```

이후 아래 명령어로 실행 권한을 추가했다.

```bash
chmod +x hello.sh
```

그 결과 `./hello.sh` 명령어로 스크립트를 실행할 수 있었다.

```bash
./hello.sh
```

실행 결과:

```text
Hello Linux
```
