# \[Django\] - Social Login

이번 세션은 8월 25일 댓글 CRUD를 구현했던 `myblogproject`에서 이어 진행합니다.<br><br>

## 개요

다들 한번 쯤 구글으로 로그인,네이버로 로그인,카카오톡으로 로그인을 해보신 적이 있으실겁니다.

저희는 오늘 이 소셜 로그인 기능을 구현해보려고 합니다.<br><br>

**1\. 패키지 설치하기**

먼저 소셜 로그인 기능을 사용하기 위해 필요한 패키지를 설치해 주겠습니다.

```
    $ pip install django-allauth
```

<br><p align="center"><img src="/img3/1.png" width = "800px"></p><br><br>

**2\. settings.py 수정**

그리고 settings.py를 수정해줘야 합니다.

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.sites', #추가
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'myblog',
    
    #allauth 추가
    'allauth',
    'allauth.account',
    'allauth.socialaccount',

    #provider 구글 페이스북 카톡 깃허브 등 소셜로그인 제공업체 추가
    'allauth.socialaccount.providers.google',
]
```

-   django.contrib.sites 추가
-   allauth 하단의 세 사지 설정 추가(계정과 관련된 설정들)
-   provider 추가 (어떤 소셜로그인 제공업체로부터 서비스를 제공 받을 것인지 작성)
    -   저희는 google로 실습을 해보겠습니다.
    -   만약 facebook이나 다른 소셜 로그인기능을 구현하고 싶다면 providers.facebook과 같이 작성합니다.

<br><br>
그 다음 스크롤을 settings.py 가장 밑으로 내려서 아래의 코드를 추가로 작성합니다.

```
AUTHENTICATION_BACKENDS = (
    'django.contrib.auth.backends.ModelBackend',
    'allauth.account.auth_backends.AuthenticationBackend',
)

