# Projeto Django Livraria 

Para criação da app django_livraria iniciamos criando uma pasta com o nome da que referência a aplicação. Lembrando que utilizaremos o ambiente virtual para criação do projeto. 

## Criando o ambiente virtual


```python
mkdir django_livraria
```

cd django_livraria

Atualizar o sistema


```python
sudo apt update 
```


```python
sudo apt -y upgrade
```

Instalar o pip3


```python
sudo apt install python3-pip
```

Instalar ferramentas adicionais


```python
sudo apt install build-essential libssl-dev libffi-dev python3-dev
```

Instalando o env e virtualenv


```python
sudo apt install -y python3-venv
```


```python
sudo apt install python3-virtualenv
```

Instalando no MacOS

```python
sudo pip uninstall virtualenv

sudo -H pip install virtualenv
```


Criando seu ambiente virtual. Vamos chamá-lo de generic env


```python
virtualenv env
```

Criando o seu ambiente virtual no Windows 

```python
python -m virtualenv env
```

Criando seu ambiente virtual no MacOS

```python
virtualenv -p python3 <desired-path>


virtualenv -p python3 env

```

Ative o ambiente virtual 


```python
. env/bin/activate
```

Instalar o framework Django:


```python
pip install django
```

Caso queira desativar o ambiente virtual, na pasta


```python
deactivate 
```


```python
quit()
```

## Criando o projeto Django Livraria

Depois de criaremos a pasta seguiremos alguns passos classícos do Django. Dentro da pasta executaremos o seguinte comando:


```python
django-admin startproject core .
```

`django-admin` é um script que criará os diretórios e arquivos para você. 

No diretório core (núcleo) do projeto será criada uma estrutura de diretórios, você também verá o o diretório do virtualenv, venv, que criamos antes. 

manage.py é um script que ajuda com a gestão do site. Com ele, podemos iniciar um servidor de web no nosso computador sem instalar nada, entre outras coisas.

O arquivo `settings.py` contém a configurações do seu site, como a de conexão com o banco de dados. 

O arquivo `urls.py` contém uma lista dos padrões usados por urlresolver.

### Mudando as configurações

Vamos fazer algumas alterações no core/settings.py. Abra o arquivo usando o editor de código que você instalou anteriormente.

Vá até o final do arquivo e, logo abaixo da linha com STATIC_URL, adicione uma nova variável chamada STATIC_ROOT:


```python
import os

 ...
 
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```
### Configurações para o Banco de Dados MySQL

Dado que você tenha instalando o bando de dados, você deve realizar a seguinte configuração. 
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'livraria',
        'USER': 'root',
        'PASSWORD': '12345',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```
 
 Erro
```
django.core.exceptions.ImproperlyConfigured: Error loading MySQLdb module.
Did you install mysqlclient?
```
 
 Solução
```
pip install mysqclient
```
Referencia. https://docs.djangoproject.com/en/4.1/ref/databases/


### Criando um banco de dados para a livraria 

Para criar um banco de dados para o nossa livraria, vamos executar o seguinte comando no console (precisamos estar no diretório que contém o arquivo manage.py).


```python
python manage.py migrate 
```

Vamos startar o servidor web com o segunte comando:


```python
python manage.py runserver #startando o servidor
```

## Modelos do Django

Sabemos os conceitos de classe e objeto nesse momento iremos criar um modelo para algumas classes do nosso projeto livraria 

Um modelo no Django é um tipo especial de objeto -- ele é salvo em um banco de dados. Um banco de dados é uma coleção de dados. Um modelo no Django é um tipo especial de objeto -- ele é salvo em um banco de dados. Um banco de dados é uma coleção de dados. Ele é um local em que você vai salvar dados sobre usuários, suas postagens, etc. Usaremos um banco de dados chamado SQLite para armazenar as nossas informações. Este é o banco de dados padrão do Django -- e ele será suficiente neste primeiro momento.os. Ele é um local em que você vai salvar dados sobre usuários, suas postagens, etc. Usaremos um banco de dados chamado SQLite para armazenar as nossas informações. Este é o banco de dados padrão do Django -- e ele será suficiente neste primeiro momento. Ref. https://tutorial.djangogirls.org/pt/django_models/

### Criando uma aplicação
Para manter tudo arrumado, vamos criar uma aplicação separada dentro do nosso projeto. É muito bom ter tudo organizado desde o início. 


```python
python manage.py startapp livraria
```

Depois de criar uma aplicação, também precisamos dizer ao Django que ele deve usá-la. Fazemos isso no arquivo core/settings.py -- abra-o no seu editor de código. Precisamos encontrar o INSTALLED_APPS e adicionar uma linha com 'livraria', logo acima do ].


```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'livraria',
]
```

### Criando os modelos para nossa livraria

No arquivo livraria/models.py definimos todos os objetos chamados Modelos -- este é um lugar em que vamos definir os relacionamentos entre as classes que estaram presentes na nossa livraria defindos no nosso diagrama e classes.

Vamos abrir livraria/models.py no editor de código, apagar tudo dele e escrever o seguinte código:


```python
from django.db import models
from django.conf import settings

# Create your models here.

class Autor(models.Model):
    nome = models.CharField(max_length=200)

    def __str__(self):
        return self.nome 

class Categoria(models.Model):
    nome = models.CharField(max_length=200)
    descricao = models.TextField()

    def __str__(self):
        return self.nome 

class Livro(models.Model):
    nome = models.CharField(max_length=200)
    autor = models.ManyToManyField(Autor, blank = True) #relacionamento m-n muito-para-muitos
    categoria = models.ForeignKey(Categoria, on_delete=models.CASCADE, verbose_name="Categoria") #relacionamento 1-n
    codigo = models.CharField(max_length=20)
    quantidade = models.IntegerField()
    valor = models.FloatField()
    #python -m pip install Pillow
    imagem = models.ImageField(upload_to='livraria/media', blank=True)
    ano = models.IntegerField()
    descricao = models.TextField()

    def __str__(self):
        return self.nome 


```

`ManyToManyField` é a criação de um relacionamento entre tabelas muito-para-muitos.
`ForeignKey` é a criação de um relacionamento entre tabelas 1 para muitos.


Para que possamos enviar imagens precisamos instalar a biblioteca Pillow com o comando:



```python
python -m pip install Pillow
```

`imagem = models.ImageField(upload_to='livraria/media', blank=True)` criará um campo para anexarmos a imagem que desejamos vincular ao livro que está sendo cadastrado, para isso é necessário fazermos alumas configunrações, veremos com mais detalhes em sessões posteriores. 


### Criando tabelas para nossos modelos no banco de dados

O último passo é adicionar nosso novo modelo ao banco de dados. Primeiramente, precisamos fazer com que o Django entenda que fizemos algumas alterações nos nossos modelos.


```python
python manage.py makemigrations livraria
```

O Django preparou um arquivo de migração que precisamos aplicar ao nosso banco de dados. 


```python
python manage.py migrate livraria
```

## Django Admin

Para fazermos as operações de 
* Adicionar
* Editar
* Deletar 

Nas tabelas Autor, Categoria e Livro que modelamos iremos inicialmente usar o admin do Django. Vamos abrir o arquivo `livraria/admin.py`no editor de código e acrescentamos os códigos seguintes. 


```python
from django.contrib import admin
from .models import Categoria, Autor, Livro

