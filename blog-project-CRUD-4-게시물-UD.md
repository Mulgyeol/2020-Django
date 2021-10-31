## \[Django\] Blog Project - 게시물 / 댓글 CRUD (4) 게시물 UD

이제 게시물을 수정하고 삭제하는 기능을 구현해 봅시다.

#### **1\. Update**

\- detail.html에 edit 버튼과 delete 버튼을 만들어주겠습니다.

```
        <div align="right">
          <a href="#" class="btn btn-primary my-2">Edit</a>
          <a href="#" class="btn btn-primary my-2">Delete</a>
        </div>
```

<br><p align="center"><img src="/img2/24.png" width = "800px"></p><br>

아직 url 연결은 해주지 않았네요.

<br><br>

\- urls.py

```
path('<int:post_id>/edit/', views.edit, name='edit'),
```

이제 이 부분은 설명이 필요 없겠죠?

<br><br>

\- views.py

```
def edit(request, post_id):
    post = get_object_or_404(Post, pk=post_id)
    if request.method == "POST":
        form = PostForm(request.POST, request.FILES, instance=post)
        if form.is_valid():
            post = form.save(commit=False)
            post.pub_date = timezone.now()
            post.save()
            return redirect('/' + str(post.id))
    else:
        form = PostForm(instance=post)
    return render(request, 'new.html', {'form': form})
```

위와 같이 views.py를 작성해 줍니다.

return 부분에 new.html을 재사용한 것이 보이네요?

얼핏 보면 new함수와 다를 것이 없어보입니다.

여러분이 이제 다 대부분 아는 내용이실 테지만,

새로운 부분만 집고 넘어가 봅시다.

우선 new와 다르게

```
post = get_object_or_404(Post, pk=post_id)
```

현재 게시물의 정보를 받아옵니다!

detail 함수에서도 작성했었죠.

그 다음은 요청된 방식이 POST인지 확인합니다.

다 같은데 **instance=post** 라는 것이 추가되었습니다.

수정하고자 하는 글의 Post모델의 객체 정보를 가져오는 내용입니다.

이렇게 가져온 데이터를 new.html의 입력공간에 그대로 보여주는 것이죠.

<br><br>

- detail.html / edit 버튼을 연결해줍니다.

```
<a href="{% url 'edit' post.id %}" class="btn btn-primary my-2">Edit</a>
```

자, 이제 확인해봅시다.

<br><p align="center"><img src="/img2/25.png" width = "800px"></p><br>

이미 작성된 데이터들이 입력란에 들어와 있고, 이미지도 다시 선택할 수 있습니다.

<br><br>

\- 확인

<br><p align="center"><img src="/img2/26.png" width = "800px"></p><br>
<br><p align="center"><img src="/img2/27.png" width = "800px"></p><br>

<br><br>

#### **2\. Delete (간단하게 반복되는 내용으로, 설명은 생략합니다.)**

\- views.py

```
def delete(request, post_id):
    post = get_object_or_404(Post, pk=post_id)
    post.delete()
    return redirect('/')
```

<br><br>

\-urls.py

```
path('<int:post_id>/delete/', views.delete, name="delete"),
```

<br><br>

\- detail.html

```
<a href="{%url 'delete' post.id %}" class="btn btn-primary my-2">Delete</a>
```

<br><br>

\- home.html

```
<div class="btn-group">
    <button type="button" class="btn btn-sm btn-outline-secondary" onclick="location.href='{% url 'detail' post.id %}';"> View</button>
    <button type="button" class="btn btn-sm btn-outline-secondary" onclick="location.href='{% url 'delete' post.id %}';">Del</button>
```

<br><br>

\-확인 Ronnie Olson 삭제

<br><p align="center"><img src="/img2/28.png" width = "800px"></p><br>
<br><p align="center"><img src="/img2/29.png" width = "800px"></p><br>

<br><br>

[다음 : Blog Project - 게시물 / 댓글 CRUD (5) 댓글 CRUD](/9.md)


## 목차 0825
- [Blog Project - 게시물 / 댓글 CRUD (1) 기본세팅/모델다루기 ](/5.md)
- [Blog Project - 게시물 / 댓글 CRUD (2) 복습](/6.md)
- [Blog Project - 게시물 / 댓글 CRUD (3) 게시물 Create](/7.md)
- [Blog Project - 게시물 / 댓글 CRUD (4) 게시물 UD](/8.md)
- [Blog Project - 게시물 / 댓글 CRUD (5) 댓글 CRUD](/9.md)