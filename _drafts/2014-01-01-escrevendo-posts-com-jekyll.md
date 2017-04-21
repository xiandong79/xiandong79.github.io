---
layout: post
title:  Escrevendo posts com jekyll
date:   2014-01-01 20:00:00
categories: iniciante
---

> "Jamais haverá ano novo se continuar a copiar os erros dos anos velhos."

Para o primeiro post do blog vou falar da ferramenta que usei pra criá-lo.

Jekyll é um gerador de sites estaticos em Ruby e desenvolvido por 
<a href="tom.preston-werner.com">Tom Preston</a>. Ele pega um diretório de 
template que contém arquivos de texto brutos em vários formatos, 
Markdown (ou Textile) e Liquid os converte, e mostra um completo website 
estático, pronto para publicar em seu servidor web favorito. 

Instalar o Jekyll é muito fácil. Tendo o Ruby instalado siga os passos:

{% highlight bash %}
~ $ gem install jekyll
~ $ jekyll new meublog
~ $ cd meublog
~/myblog $ jekyll serve
# => Now browse to http://localhost:4000
{% endhighlight %}

Para criar um novo post, tudo que você precisa é criar um novo arquivo dentro 
do diretório _posts.
Crie o arquivo da seguinte forma:

{% highlight bash %}
YEAR-MONTH-DAY-title.MARKUP
{% endhighlight %}

Pronto! Agora você já tem um novo post no seu blog.

O Jekyll é muito leve e não usa banco de dados ou conteúdo dinâmico gerado em 
runtime. Você pode hospedar gratuitamente seu blog no 
<a href="http://pages.github.com/">GitHub Pages</a>.
