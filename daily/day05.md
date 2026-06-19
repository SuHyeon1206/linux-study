# Day 05 - 네트워크 기초

## 오늘 목표

- 내 WSL의 IP 주소 확인
- 기본 게이트웨이 역할 이해
- DNS 서버 역할 이해
- `/etc/hosts` 파일 조회 순서 이해
- 포트와 프로세스 관계 확인

---

## 사용한 명령어

~~~bash
hostname
ip addr
ping -c 4 8.8.8.8
ping -c 4 google.com
getent hosts google.com
ss -tuln
ip route
cat /etc/resolv.conf
cat /etc/hosts
getent hosts myserver.local
grep '^hosts:' /etc/nsswitch.conf
sudo ss -tulnp | grep ':53'
ps -fp 145
~~~

---

## 내 환경에서 확인한 값

~~~text
호스트 이름: home
WSL IP: 172.20.204.111
기본 게이트웨이: 172.20.192.1
DNS 서버: 10.255.255.254
~~~

> WSL의 IP, 게이트웨이, DNS 주소는 재시작하거나 네트워크 환경이 바뀌면 달라질 수 있다.

---

## 1. IP 주소와 기본 게이트웨이

`ip addr` 명령어로 WSL의 네트워크 인터페이스와 IP 주소를 확인했다.

~~~text
eth0: 172.20.204.111
~~~

`ip route` 명령어로 기본 경로를 확인했다.

~~~text
default via 172.20.192.1 dev eth0 proto kernel
~~~

### 의미

- `default` : 목적지를 따로 찾지 못할 때 사용하는 기본 경로
- `via 172.20.192.1` : 기본 게이트웨이 주소
- `dev eth0` : `eth0` 네트워크 인터페이스를 통해 통신
- `proto kernel` : 커널이 자동으로 등록한 경로

### 질문과 답

**Q. `172.20.192.1`은 네트워크에서 어떤 역할인가?**  
A. 기본 게이트웨이이다.

기본 게이트웨이는 외부 네트워크로 나갈 때 거치는 출구 역할의 IP 주소이다.  
게이트웨이가 항상 `.1`로 끝나는 것은 아니지만, 보통 그렇게 설정된 경우가 많다.

~~~text
내 WSL
172.20.204.111
   ↓
기본 게이트웨이
172.20.192.1
   ↓
외부 인터넷
~~~

---

## 2. 인터넷 연결과 DNS

~~~bash
ping -c 4 8.8.8.8
~~~

IP 주소로 외부 인터넷 연결이 되는지 확인했다.

~~~text
4 packets transmitted, 4 received, 0% packet loss
~~~

~~~bash
ping -c 4 google.com
~~~

도메인 이름으로도 통신이 되는지 확인했다.

`google.com` 접속이 성공했다는 것은 인터넷 연결과 DNS 이름 해석이 모두 정상이라는 뜻이다.

`/etc/resolv.conf` 파일에서 DNS 서버 주소를 확인했다.

~~~text
nameserver 10.255.255.254
~~~

### 질문과 답

**Q. 현재 WSL이 사용하는 DNS 서버 주소는 무엇인가?**  
A. `10.255.255.254`

**Q. DNS 서버는 무슨 역할을 하는가?**  
A. 내가 입력한 사이트 이름의 IP 주소를 찾아주는 서버이다.

~~~text
google.com 입력
→ DNS 서버에 IP 주소 질문
→ google.com의 IP 주소를 받음
→ 해당 IP 주소로 접속
~~~

---

## 3. `/etc/hosts` 파일

`/etc/hosts`는 도메인 이름과 IP 주소를 직접 연결할 수 있는 로컬 파일이다.

실습으로 아래 내용을 추가했다.

~~~text
127.0.0.1 myserver.local
~~~

~~~bash
getent hosts myserver.local
~~~

결과:

~~~text
127.0.0.1 myserver.local
~~~

