## \[Django\] Blog Project - 게시물 / 댓글 CRUD (5) 댓글 CRUD

이제 댓글 CRUD를 구현해 볼 차례입니다.

처음으로 돌아가서 잠깐 복습해 볼까요?

# 1:N 관계 설정 (Database relation)

**\- myblog/models.py**

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

> 한 게시글에는 여러개의 댓글이 작성 될 수 있으므로, 게시글과 댓글은 1:N 구조를 띈다고 할 수 있습니다.

<br><br>

### models.py 내 comment 클래스 정의

-   post는models.ForeignKey()객체가 저장되어 있는 변수입니다.  
    위의 예시의 경우, 댓글(Comment)이 새로 생성될때,  
    어떠한 게시글(Post)인지를 나타내는 대한 변수 명입니다.
-   models.ForeignKey()는 외래키를 설정하는 함수입니다.
    -   첫 번째 인자: 외래키가 연결되는 테이블을 입력함. 여기서는Post모델 클래스를 의미합니다.  
        외래키가 연결된다 함은 작성된 댓글이 해당 게시물과 연결된다고 생각하시면 편합니다.
    -   두 번째 인자: ForeignKeyField가 바라보는 값(연결된 게시물)이 삭제 될 때,  
        어떻게 처리할건지를 옵션으로 정합니다.
        -   CASCADE: 부모(게시물)가 삭제 되면, 자기 자신(댓글)도 삭제.  
            게시글에 대한 모델 클래스인Post에 따라 생성된 게시글이 삭제되면,  
            댓글에 대한 모델 클래스인 Comment에 따라 생성된 댓글도 삭제.
        -   PROTECT: 댓글이 존재하면, 게시물 삭제 불가능(ProtectedError발생시킴)
        -   SET\_NULL: 게시물이 삭제되면, 댓글의 게시물 정보를 NULL로 변경.

<br><br>

#### **1\. Read**

이제 detail 페이지로 돌아가 봅시다.

```
<!-- Comment with nested comments -->
```

이 부분은 지우고

```
<!-- Single Comment -->
```

이 부분의 코드를 아래와 같이 수정해 줍니다.

```
{% for comment in post.comment_set.all %}
    <div class="media mb-4">
        <img class="d-flex mr-3 rounded-circle" src="http://placehold.it/50x50" alt="">
        <div class="media-body">            
            <h5 class="mt-0">anonymous</h5>
            {{comment}}
        </div>
    </div>
{% endfor %}
```

그리고 처음 admin 페이지에서 댓글을 작성했던 게시물의 detail 페이지로 들어가 봅시다.

<br><p align="center"><img src="/img2/30.png" width = "800px"></p><br>

자, 저희가 처음 작성했던 댓글이 달려있는 것을 확인할 수 있네요!

작성자는 저희가 아직 기능을 구현하지 않았기 때문에 임의로 'anonymous'로 설정해 주었습니다.

post에 연결된 comment를 반복문으로 처리해 출력해준 것입니다.

```
{% for comment in post.comment_set.all %}
```

다들 이해가셨죠?!

자, 이제 댓글을 생성해보도록 합시다.

친절하게도 Leave a Comment란이 이미 구현되어 있네요!


<br><br>

#### **2\. Create**

\- views.py

```
from .models import Post, Comment #Comment 추가

def comments_create(request, post_id):
    #댓글을 달 게시물에 대한 정보 가져오기
    post = Post.objects.get(pk=post_id)
    #form 태그에서 넘어온 댓글 내용 가져오기
    content = request.POST.get('content')
 
    #댓글 생성 및 저장 
    comment = Comment(post=post, content=content)
    comment.save()
    
    #댓글 생성후, 디테일 페이지로 redirect시킴
    return redirect('/' + str(post.id))
```

-   게시물의 정보와, 폼 태그에서 작성된 댓글 내용을 가져옵니다.
-   어느 게시물에 달려있고, 무슨 내용을 가지는 지에 대한 정보를 담아 댓글을 생성, 저장합니다.

<br><br>

\- urls.py

```
path('<int:post_id>/comments/create/', views.comments_create, name='comments_create'),
```

<br><br>

\- detail.html

Comments Form부분을 다음과 같이 수정해 줍니다.

```
    <!-- Comments Form -->
        <div class="card my-4">
          <h5 class="card-header">Leave a Comment:</h5>
          <div class="card-body">
            <form action="{% url 'comments_create' post.pk %}" method="POST">
              {% csrf_token %}
              <div class="form-group">
                <textarea class="form-control" rows="3" name="content"></textarea>
              </div>
              <button type="submit" class="btn btn-primary">Submit</button>
            </form>
          </div>
        </div>
```

이제 작성된 코드를 보면 어느정도 이해가 가실겁니다!

뿌듯하지 않나요?

폼에 작성된 내용을 comments\_create에 POST로 연결합니다.

views.py를 다시 한 번 보면 이해가 더 잘 되실 겁니다.

<br><br>

\- 확인(Edit과 Delete는 바로 이어 나옵니다.)

