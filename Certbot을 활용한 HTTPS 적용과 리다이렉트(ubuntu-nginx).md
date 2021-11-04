## 목표
> 1. Ubuntu OS 기반 Nginx 웹 서버에 certbot 을 활용하여 https를 적용시킨다.
> 2. http 접속을 https 로 리다이렉트한다.

## Certbot을 설치하기 전에

Certbot 을 설치하는 방법은 다양하지만, 현재 Let`s encrypt <sup>[1](#footnote_1)</sup> 의 공식입장은 snap 패키지<sup>[2](#footnote_2)</sup>를 활용해서 설치하는 방법이다. 참고로 Shell 명령어인 certbot-auto 를 활용한 방법은 더 이상 지원하지 않고 있으며, [삭제](https://certbot.eff.org/docs/install.html)를 권하고 있다.

_snap 패키지를 사용하므로 ubuntu 16.04 이상 버전이여야 한다._

혹여나 다른 OS와 다른 웹서버에서 설치하고 싶다면 [여기](https://certbot.eff.org/instructions)를 통해 원하는 OS와 웹서버를 선택하면 Let`s encrypt 가 권장하는 공식 설치 방법을 알 수 있다. ~~영어 주의~~

Certbot 설치는 공식문서의 방법을 그대로 인용한 것으로, 원본은 여기서 [확인](https://certbot.eff.org/lets-encrypt/ubuntufocal-nginx)할 수 있다.

참고로 HTTPS는 도메인을 통해서 적용시키므로 IP로만 접속할 수 있다면 해당 IP에 도메인을 연결해야한다.

---

## Certbot 설치

```
sudo snap install core
sudo snap refresh core
sudo snap install --classic certbot

sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
```

sudo certbot --nginx 를 입력하면 다음과 같은 질문들이 나온다.  
<br/>

<img width="550" alt="certbot --nginx 첫번째 질문, 이메일" src="https://user-images.githubusercontent.com/80164141/124739318-b1e8c100-df54-11eb-94a9-d4f883ba1909.png">
디버깅을 위한 로그, 긴급한 사항 혹은 보안 사항 등을 전달할 이메일을 입력해달라는 내용, 이메일을 입력한 후 엔터
<br/>
<br/>

<img width="550" alt="certbot --nginx 두번째 질문, 약관 읽어보기" src="https://user-images.githubusercontent.com/80164141/124739581-f5432f80-df54-11eb-8bb6-98be7a8fb589.png">  
약관을 읽고 SSL 을 사용하기 위한 서버 등록을 승낙해야 한다는 이야기. Y 입력한 후 엔터
<br/>
<br/>

<img width="550" alt="certbot --nginx 세번째 질문, 재단에 이메일 공유 여부" src="https://user-images.githubusercontent.com/80164141/124740075-6551b580-df55-11eb-8ebc-e5bb1f4173a4.png">
위에서 입력했던 이메일을 재단에도 공유해서 재단의 뉴스, 캠페인, 디지털 자유운동 지원 방법 등을 받을 것인지에 대한 내용. 흔히들 생각하는 NGO단체의 이메일이라 생각하면 될 것 같다. 

_난 오픈소스의 힘을 믿으니까 Y_
<br/>
<br/>

<img width="550" alt="certbot --nginx 네번째 질문, 어떤 도메인을 HTTPS 적용시킬 것인지 여부" src="https://user-images.githubusercontent.com/80164141/124740976-3ab42c80-df56-11eb-8ec5-7524f6480aac.png">
어떤 도메인에 HTTPS를 적용시킬지 물어본다. 그냥 엔터를 누르면 목록에 뜬 모든 도메인에 HTTPS를 적용시켜주고, `1 3` 이런식으로 숫자를 띄어서 엔터를 누르면 해당 도메인만 HTTPS를 적용시켜 준다. 

_난 모든 도메인을 HTTPS 적용시키길 원하니 그냥 엔터_
<br/>
<br/>

<img width="550" alt="certbot --nginx 완료 화면" src="https://user-images.githubusercontent.com/80164141/124741806-11e06700-df57-11eb-8b99-815c48c77080.png">
Successfully deployed certificate for 도메인 과 함께, 어떤 도메인들이 HTTPS가 적용됐는지 알려주고 만약 마음에 든다면 기부를 권하는 모습이다.

~~_역시 NGO계열은 다들 비슷비슷한가보다._~~

이 방법을 따라왔다면 기본 옵션을 수정하지 않는 한, certbot 은 만료 전에 자동으로 갱신되며 리다이렉트까지 자동으로 설정되어 있다.
<br/>
<br/>
리뉴얼이 되는 모습을 확인해보고 싶다면

`sudo certbot renew --dry-run`을 사용하면 되고 다음과 같이 나오게 된다.
<img width="550" alt="certbot renew --dry-run 완료 화면" src="https://user-images.githubusercontent.com/80164141/124742554-ce3a2d00-df57-11eb-8ca4-9677d6af8a30.png">

`sudo service nginx restart` 로 웹서버를 재시작을 해주고 해당 도메인을 들어가면 https로 연결되는 것을 알 수 있다.  

---
## HTTP를 HTTPS로 리다이렉트
물론 위에서 밝혔듯, 친절한 snap 패키지의 certbot 설치는 리다이렉트까지 지원하지만, 그대로 두면 Safari 에서는 리다이렉트를 안하게 되어, 수정할 필요가 있다.  

`sudo vi /etc/nginx/sites-available/default` 로 nginx 옵션에 들어가자.  


```
... 코드들 ...

server {
    if ($host = 도메인) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    if ($host = 도메인) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    if ($host = 도메인) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


        listen 80;
        listen [::]:80;
        server_name 도메인, 도메인, 도메인;
        return 301 https://도메인; # managed by Certbot 
        
        // 403 으로 되어있을 부분을 301 https://도메인 으로 바꿔준다.
}
```

default 에서 가장 밑에 있을 해당 server 블록에서, 가장 마지막 return 403 부분을 301 https://도메인 으로 바꿔준다. 이를 통해 서버가 80번(http)을 listen 하면 자동으로 https 로 리다이렉트 시켜준다. 부가적으로 `listen 443 ssl` 에 `default_server`를 추가시켜주어서 443포트가 이 서버의 기본 포트라는 걸 알려주는 것도 괜찮다. 

여기서 301은 HTTP Status Code로, 영구 이동(Permanently moved)을 의미한다. 즉, "네가 요청한 페이지가 영구히 이전되었으므로 이 주소로 다시 접속을 시도해 봐라."라는 의미이다.<sup>[3](#footnote_3)</sup> 

여기서 실수하면 리다이렉트 횟수가 너무 많다는 등의 오류를 보게 된다. 혹시 리다이렉트 관련 오류가 생긴다면 return 되는 주소가 A가 A를 부르는 형태라든지 등을 확인하면 된다. _~~즉 리다이렉트 관련 오류는 왠만하면 설정자 잘못이다.~~_

_만약 리다이렉트 문제나 여타 관련 문제를 겪는다면 다른 브라우저 (Safari라면 크롬, Edge 등)도 사용해보고, [이곳](https://www.whatsmydns.net/#CNAME/dev.yeorumlabo.com)에서 자신의 도메인이 제대로 연결은 되었는지 확인해보자. 브라우저의 쿠키 문제로 안될 수도 있으며, 도메인 자체가 제대로 연결되어 있지 않다면 당연히 문제가 생긴다._

---
[Qualys.](https://www.ssllabs.com/ssltest/index.html) 에서 해당 도메인의 SSL이 얼만큼 강한 보안을 갖고 있는지 확인할 수 있다. 

참고로 네이버가 B이다.
_~~어?~~_

---
>**출처**  
> Certbot instruction [출처](https://certbot.eff.org/lets-encrypt/ubuntufocal-nginx)  
> Nginx에서 자동 Redirection(301 Permanently moved) 설정하기 [출처](https://www.tuwlab.com/ece/26993)  
> Let's Encrypt 위키백과 [출처](https://ko.wikipedia.org/wiki/Let%27s_Encrypt)
> *HTML에서 각주 사용하는 법 [출처](https://lynmp.com/ko/article/nu86c16d8f09c9fbd8)

***
<a name="footnote_1">1</a>: Certbot 를 제공하는 프로젝트 명으로, 공익기관 ISRG(Internet Security Research Group) 가 주도하고 있다. firefox 를 만든 모질라재단, 스탠퍼드 법학대학원, 리눅스 재단이 지원하고 있다. [위키 출처](https://ko.wikipedia.org/wiki/Let%27s_Encrypt)  

<a name="footnote_2">2</a>: 우분투 16.04 버전부터 도입한 애플리케이션 패키지 포맷이다. 스냅으로 만들어진 애플리케이션은 내부에 구동을 위한 요소를 포함하고 있어 OS에 덜 의존하게 되며 프로그램에 필요한 모든 라이브러리가 포함되어 빌드되는 형태이다. ~~_즉 express 를 스냅으로 설치한다면 node js 가 함께 설치..되나? 댓글로 알려줘요.._~~  [출처](https://openwiki.kr/tech/snap)  

<a name="footnote_2">3</a>: [Nginx에서 자동 Redirection(301 Permanently moved) 설정하기](https://www.tuwlab.com/ece/26993)
