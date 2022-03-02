# M:N 관계 설정 (좋아요 기능)

> 지난 번에 만들어본 댓글 CRUD 1:N 관계에서는 하나의 모델(N)에 대하여 하나의 모델만이 연결이 될 수 있었습니다. 
>그러나 이번에 만들어 볼 “좋아요” 기능의 경우, 한명의 유저(user)가 여러 게시글(post)에 “좋아요”를 누를 수 있으며, 반대로 하나의 게시글(post)에도 여러명의 유저(user)가 좋아요를 누를 수 있어야합니다. 
>따라서 1:N 관계가 아니라 다대다(M:N) 관계 설정이 필요하다고 할 수 있습니다.

<table align="center" style="border-collapse: collapse; width: 100%;" border="1"><tbody><tr><td style="width: 33.3333%; text-align: center;"><span style="color: #333333;">POST1</span></td><td style="width: 33.3333%; text-align: center;"><span style="color: #333333;">[P1, U1]</span></td><td style="width: 33.3333%; text-align: center;"><span style="color: #333333;">USER1</span></td></tr><tr><td style="width: 33.3333%; text-align: center;"><span style="color: #333333;">POST2</span></td><td style="width: 33.3333%; text-align: center;"><span style="color: #333333;">[P2, U2]</span></td><td style="width: 33.3333%; text-align: center;"><span style="color: #333333;">USER2</span></td></tr><tr><td style="width: 33.3333%; text-align: center;">&nbsp;</td><td style="width: 33.3333%; text-align: center;"><span style="color: #333333;">[P2, U3]</span></td><td style="width: 33.3333%; text-align: center;"><span style="color: #333333;">USER3</span></td></tr><tr><td style="width: 33.3333%; text-align: center;"><span style="color: #333333;">POST3</span></td><td style="width: 33.3333%; text-align: center;"><span style="color: #333333;">[P3, U3]</span></td><td style="width: 33.3333%; text-align: center;"><span style="color: #333333;">USER3</span></td></tr><tr><td style="width: 33.3333%; text-align: center;"><span style="color: #333333;">POST4</span></td><td style="width: 33.3333%; text-align: center;"><span style="color: #333333;">[P3, U4]</span></td><td style="width: 33.3333%; text-align: center;"><span style="color: #333333;">USER4</span></td></tr></tbody></table>

<br><br>

**1\. models.py**

```python
from django.conf import settings

class Post(models.Model):
    title = models.CharField(max_length=200)
    image = models.ImageField(upload_to='images/', null=True, blank=True)
    pub_date = models.DateTimeField('date published')
    body = models.TextField()
    like_users = models.ManyToManyField(settings.AUTH_USER_MODEL, related_name='like_posts')
    # 추가

    def __str__(self):
        return self.title

    def summary(self):
        return self.body[:20]
```

<br><p align="center"><img src="/img/deep/36.png" width = "800px"></p><br>

-   like\_users 를 추가해줍니다.
    
-   M:N 관계를 설정하기 위해ManyToManyField라는 필드를 사용합니다.
    
-   관계되는 모델을 첫번째 인자로 입력하는데,
    
    Django 내 내장된 유저모델을 사용하기 위해settings.AUTH\_USER\_MODEL을 입력해 줍니다.
    
    이를 사용하기 위해 settings를 import 해줍니다.
    
-   관계되는 변수명은related\_name속성의 속성 값으로 표시합니다.  
    이건 첫 번째 인자에서 접근할때 사용하는 변수명으로 다음과 같이 사용합니다.
    
    -   Post\=>post.like\_users (해당 게시물을 좋아하는 사용자)
        
    -   User\=>user.like\_posts (해당 유저가 좋아하는 게시물)

<br><br>
        

**2\. migration 재설정**

```bash
$ python manage.py makemigrations
```

<br><p align="center"><img src="/img/deep/37.png" width = "800px"></p><br>

```bash
$ python manage.py migrate
```

<br><p align="center"><img src="/img/deep/38.png" width = "800px"></p><br><br>

**3\. views.py & urls.py 코드 작성(좋아요 함수 구현)**

Post모델 클래스에 새로 생성한like\_users필드는 리스트의 형태를 띄고 있습니다.

이는 M:N 관계에 따라,like\_users에 하나의 값만 저장되는게 아니라 여러 값이 저장 될 수 있기 때문입니다.

이를 활용하여, 조건문을 통해 요청을 보내는 유저가like\_users리스트에 포함되지 않았을 경우, 리스트에 유저를 추가 한 후, 좋아요 기능이 활성화되고, 반대의 경우는 유저를 리스트에서 제거하여 좋아요 기능을 비활성화 시킵니다.

-   post.like\_users.all(): 인스턴스 객체인post내like\_users안의 모든 유저정보를 가지고 옵니다.
-   조건문을 이용하여 좋아요 기능 활성화/비활성화를 나눕니다.

