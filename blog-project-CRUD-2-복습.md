## \[Django\] Blog Project - 게시물 / 댓글 CRUD - (2) 복습

#### **1\. myblog/templates/home.html**

home.html 파일을 하나 만들어 줍니다.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1>Home.html</h1>
</body>
</html>
```

<br><br>


#### **2\. myblog/views.py에서 home 함수를 작성해 줍니다.**

```
def home(request):
    return render(request, 'home.html')
```

<br><br>


#### **3\. url.py**

이번엔 전에 배웠던 것을 복습할 겸, url을 App에 분리해서 다루도록 하겠습니다.
<br><br>


 - myblog App 내부에 urls.py 파일 만들기

```
$ touch myblog/urls.py
```

<br><br>

 - myblog/urls.py에 아래 내용 작성

```
#기본 path 함수를 사용하기 위해 똑같이 해당 기능을 import
from django.urls import path

# 프로젝트에서는 어떤 app의 views인지 알기 위해 blog.views 앱부터 명시했지만
# 여기에서는 동일한 폴더 내에 잇는 views라는 것을 명시하기 위해 
# from .(현재폴더) import views 로 사용한다.

from . import views

urlpatterns = [
    # Index
    ## 프로젝트의 urls 와의 차이점 - App 내의 view 인지 명시하는 부분이 빠짐
    path('', views.home, name='home'),
]
```

<br><br>
 - myblogproject/urls.py 정리

```
"""myblogproject URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/3.0/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
from django.contrib import admin
from django.urls import path, include #urls 분리에 사용되는 include 추가 import
from django.conf.urls import url #추가 작성

urlpatterns = [
    path('admin/', admin.site.urls),
    url('', include('myblog.urls')),
]
```

<br><br>

#### **4\. python manage.py runserver 확인**

<br><p align="center"><img src="/img/CRUD/10.png" width = "800px"></p><br>

---

<br><br>

#### **5\. home.html에 부트스트랩 적용하기, 복습**

이번엔 저번 세션에서 다뤄본 bootstrap 적용법을 다시 한 번 알아보겠습니다.

<br><br>

**1) 살펴보기**

-   부트스트랩 페이지로 들어가 봅니다다. ([https://getbootstrap.com/](https://getbootstrap.com/))
-   Examples에 들어가면 우리가 쓸 수 있는 웹페이지 디자인이 나옵니다.
-   지난시간에 다뤘던 Album을 블로그로 한 번 만들어 보겠습니다.
-   클릭해서 들어간 후에 우클릭-> 페이지 소스보기 한 후 코드를 그대로 복사합니다.
-   그대로 home.html에 붙여넣기 합니다.

<br><p align="center"><img src="/img/CRUD/11.png" width = "800px"></p><br>

<br><br>

**2) CDN,** ****온라인 상의 디자인요소를 가져다 쓰는 방법****

온라인 상의 부트스트랩 디자인 요소를 어떻게 내 컴퓨터 즉, 로컬로 가져다 쓸 수 있을까요?

부트스트랩 메인 페이지에 있는 BootstrapCDN 부분을 확인해 봅시다.

<br><p align="center"><img src="/img/CRUD/12.png" width = "800px"></p><br>

> ****Contents Delivery Network(콘텐츠 전송 네트워크)  
> ****
> 
> -   지리,물리적으로 떨어져 있는 사용자에게 컨텐츠를 더 빠르게 제공할 수 있는 기술
> -   느린 응답속도 / 다운로딩 타임 을 극복하기 위한 기술

_When you only need to include Bootstrap's compiled CSS or JS, you can use BootstrapCDN._

오른쪽의 코드를 home.html 안에 넣어주면 바로 CSS와 JS가 로드 됩니다.

head태그 안에 오른쪽의 태그를 집어넣고 확인해 봅시다.

<br><p align="center"><img src="/img/CRUD/13.png" width = "800px"></p><br>

본인의 블로그를 꾸민다고 생각하고, home.html을 수정해줍니다.

<br><p align="center"><img src="/img/CRUD/14.png" width = "800px"></p><br>

```
{% load static %}
<!doctype html>
<html lang="en">

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="description" content="">
    <meta name="author" content="Mark Otto, Jacob Thornton, and Bootstrap contributors">
    <meta name="generator" content="Jekyll v4.1.1">
    <title>Mulgyeol's Blog</title>

    <link rel="canonical" href="https://getbootstrap.com/docs/4.5/examples/album/">

    <!-- Bootstrap core CSS -->
    <link href="/docs/4.5/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z" crossorigin="anonymous">
    

    
    <!-- Favicons -->
    <link rel="apple-touch-icon" href="/docs/4.5/assets/img/favicons/apple-touch-icon.png" sizes="180x180">
    <link rel="icon" href="/docs/4.5/assets/img/favicons/favicon-32x32.png" sizes="32x32" type="image/png">
    <link rel="icon" href="/docs/4.5/assets/img/favicons/favicon-16x16.png" sizes="16x16" type="image/png">
    <link rel="manifest" href="/docs/4.5/assets/img/favicons/manifest.json">
    <link rel="mask-icon" href="/docs/4.5/assets/img/favicons/safari-pinned-tab.svg" color="#563d7c">
    <link rel="icon" href="/docs/4.5/assets/img/favicons/favicon.ico">
    <meta name="msapplication-config" content="/docs/4.5/assets/img/favicons/browserconfig.xml">
    <meta name="theme-color" content="#563d7c">

    <!-- CSS only -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z" crossorigin="anonymous">

    <!-- JS, Popper.js, and jQuery -->
    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.1/dist/umd/popper.min.js" integrity="sha384-9/reFTGAW83EW2RDu2S0VKaIzap3H66lZH81PoYlFhbGU+6BZp6G7niu735Sk7lN" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js" integrity="sha384-B4gt1jrGC7Jh4AgTPSdUtOBvfO8shuf57BaghqFfPlYxofvL8/KUEfYiJOMMV+rV" crossorigin="anonymous"></script>


    <style>
        .bd-placeholder-img {
            font-size: 1.125rem;
            text-anchor: middle;
            -webkit-user-select: none;
            -moz-user-select: none;
            -ms-user-select: none;
            user-select: none;
        }

        @media (min-width: 768px) {
            .bd-placeholder-img-lg {
                font-size: 3.5rem;
            }
        }
    </style>
    <!-- Custom styles for this template -->
    <link href="album.css" rel="stylesheet">
</head>

<body>
    <header>
        <div class="collapse bg-dark" id="navbarHeader">
            <div class="container">
                <div class="row">
                    <div class="col-sm-8 col-md-7 py-4">
                        <h4 class="text-white">About</h4>
                        <p class="text-muted"> Django Blogproject 페이지입니다.
                            <br> written by Mulgyeol Jung
                        </p>
                    </div>
                    <div class="col-sm-4 offset-md-1 py-4">
                        <h4 class="text-white">Contact</h4>
                        <ul class="list-unstyled">
                            <li>
                                <a href="https://github.com/mulgyeol" class="text-white">Github</a>
                            </li>
                            <li>
                                <a href="#" class="text-white">Blog</a>
                            </li>
                            <li>
                                <a href="#" class="text-white">mulgyeoljung@gmail.com</a>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
        <div class="navbar navbar-dark bg-dark shadow-sm">
            <div class="container d-flex justify-content-between">
                <a href="#" class="navbar-brand d-flex align-items-center">
                    <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="none" stroke="currentColor"
                        stroke-linecap="round" stroke-linejoin="round" stroke-width="2" aria-hidden="true" class="mr-2"
                        viewBox="0 0 24 24" focusable="false">
                        <path d="M23 19a2 2 0 0 1-2 2H3a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h4l2-3h6l2 3h4a2 2 0 0 1 2 2z" />
                        <circle cx="12" cy="13" r="4" /></svg>
                    <strong>Mulgyeol's Blog</strong>
                </a>
                <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarHeader"
                    aria-controls="navbarHeader" aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
                </button>
            </div>
        </div>
    </header>

    <main role="main">
        <section class="jumbotron text-center">
            <div class="container">
                <h1>Mulgyeol's Blog</h1>
                <p class="lead text-muted">This is myblogproject</p>
                <p>
                    <br>
                    <a href="#" class="btn btn-primary my-2">Add Post</a>
                </p>
            </div>
        </section>
        
        <div class="album py-5 bg-light">
            <div class="container">
        
                <div class="row">
                    <div class="col-md-4">
                        <div class="card mb-4 shadow-sm">
                          <svg class="bd-placeholder-img card-img-top" width="100%" height="225" xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMidYMid slice" focusable="false" role="img" aria-label="Placeholder: Thumbnail"><title>Placeholder</title><rect width="100%" height="100%" fill="#55595c"/><text x="50%" y="50%" fill="#eceeef" dy=".3em">Thumbnail</text></svg>
                          <div class="card-body">
                            <p class="card-text">This is a wider card with supporting text below as a natural lead-in to additional content. This content is a little bit longer.</p>
                            <div class="d-flex justify-content-between align-items-center">
                              <div class="btn-group">
                                <button type="button" class="btn btn-sm btn-outline-secondary">View</button>
                                <button type="button" class="btn btn-sm btn-outline-secondary">Edit</button>
                              </div>
                              <small class="text-muted">9 mins</small>
                            </div>
                          </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </main>


    <footer class="text-muted">
        <div class="container">
            <p class="float-right">
                <br>
                2020.08.25
            </p>
        </div>
    </footer>
    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js"
        integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj"
        crossorigin="anonymous"></script>
    <script>window.jQuery || document.write('<script src="/docs/4.5/assets/js/vendor/jquery.slim.min.js"><\/script>')</script>
    <script src="/docs/4.5/dist/js/bootstrap.bundle.min.js"
        integrity="sha384-LtrjvnR4Twt/qOuYxE721u19sVFLVSA4hf/rRt6PrZTmiPltdZcI7q7PXQBYTKyf"
        crossorigin="anonymous"></script>
</body>
</html>

```

<br><br>

#### **6\. 템플릿 상속**

지난 세션에서 배운 템플릿 상속을 이용해서 더욱 편하게 html을 작성해보겠습니다.

<br><br>

\- myblogproject/templates 폴더 만들고 base.html 생성.

<br><br>

\- myblogproject/settings.py 에 'DIRS' 부분 추가

```
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': ['myblogproject/templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

<br><br>

\- base.html에 home.html의 `<body>` 태그 내부의 `<main>` 태그를 제외한 부분만 작성

```
{% load static %}
<!doctype html>
<html lang="en">

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="description" content="">
    <meta name="author" content="Mark Otto, Jacob Thornton, and Bootstrap contributors">
    <meta name="generator" content="Jekyll v4.1.1">
    <title>Mulgyeol's Blog</title>

    <link rel="canonical" href="https://getbootstrap.com/docs/4.5/examples/album/">

    <!-- Bootstrap core CSS -->
    <link href="/docs/4.5/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z" crossorigin="anonymous">
    

    
    <!-- Favicons -->
    <link rel="apple-touch-icon" href="/docs/4.5/assets/img/favicons/apple-touch-icon.png" sizes="180x180">
    <link rel="icon" href="/docs/4.5/assets/img/favicons/favicon-32x32.png" sizes="32x32" type="image/png">
    <link rel="icon" href="/docs/4.5/assets/img/favicons/favicon-16x16.png" sizes="16x16" type="image/png">
    <link rel="manifest" href="/docs/4.5/assets/img/favicons/manifest.json">
    <link rel="mask-icon" href="/docs/4.5/assets/img/favicons/safari-pinned-tab.svg" color="#563d7c">
    <link rel="icon" href="/docs/4.5/assets/img/favicons/favicon.ico">
    <meta name="msapplication-config" content="/docs/4.5/assets/img/favicons/browserconfig.xml">
    <meta name="theme-color" content="#563d7c">

    <!-- CSS only -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z" crossorigin="anonymous">

    <!-- JS, Popper.js, and jQuery -->
    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.1/dist/umd/popper.min.js" integrity="sha384-9/reFTGAW83EW2RDu2S0VKaIzap3H66lZH81PoYlFhbGU+6BZp6G7niu735Sk7lN" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js" integrity="sha384-B4gt1jrGC7Jh4AgTPSdUtOBvfO8shuf57BaghqFfPlYxofvL8/KUEfYiJOMMV+rV" crossorigin="anonymous"></script>


    <style>
        .bd-placeholder-img {
            font-size: 1.125rem;
            text-anchor: middle;
            -webkit-user-select: none;
            -moz-user-select: none;
            -ms-user-select: none;
            user-select: none;
        }

        @media (min-width: 768px) {
            .bd-placeholder-img-lg {
                font-size: 3.5rem;
            }
        }
    </style>
    <!-- Custom styles for this template -->
    <link href="album.css" rel="stylesheet">
</head>

<body>
    <header>
        <div class="collapse bg-dark" id="navbarHeader">
            <div class="container">
                <div class="row">
                    <div class="col-sm-8 col-md-7 py-4">
                        <h4 class="text-white">About</h4>
                        <p class="text-muted"> Django Blogproject 페이지입니다.
                            <br> written by Mulgyeol Jung
                        </p>
                    </div>
                    <div class="col-sm-4 offset-md-1 py-4">
                        <h4 class="text-white">Contact</h4>
                        <ul class="list-unstyled">
                            <li>
                                <a href="https://github.com/mulgyeol" class="text-white">Github</a>
                            </li>
                            <li>
                                <a href="#" class="text-white">Blog</a>
                            </li>
                            <li>
                                <a href="#" class="text-white">mulgyeoljung@gmail.com</a>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
        <div class="navbar navbar-dark bg-dark shadow-sm">
            <div class="container d-flex justify-content-between">
                <a href="{% url 'home' %}" class="navbar-brand d-flex align-items-center">
                    <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="none" stroke="currentColor"
                        stroke-linecap="round" stroke-linejoin="round" stroke-width="2" aria-hidden="true" class="mr-2"
                        viewBox="0 0 24 24" focusable="false">
                        <path d="M23 19a2 2 0 0 1-2 2H3a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h4l2-3h6l2 3h4a2 2 0 0 1 2 2z" />
                        <circle cx="12" cy="13" r="4" /></svg>
                    <strong>Mulgyeol's Blog</strong>
                </a>
                <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarHeader"
                    aria-controls="navbarHeader" aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
                </button>
            </div>
        </div>
    </header>

    <main role="main">
    {% block content %}
    {% endblock %}
    </main>


    <footer class="text-muted">
        <div class="container">
            <p class="float-right">
                <br>
                2020.08.25
            </p>
        </div>
    </footer>
    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js"
        integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj"
        crossorigin="anonymous"></script>
    <script>window.jQuery || document.write('<script src="/docs/4.5/assets/js/vendor/jquery.slim.min.js"><\/script>')</script>
    <script src="/docs/4.5/dist/js/bootstrap.bundle.min.js"
        integrity="sha384-LtrjvnR4Twt/qOuYxE721u19sVFLVSA4hf/rRt6PrZTmiPltdZcI7q7PXQBYTKyf"
        crossorigin="anonymous"></script>
</body>
</html>

```

<br><br>

\- home.html에는 <`main태그>` 내부의 내용만 입력

```
{% extends 'base.html' %}


{% block content %}
    <main role="main">
        <section class="jumbotron text-center">
            <div class="container">
                <h1>Mulgyeol's Blog</h1>
                <p class="lead text-muted">This is myblogproject</p>
                <p>
                    <br>
                    <a href="#" class="btn btn-primary my-2">Add Post</a>
                </p>
            </div>
        </section>
        
        <div class="album py-5 bg-light">
            <div class="container">
        
                <div class="row">
                    <div class="col-md-4">
                        <div class="card mb-4 shadow-sm">
                            <svg class="bd-placeholder-img card-img-top" width="100%" height="225" 
                            xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMidYMid slice" 
                            focusable="false" role="img" aria-label="Placeholder: Thumbnail">
                            	<title>Placeholder</title>
                            	<rect width="100%" height="100%" fill="#55595c"/>
                            	<text x="50%" y="50%" fill="#eceeef" dy=".3em">Thumbnail</text>
                            </svg>
                            <div class="card-body">
                                <p class="card-text">This is a wider card with supporting text below as a natural lead-in to additional content. This content is a little bit longer.</p>
                                <div class="d-flex justify-content-between align-items-center">
                                <div class="btn-group">
                                    <button type="button" class="btn btn-sm btn-outline-secondary">View</button>
                                    <button type="button" class="btn btn-sm btn-outline-secondary">Edit</button>
                                </div>
                                <small class="text-muted">9 mins</small>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </main>

{% endblock%}

```

<br><br>

\- 결과 확인

<br><p align="center"><img src="/img/CRUD/15.png" width = "800px"></p><br>

<br><br>

#### **7\. Home.html**

반복되고, 배운지 얼마안된 내용들은 설명을 생략합니다!

혹시 기억이 안나시는 분들은 지난세션 자료를 참고하시거나,

질문해주시면 바로 설명해드리겠습니다!

<br><br>

\- views.py 수정

```
from .models import Post

def home(request):
    posts = Post.objects
    return render(request, 'home.html', {'posts':posts})
```

<br><br>

\-urls.py 수정

```
from django.urls import path
from django.conf import settings
from django.conf.urls.static import static
from . import views

urlpatterns = [
    path('', views.home, name='home'),
]+ static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

<br><br>

\- home.html 수정

반복문을 이용해 post의 데이터를 출력해 봅니다!

```
{% extends 'base.html' %}


{% block content %}


<section class="jumbotron text-center">
    <div class="container">
        <h1>Mulgyeol's Blog</h1>
        <p class="lead text-muted">This is myblogproject</p>
        <p>
            <br>
            <a href="#" class="btn btn-primary my-2">Add Post</a>
        </p>
    </div>
</section>

<div class="album py-5 bg-light">
    <div class="container">

        <div class="row">
            {% for post in posts.all %}
            <div class="col-md-4">
                <div class="card mb-4 shadow-sm">
                    {% if post.image != '' %} <!-- 이미지가 비어있지 않으면-->
                    <img class="card-img-top" src="{{ post.image.url }}" height= "225" alt=" Card image cap">    

                    {% else %}<!-- 이미지가 비어있다면-->
                    <svg class="bd-placeholder-img card-img-top" width="100%" height="225" xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMidYMid slice" focusable="false" role="img" aria-label="Placeholder: Thumbnail"><rect width="100%" height="100%" fill="#55595c"/><text x="50%" y="50%" fill="#eceeef" dy=".3em">No image</text></svg>
                    
                    {% endif %}

                    <div class="card-body">
                        <h4>{{ post.title }}</h4>
                        <p class="card-text">{{ post.summary }}</p>
                        <div class="d-flex justify-content-between align-items-center">
                            <div class="btn-group">
                                <button type="button" class="btn btn-sm btn-outline-secondary">View</button>
                                <button type="button" class="btn btn-sm btn-outline-secondary">Edit</button>
                            </div>
                            <small class="text-muted">{{ post.pub_date }}</small>
                        </div>
                    </div>
                </div>
            </div>
            {% endfor %}
        </div>
    </div>
</div>


{% endblock%}
```

<br><br>

\- 확인

<br><p align="center"><img src="/img/CRUD/16.png" width = "800px"></p><br>

<br><br>

#### **8\. faker로 랜덤 게시물 만들기**

이번엔 faker를 이용하여 게시물을 쉽게 생성해보도록 하겠습니다.

<br><br>

\- 패키지 설치

```
$ pip install faker
```

<br><p align="center"><img src="/img/CRUD/17.png" width = "800px"></p><br>

<br><br>

\- views.py 수정

지난 시간에 작성해본 코드들이 views.py에 들어왔습니다.

버튼을 누르면 게시물이 10개 생성되는 기능을 구현해 보겠습니다.

```
from django.shortcuts import render, redirect #redirect 추가
from .models import Post
from faker import Faker #faker 추가
from django.utils import timezone # timezone 추가

# Create your views here.

def home(request):
    posts = Post.objects
    return render(request, 'home.html', {'posts':posts})

def create10(request):
    ifake = Faker()
    for i in range(10):
        post = Post()
        post.title = ifake.name()
        post.body = ifake.sentence()
        post.pub_date = timezone.datetime.now()
        post.save()
    return redirect('/')
```

<br><br>

\-urls.py 수정

```
from django.urls import path
from django.conf import settings
from django.conf.urls.static import static
from . import views

urlpatterns = [
    path('', views.home, name='home'),
    path('create10/', views.create10, name='create10'),
]+ static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

<br><br>

\- home.html AddPost 옆에 'create10 버튼' 추가

```
<a href = "{%url 'create10' %}" class = "btn btn-secondary my-2">create 10 Post</a>
```

<br><br>

\- 확인

<br><p align="center"><img src="/img/CRUD/18.png" width = "800px"></p><br>
<br><p align="center"><img src="/img/CRUD/19.png" width = "800px"></p><br>

<br><br>

[다음 : Blog Project - 게시물 / 댓글 CRUD (3) 게시물 Create](/7.md)


## 목차 0825
- [Blog Project - 게시물 / 댓글 CRUD (1) 기본세팅/모델다루기 ](/5.md)
- [Blog Project - 게시물 / 댓글 CRUD (2) 복습](/6.md)
- [Blog Project - 게시물 / 댓글 CRUD (3) 게시물 Create](/7.md)
- [Blog Project - 게시물 / 댓글 CRUD (4) 게시물 UD](/8.md)
- [Blog Project - 게시물 / 댓글 CRUD (5) 댓글 CRUD](/9.md)