admin.site.register(Categoria)
admin.site.register(Autor)
admin.site.register(Livro)
```

Vamos startar o servidor web 


```python
python manage.py runserver #startando o servidor
```

Vamos acessar a área do administrador do sistema que já vem prontinho para gente graças ao framework Django, para isso iremos usamos o segunte endereço no navegador de sua preferência:


```python
http://127.0.0.1:8000/admin/
```

Para fazer login, você precisa criar um `superusuário (superuser)` - uma conta de usuário que pode controlar tudo no site. 


```python
python manage.py createsuperuser
```

Quando for solicitado, insira seu nome de usuário (letras minúsculas, sem espaços), e-mail e senha. **Não se preocupe por não conseguir ver a senha que está digitando - é assim mesmo**. Digite a senha e aperte a tecla enter para continuar. 

Depois disso, volte ao seu navegador. Faça login com as credenciais de superusuário que você escolheu; você deverá ver o painel de controle de administração do Django.

## URLs 

Uma URL é um endereço da web. Você pode ver uma URL toda vez que você visita um website - ela aparece na barra de endereços do seu navegador. (Sim! 127.0.0.1:8000 é uma URL! E https://djangogirls.org também é uma URL.)

Cada página na Internet precisa de sua própria URL. Desta forma, sua aplicação sabe o que deve mostrar a um usuário que abre uma URL. Em Django, usamos algo chamado URLconf (configuração de URLs). URLconf é um conjunto de padrões que o Django vai usar para comparar com a URL recebida para encontrar a resposta correta. Ref. https://tutorial.djangogirls.org/pt/django_urls/

### Como funcionam as URLs em Django?

Abra o arquivo core/urls.py no seu editor de código preferido e veja o que aparece:



```python
"""core URL Configuration

[...]
"""
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
]
```

A URL do admin, que você visitou no capítulo anterior, já está aqui:


```python
path('admin/', admin.site.urls),
```

Isso significa que para cada URL que começa com admin/, o Django irá encontrar uma view correspondente. Neste caso nós estamos incluindo várias URLs de admin de uma vez a partir de uma lista criada pelo próprio Django em admin.site.urls. Desta forma, não temos que repetir todas URLs no nosso modesto arquivo -- é mais legível e mais limpo.

### Sua primeira URL no Django!

É hora de criar nossa primeira URL! Queremos que http://127.0.0.1:8000/ seja a página inicial da nossa livraria e exiba uma lista de livros.

Também queremos manter o arquivo de core/urls.py limpo, e portanto importaremos as URLS da nossa aplicação livraria no arquivo principal core/urls.py.

Vamos adicionar uma linha que importará livraria.urls Você também irá precisar alterar a linha from django.urls... porque nós estamos utilizando a função `include` aqui, então você precisará adicionar aquele import para a linha.

O seu arquivo `core/urls.py` deve agora se parecer com isto:


```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('livraria.urls')),
]
```

O Django agora irá redirecionar tudo o que entra em 'http://127.0.0.1:8000/' para livraria.urls e procurar por novas instruções lá.

### Criando livraria.urls

Crie um arquivo chamado `urls.py` na pasta `livraria/urls.py` do seu projeto.


```python
from django.urls import path
from . import views #arquivo views que ainda não utilizamos

urlpatterns = [
    path('', views.listar_livros, name='listar_livros'),
]
```

Como você pode ver, estamos agora atribuindo uma view chamada `listar_livros` à URL raiz. Este padrão de URL corresponde a uma sequência de caracteres vazia, e o resolvedor de URLs do Django irá ignorar o nome de domínio (ou seja, http://127.0.0.1:8000 /) que antecede o caminho completo da URL. Este padrão dirá ao Django que `views.listar_livros` é o lugar correto aonde ir se alguém entra em seu site pelo endereço 'http://127.0.0.1:8000/'.

A última parte, `name='listar_livros'`, é o nome da URL que será usado para identificar a view. Pode ser o mesmo nome da view, mas também pode ser algo completamente diferente. Nós vamos usar URLs nomeadas mais à frente, então é importante nomearmos agora todas as URLs de nossa aplicação.

Agora vamos testar a aplicação, caso tenha interrompido o servidor você pode usar o comando para starta-lo. 


```python
python manage.py runserver #startando o servidor
```

## Django views 

Uma view é o lugar onde nós colocamos a "lógica" da nossa aplicação. Ela vai extrair informações do `model` que você criou e entregá-las a um `template`. Views são apenas funções Python um pouco mais complicadas do que aquelas que criamos no capítulo Introdução ao Python.

As views são colocadas no arquivo `views.py`. Nós vamos adicionar nossas views ao arquivo `livraria/views.py`.




```python
from django.shortcuts import render

def listar_livros(request):
    return render(request, 'livraria/listar_livros.html', {})

```

Temos que criar o arquivo `listar_livros.html` para isso devemos criar a pasta `livraria\templates`, em seguida crie um diretório chamado `livraria`, 

* livraria
    * templates
        * livraria
            * listar_livros.html

No arquivo listar_livros.html, vamos escrever o seguinte código html. 


```python
<html>
    <head>
        <title>Django Livraria</title>
    </head>
    <body>
        <p>Seja bem vindo!</p>
        <p>Boas compras</p>
    </body>
</html>
```

Vamos startar o servidor web 


```python
python manage.py runserver #startando o servidor
```

## QuerySets e ORM do Django


### O que é um QuerySet?
Uma QuerySet (conjunto de busca) é, em essência, uma lista de objetos de um dado modelo. QuerySet permite que você leia os dados a partir de uma base de dados, filtre e ordene.


### O que o ORM do Django faz?
O Django ORM é uma implementação do conceito de mapeamento objeto-relacional (ORM). O ORM do Django é compatível com MySQL, PostgreSQL, SQLite e Oracle.

O ORM do Django é fornecido com construções especiais de abstração que podem ser usadas para criar consultas complexas ao banco de dados. Em outras palavras, no lugar de realizar uma ação direta no banco de dados com código SQL por exemplo, utilizamos o ORM como ponte de comunicação entre o banco e a aplicação. Ref. https://www.alura.com.br/artigos/django-query-sets-e-orm


### O Shell do Django

É um terminal que tem as configurações do Django já importadas, portanto, permite que você trabalhe diretamente da pasta raiz de um projeto Django.

Para entrar no sheel do Django digite:


```python
python manage.py shell
```

Vamos mostrar os dados já inseridos no nosso bando de dados Sqlite. 


```python
Autor.objects.all() #mostrando todos os dados da tabela Autor. Equivalente ao SELECT * FROM Autor;
```


```python
Categoria.objects.all() #mostrando todos os dados da tabela Categoria
```


```python
Livro.objects.all() #mostrando todos os dados da tabela Categoria
```

Todas as consultas vão dar erro, pois não fizemos o import das nossas classes. Para isso digitaremos a seguinte linha: 


```python
from livraria.models import Autor, Categoria, Livro #No shell temos que fazer a referencia app livraria
```


```python
Autor.objects.all() #mostrando todos os dados da tabela Autor. Equivalente ao SELECT * FROM Autor;
```

Pelo shell podemos inserir no banco de dados um Autor, Categoria ou Livro. Vamos inserir um Autor, para isso vamos lembrar da nossa classe Autor do nosso model.py


```python
"""
código omito
"""
class Autor(models.Model):
    nome = models.CharField(max_length=200)

    def __str__(self):
        return self.nome 
