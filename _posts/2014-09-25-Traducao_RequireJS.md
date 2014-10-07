---
layout: post
title: Tradução: Five Helpful Tips When Using RequireJS
       Cinco dicas úteis ao usar RequireJS
---

Texto Original: link

Tradução feita por André Nardy.

RequireJS – a "JavaScript file and module loader" – is a powerful way to organize, manage, build and load JavaScript in your web application. I've been using RequireJS for a couple of years, and while it's admittedly difficult to limit myself to only five, these five tips are things I really wished I'd known sooner rather than later. Heads up – I'm assuming you have some knowledge of RequireJS, or at least are aware of what AMD, CommonJS and script loaders are. The RequireJS site is a great resource to get familiar with both the "why" as well as the API.

RequireJS - "arquivo JavaScript e carregador de módulo" - é um poderoso organizador, gerenciador, build e carregador JavaScript em sua aplicação web. Eu tenho usado RequireJS faz uns dois anos, e enquanto é reconhecidamente difícil de me limitar a apenas cinco, essas cinco dicas são coisas que eu realmente queria ter conhecido mais cedo. Heads... - Estou assumento que você tem algum conhecimento de RequireJS ou pelo menos estão conscientes do que é AMD, CommonJS e carregadores de script. O site do RequireJS é um grande recurso para se familiarizar tanto como o "por que" bem como a API.

In this post, we're going to focus on using RequireJS itself, and not r.js (the optimizer).

Neste post, nós vamos dar foco no uso do RequireJS, e não no r.js (otimizador).

1.) Know Your Options When Defining Modules

1.) Conhecendo suas opções enquanto definimos módulos

Most people using RequireJS are familiar with this approach of defining a module:

Muitas pessoas são familiares com o uso do RequireJS está abordagem de definição de módulo:

define(["dependencyA", "dependencyB"], function(depA, depB) {
    /* module code here using depA and depB */
    /* código do módulo aqui usado depA e depB */
    return myModule;
}

98% of the time, I use the above approach. However, you have some other choices, depending on your needs. Have a module that takes no dependencies? If so, you can do this:

98% do tempo, eu uso a abordagem acima. Entretanto, você tem outras opções dependento do que você precisa. Tem um módulo que não tem dependências? Então, você pode fazer isso:

define({
    someProp: "Oooh, how interesting!",
    someProp: "Oooh, que interessante!",
    someMethod: function() {
        // do interesting work
        // fazer um trabalho interessante
        return compellingValue;
    }
});

Have a module that takes no dependencies, but you need to perform some sort of setup work (or store private state)? You can do this:

Você tem um módulo que não tem dependências, mas que você precisa para realizar algum tipo de trabalho de configuração (ou armazenar o estado privado)? Você pode fazer isso:

define(function() {
    var secretValue = "seekret";
    // do other initialization work here...
    // fazer outro trabalho de inicialização aqui...
    return {
        someProp: "Oooh, how interesting!",
        someProp: "Oooh, que interessante!",
        tellMeSecrets: function() {
            // do interesting work
            // fazer um trabalho interessante
            return secretValue;
        }
    };
});

2.) How to Work with Non-AMD Libs

2.) Como trabalhar com bibliotecas sem AMD

There are a ton of great JavaScript libraries out there. Majority of which don't follow the AMD spec, but don't worry that doesn't have to keep you from using them! Since RequireJS 2.1.0, the "shim" feature has been available, allowing you to tell RequireJS about non-AMD modules that you'd like to use inside your application and load as dependencies into other AMD modules. Let's look at a couple of examples of shimming non-AMD libs.

Existem uma grande quantidade de bibliotecas JavaScript por ae. A maioria não seguem a especificação AMD, mas não se preocupe existe uma maneira de usá-las. Desde a versão 2.1.0 do RequireJS, a feature "shim" está disponível, permitindo você usar RequireJS com módulos não AMD que você gosta de usar em suas aplicações e carregar depêndencias dentro de outro módulo AMD. Vamos ver dois exemplo de shimming com bibliotecas não AMD.

Shim Without Dependencies

Shim sem dependências

You might remember the drama that ensued when Backbone and Underscore removed AMD support. Thanks to the shim feature, it's still possible (and easy) to use a library like Underscore with RequireJS:

