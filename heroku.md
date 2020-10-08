# Django Project 배포하기 - 2.heroku

앞서 Elastic Beanstalk을 이용한 배포를 경험했습니다.<br>
이번에는 AWS가 아닌 heroku를 이용한 배포를 해보겠습니다.<br>


## 배포할 프로젝트
앞서 배포를 했던 프로젝트를 그대로 사용하겠습니다.<br>
바탕화면에 `herokudeploy`라는 폴더를 만들어줍니다.<br>
그리고 프로젝트를 이 폴더로 복사해줍니다.<br>

그리고 herokudeploy에 가상환경을 만들어주고 활성화 시켜줍니다.<br>

```
    $ pip install django==2.1.1
```

<br><p align="center"><img src="/dp_img/1001.PNG" width = "400px"></p><br>


## 웹사이트에 올릴 준비하기
우리의 프로젝트, 그리고 앞으로 장고로 만들 프로젝트는<br>
보안이나 성능상의 문제가 생길 가능성이 매우 높습니다. <br><br>

일단 가장 중요한 두가지만 settings.py에서 수정해봅시다.<br>

### settings.py 수정하기

반드시 체크해야될 사항은 두가지 입니다.<br>

- DEBUG

    개발시 페이지가 없다던지, url이 잘못되었다던지 친절하게 설명해주든 DEBUG 기능을 꺼야합니다.
    이 기능이 켜져있는 상태에서 배포된다면, 프로그래밍에 조예가 깊은 다른 분들이 들어와서 내 프로덕트를 수정해줄지 모릅니다.

```
    # settings.py
    # SECURITY WARNING: don't run with debug turned on in production!
    DEBUG = True
```

를

```
    # SECURITY WARNING: don't run with debug turned on in production!
    # DEBUG = True
    DEBUG = bool( os.environ.get('DJANGO_DEBUG', True) )
```

로 바꿔줍니다.<br><br>



- SECRET_KEY

    CRSF 보안 등을 위해 사용되는 큰 숫자의 랜덤값입니다. 
    이게 털리면 서버가 비밀리에 간직해야되는 모든 정보의 암호화가 무용지물이 된다고 생각해도 좋습니다.

```
    # settings.py
    # SECURITY WARNING: keep the secret key used in production secret!
    SECRET_KEY = '기존 키'
```

를

```
    # settings.py
    # SECURITY WARNING: keep the secret key used in production secret!
    # SECRET_KEY = '(p7p0#$^HHFGHHdggtherververvewkjbnp3290vdvfd)w'
    # SECURITY WARNING: keep the secret key used in production secret!
    import os
    SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY', '기존 키')
```
로 바꿔줍니다.<br><br>



- ALLOWED_HOSTS = ['*'] 로 수정해줍니다.


<br><br><br>

## Heroku 설치하기