"""
código omito
"""
```


```python
Autor.objects.create(nome="Eric Matthes") #Equivalente a: INSERT INTO Autor (nome) VALUES (value1);
# INSERT INTO table_name (column1, column2, column3, ...) VALUES (value1, value2, value3, ...);
```


```python
Autor.objects.create(nome="Nilo Ney Coutinho Menezes")
```


```python
Autor.objects.all() #mostrando todos os dados da tabela Autor. Equivalente ao SELECT * FROM Autor;
```


```python
Categoria.objects.create(nome="Matemática")
```


```python
Categoria.objects.create(nome="Física")
```


```python
Categoria.objects.all() #mostrando todos os dados da tabela Categoria
```

Para consultar os usuários existentes no sitema tempos que importar o modelo User (classe que já dada prontinha para gente pelo Django )


```python
from django.contrib.auth.models import User
```


```python
User.objects.all()
```

### Filtrando objetos
Um recurso importante dos QuerySets é a possibilidade de filtrar. 

Caso você queira fazer uma pesquisa dos Autores que tem a letra A no nome por exemplo.


```python
Autor.objects.filter(nome__contains='A') #Equivalente a: SELECT * FROM Autor WHERE nome LIKE '%A%';
```


```python
Autor.objects.filter(nome__contains='Andrew') #Equivalente a: SELECT * FROM Autor WHERE nome LIKE '%Andrew%';
```

Digamos que queremos encontrar um livro de determinado Autor. Para isto, usamos filter ao invés de all em Livro.objects.all(), para isso vamos lembrar da nossa classe Livro do nosso model.py


```python
'''Código omitido'''

class Livro(models.Model):
    nome = models.CharField(max_length=200)
    autor = models.ManyToManyField(Autor, blank = True) #relacionamento m-n muito-para-muitos
    categoria = models.ForeignKey(Categoria, on_delete=models.CASCADE, verbose_name="Categoria") #relacionamento 1-n
    codigo = models.CharField(max_length=20)
    quantidade = models.IntegerField()
    valor = models.FloatField()
    imagem = models.ImageField(upload_to='livraria/media', blank=True)
    ano = models.IntegerField()
    descricao = models.TextField()

    def __str__(self):
        return self.nome 


```

Primeiramente devemos saber o id do Autor para assim fazermos uma busca na tabela livraria, pois o autor está na tabela livro, pórem como um relacionamento (ManyToManyField), então o que existe dentro da tabela livro do autor é apenas um id (número) o qual podemos identificar na tabela autor.


```python
autor = Autor.objects.get(id=1) #vamos conseguir o autor cujo o id = 1
```


```python
autor.id #será mostrado o id
```


```python
autor.nome #será mostrado o nome do autor sujo o id é 1
```


```python
Livro.objects.filter(autor__id=autor.id) #Selecione todos os livros do autor com o id indicado
```

### Ordenando os objetos 

Podemos ordenar uma determinada consulta de uma tabela, como a consulta em SQL: `SELECT * FROM Customers ORDER BY Country;` Ref. https://docs.djangoproject.com/en/3.1/topics/db/queries/


```python
Autor.objects.all()
```


```python
Autor.objects.order_by('nome') #SELECT * FROM Autor ORDER BY nome; 
```


```python
exit() #para sair do shell
```

## Dados dinâmicos em templates

Até o momento, temos diferentes peças: o modelo Categoria, Autor e Livro estão definidos em `models.py`, e temos listar_livros em `views.py` e o template adicionado. Mas como faremos de fato para que os dados cadastrados apareçam no nosso template em HTML? Porque é isso que nós queremos: pegar algum conteúdo (modelos salvos no banco de dados) e exibi-lo de uma maneira bacana no nosso template, certo?

E isso é exatamente o que as `views` devem fazer: conectar modelos e templates. Vamos precisar pegar os modelos que queremos exibir e passá-los para o template utilizando a view. Em uma view, nós decidimos o que (qual modelo) será exibido em um template.

No nossa app iremos criar 3 views para mostramos as nossas categorias, autores e livros.

A primeira coisa que devemos fazer é ir ao arquivo `livraria/views.py` e fazer add o importe seguinte que já vimos na aula sobre QuereSets.


```python
from livraria.models import Autor, Categoria, Livro
```


```python
'''Como ele irá ficar'''
from django.shortcuts import render
from livraria.models import Autor, Categoria, Livro

def listar_livros(request):
    return render(request, 'livraria/listar_livros.html', {})

```

Iremos criar mais duas funções:


```python
'''Como ele irá ficar'''
from django.shortcuts import render
from livraria.models import Autor, Categoria, Livro

def listar_categorias(request):
    return render(request, 'livraria/listar_categorias.html', {})

def listar_autores(request):
    return render(request, 'livraria/listar_autores.html', {})

def listar_livros(request):
    return render(request, 'livraria/listar_livros.html', {})

```

Iremos criar dois novos arquivos HTML na pasta livraria/templates/livraria.

- listar_categorias.html
- listar_autores.html

Teremos que criar também três novas rotas no arquivo livraria/urls.py


```python

from django.urls import path
from . import views #arquivo views que ainda não utilizamos

urlpatterns = [
    path('', views.listar_livros, name='listar_livros'),
    path('listar_categorias', views.listar_categorias, name='listar_categorias'), #nova url
    path('listar_autores', views.listar_autores, name='listar_autores'), #nova url
]
```

### QuerySet

Você já deve estar familiarizada com o modo que os QuerySets funcionam. Nós conversamos sobre isso no capítulo QuerySets e ORM do Django.


```python
from django.shortcuts import render
from livraria.models import Autor, Categoria, Livro

def listar_categorias(request):
    categorias = Categoria.objects.all()
    return render(request, 'livraria/listar_categorias.html', {'categorias':categorias})

def listar_autores(request):
    autores = Autor.objects.all()
    return render(request, 'livraria/listar_autores.html', {'autores':autores})

def listar_livros(request):
    livros = Livro.objscts.all()
    return render(request, 'livraria/listar_livros.html', {'livros':livros})

