---
layout: post
title: O Guia Iniciante Coersão de Tipos
---

Texto Original: [The Beginner's Guide to Type Coercion: Data Types](http://code.tutsplus.com/articles/the-beginners-guide-to-type-coercion-data-types--cms-21913) Autor: Tom McFarlin

Tradução feita por André Nardy.

Para alguém que tem programado por um grande período de tempo, provavelmente já usou diferentes linguagens de programação. Dado o cenário de software hoje em dia, é provável que você também já tenha trabalhado com ambos os tipos de linguagens: fortemente tipada e fracamente tipada.

Isto é, você já trabalhou com linguagens de programação que exigem que você especifique o tipo de suas variáveis, quais são os retornos das funções e assim por diante, você já trabalhou com linguagem de programação que não exigem que você defina explicitamente o tipo de informação.

Se você conhece exatamente o que eu estou falando, então está série de artigos provavelmente não é para você; no entanto, se você está começando com programação ou você está começando a usar um nova linguagem que é dinamicamente tipada (ou fracamente tipada), então há um número de coisas dignas de nota do que refere-se ao trabalho com tipos de dados.

Nesta série, vamos ter o olhar de um iniciante em linguagens dinâmicas, como as variáveis são definidas, como seus tipo de dados são inferidos e são diferentes de suas contra partes estaticamente e como evitar algumas das principais armadilhas que vêm com o trabalho com essas linguagens.

# Entendendo os Tipo de Dados

Antes de realmente olhamos as armadilhas do tipo coerção e estamos propensos a experimentar as suas armadilhas, é importamente entender o que são tipos de dados e como eles variam de linguagens fortemente tipadas e linguagens dinamicamente tipadas.

# Linguagens fortemente tipadas

De um modo geral, é mais provável que você encontre linguagens que estão na família das linguagens de programação fortemente tipadas que são compiladas. Isso inclui linguagens como C e C++.

No entanto, há exeções.

Há algumas linguagens que são compiladas em forma de bytecodes ou alguma tipo de linguagem intermediária e então são processadas por um interpretador. Java é uma dessas linguagens. Essas linguagens são fortemente tipadas. Elas também são compiladas. Mas elas não são necessariamente compiladas em binários executáveis, mas o bytecode é interpretado por software terceiros.

Eu sei que soa um pouco confuso, então talvez algum código irá ajudar a esclarecer o assunto. Em linguagens fortemente tipadas, você sempre declara o tipo de dado que uma variável irá representar.

Por exemplo:

{% highlight java  %}
string example = "The quick brown fox jumps over the lazy dog.";

int number = 42;

double cash = 99.99;

boolean exists = true;
{% endhighlight %}

Embora o código pareça ser simples o suficiente para ser auto-explicativo, observe que ele mostra as variáveis que possuem strings, tipos númericos e valores booleanos.

Em linguagens fortemente tipadas, você também deve informar o tipo de retorno da função. Veja o exemplo a seguir:

{% highlight java  %}
public string getGreeting() {
    return "Hello world!";
}
 
public boolean isAlive() {
    return true;
}
 
public void doSomething() {
    for ( int i = 0; i < 10; i++ ) {
        // Perform something
    }
}
{% endhighlight %}

Observe que no exemplo acima, a última função tem um retorno do tipo void. Isto significa que a função não retorna nada. Quando nós começarmos a olhar as linguagens dinâmicas, veremos o quão diferente isso é.

Obvialmente, estes exemplos são extremamente simples, mas tudo bem, como eles são para fazer um marco: que as linguagens fortemente tipadas tem variáveis e funções que têm o seus tipos de dados explicitamente definidos.

# Linguagens Dinamicamente Tipadas

Quando vamos para as linguagens dinamicamente tipadas, há uma série de luxos que vêm com a possibilidade de definir as variáveis e criar funções.

Nós exemplos anteriores, a exemplo de variável que só pode armazenar uma string. Que significa que ela não pode conter um número flutuante (float) ou um valor booleano (boolean) - deve conter uma string. Em linguagens dicamicamente tipadas, não existe este caso.

Em vez disso, as variáveis podem referenciar uma string até o ponto de duração do ciclo de vida do programa, um inteiro em outro ponto e um valor booleano em outro ponto. Claro, isso pode ser confuso se algum tipo de padrão de codificação não for adotado, mas isso está além do escopo nesse ponto.

O ponto é variáveis definidas em linguagem dinamicamente tipadas podem referir-se a diferentes tipos de dados durante a execução de um programa.

Por exemplo:

{% highlight javascript  %}
var example;
example = "The quick brown fox jumps over the lazy dog.";
example = 42;
example = 99.99;
example = true;
{% endhighlight %}

Note que essas variáveis não têm tipos de dados - elas são simplesmente declaradas com o tipo var e então são definidas conforme a necessidade. Algumas linguagens definem variáveis de forma diferente do que você viu acima, mas o ponto não é mostrar como um linguagem é sobre outro. É para mostra como as variáveis simplesmente não se referem a um tipo especifico de dados.

As funções trabalham de forma semelhante. Ou seja, ao invés de definir o tipo de retorno do dado, basta você definir a função e retornar um valor.

{% highlight javascript  %}
function getGreeting() {
    return "Hello world!";
}
 
function isAlive() {
    return true;
}
 
function doSomething() {
 
    for ( var i = 0; i < 10; i++ ) {
        // Perform something
    }
     
}
{% endhighlight %}

De novo, diferentes linguagens exigem declarações de funções de forma diferentes (por exemplo, algumas linguagens não usam a palavra chave function, mas usam a palavra chave def), mas a essência é que você não tem que declarar explicitamente o tipo de dado que será retornado pela função.

Esta pode ser uma ferramenta realmente poderosa. Entretanto, também pode tornar difícil a leitura do código, difícil o entendimento do que a função irá retornar, e/ou difícil de saber como configurar o código externo para chamar funções (como no caso de condições, e assim por diante).

É por isso que os padrões de codificações e convenções de nomes limpos são importantes. Mais um vez, este tópico está fora do escopo desta série.

# No próximo...

Agora que já demos uma visão geral na forma com que as linguagens de programação fortemente tipadas e linguagens dinamicamente tipadas gerenciam variáveis, funções e tipos de dados em geral, nós podemos voltar nossa atenção para como o tipo de coerção funciona dentro do contexto da maioria das aplicações que são escritos em linguagens dinamicamente tipadas.

Especificamente, nós vamos olhar como podemos usar a performance para o nosso favor e nós vamos olhar como podemos introduzir erros por não ser explicitamente claros em nosso código.