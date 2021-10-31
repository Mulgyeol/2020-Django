## \[Django\] Blog Project - 게시물 / 댓글 CRUD (1) 기본세팅/모델다루기 

오늘은 지난 세션에 쭉 이어서

블로그 프로젝트의 POST CRUD를 완성하기.

부트스트랩을 적용해 그럴싸한 페이지 만들기.

댓글 CRUD 기능 구현하기.

등을 해볼 예정입니다!

꼭 처음부터 새로 생성하여 해보시길 바랍니다.

<br><br>

#### **1\. 기본 세팅**

```
$ python -m venv 'myvenv' #가상환경 생성

$ source myvenv/scripts/activate # 가상환경 실행

$ pip install django # django 설치

$ pip install pillow # pillow 설치

$ django-admin startproject 'myblogproject' #프로젝트 생성

# 이때 manage.py가 위치한 프로젝트 안(BASE_DIR)으로 들어간다.

$ python manage.py startapp 'myblog' # app 생성

$ python manage.py runserver # http://127.0.0.1:8000/ #확인하기
```

<br><p align="center"><img src="/img2/1.png" width = "800px"></p><br>
<br><p align="center"><img src="/img2/2.png" width = "800px"></p><br>
<br><p align="center"><img src="/img2/3.png" width = "800px"></p><br>
<br><p align="center"><img src="/img2/4.png" width = "800px"></p><br>

<br><br>

#### **2\. myproject/settings.py에 'myblog'앱 연결**

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'myblog',
]
```

<br><p align="center"><img src="/img2/5.png" width = "800px"></p><br>
---

<br><br>

# 1:N 관계 설정 (Database relation)

#### **3\. myblog/models.py**

```
from django.db import models

# Create your models here.
# 이미 배운 내용의 설명은 생략합니다.

class Post(models.Model):
    title = models.CharField(max_length=200)
    image = models.ImageField(upload_to='images/', null=True, blank=True)
    pub_date = models.DateTimeField('date published')
    body = models.TextField()

    def __str__(self):
        return self.title

    def summary(self):
        return self.body[:20]


class Comment(models.Model):
    post = models.ForeignKey('myblog.Post', on_delete = models.CASCADE)
    content = models.TextField()

    def __str__(self):
        return self.content

```

> 앞서, 게시글을 작성하는 코드를 작성해보았습니다.  
> 그리고 저희는 여기서 더 나아가, 각 게시글에 댓글을 쓰는 코드(CRUD)를 작성해 보도록 할건데요,  
> 이때 한 게시글에는 여러개의 댓글이 작성 될 수 있으므로, 게시글과 댓글은 1:N 구조를 띈다고 할 수 있습니다.

<br><br>

### models.py 내 comment 클래스 정의

위의 models.py에서 Comment 클래스가 새로 정의되었습니다.

코드를 천천히 살펴보도록 할게요.

-   post는models.ForeignKey()객체가 저장되어 있는 변수입니다.  
    위의 예시의 경우, 댓글(Comment)이 새로 생성될때,  
    어떠한 게시글(Post)인지를 나타내는 대한 변수 명입니다.
-   models.ForeignKey()는 외래키를 설정하는 함수입니다. 게시물과 댓글을 연결하는 설정을 해주는 과정입니다.
    -   첫 번째 인자: 외래키가 연결되는 테이블을 입력합니다. 여기서는Post모델 클래스를 의미합니다.  
        다시 한 번, 외래키가 연결된다 함은 작성된 댓글이 해당 게시물과 연결된다고 생각하시면 편합니다.
    -   두 번째 인자: ForeignKeyField가 바라보는 값(연결된 게시물)이 삭제 될 때,  
        어떻게 처리할건지를 옵션으로 정합니다.
        -   CASCADE: 부모(게시물)가 삭제 되면, 자기 자신(댓글)도 삭제.  
            게시글에 대한 모델 클래스인Post에 따라 생성된 게시글이 삭제되면,  
            댓글에 대한 모델 클래스인 Comment에 따라 생성된 댓글도 삭제.
        -   PROTECT: 댓글이 존재하면, 게시물 삭제 불가능(ProtectedError발생시킴)
        -   SET\_NULL: 게시물이 삭제되면, 댓글의 게시물 정보를 NULL로 변경.
-   image의 세 번째 인자 : null=True는 필드 값이 NULL(정보없음)으로 저장되는 것을 허용합니다.
-   image의 네 번째 인자 : blank = True 는 폼(입력 양식)에서 빈 채로 저장되는 것을 허용합니다.

<br><br>

자, 정리하면 게시물과 1:N 구조로 연결된 댓글을 정의해 준 것이고,

이 댓글은 게시물이 삭제되면 따라서 삭제됩니다!

<br><br>

**4.  DB 등록**

```
$ python manage.py makemigrations
$ python manage.py migrate
```

터미널에 모델의 내용을 DB에 적용하는 명령어를 입력합니다.

<br><br>

**5\. Admin 만들기**

```
$ python manage.py createsuperuser
```

-   이름
-   이메일
-   비밀번호 (안보이는게 정상)
-   비밀번호 확인 (안보이는게 정상)

<br><br>

**6\. myblog/admin.py 에 model 등록**

```
from django.contrib import admin
from .models import Post, Comment

# Register your models here.

admin.site.register(Post)
admin.site.register(Comment)
```

<br><br>

**7\. myblog/static 폴더 생성**

<br><br>

**8\. myblogproject/settings.py**

```
import os


#생략


#static 파일들이 들어있는 경로
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'myblog', 'static')
]

#파일을 모아줄 위치
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
STATIC_URL = '/static/'


MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
MEDIA_URL = '/media/'
```

<br><br>

**10\. static 파일들을 한곳에 모아주는 명령어 입력**

```
$ python manage.py collectstatic
```

<br><br>

**11\. 관리자 페이지 확인**

이제 관리자 페이지에 로그인을 해서 저희가 만든 model, Post와 Commnet를 확인해 보겠습니다.

<br><p align="center"><img src="/img2/6.png" width = "800px"></p><br>

<br><p align="center"><img src="/img2/7.png" width = "800px"></p><br>

아직 게시물이 없기 때문에 Post 부분에 선택사항이 존재하지 않습니다.

일단, Post를 하나 추가해주겠습니다.

<br><p align="center"><img src="/img2/8.png" width = "800px"></p><br>

그리고 나서 Add comment를 해주면 Post에서 방금 작성한 게시물을 선택할 수 있습니다.

<br><p align="center"><img src="/img2/9.png" width = "800px"></p><br>

저장버튼을 눌러주시면

이제 하나의 게시물과 그 게시물에 댓글 하나가 생성된 상태입니다.

<br><br>

[다음 : Blog Project - 게시물 / 댓글 CRUD (2) 복습](/6.md)


## 목차 0825
- [Blog Project - 게시물 / 댓글 CRUD (1) 기본세팅/모델다루기 ](/5.md)
- [Blog Project - 게시물 / 댓글 CRUD (2) 복습](/6.md)
- [Blog Project - 게시물 / 댓글 CRUD (3) 게시물 Create](/7.md)
- [Blog Project - 게시물 / 댓글 CRUD (4) 게시물 UD](/8.md)
- [Blog Project - 게시물 / 댓글 CRUD (5) 댓글 CRUD](/9.md)