```

Pronto! Hora de voltar para o nosso template e exibir essa QuerySet! 


### Templates para lista de Categorias, Autores e Livros

A primeira template que iremos construir é a de categorias `livraria/templates/livraria/listar_categorias.html`. 



```python
<html>
    <head>
        <title>Django Livraria</title>
    </head>
    <body>
        <h3>Lista das nossas Categorias</h3>
        {{categorias}}
    </body>
</html>
```

Para que possamos percorrer a lista de categorias devemos utilizar um laço de repetição, para isso usaremos o for.


```python
<html>
    <head>
        <title>Django Livraria</title>
    </head>
    <body>
        <h3>Lista das nossas Categorias</h3>
        
        {% for categoria in categorias %}
        
            {{categoria.nome}}
            
        {% endfor %}
    
    </body>
</html>
```

Agora que já entendemos o procendimento de criação de templates dinâmicas podemos criar a nossa template para listar os nossos Autores. Arquivo `livraria/templates/livraria/listar_autores.html`.


```python
<html>
    <head>
        <title>Django Livraria</title>
    </head>
    <body>
        <h3>Lista dos nossos Autores</h3>    
        {% for autor in autores %}
            {{autor.nome}}   
        {% endfor %} 
    </body>
</html>
```

Você pode modificar a queryset do aquivo views e ordenar os autores por nome. 

Agora vamos modificar a template dos livros, onde iremos listar todos os livros cadastrados na nossa livraria. Arquivo `livraria/templates/livraria/listar_livros.html`.


```python
<html>
    <head>
        <title>Django Livraria</title>
    </head>
    <body>
        <p>Seja bem vindo!</p>
        <p>Boas compras</p>

        {% for livro in livros %}
            Nome: {{ livro.nome }}<br/>
            Autores: <br/>
            {% for nome in livro.autor.all %}
                {{ nome }}<br/>
            {% endfor %}
        {% endfor %}

    </body>
</html>
```

Veja que temos dois for's em encadeados 


```python
{% for livro in livros %} # ira listar todos os livros
    {% for nome in livro.autor.all %}  # ira listar todos os autores por livro
```

 - O primeiro for irá listar todos os livros.
 - O segnudo irá listar todos os autores por livro, pois podemos ter mais de um autor por livro. 


```python
<html>
    <head>
        <title>Django Livraria</title>
    </head>
    <body>
        <p>Seja bem vindo!</p>
        <p>Boas compras</p>

        {% for livro in livros %}
            Nome: {{ livro.nome }}<br/>
            Código: {{ livro.codigo }}<br/>
            Ano: {{ livro.ano }}<br/>
            Valor: {{ livro.valor }}<br/>
            <img height="300" width="200" src="{{ livro.imagem.url }}">
            Autores: <br/>
            {% for nome in livro.autor.all %}
                {{ nome }}<br/>
            {% endfor %}     
        {% endfor %}

    </body>
</html>
```

Para que possamos exibir a imagem de cada livro cadastrado temos que utilizar a tag do HTML `<img>` e em seu atributo src colocar o endereço da imagem que conseguimos acessar com livro.imagem.url, a url irá localizar o caminho da imagem na pasta `livraria/media`. 

`<img height="300" width="200" src="{{ livro.imagem.url }}">`

Outra configuração que deve ser adicionada é no arquivo `core/urls.py`, ao fim do arquivo devemos adicionar a seguinte linha que fará referência a pasta media.


```python
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

Como o arquivo `core/urls.py` irá ficar:


```python
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('livraria.urls'))
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

Agora é startar o servidor e vermos os resultados. 


```python
python manage.py runserver #startando o servidor
```

## Deixando tudo mais bonito

Está na hora de deixar ele mais bonitinho! Para isso, nós usaremos o CSS.

O que é CSS?
Cascading Style Sheets (CSS - Folhas de Estilo em Cascata, em português) é uma linguagem utilizada para descrever o visual e a formatação de um website escrito numa linguagem de marcação (como HTML). Considere como uma maquiagem para a nossa página web. :)

Mas não queremos começar do zero de novo, né? Mais uma vez, usaremos algo que outros programadores lançaram na Internet de graça. Você sabe, reinventar a roda não é divertido.

### Quick start

Vamos começar com Bootstrap, a estrutura mais popular do mundo para a criação de sites responsivos para dispositivos móveis, com jsDelivr e uma página inicial de modelo.

Ref. https://getbootstrap.com/docs/5.0/getting-started/introduction/

Iremos iniciar a estilização das páginas de forma simples utilizando apenas um link de configuração do bootstrap. Vamos introduzir na nossa página inicial no arquivo `livraria/templates/livraria/listar_livros.html`.

Dentro da tag head iremos coloca o link abaixo: 


```python
<link 
href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" 
rel="stylesheet" 
integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" c
rossorigin="anonymous">
```

A página ficará dessa forma:


```python
<html>
    <head>
        <title>Django Livraria</title>
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" 
        rel="stylesheet" 
        integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" 
        crossorigin="anonymous">
    </head>
    <body>
        <p>Seja bem vindo!</p>
        <p>Boas compras</p>

        {% for livro in livros %}
            Nome: {{ livro.nome }}<br/>
            Código: {{ livro.codigo }}<br/>
            Ano: {{ livro.ano }}<br/>
            Valor: {{ livro.valor }}<br/>
            <img height="300" width="200" src="{{ livro.imagem.url }}">
            Autores: <br/>
            {% for nome in livro.autor.all %}
                {{ nome }}<br/>
            {% endfor %}     
            <br>
        {% endfor %}

    </body>
</html>
```

O bootstrap tem várias templates prontas para uso, iremos baixar os disponíveis no site https://getbootstrap.com/docs/5.0/getting-started/download/ 

Agora iremos cria uma pasta chamada static dentro da pasta livraria. 

- livraria
    - static *
    - templates



Vamos utilizar o exemplo `navbar-fixed`, iremos também utilizar duas pasta (`css, js`) com arquivos muito importantes para a configuração das nossas páginas, presente no arquivo baixado que estão na pasta:

- assets
    - dist 
        - css
        - js


Para verificarmos a configuração das páginas com o bootstrap usaremos os arquivos dentro da pasta `navbar-fixed` como exemplo inicialmente. Copiaremos e levaremos para a nossa pasta `static`.

No arquivo livraria/listar_livros.html vamos colocar o conteúdo do arquivo index.html da pasta `navbar-fixed`, porém inicialmente vamos colocar o código abaixo para realizar o carregamento dos arquivos da pasta static.


```python
{% load static %}

<!doctype html>
<html lang="en">
    <head> 
    ''' código omitido'''
```

Outros trechos de códigos que devemos configurar são os links relacionados ao arquivo `bootstrap.min.css`, `navbar-top-fixed.css` e `bootstrap.bundle.min.js`.


```python
 ''' código omitido'''
    <!-- Bootstrap core CSS -->
    <link rel="stylesheet" href="{% static 'css/bootstrap.min.css' %}">
    <link rel="stylesheet" href="{% static 'navbar-top-fixed.css' %}">
