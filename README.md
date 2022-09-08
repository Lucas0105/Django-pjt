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

# Django-03
- [Django Form](#django-form)
- [Django ModelForm](#django-modelform)
- [View decorators](#view-decorators)

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



## 기타
데이터베이스에 저장될 때는 기본으로 UTC 시간으로 저장됨     
한국 시간으로 변경한다는 의미는 UTC 시간을 한국 시간으로 변경하여 사용한다는 의미     


pk는 get으로만 조회
filter는 빈셋또는 셋으로 감싸서 주기 때문에 에러를 발생시키지 않고 한번 더 set에 들어가야 하는 문제가 있기 때문