즉, `myserver.local`은 DNS 서버를 거치지 않고 내 WSL을 가리킨다.

~~~text
myserver.local
→ 127.0.0.1
→ 내 WSL Ubuntu
~~~

`/etc/nsswitch.conf`에서 이름 조회 순서를 확인했다.

~~~text
hosts: files dns
~~~

### 의미

~~~text
1. files : /etc/hosts 파일 확인
2. dns   : DNS 서버에 질문
~~~

### 질문과 답

**Q. `myserver.local`을 입력했을 때 DNS 서버에 묻기 전에 가장 먼저 확인하는 파일은?**  
A. `/etc/hosts` 파일

---

## 4. 포트와 프로세스

~~~bash
ss -tuln
~~~

명령어로 현재 열려 있거나 요청을 기다리는 포트를 확인했다.

~~~text
tcp LISTEN 127.0.0.54:53
~~~

### 의미

- `127.0.0.54` : WSL 내부에서 사용하는 로컬 IP 주소
- `53` : DNS 포트 번호
- `LISTEN` : 요청을 받을 준비 상태
- `tcp` : TCP 방식으로 통신

### 질문과 답

**Q. `127.0.0.54:53`에서 `53`은 무엇인가?**  
A. 포트 번호

~~~text
IP 주소 = 어느 컴퓨터인지
포트 번호 = 그 컴퓨터 안의 어느 프로그램인지
~~~

비유하면:

~~~text
IP 주소 = 건물 주소
포트 번호 = 건물 안의 호수
~~~

~~~bash
sudo ss -tulnp | grep ':53'
~~~

명령어로 53번 포트를 사용 중인 프로세스를 확인했다.

~~~text
systemd-resolve
pid=145
~~~

~~~bash
ps -fp 145
~~~

명령어로 PID 145 프로세스를 다시 확인했다.

~~~text
/usr/lib/systemd/systemd-resolved
~~~

`systemd-resolved`는 DNS 이름 해석을 처리하는 프로세스이며, DNS와 관련된 53번 포트를 사용한다.

DNS는 일반적으로 UDP 53번 포트를 주로 사용하고, 응답이 크거나 특별한 경우 TCP 53번 포트도 사용할 수 있다.

---

## 오늘 이해한 것

1. 기본 게이트웨이는 외부 네트워크로 나갈 때 거치는 출구 역할의 IP 주소이다.
2. DNS 서버는 내가 입력한 사이트 이름의 IP 주소를 찾아주는 서버이다.
3. `/etc/hosts` 파일은 DNS 서버에 묻기 전에 가장 먼저 확인하는 파일이다.
4. IP 주소는 어느 컴퓨터인지 나타내고, 포트 번호는 그 컴퓨터 안의 어느 프로그램인지 나타낸다.
5. `53`번 포트는 DNS와 관련된 대표적인 포트 번호이다.

---

## 전체 통신 흐름

~~~text
google.com 입력
→ /etc/hosts 파일 확인
→ 없으면 DNS 서버에 IP 주소 질문
→ 받은 IP 주소 확인
→ 외부 네트워크라면 기본 게이트웨이로 전달
→ 인터넷을 통해 목적지와 통신
~~~

## 느낀 점

처음에는 인터넷 연결과 DNS가 같은 개념이라고 생각했지만, IP 주소로 통신되는지 확인하는 것과 도메인 이름이 IP 주소로 변환되는지는 별개라는 것을 알게 됐다.

또한 `/etc/hosts` 파일을 이용하면 DNS 서버에 묻지 않고도 특정 이름과 IP 주소를 직접 연결할 수 있다는 점을 확인했다.
기본 게이트웨이는 외부 인터넷으로 나가기 위한 출구 역할을 하고, 포트 번호를 통해 하나의 컴퓨터 안에서도 어떤 프로그램이 통신을 처리하는지 구분할 수 있다는 점도 이해했다.