''' código omitido'''
```

Ao fim do arquivo vamos configurar o link para o javascript. 


```python
<script  rel="stylesheet" src="{% static 'js/bootstrap.bundle.min.js' %}"></script>
```

Dentro da tag `main` iremos colocar o trecho de código que lista todos os livros cadastrados.


```python
<main class="container">
  <div class="bg-light p-5 rounded">
    <h1>Lista de Livros</h1>
        {% for livro in livros %}
            Nome: {{ livro.nome }}<br/>
            Código: {{ livro.codigo }}<br/>
            Ano: {{ livro.ano }}<br/>
            <img height="300" width="200" src="{{ livro.imagem.url }}"><br/>
            Valor: {{ livro.valor }}<br/>
            Autores: <br/>
            {% for nome in livro.autor.all %}
                {{ nome }}<br/>
            {% endfor %}     
            <br>
        {% endfor %}
  </div>
</main>
```

## Estendendo os templates
Outra coisa boa que o Django tem para você é o template extending - extensão de templates. O que isso significa? Significa que você pode usar as mesmas partes do seu HTML em diferentes páginas do seu site.

Templates ajudam quando você quer usar a mesma informação ou layout em mais de um lugar. Você não precisa se repetir em todos os arquivos. E, caso queira mudar algo, você não precisa fazê-lo em todos os templates, apenas em um!

### Criando um template base

Um template base é o template mais básico sobre o qual você construirá em cada página do seu site.

Vamos criar um arquivo `base.html` na pasta `livraria/templates/livraria/`:


```python
{% load static %}

<!doctype html>
<html lang="en">
    <head> 
    
'''código omitido'''
<main class="container">
  <div class="bg-light p-5 rounded">
      {% block content %}
        
      {% endblock %}
  </div>
</main>

'''código omitido'''
```

O arquivo `livraria/templates/livraria/listar_livros.html` fica agora da seguinte forma:


```python
{% extends 'livraria/base.html' %}

{% block content %}
    <h1>Lista de Livros</h1>
        {% for livro in livros %}
            Nome: {{ livro.nome }}<br/>
            Código: {{ livro.codigo }}<br/>
            Ano: {{ livro.ano }}<br/>
            <img height="300" width="200" src="{{ livro.imagem.url }}"><br/>
            Valor: {{ livro.valor }}<br/>
            Autores: <br/>
            {% for nome in livro.autor.all %}
                {{ nome }}<br/>
            {% endfor %}     
            <br>
        {% endfor %}
{% endblock %}
```

## Detalhando as informações sobre os Livros

Vamos começar com a edição de um link dentro do arquivo livraria/templates/livraria/listar_livros.html. Neste momento ele deve se parecer com: Abra-o no editor de código e, até agora, deve ficar assim:


```python
{% extends 'livraria/base.html' %}

{% block content %}
    <h1>Lista de Livros</h1>
        {% for livro in livros %}
            Nome: {{ livro.nome }}<br/>
            Código: {{ livro.codigo }}<br/>
            Ano: {{ livro.ano }}<br/>
                
            <a href="{% url 'detalhar_livro' id=livro.id %}">
                <img height="300" width="200" src="{{ livro.imagem.url }}">
            </a>
            
            Valor: {{ livro.valor }}<br/>
            Autores: <br/>
            {% for nome in livro.autor.all %}
                {{ nome }}<br/>
            {% endfor %}     
            <br>
        {% endfor %}
{% endblock %}
```

No arquivo `livraria/urls.py` vamos inserir uma nova rota 


```python
from django.urls import path
from . import views #arquivo views que ainda não utilizamos

urlpatterns = [
    path('', views.listar_livros, name='listar_livros'),
    path('listar_categorias', views.listar_categorias, name='listar_categorias'),
    path('listar_autores', views.listar_autores, name='listar_autores'), 
    path('livro/<int:id>/', views.detalhar_livro, name='detalhar_livro'), #nova url
]
```

Agora iremos criar uma função chamada `detalhar_livro` no arquivo `livraria/views.py` que ira receber o id do livro que o usuário irá visualizar os detalhes.


```python
from django.shortcuts import render, get_object_or_404

from livraria.models import Autor, Categoria, Livro
# Create your views here.

def detalhar_livro(request, id):
    livro = get_object_or_404(Livro, pk=id)
    return render(request, 'livraria/detalhar_livro.html', {'livro':livro})


'''código omitido'''
```

Caso façamos o chamado da `Livro.objects.get(pk=id)` iriamos gerar um erros inesperado caso não tenhamos o id cadastrado para que isso não aconteça o Django nos da a função `get_object_or_404(Livro, pk=id)` que chama get() em um determinado gerenciador de modelo (Livro), mas gera **Http404** em vez da exceção DoesNotExist do modelo. Ref. https://docs.djangoproject.com/en/3.1/topics/http/shortcuts/

Depois disso vamos criar na pasta `livriaria/templates/livraria/` o arquivo `detalhar_livro.html` que ficará da seguinte forma.


```python
{% extends 'livraria/base.html' %}

{% block content %}
    <h3>{{ livro.nome }}</h3>
        Código: {{ livro.codigo }}<br/>
        Ano: {{ livro.ano }}<br/>
        <img height="300" width="200" src="{{ livro.imagem.url }}"> 
        Valor: {{ livro.valor }}<br/>
        Autores: <br/>
        {% for nome in livro.autor.all %}
            {{ nome }}<br/>
        {% endfor %}     
            <br>
{% endblock %}
```

## Formulários do Django

Por último, queremos uma forma legal de adicionar e editar os livros casdastrados no nosso sistema. A ferramenta de administração do Django é legal, mas é um pouco difícil de personalizar e de deixar mais bonita. Com **formulários**, temos poder absoluto sobre nossa interface - podemos fazer quase tudo que pudermos imaginar!

Uma coisa legal do Django é que podemos tanto criar um formulário do zero, como criar um ModelForm que salva o resultado do formulário em um determinado modelo.

É exatamente isso que queremos fazer: criar um formulário para o nosso modelo `Livro`. 

    Ref. https://docs.djangoproject.com/en/3.1/topics/forms/
    Ref. https://docs.djangoproject.com/en/3.1/topics/forms/modelforms/
    Ref. https://tutorial.djangogirls.org/pt/django_forms/

Precisamos criar um arquivo com o nome `forms.py` dentro da pasta `livraria`. 


```python
from django import forms

from livraria.models import Livro

class LivroForm(forms.ModelForm):

    class Meta:
        model = Livro
        fields = '__all__' ##todos os campos da tabela Livro

```

Ou podemos fazer de forma mais detalhada e organizada utilizando o Bootstrap para estilizar as páginas.


```python
from django import forms

from livraria.models import Livro