Você pode se lembrar do drama que foi quando o Backbone e Underscore tiraram o suporte do AMD. Obrigado shim feature, é possível (e fácil) usar uma biblioteca como Underscore com RequireJS:

require.config({
    paths: {
        underscore : "libs/underscore.min"
    },
    shim : {
        underscore : {
            exports : "_"
        } 
    }
});

You can see in the above require.config call that we are providing a path to a minified underscore.js. Below that, on the shim object, we add a property that matches the name of the path we added (underscore). The value for this property is an object that, at least, provides an exports member, which tells RequireJS what member on the global object (the window, assuming you're in a browser, of course) is the actual module value. Since Underscore adds itself to the window as _, then we make our exports value _. From now on, if an AMD module requires Underscore as a dependency, RequireJS will provide the _ value that appeared on the global object.

Você pode ver no require.config acima que nós estamos fornecendo o caminho para um underscore.js minificado. Abaixo disso, no objeto shim, nós adicionamos uma propriedade que corresponda ao nome do caminho que adicionamos (underscore). O valor para está propriedade é um objeto que, pelo menos, fornece um membro exports, que conta com RequireJS como membro no objeto global (o window, assumindo
que você está em um navagador, é claro) é o valor do módulo atual. Underscore acrescenta-se à window como _, então nós fazemos nosso valor de exports _. A partir de agora, se um módulo AMD requer Unsersocre como uma depêndencia, RequireJS irá fornecer o valor _ que apareceu no objeto global.

That's an easy example though, since Underscore doesn't have any dependencies of its own. How do we shim a non-AMD library that takes dependencies?

Esse é um exemplo simples porém, só é valido se o Underscore não possuir dependências. Como nós configuramos o shim para biblioteclas não AMD que possuem dependências?

Shim with Dependencies

Shim com dependências

Let's look at how we'd do this for Backbone since it depends on Underscore and jQuery:

Vamos ver como nós podemos 

require.config({
    paths: {
        jquery     : "libs/jquery.min",
        backbone   : "libs/backbone.min",
        underscore : "libs/underscore.min",
    },
    shim : {
        underscore : {
            exports : "_"
        },
        backbone : {
            deps    : [ "jquery", "underscore" ],
            exports : "Backbone"
        }
    }
});

Our shim configuration for Backbone has a deps property in addition to exports. The deps member is an array containing the dependency names which should be loaded before Backbone is loaded. Once those dependencies are loaded, Backbone itself will be loaded, and then RequireJS will grab the Backbone member from the global object and use that as the backbone module value.

Nossa configuração shim para Backbone tem uma propriedade deps adicionado ao exports. O membro dps é um array que contém os nomes das dependências que devem ser carregadas antes do Backbone ser carregado. Uma vez que essas dependências são carregas, o Backbone será carregado e então o RequireJS vai agarrar o membro Backbone do objeto global e ussar isso como o valor do módulo backbone.

What about CommonJS modules?

Que tal módulos CommonsJS?

Have a CommonJS module that you'd like to use with RequireJS? No problem! You can define a module, and provide a factory function which takes three arguments: require, exports and module. Most of the time you'll probably only need to worry about using the require argument (so you can omit the other two arguments in those cases). With the require argument, you can require a module using the CommonJS style syntax, like below:

Você tem um módulo CommonJS que você gostaria de usar com RequireJS? Sem problemas! Você pode definir um módulo e forneceer um fábrica
de função que recebe três argumentos: requere, exports e module. Na maioria das vezes você provavelmente só vai se preocupar com o
argumento require (então você pode omitir os outros dois argumentos nesses casos). Como o argumento require, você pode exigir um
módulo usando a sintaxe estilo CommonJS, como abaixo:

define(function(require, exports, module){
    var cjs = require('myCommonJSModule');
    return {
        findDroids: function( droids ) {
            var res = [], i = 0, len = droids.length;
            for( ; i < len; i++ ) {
                if( cjs.isDroidWeAreLookingFor( droids[i] )) {
                    res.push(droids[i]);
                }
            }
            return res;
        }
    };
});

Special note (or shameless teaser) for my upcoming r.js article: Additional CommonJS utilities are available when you use the r.js optimizer. You have the option of converting CommonJS modules to modules wrapped with define(). Just be aware that if your CommonJS module has branching logic that conditionally invokes require, then the conversion approach will not work. For example, if the myCommonJSModule above had this internally (see the comments in the snippet below to understand what would prevent this from working with RequireJS):

Esteja ciente de que seu módulo CommonJS tem lógica de ramicação que condicionalmente invoca require, então a abordagem de conversão
não irá funcionar. Por exemplo, se o myCommonJSModule acima teve isso internamente (veja os comentários no trecho abaixo para entender
o que iria impedir que isso funciona-se com RequireJS):

// inside myCommonJSModule
module.exports = {
    isDroidWeAreLookingFor : function( droid ) {
        var finder;
        // OHSNAP! The conditional logic around "require"
        // means we can't wrap it with "define" in RequireJS
        
        // A lógica condicional em torno do "require"
        // significa que não podemos envolvê-lo com "define" no RequireJS
        if(isObiWanPresent) {
            finder = require('forceFilter');
        } else {
            finder = require('normalTrooper');
        }
        return finder.find( droid );
    }
}

3.) CDN Fallbacks

