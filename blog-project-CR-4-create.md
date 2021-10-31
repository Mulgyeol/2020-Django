# Blog project CR-4 (Create)

이번에는 main 페이지에서 게시물을 추가할 수 있는 기능을 구현해 보겠습니다.
<br><br><br><br>
1\. templates에 new.html 작성

blog폴더 내부의 templates 폴더에 아래와 같이 new.html을 작성해 줍니다.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div calss="container">
        <form action = "">
            <h3>제목 : </h3>
            <input type="text" name="title">
            <br>
            <br>
            <h3>본문 : </h3>
            <textarea cols=40 rows=10 name="body"></textarea>
            <br>
            <br>
            <input type="submit" value="제출하기">
        </form>
    </div>
</body>
</html>
```

<br><br><br><br>

2\. Views.py

Views.py에 new를 작성해줍니다.

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

def new(request):
    return render(request, 'new.html')
```
<br><br><br><br>

3\. urls.py

blogproject폴더 내부의 urls.py에 new를 연결해 줍니다.

```
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', blog.views.home, name="home"),
    path('<int:post_id>/', blog.views.detail, name="detail"),
    path('new/', blog.views.new, name="new"),
]
```

<br><br><br><br>

4\. home.html에 {% url 'new' %} 추가

home.html에서 new.html로 넘어가는 링크를 걸어 줍니다.

```
<!-- body부분에 추가 -->
<a href = "{%url 'new' %}">Add Post</a>
```

<br><br><br><br>

5\. 클릭해서 new로 넘어가는지 확인.

<p align="center"><img src="/img/20.PNG" width = "800px"></p><br><br>

Add Post를 눌렀을 때, new로 넘어가지만

제출하기 버튼은 아직 동작하지 않습니다.

<br><br><br><br>

### 제출하기 버튼 작동하게 하기

1\. new.html 수정하기

templates 폴더에 있는 new.html을 열고,

form 태그의 비어있는 action 부분에 create url"{% url 'create' %}"를 넣어줍니다.

```
<form action = "{% url 'create' %}">
```

<br><br><br><br>

2\. url 설정하기

```
path('create/', blog.views.create, name='create'),
```

<br><br><br><br>

3\. view 작성하기

```
from django.shortcuts import render, get_object_or_404, redirect
from django.utils import timezone
from .models import Post

.
.
.


def create(request):
    post = Post()
    post.title = request.GET['title']
    post.body = request.GET['body']
    post.pub_date = timezone.datetime.now()
    post.save()
    return redirect('/' + str(post.id))
```

-   **request.GET\['title'\]** : new.html 파일에 form태그 안에 있는 것입니다. 코드를 확인해 보면 name="title"이라고 적혀있을텐데 그래서 GET\[\] 안에 'title'이 들어가는 겁니다. blog.body도 동일한 논리입니다. form태그의 해당 부분을 받겠다. 라는 뜻입니다.
-   **blog.pub\_date** 의 경우는 입력한 시간이 자동으로 넘어가가 현재시간이 넘어갑니다. 이때 timezone이라는 패키지를 사용하기 때문에, import 해줍니다.
-   **redirect** 는 요청을 처리하고 보여주는 페이지 입니다. render가 '요청이 들어오면 이 html 파일을 보여줘'라고 한다면, redirect는 '요청을 들어오면 저쪽 url로 보내줘' 하는 것입니다. 이것 또한 첫 줄에서 import 해주었습니다.

<br><br><br><br>

4\. 확인

<p align="center"><img src="/img/21.PNG" width = "800px"></p><br><br>

<p align="center"><img src="/img/22.PNG" width = "800px" ></p><br><br>


끝!


## 목차 
- [Blog project CR-1 (Model 다루기)](/1.md)
- [Blog project CR-2 (Model에서 데이터 가져오기)](/2.md)
- [Blog project CR-3 (Detail 페이지)](/3.md)
- [Blog project CR-4 (Create))](/4.md)
