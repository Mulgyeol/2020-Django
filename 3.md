# Blog project CR-3 (Detail 페이지)

admin 페이지에서 아주 긴 본문을 가진 게시물을 추가한 다음,

메인페이지를 확인해 보겠습니다.

<p align="center"><img src="/img/15.PNG" width = "800px"></p><br><br>

아주 보기 어지럽네요.

아무래도 본문의 일부분만 보여주고 나서

클릭을 하면 자세한 내용을 확인할 수 있는 방식이 좋겠습니다.

그럼 한 번 만들어 보겠습니다.

디테일 페이지를 먼저 만들어보도록 하겠습니다.
<br><br><br><br>
1\. templates 폴더 내부에 detail.html 파일을 만들어줍니다.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1>Detail Page</h1>
</body>
</html>
```

자, 여기서 잠깐 정리를 해봅시다.

우리의 모든 게시글은 detai l페이지를 필요로 합니다.

그렇다면 링크를 클릭했을 때 각각의 상세페이지가 떠야합니다.

그럼 우리가 등록한 게시글 수 만큼 detail페이지가 필요하게 됩니다.
<br><br><br><br>
1번 게시글을 클릭했을 땐 1번 게시글의 상세페이지로,

2번 게시글을 클릭했을 땐 2번 게시글의 상세페이지로 이동해야 하기 때문입니다.

그러다 만약 게시글이 무수히 많아진다면 어떻게 될까요?

물론 엄청난 시간과 노력을 들여 해결할 수도 있겠지만,

저희는 좀더 나은 방법을 선택하겠습니다.

어떻게 하면 좋을지 urls.py 먼저 수정해보며 하나하나 살펴보겠습니다.
<br><br><br><br>
2\. urls.py에 path 추가

```
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', blog.views.home, name="home"),
    path('<int:post_id>/', blog.views.detail, name="detail"),
]
```

위처럼 작성을 해줍니다.

<int:blog\_id>라고 적힌 부분은 각 게시물의 id값이 들어갈 공간입니다.

우리의 게시글이 100개라고 해서 100개의 path를 다 적어줄 필요는 없습니다.

장고는 각 게시물들의 id값을 전달받아, 알아서 url을 디자인 해줍니다.

urls.py에서 detail이라는 함수에 post\_id라는 인자를 넘겼으니

views.py의 detail함수는 post\_id를 전달받게 합니다.

<br><br>

```
path-converter란?

우리가 아까 적어줬던 <int:post\_id>같은 것들을 path-converter라고 합니다.

장고에서 여러 객체들을 다루는 계층적 url이 필요할 경우에 사용하며, <type:name>와 같은 모양입니다.

'지정한 converter type의 name변수를 view 함수로 넘겨라' 하고 정리할 수 있겠네요.

converter의 다양한 타입에 대해 궁금하시면 한 번 찾아보시기 바랍니다.
```

<br><br><br><br>

3\. views.py

다음은 아래와 같이 views.py를 작성해 줍니다.

```
from django.shortcuts import render, get_object_or_404
from .models import Post

# Create your views here.

def home(request):
    posts = Post.objects
    return render(request, 'home.html', {'posts':posts})

def detail(request, post_id):
    post_detail = get_object_or_404(Post, pk=post_id)
    return render(request, 'detail.html', {'post':post_detail})
```
<br><br>

-   import render, get\_object\_or\_404 라고 수정해줍니다.
-   get\_object\_or\_404 : object를 가져오고 없으면 404 에러를 띄우라는 내용의 함수입니다. post\_detail에 object를 담아서 detail에 넘겨줍니다. 안에 모델명(Post)과 불러올 post의 id값을 적어 주면 됩니다. 여기서 게시글의 id가 pk가 됩니다.
-   이렇게 담은 데이터를 'post'에 담아 넘겨줍니다.

<br><br>

```
pk(primary key)란?

모델에서 찍어낸 수많은 객체들을 구분할 수 있는 구분자라고 할 수 있습니다. 
서류철의 라벨처럼 데이터를 구분 지을 수 있는 이름표라고 이해하면 쉽습니다.
```

<br><br>
위의 detail함수는 request와 post\_id를 함께 받아 해당 데이터를 넘겨줍니다.

urls.py에서 받아달라고 인자를 넘기고

views.py 받아주는 게 되겠습니다.
<br><br><br><br>
자, 우리는 이제 모델에서 id기준으로 데이터를 살펴서,

데이터가 있으면 보여주고 없으면 404 에러를 띄워줍니다.

detaili 페이지가 잘 동작하는지 서버를 켜 확인해봅시다.

127.0.0.1:8000/2/로 접속해 보겠습니다.
<br><br><br><br>
4\. 확인

<p align="center"><img src="/img/16.PNG" width = "800px"></p><br><br>



Detail Page라는 글이 제대로 보이는 것을 확인했습니다.

하지만 아직 갈길이 조금 더 남았죠?

메인 페이지에서 본문의 내용을 일부만 보이게 하고,

디테일 페이지에는 각 게시물의 내용들이 보이게끔 만들어줘야 합니다.

먼저 메인페이지를 다뤄보겠습니다.
<br><br><br><br>
5\. models.py

blog폴더의 models.py에 코드를 추가로 작성해 주겠습니다.

```
from django.db import models

# Create your models here.

class Post(models.Model):
    title = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')
    body = models.TextField()

    def __str__(self):
        return self.title
# 아래 코드 추가
    def summary(self):
        return self.body[:20]
```
<br><br>

-   def summary(self): 부분을 추가합니다.
-   summary는 body의 20번째 글자 전까지만 보여주게 합니다.

<br><br><br><br>
6\. home.html 수정 + detial페이지로 url 연결
이제 html을 다시 수정해 봅시다.

```
.
.
        <tbody>
            {% for post in posts.all %}
            <tr>
                <td>{{forloop.counter}}</td>
                <td><a href = "{%url 'detail' post.id %}">{{ post.title }}</a></td>
                <td>{{ post.pub_date }}</td>
                <td>{{ post.summary }}...</td>
            </tr>
            {% endfor %}
        </tbody>
.
.
```
<br><br>

-   title부분을 템플릿언어를 사용해 detail + post.id로 연결하는 링크를 걸어줍니다.
-   {{post.body}}를 {{post.summary}} 로 바꿔줍니다.

<br><br><br><br>
7\. 메인페이지 확인

<p align="center"><img src="/img/17.png" width = "800px"></p><br><br>

title에 detail로 향하는 링크가 걸렸습니다.

저는 네 번째 게시물을 클릭해 보겠습니다.

<p align="center"><img src="/img/18.png" width = "800px"></p><br><br>

/4/로 연결은 잘 됐지만 이제 각각 제목, 작성일자, 본문을 표시해 줘야 합니다.
<br><br><br><br>
8\. detail.html 수정

아래와 같이 detail페이지를 수정해 줍니다.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div class="container">
        <h1>{{ post.title }}</h1>
        <p>{{ post.pub_date }}</p>
        <p>{{ post.body }}</p>
    </div>
    <a href="{% url 'home' %}">돌아가기</a>
</body>
</html>
```

title, pub\_date, body를 출력하고

home과 연결하는 링크를 작성해 줍니다.
<br><br><br><br>
9\. 확인

<p align="center"><img src="/img/19.PNG" width = "800px"></p><br><br>


## 목차 
- [Blog project CR-1 (Model 다루기)](/1.md)
- [Blog project CR-2 (Model에서 데이터 가져오기)](/2.md)
- [Blog project CR-3 (Detail 페이지)](/3.md)
- [Blog project CR-4 (Create)](/4.md)
