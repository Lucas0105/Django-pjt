# Django-01
- [DTL(Django Template Language) Syntax](#dtl-syntax)
- [템플릿 상속](#템플릿-상속)
- [sending and retrieving](#sending-and-retrieving)
- [App URL mapping](#app-url-mapping)
- [Naming URL patterns](#naming-url-patterns)

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

