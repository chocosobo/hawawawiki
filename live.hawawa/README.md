# hawawa live
hawawa live 빌드 가이드

## 1. 우분투 빌드


### 1. 참조

[OME 서버 설정](https://airensoft.gitbook.io/ovenmediaengine/getting-started)

[Oven Player](https://airensoft.gitbook.io/ovenplayer/)

### 2. 서버 준비 

#### 기본 설치

~~~
apt-get update && apt-get upgrade

sudo apt-get install build-essential

(curl -LOJ https://github.com/AirenSoft/OvenMediaEngine/archive/v0.12.1.tar.gz && tar xvfz OvenMediaEngine-0.12.1.tar.gz)
OvenMediaEngine-master/misc/prerequisites.sh

sudo apt-get update
cd OvenMediaEngine-0.12.1/src
make release
sudo make install
systemctl start ovenmediaengine
# If you want automatically start on boot
systemctl enable ovenmediaengine.service 
~~~

#### 방화벽 설정

~~~
# srt -in
ufw allow 9999
# ovt
ufw allow 9000

# webrtc -in,out
ufw allow 3333
# webrtc tcp
ufw allow 3478
# webrtc ice -out
ufw allow 10000
ufw allow 10001
ufw allow 10002
ufw allow 10003
ufw allow 10004
ufw allow 10005
ufw allow 10006
ufw allow 10007
ufw allow 10008
ufw allow 10009
ufw allow 10010

# dash,hls -out
ufw allow 80
ufw allow 443

ufw enable
~~~

### 3. OME 준비

~~~
nano /usr/share/ovenmediaengine/conf/Server.xml

<Application>
  <Name>앱이름(event)</Name>
  ...
  ...
</Application>

---

<OutputProfiles>
  <OutputProfile>
      <Name>스트림이름(hawawa)</Name>
      ...
  </OutputProfile>
</OutputProfiles>



<LLDASH>
    <SegmentDuration>5(into 2)</SegmentDuration>
    <CrossDomains>
        <Url>*</Url>
    </CrossDomains>
</LLDASH>
~~~

### 4. certbot 준비

[Certbot 설치](https://certbot.eff.org/lets-encrypt/ubuntubionic-other)

~~~
apt-get install snapd
sudo snap install core; sudo snap refresh core

sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot certonly --standalone
~~~

pem 변환

~~~
openssl rsa -in /etc/letsencrypt/live/***/privkey.pem -text > privkey.key
openssl x509 -inform PEM -in /etc/letsencrypt/live/***/cert.pem -out cert.crt
openssl x509 -inform PEM -in /etc/letsencrypt/live/***/chain.pem -out fullchain.crt
~~~

~~~
<Host>
  <Names>
    <Name>live.hawawa.wiki</Name>
  </Names>
  <TLS>
    <CertPath>/etc/letsencrypt/live/***/cert.crt</CertPath>
    <KeyPath>/etc/letsencrypt/live/***/privkey.key</KeyPath>
    <ChainCertPath>/etc/letsencrypt/live/***/chain.crt</ChainCertPath>
  </TLS>
</Host>

~~~




Webrtc 적용 안하면 현재 lldash로 8초


## 2. 방송 설정

srt로 사용

srt://(ip):port?streamid=srt%3A//(ip)%3A(Port)/(앱이름)/(스트림이름)%3Fquery%3Dvalue

LLDASH
http://<Domain>:80/<앱이름>/<스트림이름>/manifest_ll.mpd
Secure LLDASH
https://<Domain>:443/<앱이름>/<스트림이름>/manifest_ll.mpd


