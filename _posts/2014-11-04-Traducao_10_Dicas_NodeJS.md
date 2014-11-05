---
layout: post
title: 10 dicas para fazer aplicação NodeJS mais rápida
---

Texto Original: 10 Tips to Make Your Node.js Web App Faster[http://www.sitepoint.com/10-tips-make-node-js-web-app-faster/] Autor: Sandeep Panda

Tradução feita por André Nardy.

Node.js está em crescimento graças a sua natureza de eventos assíncronos. Mas, na web moderna apenas ser rápido não é suficiente. Se você está planejando desenvolver sua próxima aplicação web usando Node.js você deve tomar algumas medidas, para garantir que sua aplicação seja mais rápida que o habitual. Este artigo apresenta 10 dicas para melhor o desempenho da sua aplicação web. Então, vamos ver cada uma delas.

# 1. Execução em Paralelo

Durante a construção de aplicações web, algumas vezes você precisa fazer várias chamadas de API interna para buscar vários dados. Neste instante, pense em um dashboard de usuário. Enquanto renderiza o dashboard você pode executar as seguintes hipotéticas chamadas:

Perfil do usuário – getUserProfile().
Atividade recente – getRecentActivity().
Assinaturas - getSubscriptions().
Notificações – getNotifications().

A fim de recuperar esses detalhes você pode criar um middleware separado para cada função e anexar no dashboard de rotas. Mas o problema com essa abordagem é que uma função tem que esperar a anterior ser concluída. Uma alternativa é executar estas chamadas em paralelo.

Como todos nós sabemos Node.js é muito eficiente na execução de múltiplas funções em paralelo devido à sua natureza assíncrona. Podemos tirar vantagem disso. Como as funções que menceionei acima não dependem umas das outras, podemos executá-las em paralelo. Isso irá reduzir o número
de middlewares e melhorar a velocidade.

Para paralerizar, nós podemos usar async.js, que é um módulo Node que ajuda a minimizar o JavaScript assíncrono. Aqui é um trecho que mostra como diferentes funções podem executar em paralelo usando async.js:

{% highlight javascript  %}
function runInParallel() {
  async.parallel([
    getUserProfile,
    getRecentActivity,
    getSubscriptions,
    getNotifications
  ], function(err, results) {
	  // Esse callback será executado quando as funções terminarem
  });
}
{% endhighlight %}

Se você quiser aprender mais sobre async.js dê uma olhada na página do projeto no GitHub.

# 2. Assíncrono

O design do Node.js é uma única thread. Por causa deste fato, o código síncrono pode potencialmente travar a aplicação inteira. Por exemplo, a maioria das APIs do sistema de arquivos têm os seus homólogos síncronos. O trecho a seguir mostra como um arquivo de operação de leitura pode ser feito tanto de forma síncrona como assíncrona:

{% highlight javascript  %}
// Assíncrona
fs.readFile('file.txt', function(err, buffer) {
  var content = buffer.toString();
});
 
// Síncrona
var content = fs.readFileSync('file.txt').toString();
{% endhighlight %}

Mas se você executar por muito tempo operações de bloqueio, sua thread ficará bloqueada até a operação finalizar. Isso reduz drasticamente o desempenho da sua aplicação. Então, certifique-se sempre de usar APIs assíncronas no seus código, pelo menos em seções críticas de desempenho.
Também seja cuidadoso ao escolher módulos de terceiros. Mesmo que você tome todas as precauções para evitar código síncrono, uma biblioteca externa pode fazer uma chamada síncrona, afetando o desempenho da sua aplicação.

# 3. Use Caching

Se você está buscando algum dado que não muda frequentemente, você pode armazená-lo em cache para melhorar o desempenho. Por exemplo, veja o seguinte trecho que vai buscar os últimos posts para exibir em uma view:

{% highlight javascript  %}
var router = express.Router();
 
router.route('/latestPosts').get(function(req, res) {
  Post.getLatest(function(err, posts) {
    if (err) {
      throw err;
    }
 
    res.render('posts', { posts: posts });
  });
});
{% endhighlight %}

Se você não pública post no blog com muita frequência, você pode armazenar em cache o array de posts e limpar o cache depois de um tempo. Por exemplo, podemos usar o módulo redis para alcançar isso. Para isso, você precisa ter o Redis instalado em seu servidor. Então você pode usar um
cliente chamado node_redis para armazenar os pares chave/valor. O trecho a seguir mostra como podemos armazenar os posts em cache:

{% highlight javascript  %}
var redis = require('redis'),
    client = redis.createClient(null, null, { detect_buffers: true }),
    router = express.Router();
 
router.route('/latestPosts').get(function(req,res){
  client.get('posts', function (err, posts) {
    if (posts) {
      return res.render('posts', { posts: JSON.parse(posts) });
    }
 
    Post.getLatest(function(err, posts) {
      if (err) {
        throw err;
      }
 
      client.set('posts', JSON.stringify(posts));    
      res.render('posts', { posts: posts });
    });
  });
});
{% endhighlight %}

Então, primeiro verificamos se os posts existem no cache do Redis. Se existem, entregamos o array de posts do cache. Caso contrário, recebemos o conteúdo do BD e então armazenamos em cache. Além disso, depois de um intervalo de tempo podemos limpar o cache do Redis, para que o novo conteúdo possa ser buscado.

# 4. Use compressão gzip

Compressão com gzip pode afetar muito no desempenho da sua aplicação web. Quando um navegador compatível com gzip solicita algum recurso, o servidor pode comprimir a resposta antes de enviá-la para o navegador. Se você não usar o gzip para comprimir seu recurso estático pode levar mais tempo para o navegador buscá-lo.

Em uma aplicação com Express, você pode usar o express.static() no middleware para servir conteúdo estático. Além disso, você pode usar o middleware de compressão para comprimir e servir conteúdo estático. Aqui está um trecho que mostra como faz isso:

{% highlight javascript  %}
var compression = require('compression');
 
app.use(compression()); //use compression 
app.use(express.static(path.join(__dirname, 'public')));
{% endhighlight %}

# 5. Use renderização no Client Side quando possível

Com o aumento de poderosos frameworks client-side MVC/MVVM como AnguglarJS, Ember, Meteor, etc, tornou fácil criar aplicativos de uma única página. Basicamente, ao invés de renderização no server side você só vai expor APIs que enviam respostas JSON para o cliente. 
No client side, você pode usar um framework para consumir o JSON e exibir na interface gráfica do cliente. O envio do JSON do servidor poder economizar largura de banda e assim, melhorar a velocidade, pois você não envia marcação de layout para cada requisição. Ao invés disso você envia um JSON que é processado no client side.   

# 6. Não armazene muitos dados em Sessões

Em uma típica aplicação Express, os dados da sessão é por padrão armazenado na memória. Quando você armazena muitos dados na sessão, ele adiciona uma sobrecarga significativa para o servidor. Então, você pode mudar para algum outro tipo de armazenado para manter os dados na sessão ou tentar minimizar a quantidade de dados armazenados na sessão.

Por exemplo, quando os usuários fazem logon em sua aplicação você pode armazenar apenas o id na sessão, em vez de armazenar todo o objeto. Posteriormente, em cada requisição você pode recuperar o objeto a partir do id. Você também pode usar o MongoDB ou Redis para armazenar os dados da sessão.

# 7. Otimizando suas Queries

Suponha que você tenha um aplicativo de blogs que mostra os últimos posts na página principal. Você pode escrever algo como isto para buscar os dados usando Mongoose:

{% highlight javascript  %}
Post.find().limit(10).exec(function(err, posts) {
  //send posts to client
});
{% endhighlight %}

Mas o problema é que a função find() no Mongoose busca todos os campos de um objeto e pode haver vários campos no objeto Post que não são necessários na página principal. Por exemplo, comentários é um campo que contém um array de comentários para um determinado post. Como não apresentamos os comentários, nós podemos excluir ao buscar. Isto certamente irá melhorar a velocidade. Nós podemos otimizar a consulta acima com algo como isto:

{% highlight javascript  %}
Post.find().limit(10).exclude('comments').exec(function(err, posts) {
  //send posts to client
  //enviar posts para o cliente
});
{% endhighlight %}

# 8. Use Funções V8 Padrão

Diferentes operações em coleções (collections) como map, reduce e forEach não são suportadas por todos os navegadores. Para superar os problemas de compatibilidade dos navegadores temos usado algums bibliotecas front end no client side. Mas com Node.js, você sabe exatamente quais operações são suportadas pela engine JavaScript V8 do Google. Então, você pode usá-las diretamente para construir suas funções para a manipulação de coleções no server side.

# 9. Use nginx no front do Node

Nginx é um pequeno e leve servidor web que pode ser usado para reduzir a carga no seu servidor Node.js. Ao invés de servir arquivos estáticos do Node, você pode configurar o nginx para servir arquivos estáticos. Você também pode configurar o nginx para comprimir a resposta usando gzip para que o tamanho total da resposta seja menor. Então, se você está executando uma aplicação em produção você pode usar nginx para melhorar a velocidade.

# 10. Minificando (Minify) e Concatenando JavaScript

Finalmente, a velocidade da sua aplicação web pode aumentar usando "minificação" (minify) e concatenação de vários arquivos JS em um único arquivo. Quando o navegador encontrar um elemento TAGscriptTAG a renderização da página é bloqueada até que o script seja buscado e executado (a não ser que o atributo async seja atribuido).
Por exemplo, se sua página tem cinco arquivos JavaScript, o navegador fará cinco requisições HTTP separadas para buscá-los. O desempenho geral pode ser melhorado usando minificação e concatenação dos cincos arquivos dentro de um único arquivo. O mesmo aplica para arquivos CSS. Você pode usar uma ferramenta de build como Grunt/Gulp para minificar e concatenar seus arquivos ativos.

# Conclusão

Essas 10 dicas podem certamente melhorar a velocidade da sua aplicação web.