SITE_ID = 1
LOGIN_REDIRECT_URL = '/'
```

<br><p align="center"><img src="/img3/2.png" width = "800px"></p><br>

-   allauth라는 기능을 사용하기 위해서는 AUTHENTICATION\_BACKENDS 라는 튜플을 입력해 줘야합니다. (이렇게 작성해야 하는구나 정도로 이해하시면 됩니다.)
-   아래쪽의 LOGIN\_REDIRECT\_URL 은 로그인 후 호출되는 url을 지정해주는 부분입니다.

<br><br>

**3\. myblogproject/urls.py 수정**

우리는 아까 pip install을 통해 allauth 패키지를 설치했습니다.

그래서 구현하지 않았았지만, 우리의 프로젝트 어딘가에는 이미 allauth의 url이 있고, 우린 그냥 써 주기만 하면 됩니다.

accounts로 시작하는 url이 들어올 경우 allauth.urls를 include해 사용합니다.

```
urlpatterns = [
    path('admin/', admin.site.urls),
    url('', include('myblog.urls')),
    path('accounts/', include('allauth.urls')),
]
```

<br><br>

**4\. migrate**

settings.py에 변경한 내역들을 migrate를 통해 알려줍니다.

<br><p align="center"><img src="/img3/3.png" width = "800px"></p><br><br>

**5\. admin 페이지**

이제 서버를 켜고 admin 페이지로 가서 확인해 봅시다.

<br><p align="center"><img src="/img3/4.png" width = "800px"></p><br>

이전 과 다르게 못 보던 탭들이 생긴 것을 확인할 수 있습니다.

먼저, Sites를 눌러보겠습니다.

<br><br>

**\- Sites**

<br><p align="center"><img src="/img3/5.png" width = "800px"></p><br>

처음 접속하면 examplesite라고 뜰겁니다.

sites는 우리가 소셜 로그인을 사용할 사이트를 의미합니다.

example site를 클릭합니다.

<br><p align="center"><img src="/img3/6.png" width = "800px"></p><br>

위의 Domain name과 Display name을 다음과 같이 바꿔줍니다.

<br><p align="center"><img src="/img3/7.png" width = "800px"></p><br>

그리고 save를 눌러줍니다.

<br><p align="center"><img src="/img3/8.png" width = "800px"></p><br>

이제 저희 사이트는 소셜 로그인이 어떤 사이트를 기반으로 동작할지 알게 되었습니다.

구글 계정과 저희의 소셜 로그인 기능을 연동 하려면 social applications에 저희 계정을 추가해 줘야 합니다.

Social applications를 선택합니다.

<br><br>

**\-Socail applications**

<br><p align="center"><img src="/img3/9.png" width = "800px"></p><br>

표시된 Add Social Applicaton을 눌러줍니다.

<br><p align="center"><img src="/img3/10.png" width = "800px"></p><br>

Provider의 드롭다운 버튼을 클릭해 보면 우리가 아까 settings.py에 등록해 뒀던 provider들이 출력됩니다.

우리는 google을 등록했으니 google이 목록에 있을 겁니다. 선택해주세요.

아래쪽에는 우리의 소셜 앱 이름을 적습니다. 저는 MyBlog라고 적겠습니다.

그 밑에 항목으로 Client id, Secret key가 보입니다. 뭘 넣어야할지 모르겠죠?

같이 한 번 해보겠습니다.

<br><br>

**6\. Google API**

아래 페이지로 접속합니다.

[https://console.developers.google.com/?pli=1](https://console.developers.google.com/?pli=1)

<br><p align="center"><img src="/img3/11.png" width = "800px"></p><br>

처음 접속하면 위와 같은 화면이 뜰겁니다. 

서비스 약관에 체크한 후 동의 및 계속하기를 눌러줍니다.

<br><p align="center"><img src="/img3/12.png" width = "800px"></p><br>

그리고 보여지는 다음 화면에서 프로젝트 선택을 눌러줍니다.

<br><p align="center"><img src="/img3/13.png" width = "800px"></p><br>

새 프로젝트를 눌러줍니다.

<br><p align="center"><img src="/img3/14.png" width = "800px"></p><br>

프로젝트 이름은 적고 만들기를 눌러줍니다. 여기서는 생성된 기본값으로 만들었습니다.

<br><p align="center"><img src="/img3/15.png" width = "800px"></p><br>

프로젝트 선택을 눌렀던 곳에 만든 프로젝트 명이 적혀져있는 것을 확인할 수 있습니다.

그 다음으로는 표시된 사용자 인증 정보를 선택해 줍니다.

<br><p align="center"><img src="/img3/16.png" width = "800px"></p><br>

사용자 인증 정보 만들기를 눌러줍니다.

<br><p align="center"><img src="/img3/17.png" width = "800px"></p><br>

OAuth 클라이언트 ID를 선택해 줍니다.

<br><p align="center"><img src="/img3/18.png" width = "800px"></p><br>

인증 정보를 얻기 위해 해줘야 하는 게 있다고 뜹니다.

동의 화면 구성 버튼을 클릭합니다.

<br><p align="center"><img src="/img3/19.png" width = "800px"></p><br>

외부를 클릭하고 만들어줍니다.

<br><p align="center"><img src="/img3/20.png" width = "800px"></p><br>

그 다음 화면에서는 애플리케이션 이름만 간단하게 작성하고 저장해 줍니다.

화면으로 돌아와 애플리케이션 유형 중 웹 애플리케이션을 선택해줍니다.

다시 사용자 인증 정보 -> 사용자 인증 정보 만들기 -> OAuth 클라이언트 ID 만들기 를 선택해줍니다.

<br><p align="center"><img src="/img3/21.png" width = "800px"></p><br>

웹 애플리케이션을 선택합니다.

<br><p align="center"><img src="/img3/22.png" width = "800px"></p><br>

그 다음 생기는 화면에는 다음과 같이 작성하고 저장해줍니다.

<br><p align="center"><img src="/img3/23.png" width = "800px"></p><br>

다음과 같이 생성된 것을 확인할 수 있습니다.

표시된 ID를 클릭해 줍니다.

<br><p align="center"><img src="/img3/24.png" width = "800px"></p><br>

우측에 표시되어있는 위의 내용을 Admin 페이지 탭으로 돌아가 복붙 해주시면 됩니다.

<br><p align="center"><img src="/img3/25.png" width = "800px"></p><br>

그리고 나서

<br><p align="center"><img src="/img3/26.png" width = "800px"></p><br>

Sites의 왼쪽에 있는 항목을 오른쪽으로 넘겨(choose)줍니다.

그리고 저장버튼을 눌러주세요.

이제 구글과 우리 사이트가 연결되어 로그인 페이지를 구현하기 위한 모든 준비가 완료 되었습니다.

<br><br>

**7\. Templates 수정**

이제 로그인을 구현할 home.html을 수정해줍니다.

아래 내용을 적절한 위치에 추가해줍니다.

추가적으로 보기좋게 만들어주면 좋습니다. 

```
<!-- {% extends 'base.html' %} 밑에 아래 두 줄 추가 -->
{% load socialaccount %}
{% providers_media_js %}