While using a Content Delivery Network can give your site performance advantages, you don't want your site to be broken & unusable in the event that the CDN is unreachable. Thankfully, RequireJS makes it very easy to provide fallback paths. A normal path configuration might look something like this:

Apesar de usar um Content Delivery Network pode dar vantagens de desempenho ao seu site, você não quer que seu site fique quebrado e/ou
inutilizável no caso de um CDN esteja inacessível. Felizmente, RequireJS torna muito fácil fornecer caminhos de fallbacks. A configuração
do caminho normalmente pode ser algo como isto:

require.config({
    paths: {
        kendoui : "http://cdn.kendostatic.com/2013.2.716/js/kendo.all.min.js"
    }
});

Of course, we hope that the Kendo UI CDN never goes down, but if it does, we can fall back to a different path by modifying our configuration to look like this:

Esperamos que o Kendo UI CND nunca caia, mais se cair, nós podemos cair de volta para um caminho diferente, modificando nossa configuração
para ficar assim:

require.config({
    paths: {
        kendoui : [
            "http://cdn.kendostatic.com/2013.2.716/js/kendo.all.min",
            "libs/kendoui/kendoui.min"
        ]
    }
});

Notice that instead of just providing a string path for the kendoui value, we're now using an array of paths. With the above configuration, RequireJS will attempt to load the file from the CDN first. If that fails, it will attempt the second path. It's true that falling back will add time to how long your script takes to load, but it beats an unusable site!

Observe que ao invés de apenas fornecer o caminho de cadeia de valor do KendoUI, agora estamos usando um array de caminhos. Com a
configuração acima, RequireJS primeiro irá tentar carregar o arquivo pelo CDN. Se falhar, ele tentará o segundo caminho. É verdade
que fallback irá aumento o tempo de carregamento do site, mais é melhor que um site inutilizável!

4.) Plugins!

Perhaps one of the nicest value-adds for RequireJS is the use of loader plugins. Loader plugins provide support for various non-JavaScript assets - allowing you to require them as dependencies like any other module (for the most part). Arguably the most popular is the "text" plugin - which allows you to load a plain text file dependencies (think HTML & CSS, etc.). This can be incredibly useful for loading templates. To understand how it can be useful, let's look at the approach many people use today:

Talvez uma das coisas mais legais para acrecentar valor ao RequireJS é o uso de plugins. Plugins fornecem suporte para vários ativos
não JavaScript - permitindo que você obrigue eles como uma dependência de qualquer outro módulo (para a sua maior parte). Provavemnte
o mais popular é o plugin "text" - que permite que você carregue uma simples dependências do arquivo texto (como HTML & CSS, etc.).
Isso pode ser extramamente útil para carregamento de templates. Para entender como isso pode ser útil, vamos ver uma abordagem
que muitas pessoas usam hoje:

This snippet shows a Backbone view retrieving the contents of an underscore.js template, pre-compiling it, and using it for rendering - without RequireJS/text in the mix:

Este trecho mostra uma view Backbone recuperando o conteúdo de um template underscore.js, pré-compilado e usando para renderização
sem misturar text e RequireJS.

var MyView = Backbone.View.extend({
    initialize: function() {
        this.template = _.template( $( "generated-gif-template" ).text() );
    },
    render : function() {
        this.$el.html( this.template( this.model.toJSON() ));
    }
});