```python
#views.py
def like(request, post_id):
    post = get_object_or_404(Post, pk=post_id)
    
    if request.user in post.like_users.all():
        post.like_users.remove(request.user)
    else:
        post.like_users.add(request.user)
    
    return redirect('/' + str(post.id))
```

```python
#urls.py
path('<int:post_id>/like/', views.like, name='like'),
```

<br><br>

**4\. detail.html 수정**

좋아요 버튼의 하트 아이콘을 사용하기 위해서 font Awesome의 CDN을 활용합니다.

font Awesome은 벡터 기반의 웹폰트 아이콘 제공하는 사이트로, CDN을 활용하여 아이콘을 웹페이지에 삽입 시킬 수 있습니다.

원하는 아이콘이 있다면 찾아서 html 문서 안에 코드를 삽입하면 됩니다.

이번엔 좋아요 기능을 나타내기위해 꽉찬 하트와 빈 하트를 각각 사용해보겠습니다.

-   조건문을 통해 현재 user가 posts.like\_users리스트 안에 있을 경우(좋아요가 이미 된 경우), 좋아요 이모티콘(꽉찬 하트)를 표시합니다. 그렇지 않은 경우(좋아요가 되지 않은 경우), 빈 하트 이모티콘을 표시합니다.
-   추가적으로 post.like\_users.count를 통해 좋아요의 갯수를 카운팅 할 수 있습니다.
-   fontAwesome을 사용하기 위한 CDN 코드를 base.html에 삽입합니다.
-   좋아요를 누른 유저를 확인하기 위해 {{likeuser.username}}을 작성해줍니다.

```html
<!--font Awesome CDN-->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.8.2/css/all.min.css"/>
```

```html
        <!-- Like-->
        <div class="card my-4">
          <div class="card-header">
            {% if user.is_authenticated %}
              {% if user in post.like_users.all %}
                <a href="{% url 'like' post.id %}"><i class="fas fa-heart"></i></a>
              {% else %}
                <a href="{% url 'like' post.id %}"><i class="far fa-heart"></i></a>
              {% endif %}
                <!--like user count-->
                {{ post.like_users.count }}명이 좋아합니다.<br><br>
                    
            {% else %}
            <i class="far fa-heart"></i> {{ post.like_users.count }}명이 좋아합니다.
            <br>좋아요를 누르려면 로그인이 필요합니다.
              
            {% endif %}

            {% for likeuser in post.like_users.all %}
              {{likeuser.username}}
            {% endfor %}
          </div>
        </div>
```

<br><br>

**5\. base.html navbar 수정**

로그인 상태를 navbar에 표시하기 위해 수정작업을 해줍니다.

```html
        <div class="navbar navbar-dark bg-dark shadow-sm">
            <div class="container d-flex bd-highlight">
                <div class="mr-auto p-2 bd-highlight">
                    <a href="{% url 'home' %}" class="navbar-brand d-flex align-items-center">
                        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="none" stroke="currentColor"
                            stroke-linecap="round" stroke-linejoin="round" stroke-width="2" aria-hidden="true" class="mr-2"
                            viewBox="0 0 24 24" focusable="false">
                            <path d="M23 19a2 2 0 0 1-2 2H3a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h4l2-3h6l2 3h4a2 2 0 0 1 2 2z" />
                            <circle cx="12" cy="13" r="4" /></svg>
                        <strong>Mulgyeol's Blog</strong>
                    </a>
                </div>
                
                <div class="p-2 bd-highlight">
                    {% if user.is_authenticated %}
                        <div class="navbar-text">{{user.username}}님이 로그인 중입니다.</div>
                    {% endif %}
                </div>

                <div class="p-2 bd-highlight">
                <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarHeader"
                    aria-controls="navbarHeader" aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
                </button>
                </div>
            </div>
        </div>
```

<br><br>

**확인**

\- 로그인되지 않은 상태

<br><p align="center"><img src="/img/deep/39.png" width = "800px"></p><br>

\- 로그인 된 상태

<br><p align="center"><img src="/img/deep/40.png" width = "800px"></p><br>

\- mulgyeol이라는 user로 로그인해서 좋아요를 누른 상태

<br><p align="center"><img src="/img/deep/41.png" width = "800px"></p><br>

\- testuser1로 로그인 한 상태(좋아요 누르기 전)

<br><p align="center"><img src="/img/deep/42.png" width = "800px"></p><br>

\- testuser1로 로그인 한 상태(좋아요 누른 후)

<br><p align="center"><img src="/img/deep/43.png" width = "800px"></p><br>

\- mulgyeol로 또 다른 게시물 좋아요 누르기

<br><p align="center"><img src="/img/deep/44.png" width = "800px"></p><br><br>

# 끝!


[이전 : \[Django\] - Social Login](/social_login.md)


## 목차 0910
- [\[Django\] - Social Login](social_login.md)
- [\[Django\] - Like](/like.md)