<!-- 아래의 코드를 본인이 원하는 위치에 추가 -->

<a href="/accounts/signup">회원가입</a><br>

{% if user.is_authenticated %}
<a href="/accounts/logout">로그아웃</a><br>
{{user.username}} 님이 로그인 중

{% else %}
<a href="/accounts/login">Login</a>
<a href="{% provider_login_url 'google' %}">Login with google</a><br>


{% endif %}
```

-   {% load socialaccount %} 를 통해 외부에서 socialaccount 기능을 가져와 사용한다는 것을 알려줍니다.
-   {% providers\_media\_js %} 를 통해 로그인 페이지의 기본적 템플릿 등을 가져와 사용할 것이라고 알려줍니다.
-   `<a href="/accounts/signup&login&logout">` : 직접 구현하지 않아도 설치했던 allauth라는 패키지가 알아서 url을 만들고 연결해줍니다.
-   {% if user.is\_authenticated %} 으로 현재 상태를 체크해, 사용자가 로그인 되어있는 상태라면 현재 상태와 함께 로그아웃 기능을 제공하고 로그인 되어있지 않다면 로그인 기능이 제공되게끔 합니다.
-   {% provider\_login\_url 'google' %} 를 통해 구글 로그인을 사용할거라고 알려줍니다.
-   본인이 원하는 모양으로 꾸며주시면 더 보기 좋겠죠?

<br><br><br><br>

\- 확인

1) 로그인 상태

<br><p align="center"><img src="/img3/27.png" width = "800px"></p><br>

관리자 계정으로 로그인 되어있기 때문에 다음과 같은 화면이 뜹니다.

<br><br>

2) 로그아웃 상태

<br><p align="center"><img src="/img3/28.png" width = "800px"></p><br><br>

3) 회원가입

<br><p align="center"><img src="/img3/29.png" width = "800px"></p><br><br>

4) 로그인

<br><p align="center"><img src="/img3/30.png" width = "800px"></p><br><br>

5) 구글 로그인

<br><p align="center"><img src="/img3/31.png" width = "800px"></p><br><br>

6) 구글로 로그인 한 상태

<br><p align="center"><img src="/img3/32.png" width = "800px"></p><br><br>

**8\. 오류**

**\- 400 오류가 나는 경우**

구글 로그인을 클릭했을 때, 계정 선택화면이 아니라 다음과 같이 승인 오류가 나는 경우

<br><p align="center"><img src="/img3/33.png" width = "800px"></p><br>

OAuth 클라이언트 ID 를 다시 클릭해 리디렉션 URI를 추가합니다.

callback 주소가 등록되지 않아 그렇습니다.

<br><p align="center"><img src="/img3/34.png" width = "800px"></p><br>

**\- 회원가입 기능을 사용해보고 싶은데, 가입완료 버튼을 누르면 오류페이지로 넘어가는 경우. (사실 가입은 되어있습니다.)**

settings.py에 아래 코드를 추가해 줍니다.

```
ACCOUNT_EMAIL_VERIFICATION = 'none'
```

<br><p align="center"><img src="/img3/35.png" width = "800px"></p><br>

[다음 : \[Django\] - Like](/like.md)


## 목차 0910
- [\[Django\] - Social Login](social_login.md)
- [\[Django\] - Like](/like.md)
