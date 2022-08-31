# Django-01
- [DTL(Django Template Language) Syntax](#dtl-syntax)
- [템플릿 상속](#템플릿-상속)
- [sending and retrieving](#sending-and-retrieving)
- [App URL mapping](#app-url-mapping)
- [Naming URL patterns](#naming-url-patterns)

<br/>    

# Django-02
- Namespace
- Django Model
- Queryset API
- CRUD with view functions

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

## 기타
데이터베이스에 저장될 때는 기본으로 UTC 시간으로 저장됨     
한국 시간으로 변경한다는 의미는 UTC 시간을 한국 시간으로 변경하여 사용한다는 의미     


pk는 get으로만 조회
filter는 빈셋또는 셋으로 감싸서 주기 때문에 에러를 발생시키지 않고 한번 더 set에 들어가야 하는 문제가 있기 때문