1. [https://www.heroku.com](https://www.heroku.com)에 접속해서 회원가입을 해줍니다. 
절차는 어렵지 않으니 생략합니다
<br><p align="center"><img src="/dp_img/1002.PNG" width = "500px"></p><br>

2. [https://devcenter.heroku.com/articles/getting-started-with-python](https://devcenter.heroku.com/articles/getting-started-with-python)

그리고 위 주소에서 배포를 연습할 수 있습니다.<br>

먼저 setup까지만 해줍니다.<br>

본인에게 해당되는 운영체제를 선택해서 설치해줍니다.<br>

<br><p align="center"><img src="/dp_img/1003.png" width = "800px"></p><br>


3. .gitignore 파일 만들기

manage.py가 있는 경로에 .gitignore 파일을 만들어 줍니다.<br>

```
# .gitignore 
### Django ### 
*.log *.pot 
*.pyc 
__pycache__/ 
local_settings.py 
db.sqlite3 
edia
```

4. githup repo에 project 업로드하기

다시 한 번, 새 repo를 만들어 project를 커밋 푸쉬해줍니다.

## Heroku에 올리기 위한 작업
<br><br>

### 1. Procfile 작성
manage.py가 있는 경로애 `Procfile`이라는 파일을 만들어 다음 내용을 작성해 줍니다.

```
    web: gunicorn <프로젝트 명>.wsgi --log-file -
```

<br><p align="center"><img src="/dp_img/1004.PNG" width = "800px"></p><br>
<br><br>

### 2. Gunicorn 설치하기

이것도 설치해야합니다.

```
    $ pip install gunicorn
```
<br><br>

### 3. Database 설정하기

앞서 개발에서는 sqlite가 기본으로 설정되어 있지만 heroku에서는 사용할 수 없습니다. <br>
heroku가 postgresql를 기본 옵션으로 하고 있기 때문이라고만 생각하고 넘어갑시다.<br>

이를 위해서도 새로운걸 설치해줘야 합니다.<br>

```
    $ pip install dj-database-url
    $ pip install psycopg2-binary
```

그리고 settings.py 맨하단에 아래내용을 붙여넣기 해봅시다.<br>

```python
    # settings.py
    # Heroku: Update database configuration from $DATABASE_URL.
    import dj_database_url

    db_from_env = dj_database_url.config(conn_max_age=500)
    DATABASES['default'].update(db_from_env)
```
<br><br>

### 4. static file 을 위한 설정하기

```
    $ pip install whitenoise
```
먼저 whitenoise를 설치해줍니다. <br>
그리고 setting.py의 MIDDLEWEAR 설정에서 <br>
`SecurityMiddleware` 위에 `WhiteNoiseMiddleware`를 추가합니다.<br>

```
    MIDDLEWARE = [
        'whitenoise.middleware.WhiteNoiseMiddleware',
        'django.middleware.security.SecurityMiddleware',
        'django.contrib.sessions.middleware.SessionMiddleware',
        'django.middleware.common.CommonMiddleware',
        'django.middleware.csrf.CsrfViewMiddleware',
        'django.contrib.auth.middleware.AuthenticationMiddleware',
        'django.contrib.messages.middleware.MessageMiddleware',
        'django.middleware.clickjacking.XFrameOptionsMiddleware',
    ]
```
<br><br>

### 5. 파이썬 관련 라이브러리 설치

```
    pip freeze > requirements.txt
```

아까 했던, 관련 목록을 리스트업 해주는 과정입니다. <br>
이렇게 리스트업해두면 이번에도 heroku가 알아서 설치합니다. 
<br><br>

### 6. heroku에게 python 버전 알려주기

우리가 만든 프로젝트의 python 버전을 heroku에게 알려줘야 충돌을 줄일수 있습니다.<br>
manage.py가 있는 경로에<br>
runtime.txt 라는 파일을 만들고 안에 python 버전을 적어줍시다.<br>

<br><p align="center"><img src="/dp_img/1005.PNG" width = "800px"></p><br>

```
    python-3.7.3
```

<br><p align="center"><img src="/dp_img/1006.PNG" width = "800px"></p><br>


지금까지의 작업을 github repo에 커밋, 푸쉬 해줍니다.


## Heroku 만들기

가상환경을 비활성화 시킨 다음,<br>
터미널에서 아래의 명령들을 쳐가며 따라해 봅시다.<br>
명령어가 실행되지 않으면 재부팅을 한 번 해줍니다.<br>

```
    $ heroku login
```

<br><p align="center"><img src="/dp_img/1007.PNG" width = "800px"></p><br>
<br><p align="center"><img src="/dp_img/1008.PNG" width = "800px"></p><br>

로그인을 해준다.

<br><p align="center"><img src="/dp_img/1009.PNG" width = "800px"></p><br>
<br><p align="center"><img src="/dp_img/1010.PNG" width = "800px"></p><br>

```
    $ heroku create
```
<br><p align="center"><img src="/dp_img/1011.PNG" width = "800px"></p><br>

- heroku 에 push하기전에 위 수정 사항을 전부 commit 했는지 꼭 확인하시기 바랍니다.

```
    $ git push heroku master
```

```
    $ heroku run python manage.py migrate
```

migrate까지 해줬으면 끝입니다. <br>
아래 명령어를 통해 배포된 페이지를 열어보세요.<br>

```
heroku open
```

<br><p align="center"><img src="/dp_img/1012.PNG" width = "800px"></p><br>


# 끝!


만약, db에 필요한 정보들이 들어있는 프로텍트를 배포한 경우,<br>
db가 달라졌기때문에 본인이 만들었던 데이터들도 모두 없을 겁니다. (local에 있는 sqlite안에 있습니다.) 

heroku에 있는 db에 다가는 새로 넣어줘야합니다.<br>

```
heroku run python manage.py createsuperuser
```


## 목차 1008
- [Elastic Beanstalk](/eb.md)
- [Heroku](/heroku.md)
