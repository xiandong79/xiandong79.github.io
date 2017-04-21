---
layout: post
title:  Ambientes virtuais Python - Parte 1
date:   2014-01-03 20:00:00
categories: iniciante
---

> "A melhor maneira de se ter uma boa idéia é ter várias boas idéias".

Virtualenv é uma ferramenta para criar ambientes python isolado. Se você usa 
Linux  saiba que as distribuições dele já vem com uma versão python instalada, 
e se você não quiser comprometer seu sistema é importante que você use o 
virtualenv. Também é útil pra quem trabalha em diversos projetos ao mesmo tempo 
e com várias versões do python.

Para instalar o virtualenv globalmente basta digitar o comando abaixo:

{% highlight bash %}
~ $ [sudo] pip install virtualenv
{% endhighlight %}

Após a instalação agora é hora de criar o seu ambiente virtual.
Digite virtualenv mais o nome que você deseja para seu ambiente. 

{% highlight bash %}
~ $ virtualenv ENV
{% endhighlight %}

Esse comando irá criar um diretório chamado ENV com mais dois diretórios:

bin - executável do interpretador, o script easy_install e o arquivo activate
que será usado para “ativar” o ambiente. Quando o ambiente está “ativo” os 
executáveis dos aplicativos python são instalados aqui também.

lib – a árvore com links simbólicos e/ou cópias de todos os módulos e 
bibliotecas do Python. Quando esse ambiente está “ativo” os módulos e pacotes
serão sempre instalados dentro desse diretório.

O novo virtualenv também vem com o `pip`installer para você instalar pacotes 
adicionais.

Entre no diretório:

{% highlight bash %}
~$ cd env
{% endhighlight %}

Para ativar o ambiente use:


{% highlight bash %}
~$ source bin/activate
{% endhighlight %}

Agora você pode instalar o que você quiser sem preocupação.

## Python 3.3


O virtualenv já vem integrado na versão 3.3 do python. 

{% highlight bash %}
python3 -m venv /path/to/new/virtual/environment
{% endhighlight %}

## Referencias 

[Site Oficial do Virtualenv](http://www.virtualenv.org/en/latest/)






