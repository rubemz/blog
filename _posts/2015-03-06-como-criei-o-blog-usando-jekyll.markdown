---
layout: post
title: "Como criei este blog usando Jekyll"
date: 2015-03-06 20:30:00
categories: blog
tags: blog, jekyll
image: /assets/article_images/2015-03-06-como-criei-o-blog-usando-jekyll/ushuaia.jpg
meta_description: "Como criei este blog usando jekyll "
meta_tags: "jekyll, blog, como criei, i18n, internationalization, jekyll, português,
portugues, markdown"
---
Já fazia algum tempo que eu estava procurando uma maneira rápida e prática de blogar.
E acabei achando um
[artigo](http://zachholman.com/posts/how-github-writes-blog-posts/) do Zach
Holman que me chamou a atenção. Ali ele detalhou como o pessoal do Github
escreve e revisa cada blog *post*.

Basicamente eles abrem um *Pull Request* para cada novo *post* e rodam alguns
testes para verificar se tem algo faltando (*image alt tags, image size*,
etc). Depois quem deseja revisa o que foi escrito e dá sugestões de melhorias.
Assim, todo esse processo de escrever um blog fica muito parecido com o de escrever
programas e rodar testes automatizados. O que é muito familiar para a maioria
dos desenvolvedores. Além disso, esse processo incentiva a colaboração e troca de
ideias antes mesmo de publicar uma nova postagem.

Tudo o que foi descrito acima fica muito mas fácil se o blog for gerado com o
[Jekyll](http://jekyllrb.com/) ou algo semelhante. Basicamente, ele é um gerador de
websites e blogs estáticos. Cada post novo é simplesmente um arquivo no formato
[markdown](http://en.wikipedia.org/wiki/Markdown) e todas as configurações
ficam em um arquivo [yaml](http://en.wikipedia.org/wiki/YAML).

##Assim nasceu esse blog

Como usar o **Jekyll** para criar um site ou um blog está muito bem
[documentado](http://jekyllrb.com/docs/home/). Então vou descrever
detalhes em particular do meu blog que acho interessante compartilhar.

##Uma versão em português outra em inglês##

Eu gostaria de ter uma versão em [português](http://rnakamura.com.br/) e uma em
[inglês](http://rnakamura.com/) do meu blog. Mas
como fazer isso usando o Jekyll?

Acabei criando um *git branch*, chamado feature/en, para a versão inglesa.
No master branch fica a versão em português.

O maior problema de usar o Jekyll para blogar em português é o fato das datas
aparecerem em inglês. Para resolver esse problema existe um *plugin* do
*liquid* que facilita muito a tradução: *i18n_filter*. Se quiser dar uma olhada
nesse *plugin* clique [aqui](https://github.com/rubemz/blog/blob/master/_plugins/i18n_filter.rb).

Além disso, crie uma pasta *_locales* e adicione o
[arquivo](https://github.com/rubemz/blog/blob/master/_locales/pt-BR.yml) *locale* pt-BR.

Para utilizar este plugin é bem simples:

{% highlight liquid %}
# en
page.date | date_to_string
# => {{ page.date | date_to_string }}

# pt-BR
page.date | localize: ":short"
# => {{ page.date | localize: ":short" }}
{% endhighlight %}

O filtro *localize* é adicionado pelo plugin *i18n_filter* e traduz a data
conforme [especificado](https://github.com/rubemz/blog/blob/master/_locales/pt-BR.yml#L206)
no arquivo *locale*. Você pode alterar o formato da data e as traduções ao seu
bel-prazer.

##Deploy##

Como coloco o blog no ar?

O processo de deploy é bem simples e é feito por um *bash script* semelhante ao
que é mostrado abaixo.

{% highlight bash %}
BRANCH=`git rev-parse --abbrev-ref HEAD`
if [ $BRANCH = 'feature/en' ]
then
  echo 'English Version'
  jekyll build
  rsync -ru -v -e ssh ./_site/* --delete user@vps:/location/en/
else
  echo 'Portuguese Version'
  jekyll build
  rsync -ru -v -e ssh ./_site/* --delete user@vps:/location/pt/
fi
{% endhighlight %}

O script verifica qual o atual *git branch* e faz o deploy da versão
portuguesa ou inglesa.

##Futuro##

Planejo adicionar algumas verificações automáticas para cada blog *post*. Fique
de olho nas próximas postagens!