class LivroForm(forms.ModelForm):

    class Meta:
        model = Livro
        fields = ('nome','autor','categoria','codigo', 
        'quantidade', 'valor', 'imagem', 'ano', 'descricao')

        widgets = {
            'nome': forms.TextInput(attrs={ 'class': 'form-control', 
                                            'placeholder':'Redes de Computadores'}),
            'autor': forms.SelectMultiple(attrs={ 'class': 'form-control'}),
            'categoria': forms.Select(attrs={ 'class': 'form-control'}),
            'codigo': forms.TextInput(attrs={ 'class': 'form-control'}),
            'quantidade': forms.TextInput(attrs={ 'class': 'form-control'}),
            'valor': forms.TextInput(attrs={ 'class': 'form-control'}),
            'imagem': forms.FileInput(attrs={ 'class': 'form-control'}),
            'ano': forms.TextInput(attrs={ 'class': 'form-control'}),
            'descricao': forms.Textarea(attrs={ 'class': 'form-control'}),
        }
```

### Link para a página com o formulário

Nós iremos criar um link no nosso menu que nós leverá para a página que contém o nosso formulário para cadastramos os livros. 

No arquivo `livraria/templates/livraria/base.html` em um dos nossos links do menu iremos adicionar o seguinte techo de código. 


```python
    <li class="nav-item">
          <a class="nav-link" href="{% url 'cadastrar_livro' %}">Cadastrar Livro</a>
    </li>
```


```python
'''código omitido'''

<nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">Livraria Django</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarCollapse" aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarCollapse">
      <ul class="navbar-nav me-auto mb-2 mb-md-0">
        <li class="nav-item">
          <a class="nav-link active" aria-current="page" href="#">Home</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="{% url 'cadastrar_livro' %}">Cadastrar Livro</a>
        </li>
        <li class="nav-item">
          <a class="nav-link disabled" href="#" tabindex="-1" aria-disabled="true">Disabled</a>
        </li>
      </ul>
      <form class="d-flex">
        <input class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
        <button class="btn btn-outline-success" type="submit">Search</button>
      </form>
    </div>
  </div>
</nav>

'''código omitido'''
```

Aproveitando a oportunidade vamos adcionar também no menu um link para retornamos a página inicial da nossa aplicação que é a lista de livros.


```python
        <li class="nav-item">
          <a class="nav-link active" aria-current="page" href="{% url 'listar_livros' %}">Home</a>
        </li>
```

### Criando a nova URL 

Vamos abrir o `livraria/urls.py` e adicionar a nova rota do nosso sistema.


```python
from django.urls import path
from . import views #arquivo views que ainda não utilizamos

urlpatterns = [
    path('', views.listar_livros, name='listar_livros'),
    path('listar_categorias', views.listar_categorias, name='listar_categorias'), 
    path('listar_autores', views.listar_autores, name='listar_autores'), 
    path('livro/<int:id>/', views.detalhar_livro, name='detalhar_livro'), 
    path('livro/new/', views.cadastrar_livro, name='cadastrar_livro'), #nova url
]
```

### Criando a view cadastrar_livro

Vamos abrir o arquivo `livraria/views.py` e adicionar a nova função no arquivo.


```python
from django.shortcuts import render, get_object_or_404
from livraria.models import Autor, Categoria, Livro
from livraria.forms import LivroForm # novo import realizado

#função cadastrar livro
def cadastrar_livro(request):
    form = LivroForm()
    return render(request, 'livraria/editar_livro.html', {'form': form})

```

### Criando a  Template editar_livro.html

Vamos criar o arquivo `livraria/templates/livraria/editar_livro.html` que deve ficar da seguinte forma. 

Estamos colocando o nome `editar_livro.html` por que iremos utilizar o formulário para fazer as duas funções, a para cadastrar e para editar um livro. 

Ref. https://docs.djangoproject.com/en/3.1/ref/csrf/


```python
{% extends 'livraria/base.html' %}

{% block content %}
    <h2>Novo livro</h2>
    <form method="POST" class="table" enctype="multipart/form-data">
    {% csrf_token %}
        <div class="form-group">
            {{ form.as_p }}
         </div>
        <button type="submit" class="save btn btn-default">Save</button>
    </form>
{% endblock %}
```

### Salvando os dados do formulário

Vamos abrir o arquivo `livraria/views.py` e alterar a nossa nova função no arquivo.


Ref. https://docs.djangoproject.com/en/3.1/topics/http/shortcuts/


```python
from django.shortcuts import render, get_object_or_404, redirect
from livraria.models import Autor, Categoria, Livro
from livraria.forms import LivroForm

def cadastrar_livro(request):
    if request.method == "POST":
        form = LivroForm(request.POST, request.FILES)
        if form.is_valid():
            livro = form.save(commit=False)
            form.save()
            return redirect('detalhar_livro', id=livro.id)
    else:
        form = LivroForm()
    return render(request, 'livraria/editar_livro.html', {'form': form})


'''código omitido'''
```

Caso você queira realizar uma restrição para salvar apenas imagens do livro no formato .png e .jpeg podemos fazer a seguinte melhoria no código. Para isso vamos utilizar a biblioteca imgdr que ficará responsável por pegar o formato da imagem. 


```python
from django.shortcuts import render, get_object_or_404, redirect
from livraria.models import Autor, Categoria, Livro
from livraria.forms import LivroForm
import imghdr #biblioteca nova

def cadastrar_livro(request):
    if request.method == "POST":
        form = LivroForm(request.POST, request.FILES)
        if form.is_valid():
            livro = form.save(commit=False)
            img = request.FILES
            dados_img = imghdr.what(img['imagem'])
            if dados_img == 'png' or dados_img == 'jpeg':
                form.save()
                return redirect('detalhar_livro', id=livro.id)
            else:
                 form = LivroForm()
                return render(request, 'livraria/editar_livro.html', {'form': form})             
    else:
        form = LivroForm()
    return render(request, 'livraria/editar_livro.html', {'form': form})
```

### Editando as informações do um livro

Vamos começar com a edição de um link dentro do arquivo `livraria/templates/livraria/listar_livros.html`. Neste momento ele deve se parecer com: Abra-o no editor de código e deixe ele dessa forma:


```python
{% extends 'livraria/base.html' %}
{% block content %}
    <table class="table">
        {% for livro in livros %}
            <tr>
                <td>
                    Nome: {{ livro.nome }}<br/>
                    Código: {{ livro.codigo }}<br/>
                    Ano: {{ livro.ano }}<br/>
                    Valor: {{ livro.valor }}<br/>
                    <a href="{% url 'detalhar_livro' id=livro.id %}">
                        <img height="300" width="200" src="{{ livro.imagem.url }}">
                    </a>
                    <br/>
                    Autores: <br/>
                    {% for nome in livro.autor.all %}
                        {{ nome }}<br/>
                    {% endfor %}
                </td>
                <td>
                    <a class="btn btn-default" href="{% url 'editar_livro' id=livro.id %}">
                        <button type="button" class="btn btn-danger">Editar</button>
                    </a>
                </td>
            </tr>
        {% endfor %}
    </table> 
{% endblock %}
```

#### Criando a nova URL editar_livro

Vamos abrir o `livraria/urls.py` e adicionar a nova rota do nosso sistema.


```python
from django.urls import path
from . import views #arquivo views que ainda não utilizamos

