## \[Django\] Blog Project - 게시물 / 댓글 CRUD (3) 게시물 Create

#### 1\. Read / detail.html 작성하기

[https://startbootstrap.com/previews/blog-post/](https://startbootstrap.com/previews/blog-post/)

위의 부트스트랩 템플릿을 이용해서 게시물 detail 페이지를 만들어보겠습니다.


<br><br>


\- myblog/views.py

```
from django.shortcuts import render, get_object_or_404, redirect #get_object_or_404 추가

def detail(request, post_id):
    post_detail = get_object_or_404(Post, pk=post_id)
    return render(request, 'detail.html', {'post':post_detail})
```

<br><br>

\-myblog/urls.py

```
path('<int:post_id>/', views.detail, name="detail"),
```

<br><br>

\- myblog/templates/detail.html

위의 템플릿에서 index.html의 코드만 가져와서 일부 수정해 주겠습니다.

템플릿 상속을 이용했으니 필요한 부분만 가져와서 붙여줍니다.

```
{% extends 'base.html' %}


{% block content %}
  <!-- Page Content -->
  <div class="container">

    <div class="row">

      <!-- Post Content Column -->
      <div class="col-lg-8">

        <!-- Title -->
        <h1 class="mt-4">{{ post.title }}</h1>

        <!-- Author -->
        <p class="lead">
          by
          <a href="#">Mulgyeol</a>
        </p>

        <hr>

        <!-- Date/Time -->
        <p>{{ post.pub_date }}</p>

        <hr>

        <!-- Preview Image -->
        {% if post.image != '' %}
        <img src="{{ post.image.url }}" height= "500" alt=" Card image cap">    

        {% else %}
        <svg width="100%" height="500" xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMidYMid slice" focusable="false" role="img" aria-label="Placeholder: Thumbnail"><rect width="100%" height="100%" fill="#55595c"/><text x="50%" y="50%" fill="#eceeef" dy=".3em">No image</text></svg>
                    
        {% endif %}

        <hr>

        <!-- Post Content -->
        <p class="lead">{{post.body}}</p>

        <hr>

        <!-- Comments Form -->
        <div class="card my-4">
          <h5 class="card-header">Leave a Comment:</h5>
          <div class="card-body">
            <form>
              <div class="form-group">
                <textarea class="form-control" rows="3"></textarea>
              </div>
              <button type="submit" class="btn btn-primary">Submit</button>
            </form>
          </div>
        </div>

        <!-- Single Comment -->
        <div class="media mb-4">
          <img class="d-flex mr-3 rounded-circle" src="http://placehold.it/50x50" alt="">
          <div class="media-body">
            <h5 class="mt-0">Commenter Name</h5>
            Cras sit amet nibh libero, in gravida nulla. Nulla vel metus scelerisque ante sollicitudin. Cras purus odio, vestibulum in vulputate at, tempus viverra turpis. Fusce condimentum nunc ac nisi vulputate fringilla. Donec lacinia congue felis in faucibus.
          </div>
        </div>

        <!-- Comment with nested comments -->
        <div class="media mb-4">
          <img class="d-flex mr-3 rounded-circle" src="http://placehold.it/50x50" alt="">
          <div class="media-body">
            <h5 class="mt-0">Commenter Name</h5>
            Cras sit amet nibh libero, in gravida nulla. Nulla vel metus scelerisque ante sollicitudin. Cras purus odio, vestibulum in vulputate at, tempus viverra turpis. Fusce condimentum nunc ac nisi vulputate fringilla. Donec lacinia congue felis in faucibus.

            <div class="media mt-4">
              <img class="d-flex mr-3 rounded-circle" src="http://placehold.it/50x50" alt="">
              <div class="media-body">
                <h5 class="mt-0">Commenter Name</h5>
                Cras sit amet nibh libero, in gravida nulla. Nulla vel metus scelerisque ante sollicitudin. Cras purus odio, vestibulum in vulputate at, tempus viverra turpis. Fusce condimentum nunc ac nisi vulputate fringilla. Donec lacinia congue felis in faucibus.
              </div>
            </div>

            <div class="media mt-4">
              <img class="d-flex mr-3 rounded-circle" src="http://placehold.it/50x50" alt="">
              <div class="media-body">
                <h5 class="mt-0">Commenter Name</h5>
                Cras sit amet nibh libero, in gravida nulla. Nulla vel metus scelerisque ante sollicitudin. Cras purus odio, vestibulum in vulputate at, tempus viverra turpis. Fusce condimentum nunc ac nisi vulputate fringilla. Donec lacinia congue felis in faucibus.
              </div>
            </div>

          </div>
        </div>

      </div>

      <!-- Sidebar Widgets Column -->
      <div class="col-md-4">

        <!-- Search Widget -->
        <div class="card my-4">
          <h5 class="card-header">Search</h5>
          <div class="card-body">
            <div class="input-group">
              <input type="text" class="form-control" placeholder="Search for...">
              <span class="input-group-append">
                <button class="btn btn-secondary" type="button">Go!</button>
              </span>
            </div>
          </div>
        </div>

        <!-- Categories Widget -->
        <div class="card my-4">
          <h5 class="card-header">Categories</h5>
          <div class="card-body">
            <div class="row">
              <div class="col-lg-6">
                <ul class="list-unstyled mb-0">
                  <li>
                    <a href="#">열심히들</a>
                  </li>
                  <li>
                    <a href="#">하고</a>
                  </li>
                  <li>
                    <a href="#">계신가요</a>
                  </li>
                </ul>
              </div>
              <div class="col-lg-6">
                <ul class="list-unstyled mb-0">
                  <li>
                    <a href="#">어려운 부분이</a>
                  </li>
                  <li>
                    <a href="#">있다면 바로</a>
                  </li>
                  <li>
                    <a href="#">질문해주세요.</a>
                  </li>
                </ul>
              </div>
            </div>
          </div>
        </div>

        <!-- Side Widget -->
        <div class="card my-4">
          <h5 class="card-header">심심해서 남겨두는 Widget</h5>
          <div class="card-body">
            You can put anything you want inside of these side widgets. They are easy to use, and feature the new Bootstrap 4 card containers!
          </div>
        </div>

      </div>

    </div>
    <!-- /.row -->

  </div>
  <!-- /.container -->

  {% endblock%}

```

-   Title
-   Date/Time
-   Preview Image
-   Post Content

위에 해당하는 부분에 값을 템플릿 언어를 사용해 출력되도록 작성해 줍니다.


<br><br>

\- home.html 'view'버튼에 연결하기

```
<button type="button" class="btn btn-sm btn-outline-secondary" onclick="location.href= {% url 'detail' post.id %}"> View</button>
```

> 버튼 태그에 링크를 거는 방법  
> onclick="location.href={% url 'detail' post.id%}" 속성을 추가해 줍니다.

<br><br>

\- 확인

<br><p align="center"><img src="/img/CRUD/20.png" width = "800px"></p><br>

<br><br>

#### **2\. Create / AddPost 작성하기**

이번에는 Form을 이용해서 블로그 글을 추가하거나 수정하는 기능을 구현해 보도록 하겠습니다.

장고 폼이 좋은 점은 아무런 준비 없이도 양식을 만들 수 있고,

ModelForm을 생성해 자동으로 모델에 결과물을 저장할 수 있다는 것입니다.

이 기능이 지금 우리가 할 내용입니다.

폼을 하나 만들어서 Post모델에 적용해봅시다.

<br><br>

\- myblog 폴더 안에 forms.py를 만들어 줍니다.

```
$ touch myblog/forms.py
```

<br><br>

\-forms.py에 다음과 같이 작성해 줍니다.

```
from django import forms

from .models import Post

class PostForm(forms.ModelForm):

    class Meta:
        model = Post
        fields = ('title', 'body', 'image')
```

-   위 코드를 보면 첫 번째로 forms model을 import 해야 하고 (from django import forms),  
    그 다음으로Postmodel을 import 합니다. (from .models import Post).
-   PostForm은 이미 다들 예상 하셨듯이 우리가 만들 폼의 이름입니다.  
    그리고 장고에 forms.ModelForm을 통해 이 폼이ModelForm이라는 것을 알려줘야 합니다.
-   다음으로 class Meta은 이 폼을 만들기 위해서 어떤 model이 쓰여야 하는지 장고에 알려주는 구문입니다. (model = Post).
-   마지막으로, 이 폼에 필드를 넣으면 완성입니다.  
    이번 폼에서는 title과 body, image만 보여지게 해 봅시다.   
    pub\_date는 글이 등록되는 시간이 될 것입니다.

<br><br>

마쳤습니다! 이제뷰에서 이 폼을 사용해 템플릿에서 보여주기만 하면 되네요.

다음에는 뷰, URL, 링크 그리고 템플릿을 만들 겁니다.

<br><br>

\- views.py

```
from .forms import PostForm

def new(request):
    form = PostForm()
    return render(request, 'new.html', {'form': form})
```

새Post폼을 추가하기 위해PostForm()함수를 호출하도록 하여 템플릿에 넘깁니다.

곧 view로 다시 돌아와서 작업을 더 할 것입니다.

<br><br>

\- urls.py

```
path('new/', views.new, name="new"),
```

<br><br>

\- home.html의 Addpost에 new url을 연결해 줍니다.

```
<a href="{%url 'new' %}" class="btn btn-primary my-2">Add Post</a>
```


<br><br>

\- new.html

```
{% extends 'base.html' %}


{% block content %}

<div class="container">
    <h1>New post</h1>
    <br>
    <form method="POST" class="post-form" enctype="multipart/form-data">{% csrf_token %}
        {{ form.as_p }}
        <button type="submit" class="btn btn-primary">Save</button>
    </form>
</div>

{% endblock%}
```

-   먼저 폼이 보여야 합니다. {{ form.as\_p }}로 간단히 만들 수 있습니다!
-   위 코드를 HTML 태그로 폼을 감쌉니다.<form method="POST">...</form>
-   Save버튼이 필요합니다. <button type="submit">Save</button>
-   전체를 container로 감싸 왼쪽에 여백을 줍니다.
-   csrf 토큰 : 보안과 관련된 태그입니다. 설명은 생략하지만 중요하니 빼먹지 않도록 합시다!
-   form 태그 안에 작성된 정보를 "POST"방식으로 처리합니다! 저희 블로그의 post객체와는 상관X
-   enctype="multipart/form-data" 텍스트가 아닌, 여기선 처리할 이미지 파일을 전송할 수 있게 하는 속성입니다.

<br><br>

\- 확인

<br><p align="center"><img src="/img/CRUD/21.png" width = "800px"></p><br>

내용을 입력하고 save 버튼을 눌러봅시다.

아직 게시물이 추가되지 않는 것을 확인할 수 있습니다.

<br><br>

\- views.py 수정

```
# def new(request):
#     form = PostForm()
#     return render(request, 'new.html', {'form': form})



def new(request):
    if request.method == "POST":
        form = PostForm(request.POST, request.FILES)
        if form.is_valid():
            post = form.save(commit=False)
            post.pub_date = timezone.now()
            post.save()
            return redirect('/' + str(post.id))
    else:
        form = PostForm()
        return render(request, 'new.html', {'form': form})
```

blog/views.py를 다시 열어봅시다.

지금 여러분이 보고 있는 views.py의 new함수는 주석처리 된 내용과 같을 겁니다.

new를 요청했을 때, 비어있는 폼을 불러오는 내용이었죠.

이제 수정을 해봅시다.

저번 세션에서는 작성하는 곳인 new와 제출하는 곳인 create를 따로 만들어서 구현했지만,

이번엔 폼을 제출할 때, 같은 뷰를 불러올 겁니다.

이때request에는 우리가 입력했던 데이터들을 가지고 있는데,request.POST가 이 데이터를 가지고 있습니다.

(POST는 글 데이터를 "등록하는(posting)"하는 것을 의미합니다. 블로그 "글"을 의미하는 "post"와 관련이 없습니다.

HTML에서`<form>`정의에method="POST"라는 속성이 있었죠.

이렇게 POST로 넘겨진 폼 필드의 값들은 이제request.POST에 저장됩니다.

이제view에서 두 상황으로 나누어 처리해볼게요.

-   첫 번째: 처음 페이지에 접속했을 때입니다. 당연히 우리가 새 글을 쓸 수 있게 폼이 비어있어야겠죠.
-   두 번째: 폼에 입력된 데이터를view페이지로 가지고 올 때입니다. 여기서 조건문을 추가시켜야 해요. (if를 사용하세요)

자, 여러분이 처음 게시물을 생성하려고 AddPost 버튼을 눌렀을 때,

if 구문을 통해 요청방식이 POST인지 확인합니다.

```
if request.method == "POST":
```

저희는 아직 POST로 내용을 전달하지 않았기 때문에

else에 해당하는 부분으로 넘어와 양식을 생성합니다.

그 위에 우리가 데이터를 작성하는 것이죠.

그리고 'SAVE'버튼을 누르면 form에 작성된 내용을 "POST" 방식으로 처리하면서 다시 new를 부릅니다.

이때, 다시 if 구문을 통해 요청방식이 POST인지 확인합니다.

그러면 이제는 if 구문 안으로 들어오겠죠?

```
    if request.method == "POST":
        form = PostForm(request.POST, request.FILES)
        if form.is_valid():
            post = form.save(commit=False)
            post.pub_date = timezone.now()
            post.save()
            return redirect('/' + str(post.id))
```

만약method가POST라면, 폼에서 받은 데이터를PostForm으로 넘겨줘야 합니다.

아래와 같이 작성하면 됩니다.

양식에 맞게 작성된 데이터를 폼에 담아 form에 저장합니다.

```
form = PostForm(request.POST, request.FILES)
```

다음에는 폼에 들어있는 값들이 올바른지를 확인해야합니다.

(모든 필드에는 값이 있어야하고 잘못된 값이 있다면 저장하면 되지 않아야합니다.)

이를 위해form.is\_valid()을 사용합니다.

폼에 입력된 값이 올바른지 확인한 다음, 저장하는 과정입니다.

blog/views.py

```
        if form.is_valid():
            post = form.save(commit=False)
            post.pub_date = timezone.now()
            post.save()
            return redirect('/' + str(post.id))
```

일반적으로 이 작업을 두 단계로 나눌 수 있습니다.

form.save()로 폼을 저장하는 작업과 작성자를 추가하는 작업입니다.

(PostForm에는 작성시간필드가 없지만, 필드 값이 필요하죠!)

commit=False란 넘겨진 데이터를 바로Post모델에 저장하지는 말라는 뜻입니다.

왜냐하면, 게시물이 작성된 날짜와 시간을 추가하고 저장해야 하니까요.

대부분의 경우에는commit=False를 쓰지 않고 바로form.save()를 사용해서 저장합니다.

이제

새 블로그 글이 만들어질 겁니다.

<br><br>

\- 확인

<br><p align="center"><img src="/img/CRUD/22.png" width = "800px"></p><br>
<br><p align="center"><img src="/img/CRUD/23.png" width = "800px"></p><br>

<br><br>

[다음 : Blog Project - 게시물 / 댓글 CRUD (4) 게시물 UD](/8.md)


## 목차 0825
- [Blog Project - 게시물 / 댓글 CRUD (1) 기본세팅/모델다루기 ](/5.md)
- [Blog Project - 게시물 / 댓글 CRUD (2) 복습](/6.md)
- [Blog Project - 게시물 / 댓글 CRUD (3) 게시물 Create](/7.md)
- [Blog Project - 게시물 / 댓글 CRUD (4) 게시물 UD](/8.md)
- [Blog Project - 게시물 / 댓글 CRUD (5) 댓글 CRUD](/9.md)
