# hawawawiki
hawawa wiki용 빌드 가이드

## 1. 우분투 빌드
[ghost 우분투 설치 문서](https://ghost.org/docs/install/ubuntu/)

### 서버 준비 

AWS Lightsail 등



### 서버 사용자 설정
~~~bash
# 사용자 추가
sudo adduser <사용자>

# 사용자 su 권한 부여
sudo usermod -aG sudo <사용자>

# 사용자로 로그인
sudo su <사용자>
~~~

### 기초 설치
~~~bash
# 패키지 리스트 업데이트
sudo apt-get update

# 패키지 업데이트
sudo apt-get upgrade

#nginx 설치
sudo apt-get install nginx
sudo ufw allow 'Nginx Full'

# mysql 설치
sudo apt-get install mysql-server

# mysql 설정
sudo mysql

# '비밀번호'에 설정할 비밀번호 입력 '' 제거 안함
ALTER USER 'root'@localhost' IDENTIFIED WITH mysql_native_password BY '비밀번호';
quit

# node.js 설치
# 설치 전 지원 버전 확인 https://ghost.org/docs/faq/node-versions
# 2021/04 기준 14.x
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash
sudo apt-get install -y nodejs

# ghost cli 설치
sudo npm install ghost-cli@latest -g
~~~

### 고스트 설치
~~~
# 설치할 폴더 생성
sudo mkdir -p /var/www/사이트이름

# 사용자에게 폴터 소유권 부여
sudo chown <사용자>:<사용자> /var/www/사이트이름

# 폴더 권한 설정
sudo chmod 775 /var/www/사이트이름

# 폴더로 이동
cd /var/www/사이트이름

# 고스트 설치
ghost install
~~~

### 고스트 설정
#### Blog URL
사용할 주소 입력

#### MySQL hostname
localhost

#### MySQL username / password
root / 위 mysql 설정중 입력만 비밀번호

#### Ghost database name
자동

#### Setup up a ghost MySQL user?
사용

#### Set up NGINX?
사용

#### Set up SSL?
사용

##### Enter your email
hawawawiki@gmail.com

#### Set up systemd?
사용

#### Start Ghost?
사용

## 2. 테마 설정
[theme: Dashi](https://bironthemes.com/docs/dashi-ghost/)

[기초 스타일링](https://dashi.bironthemes.com/elements/)

### 설정 언어 ko

### Portal 사용 안함

### routes YAML 파일 테마에서 업로드

### integration에서 키 하나 생성

### partials 폴더 안 theme-config.hbs 수정
~~~
# GHOST_URL:
https://이름.hawawa.wiki
# GHOST_KEY:
바로 위에서 생성한 Content API Key
# DEFAULT_COLOR_SCHEME
dark
# COVE_ID
이후 추가
~~~

### default.hbs 수정
~~~
# jost font 대체
https://fonts.googleapis.com/css2?family=Nanum+Gothic:wght@400;700;800&display=swap
~~~

### _settings.css 수정
~~~
# --font-sans: 'Jost', Tahoma, Arial, sans-serif; 를 다음으로 교체
--font-sans: 'Nanum Gothic', Arial, Tahoma, sans-serif;
~~~

### index.hbs 수정
~~~
# {{!-- Subscribe --}} 밑 {{> welcome}} 제거
~~~

### 네비게이션 추가

#### contact page 추가

~~~
# page-contact.hbs
# <form action="" 에 formspreee 폼 주소 추가
# formspree 에서 hawawawiki 하위에 폼 추가하기
https://formspree.io/f/예시
~~~

#### tags page 추가

#### membership page 사용 안함

#### archive page 추가

#### bookmark page 추가

### 번역 추가
ko.json

### Social 링크
RSS 제외 {{!-- --}} 가두기

### 구글 애널리스트
[참고](https://bironthemes.com/blog/ghost-analytics/)

#### default.hbs 수정
~~~
# {{ghost_head}} 아래 </head> 위에 추적코드 삽입하기 - 예시
    {{ghost_head}}
	
	<!-- Global site tag (gtag.js) - Google Analytics -->
	<script async src="https://www.googletagmanager.com/gtag/js?id=G-애널리틱스ID"></script>
	<script>
	  window.dataLayer = window.dataLayer || [];
	  function gtag(){dataLayer.push(arguments);}
	  gtag('js', new Date());

	  gtag('config', 'G-애널리틱스ID');
	</script>

  </head>
~~~

### footer 변경
~~~
# 불필요한 멤버권유 제거
{{!-- Footer subscription --}}
    {{#unless @member}}
      <div class="footer__sub p-t-xl p-b-xl border-bottom-1">
        <div class="row">
          <div class="col-xs-12 col-md-6 footer__sub-text">
            <h3 class="m-b-0">{{t "Ready to get started now?"}}</h3>
            <div>{{t "Subscribe to get access to premium content or contact us if you have any questions."}}</div>
          </div>
          <div class="col-xs-12 col-md-6">
            <div class="flex footer__sub-action">
              <a href="/membership/" class="btn btn--brand btn--rounded m-r">{{t "Subscribe"}}</a>
              <a href="/contact/" class="btn btn--bordered btn--rounded">{{t "Contact us"}}</a>
            </div>
          </div>
        </div>
      </div>
    {{/unless}}

~~~

## 3. 이메일 설정
### mailgun 사용
[참고](https://ghost.org/docs/config/)

smtp credential 에서 키 받아서 사용

#### SSH로 config.production.json 수정
~~~
# 마지막 } 위에 추가
"mail": {
  "transport": "SMTP",
  "options": {
    "service": "Mailgun",
    "auth": {
      "user": "postmaster@hawawa.wiki",
      "pass": "비밀번호"
    }
  }
}
~~~


## 4. 기타 설정
### account.hbs 수정
~~~
# 불필요한 유료권유 제거
<div class="col-xs-12 col-md-4 m-b-lg{{#if @member}} col-md-offset-2{{/if}}">
  {{> pricing/monthly}}
</div>

<div class="col-xs-12 col-md-4 m-b-lg">
  {{> pricing/yearly}}
</div>
~~~
### membership.hbs 수정
~~~
# 다음과 같이 수정
{{#post}}

지원 안함

{{/post}}
~~~
