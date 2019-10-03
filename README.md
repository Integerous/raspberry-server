# :strawberry:라즈베리파이4로 개인서버 만들기
>토이프로젝트를 올릴 서버를 2019년 7월에 새로 출시한 라즈베리파이4로 만들어 보았다.

## 1. 재료 준비
>구입할 당시에 한국에 라즈베리파이4 판매처가 아예 없어서, 미국에 사는 처남에게 부탁해서 제품을 구매했다.  
>구글링을 꽤 해보고 리뷰들도 읽어 본 후에 구매한 제품들이지만, 최고의 선택이 아닐 수도 있다.  
>(제품 홍보 절대 아닙니다~) 

### 1.1 구입한 제품들

<img src="https://github.com/Integerous/images/blob/master/raspberry-pi/raspberry_whole.jpeg?raw=true" width="60%" height="60%">

1. **Raspberry Pi 4 보드** ([링크](https://www.canakit.com/raspberry-pi-4-4gb.html))
2. **USB C Type 충전기 (5V 3A)** ([링크](http://innomart.co.kr/goods/view?no=2030&market=naver&NaPm=ct%3Dk03ya1kw%7Cci%3Dfba2d08e22dc9f7d8922a9fcb37344b17954e7bc%7Ctr%3Dsls%7Csn%3D244253%7Chk%3D28ee28dec6817eca0cb3c06acab11b1450612912))
    - 생각보다 5V 3A PSU를 구하기가 힘들었다.
    - 라즈베리파이4 공식 PSU의 경우 유럽 제품을 사용해야 하는데, 유럽 사이트에서 직구해야해서 비싸다.
    - [이 제품](https://www.devicemart.co.kr/goods/view?no=1342118)을 사용해도 될 것 같다. 
3. **모니터 케이블 (Micro-HDMI to HDMI)** ([링크](https://www.amazon.com/AmazonBasics-High-Speed-Micro-HDMI-HDMI-Cable/dp/B014I8U33I/ref=cm_cr_arp_d_product_top?ie=UTF8))
4. **Micro SD 카드 + 리더기 (64GB)** ([링크](http://www.11st.co.kr/product/SellerProductDetail.tmall?method=getSellerProductDetail&prdNo=2544143323&NaPm=ct=k03v873s|ci=fc6845cda72728607bd190e48de2419986152e3c|tr=slct|sn=17703|hk=aee1cc28b613ef90d05f1b7a43e41e9303215e76&utm_term=&utm_campaign=%B3%D7%C0%CC%B9%F6pc_%B0%A1%B0%DD%BA%F1%B1%B3%B1%E2%BA%BB&utm_source=%B3%D7%C0%CC%B9%F6_PC_PCS&utm_medium=%B0%A1%B0%DD%BA%F1%B1%B3))
5. **케이스 + 쿨러** ([링크](https://www.amazon.com/gp/offer-listing/B07VDCT57F/ref=dp_olp_new_mbc?ie=UTF8&condition=new))
    - 이 케이스를 사용하면 전원케이블 끝부분을 조금 잘라내야 한다.
    - 껴보고 안 들어가는 부분을 커터칼로 도려낸 후, 전기테이프로 감아주면 된다.
    - 이런 과정이 귀찮으면 [이 제품](https://www.devicemart.co.kr/goods/view?no=12230698)이 더 나아보인다.

### 1.2 전원 연결
1. 라즈베리파이 보드를 케이스와 결합
2. 쿨링팬을 라즈베리파이 보드의 GPIO에 연결
    - [이 곳](https://pinout.xyz/)에서 핀의 위치를 확인한다.
    - 팬의 속도를 3.3v의 low speed와 5v의 high speed 중 선택할 수 있다.
    - 3.3v(low speed)의 위치는 위의 링크에서 1번(빨간선), 6번(검정선)
    - 5v(high speed)의 위치는 4번(빨간선), 6번(검정선)
3. 전원을 연결하면 빨간불과 녹색불이 켜지고, 팬이 ~~돌아간다.~~ 돌아가야 한다.


<img src="https://github.com/Integerous/images/blob/master/raspberry-pi/raspberry_power_on.png?raw=true" width="50%">


## 2. OS 설치
>[다른 선택지들](https://www.raspberrypi.org/downloads/)도 있지만, 첫 경험이므로 얌전하게 공식 OS인 Raspbian을 설치한다.

### 2.1 Raspbian 이미지 다운로드
[이 곳](https://www.raspberrypi.org/downloads/raspbian/)에서 라즈비언 이미지를 다운로드 받는다.  
나는 서버로만 사용할 예정이기 때문에 Desktop GUI가 없지만 리소스 소모가 적은 Lite 버전을 선택했다.

<img src="https://github.com/Integerous/images/blob/master/raspberry-pi/raspbian.png?raw=true">

### 2.2 SD카드에 이미지 굽기