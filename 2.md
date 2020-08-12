# Blog project CR-2 (Model에서 데이터 가져오기)

다음은 데이터를 가져와 보여주는 페이지를 만들어보겠습니다.<br><br><br><br>

1\. views.py

model에서 template로 바로 내용을 보낼 수 없으니 view를 거쳐야 합니다.

아래와 같이 views.py를 작성해 줍니다.

```
from django.shortcuts import render
from .models import Post

# Create your views here.

def home(request):
    posts = Post.objects
    return render(request, 'home.html', {'posts':posts})
```
<br><br>
-   from .models import Post : 같은 폴더의 models에서 Post를 가져온다.
-   posts = Post.objects : posts라는 변수에 Post 객체들(데이터들)을 담는다.
-   {'posts' : post} : render의 세 번째 값은 사전형 자료형으로, 데이터를 넘겨줍니다. 여기서는 posts에 담긴 데이터들을 'posts'라는 이름으로 넘겨준다는 의미입니다. _template에서 posts라는 이름으로 데이터를 다룬다._
<br><br>


위에서 Post.object를 posts라는 변수에 담았습니다.

이렇게 모델로 부터 전달받은 객체 목록을 우리는

**Queryset, 쿼리셋**이라고 부릅니다.

그리고 쿼리셋을 처리해 주는 방법을

**Method, 메소드**라고 부릅니다.
<br><br><br><br>
2\. app 내부에 templates 폴더를 만들어줍니다.
<br><br><br><br>
3\. home.html 파일 만들기

!+tab키를 이용하여 틀을 만들고, {{post}} 템플릿 변수를 이용해. 데이터를 출력하게 합니다.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    {{posts}}
</body>
</html>
```
<br><br><br><br>
4\. urls.py

blogproject의 urls.py에서 url을 연결해 줍니다.

```
from django.contrib import admin
from django.urls import path
import blog.views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', blog.views.home, name="home"),
]
```
<br><br><br><br>
5\. 확인

<p align="center"><img src="/img/9.PNG" width = "800px"></p><br><br>


원했던 출력 내용은 제목, 작성일자, 그리고 내용인데

'blog.Post.objects'라는 내용이 출력되었습니다.

뭔가 연결되긴 했는데, 더 필요하다는 뜻이겠죠?<br><br>

저희가 원하는걸 화면에 출력하려면 view를 통해 들어오는 데이터가

어떤 형태로 구성되어 있는지 알고 있어야 합니다.<br><br>

template에서 가져온 posts는 list와 같은 형식으로 넘어옵니다.

그렇기 때문에 데이터를 저희가 알맞게 잘라서, 즉 메소드를 이용해서

template에서 필요한 부분에 뿌려주면 되는것이죠.

다음과 같이 html을 작성해 봅시다.
<br><br><br><br>
6\. html 수정

 {{post}} -> {{post.all}}

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    {{ posts.all }}
</body>
</html>
```

<p align="center"><img src="/img/10.PNG" width = "800px"></p><br><br>

게시물의 제목이 나온 것을 확인할 수 있네요.

추가적으로 admin 페이지에서 2개의 게시물을 추가한 후에 확인해 보겠습니다.
<br><br>
<p align="center"><img src="/img/11.PNG" width = "800px"></p><br><br>

<p align="center"><img src="/img/12.PNG" width = "800px"></p><br><br>

all 메소드를 이용하면 모든 게시물을 확인할 수 있습니다.

그렇다면 저희가 원했던 대로 제목, 내용, 작성일자를 확인하려면 어떻게 해야할까요?

아래와 같이 코드를 수정해 봅시다.
<br><br>

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    {% for post in posts.all %}
        {{forloop.counter}}<br>
        <h1>{{ post.title }}</h1><br>
        {{ post.pub_date }}<br>
        {{ post.body }}<br>
        <br><br>
    {% endfor %}
</body>
</html>
```

body태그 안의 코드들을 하나씩 살펴보겠습니다.

-   for post in posts.all 템플릿언어를 사용한 파이썬 문법으로, posts.all에 있는 데이터를 하나씩 post에 담아 반복합니다.
-   forloop.counter : 루프 횟수를 출력합니다. (게시글 번호가 됨)
-   post.title : 제목을 출력합니다.
-   post.pub\_date :  작성일자를 출력합니다.
-   post.body : 내용을 출력합니다.
-   endfor : 반복문을 닫아줍니다.
<br><br><br><br>
7\. 결과 확인

<p align="center"><img src="/img/13.PNG" width = "800px"></p><br><br>

8\. 보기 좋게 표 태그를 이용해 html 수정

내용을 정리하여 목록화 해보겠습니다.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div class="btn-group">
        <a href = "#" class = "btn btn-info">Add Post</a>
    </div>
    <p></p>
    <table class = "table">
        <thead>
            <tr>
                <th scope="col">#</th>
                <th scope="col">Title</th>
                <th scope="col">Date</th>
                <th scope="col">Body</th>
            </tr>
        </thead>
        <tbody>
            {% for post in posts.all %}
            <tr>
                <td>{{forloop.counter}}</td>
                <td>{{ post.title }}</td>
                <td>{{ post.pub_date }}</td>
                <td>{{ post.body }}</td>
            </tr>
            {% endfor %}
        </tbody>
        
    </table>
</body>
</html>
```
<br><br><br><br>
9\. 결과 확인

<p align="center"><img src="/img/14.PNG" width = "800px"></p><br><br>



## 목차 
- [Blog project CR-1 (Model 다루기)](/1.md)
- [Blog project CR-2 (Model에서 데이터 가져오기)](/2.md)
- [Blog project CR-3 (Detail 페이지)](/3.md)
- [Blog project CR-4 (Create))](/4.md)