urlpatterns = [
    path('', views.listar_livros, name='listar_livros'),
    path('listar_categorias', views.listar_categorias, name='listar_categorias'), 
    path('listar_autores', views.listar_autores, name='listar_autores'), 
    path('livro/<int:id>/', views.detalhar_livro, name='detalhar_livro'), 
    path('livro/new/', views.cadastrar_livro, name='cadastrar_livro'), 
    path('livro/editar/<int:id>/', views.editar_livro, name='editar_livro'),#nova url
]
```

#### Criando a view cadastrar_livro

Vamos abrir o arquivo `livraria/views.py` e adicionar a nova função no arquivo.


```python
from django.shortcuts import render, get_object_or_404, redirect
from livraria.models import Autor, Categoria, Livro
from livraria.forms import LivroForm

def editar_livro(request, id):
    livro = get_object_or_404(Livro, pk=id)
    if request.method == "POST":
        form = LivroForm(request.POST, request.FILES, instance=livro)
        if form.is_valid():
            livro = form.save(commit=False)
            form.save()
            return redirect('detalhar_livro', id=livro.id)
    else:
        form = LivroForm(instance=livro)
    return render(request, 'livraria/editar_livro.html', {'form': form})



'''código omitido'''
```

Agora é startar o servidor e vermos os resultados. 


```python
python manage.py runserver #startando o servidor
```

### Buscando livros na livraria

Vamos começar com a edição de um link dentro do arquivo `livraria/templates/livraria/base.html`. Abra-o no editor de código e deixe ele dessa forma:


```python
'''código omitido'''
<form class="d-flex" action="{% url 'buscar_livro' %}" method="POST">
      {% csrf_token %}
        <input name="infor" class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
        <button class="btn btn-outline-success" type="submit">Search</button>
</form>
'''código omitido'''
```

#### Criando a nova URL buscar_livro

Vamos abrir o `livraria/urls.py` e adicionar a nova rota do nosso sistema.


```python
from django.urls import path
from . import views #arquivo views que ainda não utilizamos

urlpatterns = [
    path('', views.listar_livros, name='listar_livros'),
    path('listar_categorias', views.listar_categorias, name='listar_categorias'), 
    path('listar_autores', views.listar_autores, name='listar_autores'), 
    path('livro/<int:id>/', views.detalhar_livro, name='detalhar_livro'), 
    path('livro/novo/', views.cadastrar_livro, name='cadastrar_livro'),
    path('livro/editar/<int:id>/', views.editar_livro, name='editar_livro'),
    path('buscar_livro', views.buscar_livro, name='buscar_livro'),#nova url

]
```

#### Criando a view buscar_livro

Vamos abrir o arquivo `livraria/views.py` e adicionar a nova função no arquivo.


```python
from django.shortcuts import render, get_object_or_404, redirect
from livraria.models import Autor, Categoria, Livro
from livraria.forms import LivroForm 

def buscar_livro(request):
    infor = request.POST['infor']
    livros = Livro.objects.filter(nome__contains=infor)
    return render(request, 'livraria/listar_livros.html', {'livros':livros})

'''código omitido'''
```

## Login e logout do sistema livraria

Vamos implementar o melhor sistema de segurança ao sistema livraria. O primeiro arquivo que iremos modificar é o `livraria/templates/livraria/base.html`. Abra-o no editor de código e deixe ele dessa forma:


```python
'''código omitido''' 
    <form class="d-flex" action="{% url 'buscar_livro' %}" method="POST">
      {% csrf_token %}
        <input name="infor" class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
        <button class="btn btn-outline-success" type="submit">Search</button>
      </form>
      <ul class="nav justify-content-end">
        <li class="nav-item">
          <a class="nav-link active" aria-current="page" href="{% url 'page_login' %}">Login</a>
        </li>
      </ul>
'''código omitido'''
```

### Criando a nova URL page_login

Vamos abrir o `livraria/urls.py` e adicionar a nova rota do nosso sistema.


```python
from django.urls import path
from . import views #arquivo views que ainda não utilizamos

urlpatterns = [
    path('', views.listar_livros, name='listar_livros'),
    path('listar_categorias', views.listar_categorias, name='listar_categorias'), 
    path('listar_autores', views.listar_autores, name='listar_autores'), 
    path('livro/<int:id>/', views.detalhar_livro, name='detalhar_livro'), 
    path('livro/novo/', views.cadastrar_livro, name='cadastrar_livro'),
    path('livro/editar/<int:id>/', views.editar_livro, name='editar_livro'),
    path('buscar_livro', views.buscar_livro, name='buscar_livro'),
    #rotas do login e logout
    path('page_login', views.page_login, name='page_login'),
]
```

### Criando a view page_login

Vamos abrir o arquivo `livraria/views.py` e adicionar a nova função no arquivo. Essa função irá redirecionar o usuário do sistema para a página de login, onde iremos ter o fomulário para verificar a autenticação. 


```python
'''código omitido'''

def page_login(request):
    return render(request, 'livraria/login.html',{})

'''código omitido'''
```

### Criando o formulário de login

Bem pessoal agora vamos criar o formulário de login, onde o usuário terá que colocar as informações de cadastro dele, para o nosso login utilizaremos o username e o password do usuário. 



```python
{% extends 'livraria/base.html' %}
{% block content %}
    <form action="{% url 'autenticar_usuario' %}" method="post">
    {% csrf_token %}
        <div class="mb-3" >
            <label for="exampleInputEmail1" class="form-label">Username</label>
            <input name="username" type="text" class="form-control" id="exampleInputEmail1" aria-describedby="emailHelp">
        </div>
        <div class="mb-3">
            <label for="exampleInputPassword1" class="form-label">Password</label>
            <input name="password" type="password" class="form-control" id="exampleInputPassword1">
        </div>
    <button type="submit" class="btn btn-primary">Login</button>
    </form>
{% endblock %}
```

### Criando a rota para autenticação do usuário

Nesse momento iremos criar a rota de autenticação do usuário ao sistema, essa rota redirecionará o os dados do usuário para a nossa função que teremos no `view.py` chamada de `autenticar_usuario`. 


```python
from django.urls import path
from . import views #arquivo views que ainda não utilizamos

