# Django-01
- [DTL(Django Template Language) Syntax](#dtl-syntax)
- [템플릿 상속](#템플릿-상속)
- [sending and retrieving](#sending-and-retrieving)
- [App URL mapping](#app-url-mapping)
- [Naming URL patterns](#naming-url-patterns)

<br/>    

# Django-02
- [Namespace](#namespace)
- [Django Model](#django-model)
- [Queryset API](#queryset-api)
- [CRUD with view functions](#crud-with-view-functions)

<br/>

# Django-03(Form)
- [Django Form](#django-form)
- [Django ModelForm](#django-modelform)
- [View decorators](#view-decorators)

<br/>


# Django-04(Accounts)
- [The Django authentication system (사전설정)](#the-django-authentication-system)
- [Authentication in Web requests](#authentication-in-web-requests)
- [Authentication with User](#authentication-with-user)
- [Limiting access to logged-in users](#limiting-access-to-logged-in-users)

<br/>

# Django-05(Database)
- [ForeignKey](#foreignkey)
- [Related manager](#related-manager)
- [comment create](#comment_create)

<br/>

## DTL Syntax

### Variable
```
{{variable}}
```

### Filters
```
{{variable|filter}}
{{variable|lower}}
{{variable|truncatewords:30}}
```
참고문서 : https://docs.djangoproject.com/en/4.1/ref/templates/builtins/

### Tags
```
{{% for in %}}
{{% endfor %}}

{{% if %}}
{{% endif %}}
```

### Comments
```
{# #}
{{% comment %}}
{{% endcomment %}}
```

<br/> 


## 템플릿 상속
### 상속
```
{% extends 'base.html' %}

{% block content %}
{% endblock content %}
```
base.html에 block 태그를 작성해 html 코드가 들어갈 위치를 정해줘야 함

- 템플릿 경로 추가하기
프로젝트 최상단에 templates 디렉토리로 base.html을 설정해주고 싶을 때 경로 추가
setting.py 파일에 작성
```
TEMPLATES = [
  {
    'DIRS': [BASE_DIR / 'templates',],
  }
]
```

<br/> 

## sending and retrieving
### sending
- form data(GET, POST)
- - Query String Parameters
- - URL?key=value

- Variable routing
```
path('hello'/<name:str>/<num:int>/<name_str>/
```


### retrieving
```
def hello(request, name, num, name_str):
  request.GET.get('name')
```

## App URL mapping
### Including other URLconfs
- 프로젝트에 앱의 url을 include
```
path('articles/', include('articles.urls')),
```

- 앱에서 해당하는 url 설정
```
from . import views
path('index/', views.index),
```

- URL에 접근할 때 앱을걸쳐 routing함 : /articles/index/

<br/>

## Naming URL patterns
- path에 name 등록
```
path('index/', views.index, name='index'),
```

- html 파일에서 url 태그를 통해 주소 접근 가능
```
{% url 'index' %}
```

<br/>

<br/>

## Namespace
### URL namespace
```
app_name = 'articles'

{% url 'articles:index' %}

```

### Template namespace
- app_name/templates/app_names/ 형태로 templates를 변경
```
return render(request, 'articles/index.html')
```

<br/>

## Django Model
### Model
- 테이블의 스키마 정의(models.py)
```
class Article(models.Model):
  title = models.CharField(max_length=10)
  content = models.TextField()
```
참고문서: https://docs.djangoproject.com/en/4.1/ref/models/fields/

### Migrations
- makemigrations : 테이블을 만들기 위한 설계도(청사진)을 생성하는 것
```
python manage.py makemigrations
```
- migrate : 모델과 DB의 동기화(설계도를 db에 반영)
```
python manage.py migrate
```
- showmigrations : migrations 파일들이 migrate 됐는지 여부 확인
```
python manage.py showmigrations
```
- sqlmigrate : 해당 migrations 파일이 SQL 문으로 어떻게 해석 될 지 미리 확인
```
python manage.py sqlmigrate articles 0001
```

<br/>

## QuerySet API
### 외부 라이브러리 설치 및 설정

- ipython : 자동완성, 색상 입히기
- - 실행방법 : cmd에서 ipython
```
pip install ipython django-extensions
```

- settings.py
```
INSTALLED_APPS = [
  'django_extensions',
]
```

- shell_plus
- - 자동으로 자주 사용하는 것들을 import 해줌
```
python manage.py shell_plus
```

### Database API 구문
- Article : Model class
- objects : Manager
- all() : Queryset API
```
Article.objects.all()
```

<br/>

## CRUD with view functions
### CREATE
- 첫번째 방법
```
article = Article()
article.title = 'title'
article.save()
```

- 두번째 방법(추천)
```
article = Article(title='second', content'django!')
article.save()
```

- 세번째 방법
```
Article.objects.create(title='third', content='django!')
```

- vscode extention
- - sqlite 설치

### READ
- 전체 데이터 조회
```
Article.objects.all()
```

- 단일 데이터 조회(pk, id)
- - 없으면 DoesNotExist 예외 발생
- - 둘 이상이면 MultipleObjectsReturned 예외 발생
```
Article.objects.get(pk=1)
```

- 매개 변수와 일치하는 객체
- - 조회된 객체가 없으면 빈 QuerySet 반환
```
Article.objects.filter(content='django!')
```

- Field lookups
- - contains : 특정 키워드를 포함하는 레코드 조회
```
Article.objects.filter(content__contains='dj')
```
위에서 content는 column

### UPDATE
- 조회 -> 새로운 값 할당 -> save
```
article = Article.objects.get(pk=1)
article.title = 'update'
article.save()
```

### DELETE
- 조회 -> delete 호출
```
article = Article.objects.get(pk=1)
article.delete()
```

## Django Form
### Form Class 선언
- 앱 폴더에서 forms.py 생성 후 선언
```
from django import forms

class ArticleForm(forms.Form):
  title = forms.CharField()
```

### Form with view function
- new view 함수 변경
```
from .forms import ArticleForm

def new(request):
  form = ArticleForm()
  constext = {
    'form' : form,
  }
  return render(request, 'articles/new.html', context)
```

- new 템플릿 업데이트 => inpuyt과 label 태그 렌더링됨
```
{{ forms.as_p }}
```

- Form fields : 입력에 대한 유효성 검사 로직을 처리
- Widgets : HTML input 요소 렌더링을 담당 (단순한 출력 부분)
```
title = forms.CharField(widget=forms.Textarea(
   attrs={
    'class' : 'my-title',
    'placeholder' : 'Enter the title',
   }
))
```

## Django ModelForm
- Model => Form에 맵핑

### ModelForm 선언
```
from django import forms
from .models import Article

class ArticleForm(forms.ModleForm):
  
  class Meta:
    model = Article
    fields = '__all__'
    #  exclude = ('title', )    제외하고 모든 필드
    #  fields = ('title', 'content',)    title과 content 필드만
```

### ModelForm with view finctions
- CREATE
```
def create(request):
  if request.methd == 'POST':
    form = ArticleForm(request.POST)

    if form.is_valid():
      article = form.save()
      return redirect('articles:detail', article.pk)
  else:
    form = ArticleForm()
    
  context = {
    'form': form,
  }
  
  return render(request, 'articles/new.html', context)
```

- UPDATE : instance는 수정 대상이 되는 객체, request.POST는 사용자가 전송한 데이터
```
def update(request, pk):
  article = Article.objects.get(pk=pk)
   
  if request.methd == 'POST':
    form = ArticleForm(request.POST, instance=article)
    
    if form.is_valid():
      form.save()
      return redirect('articles:detail', article.pk)
    else:
      form = ArticleForm(instance=article)
      
    context = {
      'form': form,
      'article': article,
    }
```

- Errors : is_valid() 함수에서 False 일 경우 form에 error 내용이 딕셔너리 형태로 저장됨
```
print(f'에러: {form.errors}')
```

## View decorators
### Allowed HTTP methods
- error : 적절한 method가 아닐 때 => 405 Method Not Allowed
```
from django.views.decorators.http import require_http_methods

@require_http_methods(['GET', 'POST'])
def create(request):
  pass
  
@require_POST
def delete(request, pk):
  pass

@require_safe
def index(request):
  pass
```
## The Django authentication system
- setting.py에 INSTALLED_APPS에서 필수 구성을 확인할 수 있음
- Authentication (인증) : 신원 확인, 사용자가 자신이 누구인지 확인하는 것
- Authorization (권한, 허가) : 권한 부여, 인증된 사용자가 수행할 수 있는 작업을 결정

### 사전 설정
- cmd : accounts app 생성, !!!! 이름을 accounts로 생성해 줘야 함 !!!!
```
python manage.py startapp accounts
```

- settings.py
```
INSTALLED_APPS = [
  'accounts',
]
```

- project의 urls.py에 include해줘야 함


### substituting a custom User model
- Custom User Model : User Model을 필수적으로 대체해줘야 함, 프로젝트 처음에 진행할 것을 권장
- - 그 이유는 기본 제공해주는 User Model이 원하는 기능을 만들기 적합하지 않을 수 있기 때문에

- accounts/models.py 대체하기
```
from django.contrib.auth.models import AbstractUser

class User(AbstractUser):
  pass
```

- settings.py 파일에 AUTH_USER_MODEL 입력
```
AUTH_USER_MODEL = 'accounts.User'
```

- accounts/admin.py에 커스텀 User 모델 등록
```
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin
from .models import User

admin.site.register(User, UserAdmin)
```

## Authentication in Web requests
### Login
- Session을 Create하는 과정

- - accounts/urls.py에 login 경로 설정
- - templates에 html 파일 만들어주기
- - accounts/views.py에 login 함수(db에 세션 등록, 클라이언트에 세션 등록)
```
from django.contrib.auth.forms import AuthenticationsForm
from django.contrib.auth import login as auth_login

def login(request):
  if request.method == 'POST':
    form = AuthenticationForm(request, request.POST)
    if form.is_valid():
      auth_login(request, form.get_user())
      return redirect('articles:index')
  else:
    form = AuthenticationForm()
    
  context = {
    'form' : form
  }
  
  return reder(request, 'accounts/login.html', context)
```

- 템플릿에서 유저 정보 출력하기 : context processors에 auth가 등록되어 있기 때문에 가능
```
  {{ user }}
```

### Logout
- session data를 에서 삭제
- 클라이언트의 쿠키에서 sessionid를 삭제

```
from djnago.contrib.auth import logout as auth_logout

def logout(request):
    auth_logout(request)
    return redirect('articles:index')
```

## Authentication with User
### Custom user
- 커스텀 해줘야 하는 form(UserCreationForm, UserChangeForm)

- - accounts/form.py
```
  from django.contrib.auth import get_user_model
  from django.contrib.auth.forms import UserCreationForm, UserChangeForm
  
  class CustomUserCreationForm(UserCreationForm):
    class Meta(UserCreationForm.Meta):
      model = get_user_model()
      
  class CustomUserChangeForm(UserChangeForm):
    Class Meta(UserChangeForm.Meta):
      model = get_user_model()
```


### 회원 가입

- UserCreateForm(username, password1, password2)
```
from django.contrib.auth.forms import CustomUserCreationForm

def signup(request):
  if request.method == 'POST':
    form = UserCreationForm(request.POST)
    if form.is_valid():
      form.save()
      # 회원가입 후 곧바로 로그인 하기
      # user = form.save()
      # auth_login(request, user)
      # auth_login(request, form.user) 가능
      
      return redirec('articles:index')
  else:
    form = UserCreationForm()
  context = {
    'form' : form,
  }
  return render(request, 'accounts/signup.html', context)
```

### 회원 탈퇴
- accounts/views.py
```
def delete(request):
  request.user.delete()   # 회원 탈퇴   아래와 순서가 변경되면 안됨
  auth_logout(request)    # 로그아웃 => 세션 정보 삭제
  return redirect('articles:index')
```

### 회원정보 수정
- accounts/forms.py
```
  class CustomUserChangeForm(UserChangeForm):
    class Meta(UserChangeForm.Meta):
      model = get_user_model()
      fields = ('email', 'first_name', 'last_name',)
```

- accounts/views.py
```
def update(request):
  if request.method == 'POST':
    form = CustomUserChangeForm(request.POST, instance=request.user)
    if form.is_valid():
      form.save()
      return redirect('articles:index')
  else:
    form = CustomUserChangeForm(instance = request.user)
  context = {
    'form' : form,
  }
  return render(request, 'accounts/update.html', context)
```

### 비밀번호 변경
```
from django.contrib.auth.forms import PasswordChangeForm
from django.contrib.auth import update_session_auth_hash

def change_password(request):
  if request.method == 'POST':
    form = PasswordChangeForm(request.user, request.POST)
    if form.is_valid()
      form.save()       # 로그인 유지가 되지 않음 세션 값이 변경되기 때문에
      # update_session_auth_hash(request, form.user)   로그인 유지하면서 비밀번호 변경하기
      return redirect('articles:index')
      
  context = {
    'form' : form,
  }
  
  return render(request, 'accounts/change_password.html', context)

```

## Limiting access to logged-in users

### is_authenticated
- 사용자가 인증 되었는지 여부 => 모든 User 인스턴스에 대해 항상 True 권한을 확인하지 않음

- templates
```
  {{% if request.user.is_authenticated %}}
```

- views
```
  if request.user.is_authenticated:
```

### login_required
- 로그인 하지 않았을 시 settings.py 의 LOGIN_URL 문자열 주소로 redirect : 기본값은 /accounts/login/

```
from django.contrib.auth.decorators import login_required

@login_required
def create(request):
  pass
```

- 로그인 하지 않았을 때 권한이 필요한 URL로 강제 접속 시도 했을 시 로그인 페이지로 redirect 되고 인증 성공시 이동할 URL로 쿼리 문자열을 함께 전송함
```
/accounts/login/?next=?/articles/create/
```

- next 쿼리를 적용하거나 미적용할 수 있음
```
  return redirect(request.GET.get('next') or 'articles:index')
```

### delete에서 발생하는 문제점
- delete에 강제 접근 시 로그인 페이지로 이동 => 로그인 했을 시 get으로 delete에 접근하기 때문에 405에러를 발생 시킴
- - @login_required 데코레이터는 GET method 에서만 사용
- - is_authenticated 속성 값을 통해 처리
```
if request.user.is_authenticated:
```

## ForeignKey

ForeignKey(to, on_delete, **options) : N:1 관계를 나타냄
```
article = models.ForeignKey(Article, on_delete=models.CASCADE)

article = models.ForeignKey(Article, on_delete=models.CASCADE, related_name='comments')
```
=> db에 article_id로 만들어짐
- CASCADE : 부모 객체가 삭제 됐을 때 이를 참조하는 객체도 삭제
- N:1 관계
- related_name 속성을 사용해 역참조 시 사용할 이름을 설정해줄 수 있음 commten_set => comments

## Related manager

역참조 : N:1 에서 1이 N을 참조하는 상황
```
article.comment_set.method()
article.comment_set.all()
```
Article 모델이 Comment 모델을 역참조


## Comment_create
- forms.py
```
class CommentForm(form.ModelForm):
  
  class Meta:
    model = Comment
    exclude = ('article',)
```

- views
```
def comments_create(request, pk):
  article = Article.objects.get(pk=pk)
  comment_form = CommentForm(request.POST)
  if comment_form.is_valid():
    comment = comment_form.save(commit = False)
    comment.article = pk
    comment.save()
    return redirect()

```

## 기타
데이터베이스에 저장될 때는 기본으로 UTC 시간으로 저장됨     
한국 시간으로 변경한다는 의미는 UTC 시간을 한국 시간으로 변경하여 사용한다는 의미     


pk는 get으로만 조회
filter는 빈셋또는 셋으로 감싸서 주기 때문에 에러를 발생시키지 않고 한번 더 set에 들어가야 하는 문제가 있기 때문      

<br/>
사용할 수 있는 명령어 확인
```
dir(객체)
```
