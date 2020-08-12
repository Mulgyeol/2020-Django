# Blog project CR-1 (Model 다루기)

이번 세션에서는 Blog 프로젝트를 진행하면서

CRUD의 Create Read를 구현해볼 예정입니다.<br><br>

1\. 기본 세팅

```
$ python -m venv 'myvenv' #가상환경 생성

$ source myvenv/scripts/activate # 가상환경 실행

$ pip install django # django 설치

$ django-admin startproject 'blogproject' #프로젝트 생성

# 이때 manage.py가 위치한 프로젝트 안(BASE_DIR)으로 들어간다.

$ python manage.py startapp 'blog' # app 생성

$ python manage.py runserver # http://127.0.0.1:8000/ #확인하기
```
<br><br>
2\. settings.py에서 blog 앱 추가

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',
]
```
<br><br>
3\. models.py 작성하기

그동안 배운 MTV 패턴에서 M에 해당하는 model을 배울 차례입니다.

model은 화면구성을 채우는 내용물, 데이터베이스를 다루는 부분입니다.

DB에 저장된 내용은 사용자의 요청에 따라 view를 통해 가공되어 template에 보여지게 됩니다.

그러면 model을 만들어 보겠습니다.

모델은 일종의 엑셀표와 비슷합니다.

블로그에 게시글을 올리기 위해서는 제목, 내용이 필요합니다.

추가적으로 이번 프로젝트에서는 작성일자까지 다뤄 보도록 하겠습니다.
<br><br>

-   제목
-   내용
-   작성일자

<br><br>
blog 폴더 내에서 models.py를 열어

아래와 같은 코드를 작성해 줍니다.

```
from django.db import models

# Create your models here.

class Post(models.Model):
    title = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')
    body = models.TextField()
```

<br><br>
<p align="center"><img src="/img/1.PNG" width = "800px"></p><br>

처음 다뤄보는 내용이라 어려우시겠지만,

천천히 하나씩 살펴보도록 하겠습니다.

class 내부에 어떤 변수에 어떤 타입의 데이터를 받을 지 정의하는 과정입니다.

-   class Post(models.Model): 모델을 정의하는 코드입니다. 
-   Post는 모델의 이름입니다. (특수문자와 공백 제외한다면) 다른 이름을 붙일 수도 있습니다. 항상 클래스 이름의 첫 글자는 대문자로 써야 합니다.
-   models은 Post가 장고 모델임을 의미합니다. 이 코드로 인해 장고는 Post가 데이터베이스에 저장되어야 한다고 알게 됩니다.
<br><br>

title, pub\_date, body는 모델의 속성이됩니다.

속성은 우리가 게시글을 올리기 위해 필요한 내용들을 의미합니다.

그 뒤의 내용들은 이 속성들이 어떤 모양으로 형성될지 알려주는 것입니다.
<br><br>

-   title : 모델 안에 있는 케릭터 필드 즉, 문자를 title이라는 변수로 받겠다. (최대길이 200)
-   pub\_date : 날짜와 시간 데이터를 받겠다. (published date)
-   body : 케릭터필드보다 더 긴 문자열 데이터를 받겠다.
<br><br>

자세히 보면 형식이 models.xxxFiled(...) 로 이루어진 것 보이시나요?

여러분이 다루고자 하는 데이터 형식영역을 검색해서 알아낸다면 추가적으로 다룰 수 있답니다.

<br><br>
4.  DB 등록

터미널에 모델의 내용을 DB에 적용하는 명령어를 입력합니다.

```
$ python manage.py makemigrations
$ python manage.py migrate
```

<p align="center"><img src="/img/2.PNG" width = "800px"></p><br><br>

5\. Admin 만들기

```
$ python manage.py createsuperuser
```

<p align="center"><img src="/img/3.PNG" width = "800px"></p><br>

-   이름
-   이메일
-   비밀번호 (안보이는게 정상)
-   비밀번호 확인 (안보이는게 정상)

이제 관리자 페이지에 로그인을 해서 저희가 만든 model, Post를 확인해 보겠습니다.
<br><br><br>

6\. admin page 확인

<p align="center"><img src="/img/4.PNG" width = "800px"></p><br>

Post가 보이지 않습니다.

django에게는 우리가 model을 만들었다는 것을 migrate를 통해 알렸지만,

admin에게는 알려주지 않았기 때문입니다.

<br><br>

7\. admin.py

blog 폴더 안으로 들어가서 admin.py를 열고 아래와 같이 작성해 줍니다.

```
from django.contrib import admin
from .models import Post

# Register your models here.

admin.site.register(Post)
```

-   from .models import Post : 같은 폴더에 위치해있는 models라는 파일에 Post를 가져옵니다.
-   admin.site.register : admin 사이트에 Post라는 클래스를 등록합니다..

<br><br>
8\. admin page 확인

저장을 하고 admin페이지를 새로고침해보면

<p align="center"><img src="/img/5.PNG" width = "800px"></p><br>

위와 같이 저희가 만든 Blog앱과 Post가 보입니다.
<br><br><br><br>

9\. Add Post

오른쪽의 Add 버튼을 눌러서 post를 추가해 봅시다.

<p align="center"><img src="/img/6.PNG" width = "800px"></p><br>


저희가 model에서 작성했던 Title, Date, Body를 입력하고 저장할 수 있습니다.
<br><br><br><br>
10\. 확인

<p align="center"><img src="/img/7.PNG" width = "800px"></p>


데이터가 하나 추가된 것을 확인할 수 있습니다.

그런데 blog 게시물이라면 목록에는 title이 보이는 게 좋겠죠?
<br><br><br><br>
11\. title을 list에서 보여주기 models.py

models.py 아래에 코드를 작성해 줍니다.

```
from django.db import models

# Create your models here.

class Post(models.Model):
    title = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')
    body = models.TextField()
    
# 아래의 코드 추가
    def __str__(self):
        return self.title
```
<br><br><br><br>
12\. 재확인

그리고 확인해 보면 다음과 같이 변한 것을 확인할 수 있습니다.

<p align="center"><img src="/img/8.PNG" width = "800px"></p><br><br>

## 목차 
- [Blog project CR-1 (Model 다루기)](/1.md)
- [Blog project CR-2 (Model에서 데이터 가져오기)](/2.md)
- [Blog project CR-3 (Detail 페이지)](/3.md)
- [Blog project CR-4 (Create))](/4.md)
