# ✅Django ModelForm II

> 1. 가상환경 및 Django 설치
> 2. articles app 생성 및 등록
> 3. Model 정의 (DB 설계)
> 4. CRUD 기능 구현
>
> 
>
> 💡 Django ModelForm I 필기와의 차이점 : 
>
> - (CRUD 시작할때) 처음부터 ModelForm 활용해서  게시판 기능 구현
> - CRUD 중에 D(삭제) 기능 추가 구현
>   - Django ModelForm I 필기는 CRU 까지만 구현
>
> - Bootstrap5 패키지 활용
>   - 🗂️ [(참고자료)](https://pypi.org/project/django-bootstrap5/)
> - Django settings.py 에서 시크릿 키 분리
>   - 🗂️ [(참고자료)](https://grape-blog.tistory.com/17)
> - (프로젝트 추가 설정 단계에) base.html 적용
> - Admin site
> - Static files



## 1. 가상환경 및 Django 설치

> 가상환경 설치하는 이유 : 프로젝트마다 패키지를 별도로 가져가야하기 때문
>
> 가상환경 생성 및 실행하기 전에 .gitignore 파일, README.md 파일을 먼저 추가해놓고, 아래 명령어로 변경내역 추적 시작하기
>
> 이 때 .gitignore 파일에 가상환경 폴더를 써두어야함(`/venv`)
>
> `$ git init` 👉 `$ git add .` 👉 `$ git commit -m 'init'`

### 1-1. 가상환경 생성 및 실행

```bash
$ python -m venv venv
$ source venv/Scripts/activate
(venv)
```

### 1-2. Django / Bootstrap5 설치 및 기록

```bash
# upgrade pip
$ python -m pip install --upgrade pip

# install Django 
$ pip install django==3.2.13

# install Bootstrap5
$ pip install django-bootstrap5

# 내가 활용하고 있는 패키지들 기록지에 남기기
$ pip freeze > requirements.txt
```

### 1-3. Django 프로젝트 생성

```bash
# 현재 위치(.)에 pjt 라는 이름의 프로젝트를 생성
$ django-admin startproject pjt .

# 서버 실행되는지 확인
$ python manage.py runserver
```

### 1-4. 프로젝트 추가 설정

> 다국어 지원은 django i18n 검색 결과 참조

#### 1-4-1. 시크릿 키 분리하기

> 🗂️[(참고자료)](https://grape-blog.tistory.com/17)

#### 1-4-2. Bootstrap5 app 등록

```python
# pjt/settings.py 중반부에서 

# INSTALLED_APPS = [] 괄호 내 상단에 아래와 같이 Bootstrap5  앱 등록

INSTALLED_APPS = [
    'django_bootstrap5',
    ...,
]
```

#### 1-4-3. internationalization

```python
# pjt/settings.py 하단으로 내려가서

# LANGUAGE_CODE = 'en-us'
# TIME_ZONE = 'UTC' 부분을 아래와 같이 변경

LANGUAGE_CODE = 'ko-kr'

TIME_ZONE = 'Asia/Seoul'
```

#### 1-4-4. base.html 생성

(1) 프로젝트 폴더(pjt) 보다 더 상단에 templates 폴더 생성

(2) 새로 만든 templates 폴더 안에 base.html 파일 생성 (미리 만들어둔 base.html 파일 있으면 복붙)

(3) settings.py 에서 새로 만든 templates 폴더 등록해주기

```python
# pjt/settings.py 에서 TEMPLATES = [] 안에
# 'DIRS': [], 부분을 아래와 같이 수정

'DIRS': [BASE_DIR/"templates"],
```



## 2. articles app 생성 및 등록

> 기본적인 CRUD 기능이 동작하는 게시판 앱 만들기
>
> 앱은 MTV 패턴으로 제작
>
> 앱을 생성하기 위해, 위에서 실행하고 있던 서버를 잠깐 종료(`ctrl` + `c`)

### 2-1. app 생성

```bash
$ python manage.py startapp articles
```

### 2-2. app 등록

```python
# pjt/settings.py 에서

# INSTALLED_APPS = [] 괄호 내 최상단에 아래와 같이 생성한 앱 등록

INSTALLED_APPS = [
    'articles',
    ...,
]
```

### 2-3. urls.py 설정 

#### 2-3-1. urls.py 분리 작업

> url 관리를 각 앱에서 관리할 수 있도록, include 활용해 분리하기
>
> 💡 활용 : `articles:index` => `/articles/`
>
>   ex) Template 에서 활용 : `{% url 'articles:index' %}`
>
>   ex) View 에서 활용 : `redirect('articles:index')`

```python
# 먼저 pjt/urls.py 에서 아래와 같이 include import 한 다음
# 'articles/' 경로로 향하는 path 추가

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('articles/', include('articles.urls')),
]
```

```python
# articles/urls.py 생성하고,
# urlpatterns = [] 이런 식으로 빈 리스트 선언해야 서버 실행이 됨

urlpatterns = [
    
]
```

```python
# articles/urls.py 에서 아래와 같이 코드 계속 작성

from django.urls import path

app_name = 'articles'

urlpatterns = [

]
```

#### 2-3-2. index.html 생성

> 기본 순서 : URL - VIEW - TEMPLATE
>
> - url 을 등록하고, view 함수 생성, template 만든다
> - URL 을 요청 받아서, 처리하고 응답해주는게 Django 의 근본이니까 위와 같은 순서로 작업

```python
# articles/urls.py 에서 아래와 같이 path 채워넣기

from django.urls import path
from . import views

app_name = 'articles'

urlpatterns = [
    # 아래 주소에 들어오면 어떤 화면을 보여줄지
    # 생각하면서 path 를 작성 ...
    # http://127.0.0.1:8000/articles/
    path('', views.index, name='index'),
]
```

```python
# articles/views.py 에서 index 함수 생성

from django.shortcuts import render

# Create your views here.

# 요청 정보를 받아서..
def index(request):

    # 원하는 페이지를 render..
    return render(request, 'articles/index.html')
```

```django
<!-- articles/templates/articles 폴더 생성 후 
     폴더 최하단에 index.html 생성 -->

{% extends 'base.html' %}

{% block content %}

<h1>안녕!</h1>

{% endblock %}

<!-- 여기까지 작성 후,
     http://127.0.0.1:8000/articles/ 접속했을때
     서버 정상적으로 실행되는지 확인 -->
```



## 3. Model 정의 (DB 설계)

> SW개발에서 UI 기능 / DB 는 서로 밀접한 연관을 맺고 있음
>
> CRUD 를 만든다는 것은 DB의 생성, 조회, 수정, 삭제를 고려해야 하는 것이므로, CRUD 로 넘어가기 전에 모델 정의가 선행되어야함

### 3-1. 클래스 정의

```python
# articles/models.py 에 모델 설계

from django.db import models

# Create your models here.

'''
게시판 기능
- 제목(20글자이내)
- 내용(긴 글)
- 글 작성시간 / 글 수정시간(자동으로 기록, 날짜/시간)
'''

# 1. 모델 설계 (=DB 스키마 설계)
# Article 은 models 에 있는 Model 을 상속 받음
class Article(models.Model):
    title = models.CharField(max_length=20)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

# 2. 설계한 모델을 DB에 반영(아래 3-2. 부터 3-4. 까지)
```

### 3-2. 마이그레이션 파일 생성

```bash
$ python manage.py makemigrations

# 위 명령어 입력 후, 
# articles/migrations/0001_initial.py 생성된 것 확인
```

### 3-3. DB 반영 (`migrate`)

```bash
$ python manage.py migrate
```

### 3-4. DB 반영 잘 되었는지 Django 에서 확인

```bash
$ python manage.py showmigrations

# 이때 articles [X] 0001_initial 생성되어있으면 DB 반영된 것임
```



## 4. CRUD 기능 구현

> 위 모델에 맞는 CRUD 기능 구현해보기 

### 4-1. ModelForm 선언

> **선언된 모델에 따른** 필드 구성 
>
> (1) form 생성 (2) 유효성 검사

```python
# ModelForm 생성하기
# articles/forms.py 파일 새로 생성해서 아래와 같이 코드 채우기
# Article model 에 있는 모든 fields 를 가져다가 쓰겠다는 의미임

from django import forms
from .models import Article

class ArticleForm(forms.ModelForm):

    class Meta:
        model = Article
        fields = '__all__'

# 만약 여기서 title 입력란만 생성하고 싶으면
# Meta class 안에 fields = ['title'] 이렇게 작성하면 되고,
# title, content 입력란 생성하고 싶으면
# Meta class 안에 fields = ['title', 'content'] 이렇게 작성
```

### 4-2. [CREATE] 게시글 생성

> 핵심 : form 으로 데이터를 입력 받아서, DB 에 추가해야함
>
> = 사용자에게 HTML Form 제공, 입력받은 데이터를 처리 
>
> 원래는 위 2가지 기능이 들어가기 때문에 url 도 2개가 필요하지만(new, create)
>
> **ModelForm 로직으로 변경하면서 create url 1개에 2가지 기능 넣을 수 있음**

```django
<!-- articles/templates/articles 폴더 최하단 index.html 로 돌아가서
     새글쓰기 버튼 일단 생성 -->

{% extends 'base.html' %}

{% block content %}

<h1>안녕!</h1>
<a href="{% url 'articles:create' %}">새글쓰기</a>

{% endblock %}
```

```python
# articles/urls.py 에서 아래와 같이 path 채워넣기
# 새로 추가한 코드 : path('create/', views.create, name='create'),

from django.urls import path
from . import views

app_name = 'articles'

urlpatterns = [
    # 아래 주소에 들어오면 어떤 화면을 보여줄지
    # 생각하면서 path 를 작성 ...
    # http://127.0.0.1:8000/articles/
    path('', views.index, name='index'),
    # http://127.0.0.1:8000/articles/create/
    path('create/', views.create, name='create'),
]
```

```python
# articles/views.py 에서 create 함수 작성
# GET 메서드가 아닌 POST 메서드 사용하는게 핵심

from django.shortcuts import render, redirect
from .forms import ArticleForm

# Create your views here.

# 요청 정보를 받아서..
def index(request):

    # 원하는 페이지를 render..
    return render(request, 'articles/index.html')

def create(request):
    if request.method == 'POST':
        # DB에 저장하는 로직
        article_form = ArticleForm(request.POST)
        if article_form.is_valid():
            article_form.save()
            return redirect('articles:index')
    else: # request.method == 'GET':
        # 일반적인 사이트들은 유효하지 않을 때
        # 이슈가 발생한 페이지를 보여주고 정정하라고 하는데,
        # ModelForm 활용해서 new.html 로 넘겨주라고 else 문 작성하면
        # 우리가 원했던 기능이 구현됨
        article_form = ArticleForm()
    context = {
        'article_form': article_form
    }
    return render(request, 'articles/new.html', context=context)
```

```django
<!-- articles/templates/articles 폴더 최하단에 
     아래와 같이 new.html 생성 -->

{% extends 'base.html' %}

{% block content %}

<h1>글쓰기</h1>
<form action="{% url 'articles:create' %}" method="POST">
  {% csrf_token %}
  {{ article_form.as_p }}
  <input type="submit" value="글쓰기">
</form>

{% endblock %}

<!-- 여기까지 작성 후,
     http://127.0.0.1:8000/articles/create/ 접속했을때
     서버 정상적으로 실행되는지 확인 -->
<!-- create 기능 구현이 완료되었다면, form 에 입력한 데이터가
     실제 DB에 반영되고 있는지 Open Databese 통해 확인하기 -->
```

### 4-3. [READ] 게시글 목록 읽기

> 게시글 목록 기능을 구현하기 전에, 가장 먼저 살펴볼 부분은
>
> 이 기능을 어떤 함수에서 처리하고 있는지를 확인하는 것
>
> 현재 작성되고 있는 코드에서는 `index` 함수에서 처리하고 있음
>
> DB에서 게시글을 가져와서, template 에 전달 :  context 딕셔너리!

```python
# DB에서 게시글 가져오기 단계
# articles/views.py 에서 index 함수 수정
# 수정된 코드 : def index 부분

from django.shortcuts import render, redirect
from .models import Article

# Create your views here.

# 요청 정보를 받아서..
def index(request):
    # 게시글을 가져와서..
    # (보통 게시판은 최신글이 맨위니까 .order_by('-pk') 활용)
    articles = Article.objects.order_by('-pk')
    # template 에 뿌려준다
	context = {
        'articles': articles
    }
    return render(request, 'articles/index.html', context)

def new(request):
    return render(request, 'articles/new.html')

def create(request):
    # DB에 저장하는 로직
    title = request.GET.get('title')
    content = request.GET.get('content')
    Article.objects.create(title=title, content=content)
    return redirect('articles:index')
```

```django
<!-- template 에 전달하기 단계 -->
<!-- articles/templates/articles 폴더 최하단 index.html 에서
     게시글 title 목록 생성 (DTL 반복문 활용) -->

<body>
  <h1>안녕!</h1>
  <a href="{% url 'articles:new' %}">새글쓰기</a>
  {% for article in articles %}
  <h3>{{ article.title }}</h3>
  <p>{{ article.created_at }} | {{ article.updated_at }}</p>
  <hr>
  {% endfor %}
</body>
```



- ModelForm

  ```python
  # 변화 3. BE 단에서 ModelForm 생성하기(2)
  # ModelForm 의 인스턴스를 넘겨줘서 new.html 에 작성된 form 대체해야 함
  # 따라서 articles/views.py 에서 new 함수를 아래와 같이 수정
  
  from django.shortcuts import render, redirect
  from .models import Article
  from .forms import ArticleForm
  
  # 요청 정보를 받아서..
  def index(request):
      # 게시글을 가져와서..
      # (보통 게시판은 최신글이 맨위니까 .order_by('-pk') 활용)
      articles = Article.objects.order_by('-pk')
      # template 에 뿌려준다
      context = {
          'articles': articles
      }
      return render(request, 'articles/index.html', context)
  
  def new(request):
      article_form = ArticleForm()
      context = {
          'article_form': article_form
      }
      return render(request, 'articles/new.html', context=context)
  
  def create(request):
      # DB에 저장하는 로직
      title = request.POST.get('title')
      content = request.POST.get('content')
      Article.objects.create(title=title, content=content)
      return redirect('articles:index')
  ```

  


### 4-3. [READ_detail] 상세보기

> 수정하기(UPDATE) 기능을 구현하기 위해, 상세보기 페이지를 먼저 작성
>
> 상세보기 핵심 : '특정한' 글을 본다
>
> url 패턴 : http://127.0.0.1:8000/articles/`<int:pk>`/

```python
# articles/urls.py 에서 detail 페이지로 넘어가는 path 를 아래와 같이 작성

urlpatterns = [
    # 아래 주소에 들어오면 어떤 화면을 보여줄지
    # 생각하면서 path 를 작성 ...
    # http://127.0.0.1:8000/articles/
    path('', views.index, name='index'),
    # http://127.0.0.1:8000/articles/new/
    # path('new/', views.new, name='new'),
    # http://127.0.0.1:8000/articles/create/
    path('create/', views.create, name='create'),
    # http://127.0.0.1:8000/articles/1/ : 1번글
    # http://127.0.0.1:8000/articles/3/ : 3번글
    path('<int:pk>/', views.detail, name='detail'),
]
```

```python
# articles/views.py 에서 detail 함수를 아래와 같이 정의

def detail(request, pk):
    # 특정 글을 가져온다.
    article = Article.objects.get(pk=pk)
    # template 에 객체 전달
    context = {
        'article': article
    }
    return render(request, 'articles/detail.html', context)
```

```django
<!-- detail.html 생성하고 아래와 같이 내용 채우기 -->

<h1>{{ article.pk }}번 게시글</h1>
<p>{{ article.created_at }} | {{ article.updated_at }}</p>
<p>{{ article.content }}</p>
```

```django
<!-- index.html 에서 게시글 title 을 누르면 detail 페이지로
     넘어가게끔 a 태그의 href 작성 -->

<body>
  <h1>안녕!</h1>
  <a href="{% url 'articles:create' %}">새글쓰기</a>
  {% for article in articles %}
  <h3><a href="{% url 'articles:detail' article.pk %}">{{ article.title }}</a></h3>
  <p>{{ article.created_at }} | {{ article.updated_at }}</p>
  <hr>
  {% endfor %}
</body>

```

### 4-4. 삭제하기

> 삭제하기 핵심 : '특정한' 글을 삭제한다
>
> http://127.0.0.1:8000/articles/`<int:pk>`/delete/

### 4-5. 수정하기

> ModelForm 활용해서 수정하는 것이 중요
>
> 수정하기 핵심 : '특정한' 글을 수정한다 => 사용자에게 수정할 수 양식을 제공하고(GET) 특정한 글을 수정한다(POST)
>
> http://127.0.0.1:8000/articles/`<int:pk>`/update/

```python
# articles/urls.py 에서 아래와 같이 update path 추가

urlpatterns = [
    # 아래 주소에 들어오면 어떤 화면을 보여줄지
    # 생각하면서 path 를 작성 ...
    # http://127.0.0.1:8000/articles/
    path('', views.index, name='index'),
    # http://127.0.0.1:8000/articles/new/
    # path('new/', views.new, name='new'),
    # http://127.0.0.1:8000/articles/create/
    path('create/', views.create, name='create'),
    # http://127.0.0.1:8000/articles/1/ : 1번글
    # http://127.0.0.1:8000/articles/3/ : 3번글
    path('<int:pk>/', views.detail, name='detail'),
    # http://127.0.0.1:8000/articles/1/update : 1번글 수정
    # http://127.0.0.1:8000/articles/3/update : 3번글 수정
    path('<int:pk>/update', views.update, name='update'),
]
```

```django
<!-- detail.html 페이지에 a 태그로 수정하기 버튼 생성 -->

<h1>{{ article.pk }}번 게시글</h1>
<p>{{ article.created_at }} | {{ article.updated_at }}</p>
<p>{{ article.content }}</p>
<a href="{% url 'articles:update' article.pk %}">수정하기</a>
```

```python
# articles/views.py 에서 아래와 같이 update 함수 추가

def update(request, pk):
    # GET 처리 : Form 을 제공
    article_form = ArticleForm()
    context = {
        'article_form': article_form
    }
    return render(request, 'articles/update.html', context)
```

```django
<!-- update.html 생성하고 아래와 같이 내용 채우기 -->

<h1>글 수정하기</h1>

<form action="" method="POST">
  {{ article_form.as_p }}
  <input type="submit" value="수정">
</form>
```

```python
# articles/views.py 에서 update 함수를 아래와 같이 수정

def update(request, pk):
    # GET 처리 : Form 을 제공
    article = Article.objects.get(pk=pk)
    # 기존 instance 가진 상태의 ArticleForm()
    article_form = ArticleForm(instance=article)
    context = {
        'article_form': article_form
    }
    return render(request, 'articles/update.html', context)
```

```django
<!-- update.html 에서 form 태그 안에 csrf 코드 추가하기 -->

<h1>글 수정하기</h1>

<form action="" method="POST">
  {% csrf_token %}
  {{ article_form.as_p }}
  <input type="submit" value="수정">
</form>
```

```python
# DB 에 실제 수정된 값 반영하기(저장하기)
# articles/views.py 에서 update 함수 아래와 같이 수정하기

def update(request, pk):
    article = Article.objects.get(pk=pk)
    if request.method == 'POST':
        # POST : input 가져와서 검증하고 DB 에 저장
        article_form = ArticleForm(request.POST, instance=article)
        if article_form.is_valid():
            # 유효성 검사 통과하면 저장후 상세보기 페이지로
            article_form.save()
            return redirect('articles:detail', article.pk)
        # 유효성 검사 통과 못하면 => 오류메세지
    else: 
        # GET 처리 : Form 을 제공
        article_form = ArticleForm(instance=article)
    context = {
        'article_form': article_form
    }
    return render(request, 'articles/update.html', context)
```

