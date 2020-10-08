# Django Project 배포하기 - 1.Elastic Beanstalk

> Unilion 중앙 강의 EB배포 강의를 보완합니다.
>
> 이 세션 이후에 중앙 강의를 들으시면 좋습니다.

<br><br>

## 1. AWS 회원가입하기
1. [https://aws.amazon.com/ko/](https://aws.amazon.com/ko/)
<br><p align="center"><img src="/dp_img/9001.PNG" width = "800px"></p><br>

- AWS 계정 생성 버튼을 누릅니다.

2. 입력란을 채워줍니다.
<br><p align="center"><img src="/dp_img/9002.png" width = "800px"></p><br>

3. 다음에서 계정 유형은 `개인`을 선택하고, 나머지를 채웁니다.
<br><p align="center"><img src="/dp_img/9003.png" width = "800px"></p><br>

4. 해외 결제가 가능한 카드 정보를 입력합니다.
<br><p align="center"><img src="/dp_img/9004.png" width = "800px"></p><br>

- 해당 과정이 후 1달러가 자동적으로 결제됩니다.
- 결제 확인을 위한 과정으로 며칠 후에 다시 입금됩니다.

5. 다음화면에서 `기본 플랜`을 선택해 줍니다.
<br><p align="center"><img src="/dp_img/9005.png" width = "800px"></p><br>

<br><br>

## 2. IAM 유저 생성해보기

1. IAM이란?

AWS 리소스에 대한 액세스를 안전하게 제어할 수 있는 웹서비스입니다.

우리가 AWS에 가입한 계정은 루트 계정이며 모든 권한을 가지고 있기 때문에 루트 계정에 보안이 뚫리게 되면 공격자가 모든 권한을 가질 수 있는 문제가 있습니다.

그래서 제한된 권한을 가진 유저를 생성해서 해당 유저로 서버를 관리함으로써 보안이 뚫리더라도 모든 권한을 갖는 것은 막을 수 있습니다.

2. 유저 생성하기

- 서비스에서 IAM을 검색하거나 찾아서 들어갑니다.
<br><p align="center"><img src="/dp_img/01.PNG" width = "800px"></p><br>

- 왼쪽 탭에서 액세스 관리 > 사용자로 들어간 후 사용자 추가를 눌러줍니다.
<br><p align="center"><img src="/dp_img/02.PNG" width = "800px"></p><br>

- 사용자 이름을 입력하고, 액세스 유형은 저희는 CLI를 사용할 거기 때문에 `프로그래밍 방식 액세스`를 선택합니다.
<br><p align="center"><img src="/dp_img/03.PNG" width = "800px"></p><br>

- 다음은 이 유저에게 권한을 부여하는 과정입니다, `기존 정책 직접 연결`을 선택한 다음,
<br><p align="center"><img src="/dp_img/04.PNG" width = "800px"></p><br>

- `AmazonEC2FullAccess` `AmazonS3FullAccess` `AWSElasticBeanstalkFullAccess`를 추가해 줍니다.

<br><p align="center"><img src="/dp_img/05.PNG" width = "800px"></p><br>
<br><p align="center"><img src="/dp_img/06.PNG" width = "800px"></p><br>
<br><p align="center"><img src="/dp_img/07.PNG" width = "800px"></p><br>

- 여기선 그냥 다음 버튼을 눌러줍니다.
<br><p align="center"><img src="/dp_img/08.PNG" width = "800px"></p><br>

- `사용자 만들기`를 눌러줍니다.
<br><p align="center"><img src="/dp_img/04.PNG" width = "800px"></p><br>

- `다음` 버튼을 누르다 보면 이제 사용자 생성을 성공했다는 화면과 함께 액세스 키 ID, 비밀 액세스키가 나옵니다. 
지금이 이 키ID와 비밀 액세스키가 담긴 .csv를 다운로드 할 수 있는 마지막 기회입니다. 잘 보관해 주세요.

<br><br>

## 3. Elastic Beanstalk

### 1. Elastic Beanstalk이란?

Elastic Beanstalk은 배포를 위한 복잡한 환경설정과 엄청난한 시간투자에서 벗어날 수 있는 서비스 입니다. Java, .NET, PHP, Node.js, Python, Ruby, Go, Docker를 사용해서 웹 애플리케이션을 간편하게 배포하고 조정할 수 있습니다.

왜 배포가 간단할까요?? <br>
Elastic Beanstalk에서는 간단하게 deploy명령어 하나로 자동으로 처리해주는 작업들이 있습니다.

- amazon EC2 배포 환경 내에서 자동 설정(python, resion ...)
- EC2, RDS, S3 등의 자동 연결
- Auto Scailing으로 필요에 따라 인스턴스를 추가/제거
- 사용자 지정 : 리소스 선택 및 제어의 자유로움
- CloudWhatch 모니터링 / 관리 및 업데이트 등등

지금은 위의 사항에 관해서는 몰라도 좋습니다. 
그러면 이제 실제 배포를 해보면서 왜 배포가 간단한지에 대해서 알아봅시다.

<br><br>

### 2. 기본 프로젝트 만들기

지난 시간의 ckeditor 실습의 아주 앞부분만 생성해놓고 배포를 해보겠습니다.
[지난 세션 확인하기](https://jun108059.github.io/django-blog/CKEditor/00.ckeditor.html#_1-blog-ckeditor-%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%8C%E1%85%A6%E1%86%A8%E1%84%90%E1%85%B3-%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5)

    1. 바탕화면에 ebdeploy 폴더 생성
    2. vscode로 해당 폴더 open
    3. 가상환경 만들기 -> 활성화 (꼭 새로 만들어 줍니다.)
    4. pip install django==2.1.1 (반드시 2.1.1 버전을 설치해 줍니다.)
    5. django-admin startproject <프로젝트 명>
    6. cd <프로젝트 명>
    7. python manage.py startapp <앱 이름>
    8. python manage.py runserver
    9. settings.py
        ```
                INSTALLED_APPS = [
                    'django.contrib.admin',
                    'django.contrib.auth',
                    'django.contrib.contenttypes',
                    'django.contrib.sessions',
                    'django.contrib.messages',
                    'django.contrib.staticfiles',
                    '<앱 이름>.apps.<첫 글자 대문자 앱이름>Config', # Add
                ]
        ```
    10. ~ collectstatic 실행까지 완료

<br><br>

### 3. requirements.txt

이번엔 manage.py가 있는 경로에 requirements.txt 파일을 만들어 줄겁니다.

> requirements.txt란?
>
> 패키지 목록이 나열되어 있는 텍스트 파일입니다. 우리는 설치할 때 pip을 사용하기 때문에 pip으로 설치한 패키지 목록들이 나열되는 파일입니다.

Elastic Beanstalk에서는 이 requirements.txt를 통해 애플리케이션이 의존하는 파이썬 패키지들을 지정하게 됩니다. 그래서 스펠링이 틀리면 제대로 동작할 수 없습니다.

```
    $ pip freeze > requirements.txt
```
이 명령어를 치면 requirements.txt가 생성되면서 pip으로 설치된 패키지들이 입력이 되는걸 확인할 수 있습니다.

<br><br>


### 4. .ebextensions

그 다음엔 manage.py가 있는 경로에 .ebextensions폴더를 만들어 줍니다. 

> .ebextensions란? 
> AWS Elastic Beanstalk 구성파일을 추가하여 환경을 구성하고 환경에 있는 AWS 리소스를 사용자 지정하기 위해 필요한 폴더

그리고 그 안에 django.config 파일을 만들어줍니다.

```
    option_settings:
        aws:elasticbeanstalk:container:python:
            WSGIPath: <프로젝트 명>/wsgi.py
```

django.config 안에 채워줄 내용입니다.<br>
WSGIPath는 본인의 wsgi.py가 있는 경로를 적어주면 됩니다. <br>
wsgi는 Python에서 웹 어플리케이션이 웹 서버와 통신하기 위한 인터페이스입니다.<br>
EB는 이 파일을 통해서 우리 어플리케이션의 wsgi가 설정되어있는 경로를 알게 됩니다.<br>

<br><p align="center"><img src="/dp_img/9006.PNG" width = "300px"></p><br>

여기까지 완료했을 때 파일구조는 프로젝트 이름이나 앱 이름을 제외하고는 다음과 같아야 합니다! <br>
이 구조가 아주 중요하니깐 배포하기 전에 확인해주세요.

<br><br>

### 5. 가상환경 비활성화 deactivate
다음 단계를 위해 deactivate 명령어로 가상환경을 비활성화 시켜줍니다.
<br><br>

### 6. Elastic Beanstalk 기본 환경 설정과 배포

Elastic Beanstalk에서는 파일들의 위치, 가상환경 여부가 아주 중요합니다.


1. 설치

우선 로컬에서 Elastic Beanstalk를 사용할 수 있도록 Awsebcli를 설치해 주겠습니다.

```
    $ pip install awsebcli --upgrade --user
```

<br><p align="center"><img src="/dp_img/20.PNG" width = "800px"></p><br>

이게 제대로 설치되었나 확인해보기 위해서 명령어를 쳐봅시다.

```
    $ eb --version
```
<br><p align="center"><img src="/dp_img/21.PNG" width = "800px"></p><br>

위와 같이 나온다면 성공입니다.

eb command not found 오류가 나는 분들이 있을거에요. 
간단한 환경 설정을 해줘야 합니다.

[Window EB CLI 설치](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb-cli3-install-windows.html)



2. .gitignore 파일 만들기

이제 manage.py 가 있는 경로에 .gitignore을 만들어줄겁니다.
gitignore.io에 가서 만들면 쉽게 만들 수 있는거 다들 아시죠?

```
    ### Django ###
    *.log
    *.pot
    *.pyc
    __pycache__/
    local_settings.py
    db.sqlite3
    media
    
    ### ElasticBeanstalk ###
    .elasticbeanstalk/*
    !.elasticbeanstalk/*.cfg.yml 
    !.elasticbeanstalk/*.global.yml
```

.elasticbeanstalk은 eb명령어를 이용해서 배포하면 생기는 폴더입니다. 
필요없는건 업로드하지 말고 필요한 것만 업로드하도록 .gitignore을 작성해줍시다.

3. 프로젝트를 깃허브 새 repo에 push하기
 - manage.py가 있는 경로에서 git init 한 후 커밋 푸쉬 해 줍니다.

<br><p align="center"><img src="/dp_img/9007.PNG" width = "800px"></p><br>

4. eb init
```
    $ eb init -p python-3.6 ebdeploy
```

eb init -p python-3.6 <이름>
eb init 명령어를 이용해서 Elastic Beanstalk 플랫폼, 지역, 어플리케이션 이름을 지정하여 초기화 시켜줍시다.

<br><p align="center"><img src="/dp_img/25.PNG" width = "800px"></p><br>

이렇게 credentials를 제공하라고 뜨면 아까 저장했던 IAM credential CSV파일에 있는 id랑 key를 입력해줍니다.
<br><p align="center"><img src="/dp_img/9008.jpg" width = "800px"></p><br>

`Application ebdeploy has been created.` 라는 말이 나오면 정상적으로 생성된겁니다.


5. EB 환경 생성

다음은 Elastic Beanstalk 환경을 생성해줍니다.
여기부터는 명령 수행이 전부 끝날 떄까지 여유롭게 기다려주세요.

```
    $ eb create <환경이름>
```
<br><p align="center"><img src="/dp_img/29.PNG" width = "800px"></p><br>

`Successfully launched environment: ebdeploy` 라는 말이 나오면 정상적으로 환경이 생성된겁니다.


6. CNAME 확인
이제 우리의 도메인 별명인 CName을 확인할게요.

```
    $ eb status
```
<br><p align="center"><img src="/dp_img/30.PNG" width = "800px"></p><br>

표시된 CNAME 부분을 복사해서
settings.py의 ALLOWED_HOSTS에 넣어줍니다.

``` 
    ALLOWED_HOSTS = ['django-env.eba-p3mxxxxxx.us-west-2.elasticbeanstalk.com'] 
```


7. 배포

수정된 사항을 github repository에 업로드합니다. (commit , push) 
이제 배포를 하면 됩니다.

```
    $ eb deploy
```

eb deploy 명령을 통해서 배포해 줍니다.

```
    $ eb open
```

위 명령어로 배포가 잘 되었는지 확인해 줍니다.
<br><p align="center"><img src="/dp_img/31.PNG" width = "800px"></p><br>


8. 종료

배포를 성공한 이후에는 꼭 종료를 시켜주시기 바랍니다.
```
    $ eb teminate --all
```
<br><p align="center"><img src="/dp_img/32.PNG" width = "800px"></p><br>


## 목차 1008
- [Elastic Beanstalk](/eb.md)
- [Heroku](/heroku.md)
