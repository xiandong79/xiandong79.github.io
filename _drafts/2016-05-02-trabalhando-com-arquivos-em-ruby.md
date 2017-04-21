---
layout: post
title: Trabalhando com arquivos em Ruby
date:   2016-05-02 12:48:00
categories: iniciante
---

> "You seek the one not willing to be found"

A frase acima é um verso da música Pyramid God da banda grega de death metal sinfônico  [Septic Flesh](http://www.septicflesh.com/). Esta é a banda que estou ouvindo enquanto escrevo esse post.

![alttext](https://upload.wikimedia.org/wikipedia/en/f/fa/Septicflesh-the-great-mass-artwork.jpg)

Este é meu primeiro post sobre a linguagem Ruby e nele vou falar um pouco sobre manipulçãoes de arquivo. 
A classe File da API do Ruby é uma abstração de objetos que são criados para representar um arquivo. Com essa api podemos manipular arquivos.

Você pode criar um arquivo e saber o seu tamanho.


{% highlight bash %}
[1] pry(main)> f = File.new("lyrics.txt",  "w+")                                                                                                                         
=> #<File:newfile.txt>
[2] pry(main)> f.size                                                                                                                                                     
=> 0
[3] pry(main)> f=File.open('lyrics.txt','w') do |f|                            
[3] pry(main)*   f.puts 'So that every spirit of the firmament and of the ether'
[3] pry(main)* end   

[1] pry(main)> f=File.open('lyrics.txt','r')                                                                                                                     
[2] pry(main)> f.size                                                           
=> 55



{% endhighlight %}

No primeiro parâmetro passamos o arquivo e no segundo passamos 
o  modo como iremos manipular o arquivo. As maneiras mais comuns são:

 -  r abre o arquivo somente para leitura;
 -  a abre o arquivo somente para escrita (começa a escrita no final
da última linha existente se o arquivo já existir).
 -  w abre o arquivo somente para escrita (sobrescreve todo o conteúdo do arquivo se o mesmo existir);
 - w+ abre o arquivo tanto para leitura quanto para escrita (sobrescreve todo o conteúdo do arquivo se o mesmo existir);


Agora irei criar um projeto onde vou adicionar as músicas em uma playlist.
Criando uma pasta chamda playlist e dentro dela uma pasta chamada lib.

{% highlight bash %}
mkdir -p playlist/lib 
{% endhighlight %}


A playlist vai ficar salva em um arquivo yaml.
O primeiro arquivo que vou criar é o arquivo reponsável por carregar todas as classes desse projeto.

{% highlight bash %}
cd playlist/lib 

touch load_playlist.rb
	
touch track.rb
{% endhighlight %}

Em  load_playlist.rb

{% highlight bash %}
require File.expand_path('lib/track')
require File.expand_path('lib/playlist')

{% endhighlight %}

O `require`carrega o nome dado, retornando true se obteve êxito e falso se o recurso já está carregado.

O `File.expand_path` irá nos dar um caminho absoluto. Exemplo:

{% highlight bash %}
File.expand_path("~oracle/bin")           #=> "/home/oracle/bin"
{% endhighlight %}

Dentro da pasta lib crio mais dois arquivos
{% highlight bash %}
➜  lib touch track.rb playlist.rb
➜  lib ls
load_playlist.rb  playlist.rb  track.rb
{% endhighlight %}

Em track.rb crio uma classe chamada Track. Essa classe terá as informações da faixa (nome da música, álbum, ano de lançamento e nome da banda).

{% highlight ruby %}
class Track
  def initialize name, album, release, band
    @name=name
    @band=band
    @album=album
    @release=release
  end
  
  def to_s
    "Album: #{@album}, name: #{@name}, band: #{@band}, release:  #{@release}"
  end
end

{% endhighlight %}

A classe Playlist vai salvar as faixas em um arquivo yaml, chamado playlist.yaml.

{% highlight ruby %}
class Playlist
  def save_tracks track
    File.open('playlist.yaml', 'a') do |file|
      file.puts YAML.dump track
      file.puts ""
    end	
  end
end
{% endhighlight %}


Vamos carregar o projeto e adicionar as músicas.

{% highlight bash %}

[1] pry(main)> require File.expand_path('lib/load_playlist')                                                                                                   
=> true

track1 = Track.new "Pyramid God", "The Great Mass", '2011', 'Septicflesh'
track2 = Track.new "Thanatolatria", "Life", '2012', 'Kataphero'
track3 = Track.new "O Father O Satan O Sun!", "The Satanist", '2014', 'Behemoth'

playlist = Playlist.new

playlist.save_tracks track1
playlist.save_tracks track2
playlist.save_tracks track3




{% endhighlight %}

O resultado é o arquivo playlist.yaml

{% highlight yaml %}

--- !ruby/object:Track
name: Pyramid God
band: Septicflesh
album: The Great Mass
release: '2011'

--- !ruby/object:Track
name: Thanatolatria
band: Kataphero
album: Life
release: '2012'


--- !ruby/object:Track
name: O Father O Satan O Sun!
band: Behemoth
album: The Satanist
release: '2014'


{% endhighlight %}

Pretendo fazer mais alguns posts sobre Ruby para ir consolidando meus conhecimento e documentar meu aprendizado, mas por enquanto é só isso.