---
layout: post
title: RGraph + RequireJS
---

Há alguns anos atrás descobri o [RGraph](http://www.rgraph.net/), uma biblioteca de gráficos em HTML5 muito bacana! Desde lá venho utilizando-a em diversos projetos que há necessidade de apresentação de gráficos.

Nós últimos dias eu comecei a utilizar o [RequireJS](http://requirejs.org/) para carregar minhas bibliotecas JavaScript. Só que havia um problema... o RGraph não foi
desenvolvido utilizando o conceito de [AMD](https://github.com/amdjs/amdjs-api/wiki/AMD) que é um conceito para utilizar o RequireJS.

Até descobrir que era possível carregar os módulos com RequireJS mesmo a biblioteca não tendo suporte ao AMD. Isso é possível através da funcionalidade chamada **shim** do RequireJS. Então resolvi criar um [projeto](https://github.com/anardy/rgraph-requireJS-example) no GitHub com um exemplo de utilização do RGraph com RequireJS.

O próxima passo é utilizar o otimizador [r.js](http://requirejs.org/docs/optimization.html).

Esse exemplo serve para qualquer biblioteca que não tenha suporte ao AMD.

Referências

[Artigo do Jim Cowart sobre RequireJS](http://tech.pro/blog/1561/five-helpful-tips-when-using-requirejs)

[Artigo do Jim Cowart sobre r.js](http://tech.pro/blog/1639/using-rjs-to-optimize-your-requirejs-project)
