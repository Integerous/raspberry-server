# :strawberry:라즈베리파이4로 토이프로젝트용 서버 만들기 - 1편
>토이프로젝트를 올릴 서버를 2019년 7월에 새로 출시한 라즈베리파이4로 만들어 보았다.  
>크게 3단계로 작업을 진행할 예정이다.  
>  
>1편 - 라즈베리파이4를 서버로 만들기  
>2편 - 웹 어플리케이션 배포  
>3편 - 도커라이징


## 0. 목차

1. **재료 준비 및 시작**  
  1.0. 구입한 제품들  
  1.1. 전원 연결  
  1.2. 안전하게 종료하기
  
2. **OS 설치**  
  2.0. Raspbian 설치 방법 선택  
  2.1. Raspbian 이미지 다운로드  
  2.2. SD카드에 Raspbian 이미지 굽기  
  2.3. 라즈베리파이에 OS 설치

3. **기본 환경 설정**  
  3.0. 설정 도구 실행  
  3.1. 비밀번호 변경  
  3.2. Locale 설정  
  3.3. Timezone 설정  
  3.4. Keyboard Layout 설정  
  3.5. Wi-fi Country 설정  
  3.6. Wi-fi 설정 (raspi-config 사용)
  3.7. Wi-fi 설정 (wpa_passphrase 사용)  
  3.8. SSH 허용 및 접속  
  3.4. Hostname, Wi-fi 설정  

4. **네트워크 설정**  
  4.0. 내부IP 고정  
  4.1. DDNS 설정  
  4.2. 포트포워딩 설정  
  4.3. ipTIME 원격접속 허용

5. **Nginx 설치 및 설정**  
  5.0. Nginx 설치  
  5.1. 한글 폰트 및 입력기 설치  
  5.2 Nginx 문자셋 설정  

6. **도메인, SSL 설정**  
  6.0. 도메인 구입  
  6.1. 도메인 연결  
  6.2. SSL 설정  
  6.3. SSL 인증서 자동갱신 설정  
  
7. **기타**  
  7.0. 온도 측정용 쉘 스크립트 작성  

## 1. 재료 준비 및 시작
>구입할 당시에 한국에 라즈베리파이4 판매처가 아예 없어서, 미국에 사는 처남에게 부탁해서 제품을 구매했다.  
>구글링을 꽤 해보고 리뷰들도 읽어 본 후에 구매한 제품들이지만, 최고의 선택이 아닐 수도 있다.  
>(제품 홍보 절대 아닙니다~) 

### 1.0. 구입한 제품들

<img src="https://github.com/Integerous/images/blob/master/raspberry-pi/whole_mini.png?raw=true" width="60%" height="60%">