You'll notice that this view expects the template to be in the page. Many people add a script element, giving it a fake type (so that it's not evaluated), and place the template content inside it, like this:

Você irá notar que está view expects do template seja a página. Muitas pessoas adiconam o elemente script, dando um tipo de falso (assim que não é avalidado), e coloca o conteúdo do template dentro dele, como este:

<script type="text/underscore-template" id="generated-gif-template">
    <span class="glyphicon glyphicon-remove"></span>
    <a href="<%= dataURL %>" download="<%= id %>">
        <span class="glyphicon glyphicon-save"></span>
    </a>
    <img src="<%= dataURL %>" class="img-thumbnail gif-item">
    <div>
        <input style="display:none" type="text" id="fileName" value="<%= fileName %>" />
        <span id="lblFileName"><%= fileName %></span>
    </div>
</script>

Sure - this approach is doable, but it has a few drawbacks:

Claro - está abordagem é factível, mas tem alguns incovenientes:

You (typically) lose syntax highlighting and other IDE features when nesting template markup inside a script tag.

Você (tipicamente) perdem destaque de sintaxe e outros recursos da IDE quando nesting markup template dentro de uma tag.

The template must be present in any page in which it's used. In a multipage site, this means you either have multiple copies of the template floating around, or a custom build step that concats templates into each target HTML page.

O módulo deve estar presente em qualquer página em que ele é usado. Em um site de várias páginas, isso significa que você quer ter
várias cópias do template por aí, ou uma etapa de compilação personalizada que concats templates dentro de cada target HTML.

In an AMD app, storing a template in the DOM like this means that your module actually has a dependency on something that isn't explicitly passed to it. This makes your code brittle and easily breakable. Ideally, we'd like to have the template passed to our module like any other dependency.

Em um aplicativo AMD, o armazenamento de template no DOM como este significado que seu módulo tem realmente uma dependência de algo que não é explicitamente passada para ele. Isso faz com que seu código frágil seja facilmente quebrado. Idealmente, nós gostaríamos de ter o template passado para o nosso módulo como qualquer outra dependência.

That's where the "text" plugin comes in:

É aí que vem o plugin "text":

define([
    'backbone',
    'text!templates/generated-gif.html'
], function( Backbone, template ){
    return Backbone.View.extend({
        initialize: function() {
            this.template = _.template( template );
        },
        render : function() {
            this.$el.html( this.template( this.model.toJSON() ));
        }
    });
});

You can see above that we've included the path to our template, preceded with text!, which tells RequireJS to use the loader plugin by the name of "text". The text content of the template is passed into module as the template argument. We gain a few things by taking this approach:

Você pode ver acima que nós incluímos o carminho do nosso template, precedido com text!, que diz ao RequireJS usar o plugin loader com o nome de "text". O conteúdo do texto do template é passado dentro do módulo como argumento do tempalte. Nós ganhamos algumas coisas com está abordagem:

Our templates can live in their own files during development, allowing us the benefits of our IDE features (syntax highlighting, etc.), and preventing us from having to scour hundreds (or more) of lines of combined markup files looking for the correct script tag to edit.

Our templates can be treated like any other module asset - so passing them explicitly into a module makes it very clear that the module depends on that template (as opposed to hoping it exists in the DOM when the module executes).

Nossos templates podem ser tratados como qualquer outro módulo ativo - então para passá-los explicitamente em um módulo deixa muito claro que o módulo depende desse template (em oposição a esperança de que existe no DOM quando o módulo executa).

When we use r.js to build & optimize our RequireJS app, the templates can be concatenated & deployed along with all of our other modules - since the text plugin effectively wraps the template in a module define call, creating a module which returns the text value of the file.

That's just a quick glance at one plugin. You can see a list of other plugins here. To whet your appetite, consider some of these:

Isso é apenas uma rápida olhada em um plugin. Você pode ver uma lista de outros plugins aqui. Para abrir o apetite, considere alguns
destes:

i18n - localization

i18n - localização

image - allows you to load images like modules

image - permite carregar imagens como módulos

mdown - allows you to load markdown (it compiles it to HTML for you)

mdown - permite que você carregue markdown (ele é compilado para HTML para você)

font - allows you to load web fonts like modules