urlpatterns = [
    path('', views.listar_livros, name='listar_livros'),
    path('listar_categorias', views.listar_categorias, name='listar_categorias'), 
    path('listar_autores', views.listar_autores, name='listar_autores'), 
    path('livro/<int:id>/', views.detalhar_livro, name='detalhar_livro'), 
    path('livro/novo/', views.cadastrar_livro, name='cadastrar_livro'),
    path('livro/editar/<int:id>/', views.editar_livro, name='editar_livro'),
    path('buscar_livro', views.buscar_livro, name='buscar_livro'),
    #rotas do login e logout
    path('page_login', views.page_login, name='page_login'),
    path('autenticar_usuario', views.autenticar_usuario, name='autenticar_usuario'),
]
```

### Criando a view autenticar_usuario

Vamos abrir o arquivo `livraria/views.py` e adicionar a nova função no arquivo. Essa função irá verificar os dados do usuário, se ele realmente está cadastrado no sistema utilizando a função `authenticate` e `login`. 

Resumidamente a função `authenticate` ela realiza uma busca no banco no model User e verifica se os dados do usuário são válidos. A função `login` você irá apontar esse usuário para sessão atual do navegador para o sistema.

Ref. https://docs.djangoproject.com/en/3.1/topics/auth/default/


```python
from django.shortcuts import render, get_object_or_404, redirect
from livraria.models import Autor, Categoria, Livro
from livraria.forms import LivroForm 
from django.contrib.auth import authenticate, login, logout

def autenticar_usuario(request):
    username = request.POST['username']
    password = request.POST['password']
    user = authenticate(request, username=username, password=password)
    if user is not None:
        login(request, user)
        livros = Livro.objects.all()
        return render(request, 'livraria/listar_livros.html', {'livros':livros})
    else:
        return render(request, 'livraria/login.html',{})

'''código omitido'''
```

### Inserindo segurança na nossa lista de livros

Vamos começar com a edição do arquivo `livraria/templates/livraria/listar_livros.html`. Vamos colocar `if user.is_authenticated` que verificará se o usuário destá em autenticado em uma sessão no navegador. Abra-o no editor de código e deixe ele dessa forma:


```python
{% extends 'livraria/base.html' %}
{% block content %}
    {% if user.is_authenticated %}
        <h3>Hello my friend {{user.username}}</h3>
    {% else %}
        <p>Você precisa realiar o login</p>
    {% endif %}
    <table class="table">
        {% for livro in livros %}
            <tr>
                <td>
                    Nome: {{ livro.nome }}<br/>
                    Código: {{ livro.codigo }}<br/>
                    Ano: {{ livro.ano }}<br/>
                    Valor: {{ livro.valor }}<br/>
                    <a href="{% url 'detalhar_livro' id=livro.id %}">
                        <img height="300" width="200" src="{{ livro.imagem.url }}">
                    </a>
                    <br/>
                    Autores: <br/>
                    {% for nome in livro.autor.all %}
                        {{ nome }}<br/>
                    {% endfor %}
                </td>
                {% if user.is_authenticated %}
                <td>
                    <a class="btn btn-default" href="{% url 'editar_livro' id=livro.id %}">
                        <button type="button" class="btn btn-danger">Editar</button>
                    </a>
                </td>
                {% endif %}
            </tr>
        {% endfor %}
    </table> 
{% endblock %}

```

### Criando o logout




```python
{% extends 'livraria/base.html' %}
{% block content %}
    {% if user.is_authenticated %}
        <h3>Hello my friend {{user.username}}</h3>
        <a href="{% url 'logout_usuario' %}">Sair</a>
    {% else %}
        <p>Você precisa realiar o login</p>
    {% endif %}
    <table class="table">
        {% for livro in livros %}
            <tr>
                <td>
                    Nome: {{ livro.nome }}<br/>
                    Código: {{ livro.codigo }}<br/>
                    Ano: {{ livro.ano }}<br/>
                    Valor: {{ livro.valor }}<br/>
                    <a href="{% url 'detalhar_livro' id=livro.id %}">
                        <img height="300" width="200" src="{{ livro.imagem.url }}">
                    </a>
                    <br/>
                    Autores: <br/>
                    {% for nome in livro.autor.all %}
                        {{ nome }}<br/>
                    {% endfor %}
                </td>
                {% if user.is_authenticated %}
                <td>
                    <a class="btn btn-default" href="{% url 'editar_livro' id=livro.id %}">
                        <button type="button" class="btn btn-danger">Editar</button>
                    </a>
                </td>
                {% endif %}
            </tr>
        {% endfor %}
    </table> 
{% endblock %}
```

### Criando a nova URL logout_usuario

Vamos abrir o `livraria/urls.py` e adicionar a nova rota do nosso sistema. Essa nota irá redirecionar para a função de logout do sistema, onde será excluído os dados de sessão do usuário do navegador.


```python

from django.urls import path
from . import views #arquivo views que ainda não utilizamos

urlpatterns = [
    path('', views.listar_livros, name='listar_livros'),
    path('listar_categorias', views.listar_categorias, name='listar_categorias'), 
    path('listar_autores', views.listar_autores, name='listar_autores'), 
    path('livro/<int:id>/', views.detalhar_livro, name='detalhar_livro'), 
    path('livro/novo/', views.cadastrar_livro, name='cadastrar_livro'),
    path('livro/editar/<int:id>/', views.editar_livro, name='editar_livro'),
    path('buscar_livro', views.buscar_livro, name='buscar_livro'),
    #rotas do login e logout
    path('page_login', views.page_login, name='page_login'),
    path('autenticar_usuario', views.autenticar_usuario, name='autenticar_usuario'),
    path('logout_usuario', views.logout_usuario, name='logout_usuario'),
]
```

### Criando a view logout_usuario

Vamos abrir o arquivo `livraria/views.py` e adicionar a nova função no arquivo. Essa função é onde será excluído os dados de sessão do usuário do navegador, ele será desconectado do sistema.

Ref. https://docs.djangoproject.com/en/3.1/topics/auth/default/


```python
from django.shortcuts import render, get_object_or_404, redirect
from livraria.models import Autor, Categoria, Livro
from livraria.forms import LivroForm 
from django.contrib.auth import authenticate, login, logout

def logout_usuario(request):
    logout(request)
    return render(request, 'livraria/login.html',{})

'''código omitido'''
```

### Melhorando o visual do sistema de login e logout 

Nessa parte deixaremos o nosso sistema um pouco mais elegante colocando os dois botões de `Login` e `Sair` no menu principal do sistema e cada um sendo apresentando ao usuário no momento correto. 


```python
'''código omitido'''  
    <form class="d-flex" action="{% url 'buscar_livro' %}" method="POST">
      {% csrf_token %}
        <input name="infor" class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
        <button class="btn btn-outline-success" type="submit">Search</button>
      </form>
      <ul class="nav justify-content-end">
        <li class="nav-item">
        {% if user.is_authenticated %}
          <a class="nav-link active" aria-current="page" href="{% url 'logout_usuario' %}">Sair</a>
        {% else %}
          <a class="nav-link active" aria-current="page" href="{% url 'page_login' %}">Login</a>
        {% endif %}
        </li>
      </ul>
'''código omitido'''
```