1. **Raspberry Pi 4 보드** ([링크](https://www.canakit.com/raspberry-pi-4-4gb.html))
2. **USB C Type 충전기 (5V 3A)** ([링크](http://innomart.co.kr/goods/view?no=2030&market=naver&NaPm=ct%3Dk03ya1kw%7Cci%3Dfba2d08e22dc9f7d8922a9fcb37344b17954e7bc%7Ctr%3Dsls%7Csn%3D244253%7Chk%3D28ee28dec6817eca0cb3c06acab11b1450612912))
    - 생각보다 5V 3A PSU를 구하기가 힘들었다.
    - 라즈베리파이4 공식 PSU의 경우 유럽 제품을 사용해야 하는데, 유럽 사이트에서 직구해야해서 비싸다.
    - [이 제품](https://www.devicemart.co.kr/goods/view?no=1342118)을 사용해도 될 것 같다. 
3. **모니터 케이블 (Micro-HDMI to HDMI)** ([링크](https://www.amazon.com/AmazonBasics-High-Speed-Micro-HDMI-HDMI-Cable/dp/B014I8U33I/ref=cm_cr_arp_d_product_top?ie=UTF8))
4. **Micro SD 카드 (64GB)** ([링크](http://www.11st.co.kr/product/SellerProductDetail.tmall?method=getSellerProductDetail&prdNo=2544143323&NaPm=ct=k03v873s|ci=fc6845cda72728607bd190e48de2419986152e3c|tr=slct|sn=17703|hk=aee1cc28b613ef90d05f1b7a43e41e9303215e76&utm_term=&utm_campaign=%B3%D7%C0%CC%B9%F6pc_%B0%A1%B0%DD%BA%F1%B1%B3%B1%E2%BA%BB&utm_source=%B3%D7%C0%CC%B9%F6_PC_PCS&utm_medium=%B0%A1%B0%DD%BA%F1%B1%B3))
5. **케이스 + 쿨링팬** ([링크](https://www.amazon.com/gp/offer-listing/B07VDCT57F/ref=dp_olp_new_mbc?ie=UTF8&condition=new))
    - 반드시 필요한 것은 아니지만, 라즈베리파이4의 발열 이슈가 있는데 해외 유저들은 쿨링팬으로 해결한다고 한다.
    - 발열 이슈 때문에 보드 설계를 수정할 것이라는 제작사측의 발표도 있었다. (젠장)   

### 1.1. 전원 연결
1. 라즈베리파이 보드를 케이스와 결합
2. 쿨링팬을 라즈베리파이 보드의 GPIO에 연결
    - [이 곳](https://pinout.xyz/)에서 핀의 위치를 확인한다.
    - 팬의 속도를 3.3v의 low speed와 5v의 high speed 중 선택할 수 있다.
    - 3.3v(low speed)의 위치는 위의 링크에서 1번(빨간선), 6번(검정선)
    - 5v(high speed)의 위치는 4번(빨간선), 6번(검정선)
3. 전원을 연결하면 빨간불과 녹색불이 켜지고, 팬이 돌아간다.

<img src="https://github.com/Integerous/images/blob/master/raspberry-pi/power_on.png?raw=true" width="60%" height="60%">

### 1.2. 안전하게 종료하기
- 처음에는 라즈베리파이의 전원코드를 뽑는 방법으로 종료를 해왔는데, 위험한 방법이었다.
- [라즈베리파이 종료하는 방법](https://pimylifeup.com/how-to-shutdown-a-raspberry-pi/)이라는 글에 의하면, 
SD카드가 고장날 수 있고, 상황에 따라 데이터가 손실될 수 있다고 한다.
- `$ sudo shutdown -h now` 명령을 하면, 아래의 과정을 거쳐 안전하게 종료된다.
  1. 실행중인 모든 프로세스에 **SIGTERM** 명령을 보내서 안전하게 저장하고 종료(Exit)하게 한다.
  2. 약간의 간격을 두고, **SIGKILL** 명령을 보내서 남아있는 프로세스들을 종료(Halt)한다.
  3. 모든 파일 시스템들을 분리(unmount)한다.
  4. 화면에 **System Halted**라고 표시된다. (라즈베리파이4의 경우 표시되는 메세지를 볼 겨를 없이 모니터 연결이 종료된다.)
  5. 이제 전원코드를 뽑아도 된다.
  6. 다시 부팅하려면 전원코드를 연결하면 된다.

## 2. OS 설치
>[다른 선택지들](https://www.raspberrypi.org/downloads/)도 있지만, 첫 경험이므로 얌전하게 공식 OS인 Raspbian을 설치한다.

### 2.0. Raspbian 설치 방법 선택
1. NOOBS 사용
    - [NOOBS](https://www.raspberrypi.org/downloads/noobs/)(New Out Of the Box Software)라는 운영체제 설치 관리자를 사용해서 설치할 OS를 선택하는 방법
    - 부팅과 설치 속도가 느리다.
    - 다양한 OS 중에 선택하여 설치 가능.
2. OS 이미지를 SD카드에 기록하여 설치
    - 이미지 기록 프로그램(Etcher 또는 Win32 Disk Imager)을 사용하여 SD카드에 OS 이미지를 구워서 설치하는 방법
    - 부팅과 설치 속도가 빠르다.
    
>나의 경우 설치하려는 OS와 설치 목적이 분명하므로 2번 방법으로 설치를 진행했다.  

#### 2번 방법으로 Raspbian 설치 절차
1. OS 이미지 다운로드  
2. 다운받은 이미지(zip 또는 torrent)를 Micro SD 카드에 굽기
3. OS 이미지가 구워진 Micro SD카드를 라즈베리파이에 삽입하고 부팅하여 설치하기

### 2.1. Raspbian 이미지 다운로드
- [이 곳](https://www.raspberrypi.org/downloads/raspbian/)에서 Raspbian 이미지를 다운로드 받는다.
- 나는 서버로만 사용할 예정이기 때문에 Desktop GUI가 없지만 리소스 소모가 적은 Lite 버전을 선택했다.

<img src="https://github.com/Integerous/images/blob/master/raspberry-pi/raspbian.png?raw=true">


### 2.2. SD카드에 Raspbian 이미지 굽기

0. 기존에 사용했었던 SD카드는 포맷한 후에 사용하는 것이 안전하다. ([SD Memory Card Formatter](https://www.sdcard.org/downloads/formatter/))
1. 준비해둔 Micro SD카드를 SD Adapter 혹은 USB 리더기에 꽂아서 랩탑/데스크탑에 연결한다.  
2. [Etcher](https://www.balena.io/etcher/)를 사용해서 다운받은 OS 이미지를 Micro SD카드에 굽는다.(flash)
    - Etcher 사용법은 간단하다.
    - 이미지를 선택하고, SD카드를 선택하고, Flash(굽기)!
      - 이 때, 다운받은 Raspbian OS 이미지가 zip 파일일텐데, 압축을 풀 필요없이 그대로 사용하면 된다. 
    - <img src="https://github.com/Integerous/images/blob/master/raspberry-pi/etcher1.png?raw=true" width="60%" height="60%">  

### 2.3. 라즈베리파이에 OS 설치
1. Rasbian 이미지가 구워진 SD카드를 라즈베리파이에 삽입한다.
2. 라즈베리파이에 모니터와 키보드를 연결한 후, 전원을 연결한다.
3. 전원을 연결하면 빨간 라즈베리 4개가 화면에 표시되면서 부팅이 시작되고, Rasbian이 자동으로 설치된다.
4. 설치가 정상적으로 완료되면 (Lite 버전의 경우) `raspberrypi login: `이 보인다.
5. 초기 아이디는 `pi`, 비밀번호는 `raspberry` 


## 3. 기본 환경 설정
>Raspbian Lite 버전을 기준으로 서버로 활용하기 위한 기본 환경을 설정한다.

### 3.0. 설정 도구 실행
1. `$ sudo raspi-config` 명령을 통해 라즈베리파이 설정 도구(Raspberry Pi Software Configuration Tool)를 실행한다.  
2. 그럼 아래와 같이 ~~블루라이트 차단 안경을 무력화하는~~ 새파란 설정 도구가 실행된다.
   
  <img src="https://github.com/Integerous/images/blob/master/raspberry-pi/raspberry_config.png?raw=true" width="60%" height="60%">

### 3.1. 비밀번호 변경
1. `1. Change User Password`를 선택하여 비밀번호를 변경한다.    
2. 초기 비밀번호를 사용해도 되지만, 라즈베리파이를 분실했을 경우를 고려하면 바꾸는 것이 마음 편하다.
3. (주의) 키보드 설정을 하기 전에는 !@#$%^&* 등의 문자를 사용하지 않는 비밀번호를 사용한다.

### 3.2. Locale 설정
1. `4. Localisation Options` -> `I1 Change Locale` 클릭
2. 쭉 내려가서 `[*] en_US.UTF-8 UTF-8` 선택 (스페이스바 사용) 후 OK
    - `ko_KR.UTF-8 UTF-8`을 사용하면 에러 메세지로 구글링하기가 더 어려워서 미국으로 선택했다.
3. Default locale for the system environment 를 묻는 화면에서 `en.US.UTF-8` 선택 후 OK

### 3.3. Timezone 설정
1. `4. Localisation Options` -> `I2 Change Timezone` 클릭
2. Asia 선택, Seoul 선택
    - 서버 시간을 그리니치 표준시(GMT+0) 또는 협정 세계시(UTC+0)로 맞추려면 London을 선택한다.

### 3.4. Keyboard Layout 설정
1. `4. Localisation Options` -> `I3 Change Keyboard Layout` 클릭
2. [자세히 설명된 글](https://dullwolf.tistory.com/17)을 참고하여 키보드를 설정한다.
3. 설정하지 않으면 !@#$%^&*() 등의 Shift+숫자키로 사용하는 키를 사용할 수 없다고 한다.
4. ~~해피해킹도 선택지에 있어서 기뻤다.~~

### 3.5. Wi-fi Country 설정 
>(주의) 반드시 변경해야 되는 것은 아니다.  
>변경할 경우 `/etc/wpa_supplicant/wpa_supplicant.conf` 파일에 `country={국가코드}`가 작성되는데, 이것이 없어야만 무선 네트워크가 검색되는 경우도 있다고 한다.

1. `4. Localisation Options` -> `I4 Change Wi-fi Country` 클릭
2. `US United States` 혹은 `GB Britain (UK)` 선택
    - GB Britain (UK)를 선택해야만 정상 동작 한다는 사용자들도 있다.
    - u를 입력하면 United States(미국)을 금방 찾을 수 있다.
3. **`KR Korea (South)`를 선택하면 안된다.**
    - 한국으로 선택했을때 무선 네트워크를 검색하지 못한다는 블로그 글이 많다.
    - 나의 경우
      - 한국으로 선택해도 우리집 무선 네트워크는 검색이 된다.
        - 검색 방법 : `sudo iwlist wlan0 scan` 
      - 그런데 한국을 선택하고 `$ ping www.google.com`으로 ping을 날려보면,
      - 패킷이 전송되지 않고 `ping: www.google.com: Temporary failure in name resolution` 메세지가 뜬다.
      - 미국, 영국, 일본을 선택하면 패킷이 정상적으로 전송된다.
      - 또는 국가코드를 아예 삭제해도 패킷이 정상적으로 전송된다.
        - 국가코드 삭제 방법 : `/etc/wpa_supplicant/wpa_supplicant.conf` 파일에 `country={국가코드}` 삭제
      
### 3.6. Wi-fi 설정 (raspi-config 사용)
>와이파이를 raspi-config로 설정하는 방법과 wpa_passphrase로 설정하는 방법이 있다.

1. `2. Network Options` -> `N2 Wi-fi    Enter SSID and passphrase` 클릭
2. 라즈베리파이가 사용할 공유기의 SSID(와이파이 이름) 입력. 예시) KT_GiGA_2G_XXXX
3. 비밀번호 입력
4. 공유기 접속 확인
    - `$ iwconfig` 명령의 결과에서 `ESSID`가 입력한 것과 같으면 정상 접속. 
5. 설정 확인
    - 설정 파일(/etc/wpa_supplicant/wpa_supplicant.conf)에서 SSID와 비밀번호 확인 가능    

### 3.7. Wi-fi 설정 (wpa_passphrase 사용)
>raspi-config로 와이파이 설정 시, password가 설정 파일(/etc/wpa_supplicant/wpa_supplicant.conf)에 그대로 노출된다.  
>비밀번호를 암호화하기 위해서는 raspi-config 대신 wpa_passphrase 명령으로 와이파이를 설정하는 것이 좋다. 

1. `$ wpa_passphrase {SSID} {비밀번호}` 명령을 치면, network 정보가 아래와 같이 콘솔에 출력된다.
    ~~~
    network={
            ssid="KT_GiGA_5G_HOME"
            #psk="{비밀번호}"
            psk=7ac0c35da93c82d ....(생략)
    }
    ~~~

2. 출력된 network 정보를 설정 파일(/etc/wpa_supplicant/wpa_supplicant.conf)에 추가한다.
    - 이 때, 마우스가 없으니 출력될 정보를 Redirecting output(>>)으로 설정 파일에 넣는다.  
    - [라즈베리파이 공식 문서](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md)에 자세하게 설명되어있다. 
    ~~~sh
    $ sudo su  # root 권한으로 변경
    $ wpa_passphrase {SSID} {비밀번호} >> /etc/wpa_supplicant/wpa_supplicant.conf
    ~~~ 

3. wpa_supplicant.conf 파일은 아래와 같다.
    ~~~conf
    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    update_config=1
    
    network={
              ssid="KT_GiGA_5G_HOME"
              #psk="{비밀번호}"
              psk=7ac0c35da93c82d ....(생략)
    }
    ~~~
    - (주의) network 윗부분의 내용도 반드시 포함되어야 한다.
    - 비밀번호가 노출된 `#psk="{비밀번호}"`는 지우는 것이 좋다.
    
### 3.8. SSH 허용 및 접속
>~~시력 보호를 위해~~ 라즈베리파이에 SSH로 원격 접속해서 사용하는 것이 훨씬 편리하다.  
>SSH 접속을 허용하면 모니터와 키보드를 연결할 필요 없이,  
>PC/랩탑에서 SSH를 통해 라즈베리파이에 접속하면 된다. (마치 AWS EC2 인스턴스가 내 책상에..)
1. SSH 허용
    - `5. Interfacing Options` -> `P2 SSH` -> `YES`
2. 라즈베리파이 IP 확인
    - `$ ifconfig`
    - 무선랜의 경우, `wlan0` 의 `inet addr: xxx.xx.x.xx`에 적힌 IP 확인 (유선은 eth0)
3. SSH 접속
    - (내부망) 주로 사용하는 PC/랩탑에서 `$ ssh pi@{내부IP 주소}`로 접속
    - (외부망) 밑에서 설명한 포트포워딩 설정 후 `$ ssh -p {외부포트} pi@{외부IP주소 혹은 도메인명}`로 접속
4. SSH 끊김 현상 해결
    - ssh 접속하여 사용하다보면 끊기는 듯한 현상이 있는데, DNS 조회 설정 변경으로 이를 해결할 수 있다.
    - `$ sudo vi /etc/ssh/sshd_config` 명령으로 설정파일에 접근.
    - 파일 하단 쯤에 주석처리 되어있는 `#UseDNS no`의 주석(#)을 해제.
    - `$ sudo service ssh restart` 명령으로 sshd 재시작.
    - 참고 : [Slow SSH on RPi 3](https://www.raspberrypi.org/forums/viewtopic.php?t=180440)


## 4. 네트워크 설정
>이 부분은 사용하는 공유기에 따라 설정 방법이 다르다.  
>ipTIME A3004NS-M 모델 (펌웨어 버전 11.00.4) 기준으로 작성했다.


### 4.0. 내부IP 고정
>공유기를 통해 wi-fi를 사용하는 기기들에게 공유기는 임의로 내부IP(사설IP)를 할당한다.  
>기기를 wi-fi에 연결한 순서에 따라, 맥북은 192.168.0.2, 휴대폰은 192.168.0.3, 라즈베리파이는 192.168.0.4 이런식이다.  
>그런데 공유기에 전원이 차단되어 공유기가 재부팅되는 경우 등으로 인해 내부 IP 설정이 초기화 될 수 있다.  
>이런 상황을 대비해서 내부 IP를 공유기 내에서 고정시키는 것이 좋다.

1. ipTIME 접속 (192.168.0.1)
2. 고급설정 - 네트워크 관리 - DHCP 서버 설정
3. 하단에 [사용중인 IP 주소 정보] 중 라즈베리파이의 체크박스를 클릭하고 위에 등록 버튼 클릭.
    <img src="https://github.com/Integerous/images/blob/master/raspberry-pi/iptime01.png?raw=true" width="60%" height="60%">

### 4.1. DDNS 설정
>ISP 사업자(우리집은 KT)는 DHCP(동적 호스트를 제공하는 프로토콜)를 통해 우리집에 유동 IP를 할당해준다.   
>유동IP라도 IP가 자주 바뀌지는 않는다. 하지만 ISP 사업자가 DHCP 서버를 리셋하는 등의 수작을 부리면 우리집에 할당되었던 IP가 변경된다. (그래서 가정집은 유동IP다.)  
>이 경우 DNS에 등록한 A 레코드(IP주소)가 변경된 것이기 때문에, A 레코드를 새로운 IP로 변경하여 도메인이 새 IP를 바라보게 해야한다.  
>그런데 DDNS 서비스를 사용하면 A레코드의 변경을 감지해서 자동으로 업데이트 해주기 때문에, IP 변경에 신경 쓸 필요없이 고정IP 처럼 사용할 수 있다.  
>편리하게도 ipTIME에서 자체 DDNS 서비스를 제공한다.  
>나는 이 ipTIME DDNS를 구입한 도메인의 CNAME(혹은 ANAME, 혹은 Alias)으로 등록해서 고정IP 처럼 사용할 것이다. 

1. ipTIME 접속 (192.168.0.1)
2. 고급설정 - 특수기능 - DDNS 설정
3. 호스트이름과 사용자 ID 입력
    - 외부IP 대신 도메인(`{호스트이름}.iptime.org`)으로 라즈베리파이에 접근할 수 있다. 

### 4.2. 포트포워딩 설정
>외부에서 라즈베리파이의 IP 혹은 도메인과 지정된 포트로 접근했을 때, 연결시킬 내부 포트 설정

1. ipTIME 접속 (192.168.0.1)
2. 고급설정 - NAT/라우터 관리 - 포트포워드 설정
3. 새 규칙 추가
    - 규칙이름: 사용자 정의
    - 내부 IP주소: 3.7에서 확인한 라즈베리파이의 IP주소 입력
    - 프로토콜: TCP
    - 외부 포트: 예시- 20000(ssh), 443(https), 80(http)
    - 내부 포트: 22(ssh), 443(https), 80(http) 등

참고 : [http의 기본 포트가 80, https의 기본 포트가 443인 이유는 무엇일까?](https://johngrib.github.io/wiki/why-http-80-https-443/) 

### 4.3. ipTIME 원격접속 허용
1. ipTIME 접속 (192.168.0.1)
2. 고급설정 - 보안기능 - 공유기 접속/보안관리
3. 원격 관리 포트 사용 체크 및 원하는 포트번호 설정
4. 설정해둔 ipTIME의 DDNS 도메인 + 포트번호로 원격에서 ipTIME 접근 가능
    - (예시) 1nteger.iptime.org:22000 


## 5. Nginx 설치 및 설정
>한글 폰트와 문자셋 설정은 당장 필요하지 않지만 해두었다.

### 5.0. Nginx 설치
1. `$ sudo apt-get update`
2. `$ sudo apt-get install nginx`

### 5.1. 한글 폰트 및 입력기 설치
- `$ sudo apt-get install fonts-unfonts-core`
- `$ sudo apt-get install ibus-hangul`

### 5.2 Nginx 문자셋 설정
- `$ sudo vim /etc/nginx/nginx.conf`
- Basic Settings 하단에 아래 내용 추가
  ~~~
  charset   utf-8;
  server {
    charset utf-8;
  }
  ~~~
  
## 6. 도메인, SSL 설정

### 6.0. 도메인 구입
>블로그 도메인을 Godaddy에서 구입했었는데, 비싸다.  
>구글링 결과 Namecheap의 평가가 훨씬 좋아서 선택했다.  
>그리고 Godaddy는 ANAME이나 Alias를 제공하지 않아서 기존에 있던 도메인도 Namecheap으로 이전했다.

### 6.1. 도메인 연결
>이 부분에서 삽질을 많이 했다. 아래에 삽질 과정을 요약했다.

1. ipTIME에서 설정한 DDNS 도메인(`go-quality.iptime.org`)을 구입한 도메인(`go-quality.dev`)의 CNAME으로 등록해서 고정IP 처럼 사용하려 했다.
2. 하지만 정상적으로 접속되지 않았다.
3. `go-quality.iptime.org`는 서브 도메인(www, 등)이 없는 Root domain인데, Root domain은 서브 도메인이 없으므로 CNAME 등록하면서 Host 부분에 와일드카드(* 또는 @)를 사용하면 된다고 생각했다.
4. 알아보니 **Root domain은 CNAME으로 설정하지 않는 것이 표준([RFC 1912](https://tools.ietf.org/html/rfc1912), [RFC 2181](https://tools.ietf.org/html/rfc2181))이었다.**
5. 이 표준은 기술적인 제약은 아니고 Contractual 제약이다. 그리고 내가 사용한 Namecheap이나 Godaddy에서는 표준을 따르고 있었다.
7. 그래서 서브도메인이 없는 Root domain을 CNAME으로 등록하는 방법을 구글링하다보니 `ANAME`, `ALIAS` 등의 방법을 제공하는 업체들이 있었다.
8. Godaddy에서는 ANAME도 ALIAS도 제공하지 않았는데, 다행히 **Namecheap은 [ALIAS를 사용해서 CNAME 처럼 등록](https://www.namecheap.com/support/knowledgebase/article.aspx/10128/2237/how-to-create-an-alias-record)할 수 있었다.**
9. 하지만 또 다시 정상적으로 접속되지 않았다.
10. 알아보니 **`.dev` 도메인은 https 접속이 default인데, SSL 인증서를 세팅하지 않은 것이다.**
11. 우선 Alias로 등록한 Root domain이 CNAME 처럼 등록되었는지 먼저 테스트를 해보고 SSL 인증서를 세팅하기로 했다.
12. 테스트를 위해 Godaddy에 등록된 도메인 중 `www.놀고있던도메인.com`을 Namecheap으로 옮겼다.
13. 그리고 `www.놀고있던도메인.com` 도메인에 `go-quality.iptime.org`를 `ALIAS`로 설정했더니, CNAME 처럼 등록되었다.
14. Alias 설정 후, `www.놀고있던도메인.com` 으로 접근하니 `go-quality.iptime.org`에 띄워둔 Nginx 랜딩페이지를 볼 수 있었다. (Alias 테스트 성공)
15. 그 이후, SSL을 설정하고 `go-quality.dev`로 접근하니 아래 처럼 라즈베리파이에 실행중인 Nginx 랜딩페이지가 나타났다. (도메인 연결 성공)
    - <img src="https://github.com/Integerous/images/blob/master/raspberry-pi/nginx_index.png?raw=true" width="60%" height="60%">


### 6.2. SSL 설정
>[Let's Encrypt](https://letsencrypt.org/)를 사용해서 무료로 SSL 인증서를 세팅해본다.  
>Let's Encrypt는 "전 세계 모든 사이트를 HTTPS로 만들기!” 라는 슬로건으로 시작 된 오픈소스 프로젝트이다.  
>3개월마다 인증서를 갱신해야 하지만, 쉘스크립트로 자동화할 수 있으니 문제없다.  
>certbot을 사용할 수 있지만

1. 실행중인 Nginx 종료
    - letsencrypt가 80번 포트를 사용해서 인증을 시도하기 때문에 실행중인 nginx를 종료해야 한다.
        - `$ sudo service nginx stop`
    - 80번 포트가 사용되지 않는 것을 확인한다.
        - `$ netstat -ant`
2. 라즈베리파이에 letsencrypt를 설치한다.
    - `$ sudo apt install letsencrypt`
3. 인증서 생성
    - `sudo letsencrypt certonly --standalone -d go-quality.dev`
4. 인증서 생성 결과 확인
    - <img src="https://github.com/Integerous/images/blob/master/raspberry-pi/certbot.png?raw=true" width="60%" height="60%">
    - 위의 결과를 요약하자면,
        - 인증서와 키 파일이 각각 아래 경로에 저장되었다.
          - `/etc/letsencrypt/live/go-quality.dev/fullchain.pem`
          - `/etc/letsencrypt/live/go-quality.dev/privkey.pem`
        - 인증서는 2020년 2월 21일에 만료되고, `$ certbot renew` 명령을 통해 갱신할 수 있다.
        - Certbot 설정 디렉토리(/etc/letsencrypt)에 너의 계정 credential과 인증서, 그리고 private key가 저장되었으니, 안전한 백업폴더를 생성하는 것이 좋다.
        - Certbot에 기부좀 해줘. 
    - `$ certbot certificates` 명령으로 발급받은 인증서 목록 확인 
5. Nginx에 인증서 적용
    - Nginx 설정 파일(`/etc/nginx/nginx.conf`)의 `http{ ... }` 안에 아래 내용을 추가한다.
    ~~~conf
    server {
            listen 443 ssl default_server;
            listen [::]:443 ssl default_server;
            
            ssl_certificate /etc/letsencrypt/live/{도메인명}/fullchain.pem;
            ssl_certificate_key /etc/letsencrypt/live/{도메인명}/privkey.pem;
            ssl_protocols   TLSv1 TLSv1.1 TLSv1.2;
            ssl_ciphers     HIGH:!aNULL:!MD5;
    }
    ~~~
6. Nginx 시작 및 포트확인
    - `$ sudo service nginx start`
    - `$ netstat -ant`
      - <img src="https://github.com/Integerous/images/blob/master/raspberry-pi/ssl_443.png?raw=true" width="60%" height="60%">
      - 443 포트 listening을 확인한다.
        
7. SSL 적용 확인
    - SSL을 적용했는데 브라우저의 주소창 왼쪽 자물쇠가 여전히 잠겨있지 않다면, [whynopadlock](https://www.whynopadlock.com)에서 문제점을 진단받아본다.
    - <img src="https://github.com/Integerous/images/blob/master/raspberry-pi/ssl_inspection.png?raw=true" width="60%" height="60%">
    
- 참고 : [LETSENCRYPT 에서 SSL 인증서를 무료로 발급 받아 웹 서버에 적용하기](https://kr.minibrary.com/353/)

### 6.3. SSL 인증서 자동갱신 설정
>30일 마다 라즈베리파이에 접속해서 `$ certbot renew`를 입력하는 것은 귀찮다.  
>Crontab으로 인증서 갱신을 자동화 한다.

1. 인증서 만료일 확인
    - `$ certbot certificates` 명령으로 Expiry Date 확인
2. 시스템 시간 확인
    - `$ date`
3. 기존에 설정된 Crontab 확인
    - `$ sudo cron -l`
4. Crontab 편집
    - `$ sudo cron -e`
    - 아래 내용 추가 후 저장  
    
    ~~~sh
    # 매월 1일 새벽4시에 $ certbot renew 명령을 내리고, 갱신에 성공하면 nginx reload
    0 04 1 * * /usr/bin/certbot renew --renew-hook="sudo systemctl reload nginx"
    ~~~
    
5. Crontab 실행 로그 확인
    - 갱신이 제대로 이루어지는지 로그로 확인한다.
    - `$ view /var/log/syslog`

- 참고
    - [Let's Encrypt SSL 인증서 자동 갱신 설정 방법](https://devlog.jwgo.kr/2019/04/16/how-to-lets-encrypt-ssl-renew/)
    - [SSL 인증서 자동 갱신 오류](https://avada.tistory.com/481)


## 7. 기타

### 7.0. 온도 측정용 쉘 스크립트 작성
1. 쉘 스크립트 파일 생성
    - `$ sudo vim thermometer.sh`
2. 쉘 스크립트 작성
    ~~~sh
    #!/bin/bash
    cpuTemp=$(cat /sys/class/thermal/thermal_zone0/temp)
    cpuTemp1=$(($cpuTemp/1000))
    cpuTemp2=$(($cpuTemp/100))
    cpuTempM=$(($cpuTemp2 % $cpuTemp1))
    
    gpuTemp=$(/opt/vc/bin/vcgencmd measure_temp)
    gpuTemp=${gpuTemp}
    gpuTemp=${gpuTemp//temp=/}
    
    echo $(date "+%Y-%m-%d %H:%M")
    echo Temperature CPU : $cpuTemp1"."$cpuTempM"'C, GPU : "$gpuTemp
    ~~~
3. 라즈베리파이 온도 확인

- 참고 : [라즈베리파이 시스템 온도(발열) 확인](https://geeksvoyage.com/raspberry%20pi/get-temp-for-pi/)


## Reference
- https://brunch.co.kr/@topasvga/701
- https://geeksvoyage.com/
- https://blog.jeuke.com/49?category=818287
- https://seolin.tistory.com/99
- https://blog.rajephon.dev/2019/07/12/setup-raspberrypi-home-server/
- https://www.youtube.com/watch?v=8grooZWbH9Y
- https://withcoding.com/45
- https://pimylifeup.com/how-to-shutdown-a-raspberry-pi/
- https://dullwolf.tistory.com/17
- http://progtrend.blogspot.com/2017/08/raspberry-pi-wifi.html
- https://webnautes.tistory.com/903
- http://egloos.zum.com/lunar456th/v/6397975