font - permite você carregar fontes da web como módulo

5.) Some Tricks for Troubleshooting

5.) Alguns truues para Toubleshoting (Resolução de problemas)

You can use these API calls in your code if you need to, but I've actually found them quite useful when on the console in Chrome:

Você pode usar estas chamadas de API no seu código se você precisar, mas eu acho eles muito úteis no console do Chrome:

require.defined(moduleId) - returns true if your moduleId has been defined and is ready for use.

require.defined(moduleId) - retorna verdadeiro se seus moduleId foi definido e está pronto para uso.

require.specified(moduleId) - returns true if your moduleId has been listed as a dependency by another defined module. Note that just
because this returns true doesn't mean your moduleId is ready to use (don't you just love asynchrony?).

require.specified(moduleId) - retorna verdeiro se seu moduleId foi listado como uma dependência por outra definição de módulo. Note
que somente porque o retorno é verdeiro não significa que seu moduleId está pronta para usar (não basta você amar assincrono?)

requirejs.s.contexts._.config - I learned about this from Vernon Kesner. This is technically a "back door/undocumented" call - so it
could change or disappear without warning. However it returns a very useful object full of configuration info, see below:

requirejs.s.contexts._.config - Eu aprendi sobre isso a partir de Vernon Kerner. Isto é tecnicamente uma chamada "back door/undocumented" -
por isso poderia mudar ou desaperecer sem warning. No entanto, ele retona um objeto muito útil cheio de informçãoes de configuração,
veja abaixo:

IMAGE

This is the result of calling requirejs.s.contexts._.config inside a sample gif-generation app I used to demo Web Workers at Devlink. You can see all the relevant configuration data: the base URL, the paths we've mapped, the shim configuration, etc.

Este é o resultado da chamada do requirejs.s.contexts._.config dentro do exemplo da app gif-genaration dentro de um app de exemplo gif-fenation onde eu usei para demo Web Workers
em Devlink. Você pode ver todos os dados relevantes de configuração: a URL base, os arquivos já mapeados, a configuração shim, etc.

Two other key items to know about when it comes to troubleshooting RequireJS are 'errbacks' and the requirejs.onError method:

Dois outros itens chave para conhecimento sobre quando se trata de solução de problema no RequireJS são os métodos 'errbacks' e o requirejs.onError:

RequireJS "errbacks"

When you make a require call, you can include a third argument - a callback that receives an error argument, allowing you to react to the error, instead of ultimately generating an uncaught exception. The method signature, when using "errbacks" looks like this:

Quando você faz uma chamada require, você pode incluir um terceiro algumento - um callback que recebe um agumento de erro, permitindo
com que você conheça o erro, . A assinatura do método, quando se utiliza "errbacks"  se parece com isso:

require(
    [ "backbone" ], 
    function ( Backbone ) {
        return Backbone.View.extend({ /* your magic here */ });
    }, 
    function (err) {
        /* 
            err has err.requireType (timeout, nodefine, scripterror)
            and err.requireModules (an array of module Ids/paths)

            Inside here you could requirejs.undef('backbone') to clear
            the module from require locally - and you could even redefine
            it here or fetch it from a different location (though the
            fallback approach earlier takes care of this use-case more succinctly)
        */

        /* 
            err tem err.requireType (timeout, nodefine, scripterror)
            e err.requireModules (um array de módulo Ids/paths)

            Aqui dentro você poderia usar requirejs.undef('backbone') para limpar
            o módulo de require localmente - e você poderia até mesmo redefini-la
            aqui ou buscá-la a partir de um local diferente (embora a
            abordagem de fallback cuida desse caso de uso de forma sucinta)
        */
    }
);

requirejs.onError

RequireJS has a global onError handler that will catch any errors not already handled by "errbacks".To use it, simply set it like this:

RequireJS tem um manipulador global onError que vai pegar todos os erros que ainda não foram tratados no "errbacks". Para usá-lo, basta configurá-lo como este:

requirejs.onError = function (err) {
    /* 
        err has the same info as the errback callback:
        err.requireType & err.requireModules
    */
    console.log(err.requireType);
    // Be sure to rethrow if you don't want to
    // blindly swallow exceptions here!!!
    
    // Certifique-se de relançar se você não quer
    // engolir cegamente execeções aqui!!!
};