<br><p align="center"><img src="/img2/31.png" width = "800px"></p><br>
<br><p align="center"><img src="/img2/32.png" width = "800px"></p><br>


<br><br>

#### **3\. delete**

\-views.py

게시글의 정보와 댓글의 정보가 둘다 필요하므로, post\_id와 comment\_id를 둘다 넘겨 줘야합니다.

```
#views.py
def comments_delete(request, post_id, comment_id):
    comment = Comment.objects.get(pk=comment_id)
    comment.delete()
    
    return redirect('/'+str(post_id))


```

<br><br>

\-urls.py

```
#urls.py
path('<int:post_id>/comments/<int:comment_id>/delete/', views.comments_delete, name='comments_delete'),
```

<br><br>

\-detail.html

Delete 버튼도 당연히 있어야겠죠?

```
        <!-- Single Comment -->
        {% for comment in post.comment_set.all %}
        <div class="media mb-4">
          <img class="d-flex mr-3 rounded-circle" src="http://placehold.it/50x50" alt="">
          <div class="media-body">            
            <h5 class="mt-0">anonymous</h5>
            {{comment}} 
            <div align="right">
              <a href="{%url 'comments_delete' post.id comment.id %}" class="btn btn-primary my-2">Delete</a>
            </div>
          </div>
        </div>
        {% endfor %}
```

<br><br>

\- 확인(첫 번째 댓글 삭제)

<br><p align="center"><img src="/img2/33.png" width = "800px"></p><br>

<br><br>

#### 4\. Update

\-views.py

```
def comments_update(request, post_id, comment_id):
    post = get_object_or_404(Post, pk=post_id)
    comment = get_object_or_404(Comment, id=comment_id)

    if request.method == 'POST':
        comment.content = request.POST.get('content')
        comment.save()
        return redirect('/'+str(post_id))
    
    else:
        return render(request, 'comments_update.html', {'post':post, 'comment':comment})
```

자, 어디서 본듯한 형식이 다시 돌아왔습니다.

먼저 post와 comment 정보를 받습니다.

그 후에, 요청이 POST인지 아닌지 확인을 먼저 합니다.

처음 접속한 경우 else로 가서 comments\_update.html로 post와 comment를 넘겨줄 겁니다.

그러면 comments\_update에는 이미 작성된 댓글이 폼에 적혀져 있겠네요?

이걸 수정한 다음 POST 처리하면 다시 돌아옵니다.

이번엔 POST에 해당되니

수정된 내용을 저장하는 과정이 되겠습니다.

<br><br>

\-urls.py

```
path('<int:post_id>/comments/<int:comment_id>/update/', views.comments_update, name='comments_update'),
```

<br><br>

\- detail.html

Edit 버튼을 만들어줍니다.

```
<!-- Single Comment -->
        {% for comment in post.comment_set.all %}
        <div class="media mb-4">
          <img class="d-flex mr-3 rounded-circle" src="http://placehold.it/50x50" alt="">
          <div class="media-body">            
            <h5 class="mt-0">anonymous</h5>
            {{comment}} 
            <div align="right">
              <a href="{%url 'comments_update' post.id comment.id %}" class="btn btn-primary my-2">Edit</a>
              <a href="{%url 'comments_delete' post.id comment.id %}" class="btn btn-primary my-2">Delete</a>
            </div>
          </div>
        </div>
        {% endfor %}
```

<br><br>

\- comments\_update.html

detail.html를 그대로 복사 후 변수를 약간 수정해 줍시다.

for문의 변수를 comments로 수정하고,

views.py else구문에서 넘겨받은 댓글은 comment로 받아왔습니다.

이 두 댓글이 같은 댓글이라면 수정을하고

아니라면 형식을 그대로 유지하면

수정하기 수월하겠죠?

이 과정을 if/else 구문으로 작성했습니다.

```
<!-- Single Comment -->
        {% for comments in post.comment_set.all %}
        {% if comments.id != comment.id %}
        <div class="media mb-4">
          <img class="d-flex mr-3 rounded-circle" src="http://placehold.it/50x50" alt="">
          <div class="media-body">            
            <h5 class="mt-0">anonymous</h5>
            {{comment}} 
            <div align="right">
              <a href="{%url 'comments_update' post.id comment.id %}" class="btn btn-primary my-2">Edit</a>
              <a href="{%url 'comments_delete' post.id comment.id %}" class="btn btn-primary my-2">Delete</a>
            </div>
          </div>
        </div>
        {% else %}
        <div class="card-body">
            <form action="{% url 'comments_update' post.pk comment.id %}" method="POST">
              {% csrf_token %}
              <div class="form-group">
                <textarea class="form-control" rows="3" name="content">{{comment.content}}</textarea>
              </div>
              <div align="right">
              <button type="submit" class="btn btn-primary">Submit</button>
              </div>
            </form>
        </div>
        {% endif %}
        {% endfor %}
```

<br><br>

\- 확인

<br><p align="center"><img src="/img2/34.png" width = "800px"></p><br>
<br><p align="center"><img src="/img2/35.png" width = "800px"></p><br>

## **끝!**

## 고생하셨습니다.