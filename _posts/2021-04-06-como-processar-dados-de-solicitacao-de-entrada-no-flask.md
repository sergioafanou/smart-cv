---
title : "Como processar dados de solicitação de entrada no Flask"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/processing-incoming-request-data-in-flask-pt
image: "https://sergio.afanou.com/assets/images/image-midres-41.jpg"
---

<h3 id="introdução">Introdução</h3>

<p>Muitas vezes, os aplicativos Web necessitam do processamento dos dados de solicitação da entrada de usuários. Essa carga pode existir na forma de strings de consulta, dados de formulário e objetos JSON. O <a href="https://flask.palletsprojects.com/">Flask</a>, assim como qualquer outro framework Web, permite acessar os dados de solicitação.</p>

<p>Neste tutorial, será construído um aplicativo Flask com três rotas que aceitam strings de consulta, dados de formulário ou objetos JSON.</p>

<h2 id="pré-requisitos">Pré-requisitos</h2>

<p>Para completar este tutorial, você precisará de:</p>

<ul>
<li>Este projeto exigirá o <a href="https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-python-3">Python instalado em um ambiente local</a>.</li>
<li>Este projeto usará o <a href="https://pypi.org/project/pipenv/">Pipenv</a>, uma ferramenta pronta para a produção que visa trazer o melhor de todos os mundos do empacotamento ao mundo do Python. Ele tira proveito do Pipfile, pip e o <a href="https://virtualenv.pypa.io/en/latest/">virtualenv</a> em um único comando.</li>
<li>Baixar e instalar uma ferramenta como o <a href="https://www.getpostman.com/">Postman</a> será necessário para testar os pontos de extremidade da API.</li>
</ul>

<p>Este tutorial foi verificado com o Pipenv v2020.11.15, Python v3.9.0 e o Flask v1.1.2.</p>

<h2 id="configurando-o-projeto">Configurando o projeto</h2>

<p>Para demonstrar as diferentes formas de usar solicitações, será necessário criar um aplicativo Flask. Embora o aplicativo de exemplo use uma estrutura simplificada para as funções de visualização e rotas, o que você aprenderá neste tutorial pode ser aplicado a qualquer método para organizar suas visualizações, como as visualizações baseadas em classe, blueprints, ou uma extensão como o Flask-Via.</p>

<p>Primeiramente, será necessário criar um diretório de projeto. Abra seu terminal e execute o seguinte comando:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir <span class="highlight">flask_request_example</span>
</li></ul></code></pre>
<p>Em seguida, navegue até o novo diretório:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd <span class="highlight">flask_request_example</span>
</li></ul></code></pre>
<p>Em seguida, instale o Flask. Abra seu terminal e execute o seguinte comando:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">pipenv install Flask
</li></ul></code></pre>
<p>O comando <code>pipenv</code> criará um virtualenv para este projeto, um Pipfile, e também instalará o <code>flask</code> e um Pipfile.lock.</p>

<p>Para ativar o virtualenv do projeto, execute o seguinte comando:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">pipenv shell
</li></ul></code></pre>
<p>Para acessar os dados de entrada no Flask, é necessário usar o objeto <code>request</code> (solicitação). O objeto <code>request</code> contém todos os dados de entrada da solicitação, que inclui o tipomime, referenciador, endereço IP, dados brutos, método HTTP, cabeçalhos, entre outras coisas.</p>

<p>Embora todas as informações contidas no objeto <code>request</code> possam ser úteis, para os fins deste artigo, você se concentrará nos dados que são normalmente fornecidos diretamente pelo chamador do ponto de extremidade.</p>

<p>Para ter acesso ao objeto request no Flask, será necessário importá-lo da biblioteca do Flask:</p>
<pre class="code-pre "><code class="code-highlight language-python">from flask import request
</code></pre>
<p>Depois disso, você pode usá-lo em qualquer uma das suas funções de visualização.</p>

<p>Use seu editor de código para criar um arquivo <code>app.py</code>. Importe o <code>Flask</code> e o objeto <code>request</code>. Além disso, estabeleça rotas para <code>query-example</code>, <code>form-example</code> e <code>json-example</code>:</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python"># import main Flask class and request object
from flask import Flask, request

# create the Flask app
app = Flask(__name__)

@app.route('/query-example')
def query_example():
    return 'Query String Example'

@app.route('/form-example')
def form_example():
    return 'Form Data Example'

@app.route('/json-example')
def json_example():
    return 'JSON Object Example'

if __name__ == '__main__':
    # run app in debug mode on port 5000
    app.run(debug=True, port=5000)
</code></pre>
<p>Em seguida, abra seu terminal e inicie o aplicativo com o seguinte comando:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="(flask_request_example)">python app.py
</li></ul></code></pre>
<p>O aplicativo será iniciado na porta 5000. Sendo assim, você pode visualizar cada rota no seu navegador com os seguintes links:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example (or localhost:5000/query-example)
http://127.0.0.1:5000/form-example (or localhost:5000/form-example)
http://127.0.0.1:5000/json-example (or localhost:5000/json-example)
</code></pre>
<p>O código estabelece três rotas e visitar cada rota exibirá as mensagens de <code>"Query String Example"</code>, <code>"Form Data Example"</code> e <code>"JSON Object Example"</code>, respectivamente.</p>

<h2 id="usando-argumentos-de-consulta">Usando argumentos de consulta</h2>

<p>Os argumentos de URL que você adiciona a uma string de consulta representam uma maneira comum de passar dados para um aplicativo Web. Enquanto navegava na Web, você provavelmente já se deparou com uma string de consulta antes.</p>

<p>Uma string de consulta se assemelha ao seguinte:</p>
<pre class="code-pre "><code>example.com?arg1=value1&amp;arg2=value2
</code></pre>
<p>A string de consulta começa após o caractere de ponto de interrogação (<code>?</code>):</p>
<pre class="code-pre "><code>example.com?<span class="highlight">arg1=value1&amp;arg2=value2</span>
</code></pre>
<p>E possui pares de chave-valor separados por um caractere de E comercial (<code>&amp;</code>):</p>
<pre class="code-pre "><code>example.com?<span class="highlight">arg1=value1</span>&amp;<span class="highlight">arg2=value2</span>
</code></pre>
<p>Para cada par, a chave é seguida de um caractere de sinal igual (<code>=</code>) e então o valor.</p>
<pre class="code-pre "><code>arg1 : value1
arg2 : value2
</code></pre>
<p>As strings de consulta são úteis para passar dados que não exigem que o usuário tome nenhuma ação. Se quiser, é possível gerar uma string de consulta em algum lugar do seu aplicativo e adicioná-la a uma URL para que quando um usuário fizer uma solicitação, os dados sejam automaticamente passados a eles. Uma string de consulta também pode ser gerada por formulários que têm o GET como método.</p>

<p>Vamos adicionar uma string de consulta à rota <code>query-example</code>. Neste exemplo hipotético, você adicionará o nome de uma linguagem de programação que será exibido na tela. Crie uma chave <code>"language"</code> e um valor de <code>"Python"</code>:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example<span class="highlight">?language=Python</span>
</code></pre>
<p>Se executar o aplicativo e navegar até essa URL, você verá que ele ainda exibe uma mensagem de <code>"Query String Example"</code>.</p>

<p>Será necessário programar a parte que lida com os argumentos de consulta. Esse código lerá a chave <code>language</code> usando o <code>request.args.get('language')</code> ou <code>request.args['language']</code>.</p>

<p>Se chamarmos o <code>request.args.get('language')</code>, o aplicativo continuará em execução se a chave <code>language</code> não existir na URL. Neste caso, o resultado do método será <code>None</code>.</p>

<p>Se chamarmos o <code>request.args['language']</code>, o aplicativo retornará um erro 400 se a chave <code>language</code> não existir na URL.</p>

<p>Ao lidar com strings de consulta, é recomendável usar o <code>request.args.get()</code> para evitar que o aplicativo falhe.</p>

<p>Vamos ler a chave <code>language</code> e exibi-la como um resultado.</p>

<p>Modifique a rota <code>query-example</code> no <code>app.py</code> com o seguinte código:</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python">@app.route('/query-example')
def query_example():
    # if key doesn't exist, returns None
    <span class="highlight">language = request.args.get('language')</span>

    <span class="highlight">return '''&lt;h1&gt;The language value is: {}&lt;/h1&gt;'''.format(language)</span>
</code></pre>
<p>Em seguida, execute o aplicativo e navegue até a URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python
</code></pre>
<p>O navegador exibirá a seguinte mensagem:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
</code></pre>
<p>O argumento da URL é atribuído à variável <code>language</code> e então é retornado ao navegador.</p>

<p>Para adicionar mais parâmetros à string de consulta, adicione caracteres &amp; e os novos pares de chave-valor no final da URL. Crie uma chave <code>“framework”</code> e um valor de <code>”Flask”</code>:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python<span class="highlight">&amp;framework=Flask</span>
</code></pre>
<p>E se quiser ainda mais, continue adicionando caracteres &amp; e pares de chave-valor. Crie uma chave <code>“website”</code> e um valor de <code>”DigitalOcean”</code>:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python&amp;framework=Flask<span class="highlight">&amp;website=DigitalOcean</span>
</code></pre>
<p>Para ter acesso a esses valores, você ainda usará o <code>request.args.get()</code> ou <code>request.args[]</code>. Vamos usar ambos para demonstrar o que acontece quando uma chave estiver faltando. Modifique a rota <code>query_example</code> para atribuir o valor dos resultados às variáveis e então exibi-las:</p>
<pre class="code-pre "><code class="code-highlight language-python">@app.route('/query-example')
def query_example():
    # if key doesn't exist, returns None
    language = request.args.get('language')

    # if key doesn't exist, returns a 400, bad request error
    <span class="highlight">framework = request.args['framework']</span>

    # if key doesn't exist, returns None
    <span class="highlight">website = request.args.get('website')</span>

    <span class="highlight">return '''</span>
              <span class="highlight">&lt;h1&gt;The language value is: {}&lt;/h1&gt;</span>
              <span class="highlight">&lt;h1&gt;The framework value is: {}&lt;/h1&gt;</span>
              <span class="highlight">&lt;h1&gt;The website value is: {}'''.format(language, framework, website)</span>
</code></pre>
<p>Em seguida, execute o aplicativo e navegue até a URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python&amp;framework=Flask&amp;website=DigitalOcean
</code></pre>
<p>O navegador exibirá a seguinte mensagem:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
The framework value is: Flask
The website value is: DigitalOcean
</code></pre>
<p>Remova a chave <code>language</code> da URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?framework=Flask&amp;website=DigitalOcean
</code></pre>
<p>O navegador exibirá a mensagem a seguir. O valor <code>None</code> é utilizado se um valor não for fornecido para <code>language</code>:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: None
The framework value is: Flask
The website value is: DigitalOcean
</code></pre>
<p>Remova a chave <code>framework</code> da URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python&amp;website=DigitalOcean
</code></pre>
<p>O navegador deve encontrar um erro porque está esperando um valor para <code>framework</code>:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>werkzeug.exceptions.BadRequestKeyError
werkzeug.exceptions.BadRequestKeyError: 400 Bad Request: The browser (or proxy) sent a request that this server could not understand.
KeyError: 'framework'
</code></pre>
<p>Agora, você sabe como manusear strings de consulta. Vamos continuar para o próximo tipo de dados de entrada.</p>

<h2 id="usando-dados-de-formulários">Usando dados de formulários</h2>

<p>Os dados de formulário vêm de um formulário que foi enviado como uma solicitação POST para uma rota. Assim, ao invés de ver os dados na URL (exceto para casos quando o formulário for enviado com uma solicitação GET), os dados do formulário serão passados para o aplicativo em segundo plano. Embora os dados de formulário que são passados não possam ser facilmente vistos, seu aplicativo ainda pode lê-los.</p>

<p>Para demonstrar isso, modifique a rota <code>form-example</code> no <code>app.py</code> para aceitar tanto as solicitações GET quanto POST e retornar um formulário:</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python"># allow both GET and POST requests
@app.route('/form-example'<span class="highlight">, methods=['GET', 'POST']</span>)
def form_example():
    <span class="highlight">return '''</span>
              <span class="highlight">&lt;form method="POST"&gt;</span>
                  <span class="highlight">&lt;div&gt;&lt;label&gt;Language: &lt;input type="text" name="language"&gt;&lt;/label&gt;&lt;/div&gt;</span>
                  <span class="highlight">&lt;div&gt;&lt;label&gt;Framework: &lt;input type="text" name="framework"&gt;&lt;/label&gt;&lt;/div&gt;</span>
                  <span class="highlight">&lt;input type="submit" value="Submit"&gt;</span>
              <span class="highlight">&lt;/form&gt;'''</span>
</code></pre>
<p>Em seguida, execute o aplicativo e navegue até a URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/form-example
</code></pre>
<p>O navegador exibirá um formulário com dois campos de entrada — um para <code>language</code> e um para <code>framework</code> — e um para o botão de enviar.</p>

<p>A coisa mais importante para saber sobre este formulário é que ele executa uma solicitação POST na mesma rota que gerou o formulário. Todas as chaves que serão lidas no aplicativo vêm dos atributos <code>name</code> em nossas entradas de formulários. Neste caso, <code>language</code> e <code>framework</code> são os nomes das entradas. Sendo assim, você terá acesso a eles no aplicativo.</p>

<p>Dentro da função de visualização, será necessário verificar se o método de solicitação é o GET ou POST. Se for uma solicitação GET, você pode exibir o formulário. Caso contrário, se for uma solicitação POST, então será preciso processar os dados de entrada.</p>

<p>Modifique a rota <code>form-example</code> no <code>app.py</code> com o seguinte código:</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python"># allow both GET and POST requests
@app.route('/form-example', methods=['GET', 'POST'])
def form_example():
    # handle the POST request
    <span class="highlight">if request.method == 'POST':</span>
        <span class="highlight">language = request.form.get('language')</span>
        <span class="highlight">framework = request.form.get('framework')</span>
        <span class="highlight">return '''</span>
                  <span class="highlight">&lt;h1&gt;The language value is: {}&lt;/h1&gt;</span>
                  <span class="highlight">&lt;h1&gt;The framework value is: {}&lt;/h1&gt;'''.format(language, framework)</span>

    # otherwise handle the GET request
    return '''
           &lt;form method="POST"&gt;
               &lt;div&gt;&lt;label&gt;Language: &lt;input type="text" name="language"&gt;&lt;/label&gt;&lt;/div&gt;
               &lt;div&gt;&lt;label&gt;Framework: &lt;input type="text" name="framework"&gt;&lt;/label&gt;&lt;/div&gt;
               &lt;input type="submit" value="Submit"&gt;
           &lt;/form&gt;'''
</code></pre>
<p>Em seguida, execute o aplicativo e navegue até a URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/form-example
</code></pre>
<p>Preencha o campo <code>language</code> com o valor de <code>Python</code> e o campo <code>framework</code> com o valor de <code>Flask</code>. Em seguida, pressione <strong>Submit</strong>.</p>

<p>O navegador exibirá a seguinte mensagem:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
The framework value is: Flask
</code></pre>
<p>Agora, você sabe como manusear dados de formulários. Vamos continuar para o próximo tipo de dados de entrada.</p>

<h2 id="usando-dados-de-json">Usando dados de JSON</h2>

<p>Os dados de JSON são normalmente construídos por um processo que chama a rota.</p>

<p>Um objeto JSON de exemplo se parece com este:</p>
<pre class="code-pre "><code class="code-highlight language-json">{
  "language": "Python",
  "framework": "Flask",
  "website": "Scotch",
  "version_info": {
    "python": "3.9.0",
    "flask": "1.1.2"
  },
  "examples": ["query", "form", "json"],
  "boolean_test": true
}
</code></pre>
<p>Essa estrutura permite que dados muito mais complicados sejam passados se comparada a strings de consulta e dados de formulários. No exemplo, podemos ver objetos JSON aninhados e uma matriz de itens. O Flask é capaz de manipular este formato de dados.</p>

<p>Modifique a rota <code>form-example</code> no <code>app.py</code> para aceitar solicitações POST e ignorar outras solicitações, como GET:</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python">@app.route('/json-example'<span class="highlight">, methods=['POST']</span>)
def json_example():
    return 'JSON Object Example'
</code></pre>
<p>Diferentemente do navegador Web usado para strings de consulta e dados de formulários, para os fins deste artigo, um <a href="https://www.getpostman.com/">Postman</a> será usado para enviar solicitações personalizadas para URLs de forma a enviar um objeto JSON.</p>

<p><span class='note'><strong>Nota</strong>: se precisar de ajuda para navegar pela interface do Postman para as solicitações, consulte <a href="https://learning.postman.com/docs/postman/sending-api-requests/requests/">a documentação oficial</a>.<br></span></p>

<p>No Postman, adicione a URL e altere o tipo para <strong>POST</strong>. Na guia de corpo, mude para <strong>raw</strong> e selecione <strong>JSON</strong> na lista suspensa.</p>

<p>Essas configurações são necessárias para que o Postman consiga enviar dados de JSON corretamente e para que seu aplicativo Flask compreenda que está recebendo JSON:</p>
<pre class="code-pre "><code>POST http://127.0.0.1:5000/json-example
Body
raw JSON
</code></pre>
<p>Em seguida, copie o exemplo de JSON anterior na entrada de texto.</p>

<p>Envie a solicitação e você deve obter uma resposta <code>“JSON Object Example”</code>. Isso pode ser um tanto decepcionante, mas é de se esperar, porque o código para manusear a resposta dos dados de JSON ainda não está escrito.</p>

<p>Para ler os dados, é necessário entender como o Flask traduz dados de JSON para estruturas de dados do Python:</p>

<ul>
<li>Qualquer coisa que seja um objeto é convertida para um dict do Python. <code>{“key” : ”value”}</code> em JSON corresponde a <code>somedict['key']</code>, que retorna um valor em Python.</li>
<li>Uma matriz em JSON é convertida para uma lista no Python. Como a sintaxe é a mesma, aqui está uma lista de exemplo: <code>[1,2,3,4,5]</code></li>
<li>Os valores dentro de aspas no objeto JSON se tornam strings no Python.</li>
<li>Os booleanos <code>true</code> e <code>false</code> se tornam <code>True</code> e <code>False</code> no Python.</li>
<li>Por fim, os números sem aspas em sua volta se tornam números no Python.</li>
</ul>

<p>Agora, vamos trabalhar com o código para que leia os dados de JSON de entrada.</p>

<p>Primeiramente, vamos atribuir tudo do objeto JSON a uma variável usando o <code>request.get_json()</code>.</p>

<p>O <code>request.get_json()</code> converte o objeto JSON para dados do Python. Vamos atribuir os dados de solicitação de entrada às variáveis e retorná-las fazendo as seguintes alterações na rota <code>json-example</code>:</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python"># GET requests will be blocked
@app.route('/json-example', methods=['POST'])
def json_example():
    <span class="highlight">request_data = request.get_json()</span>

    <span class="highlight">language = request_data['language']</span>
    <span class="highlight">framework = request_data['framework']</span>

    # two keys are needed because of the nested object
    <span class="highlight">python_version = request_data['version_info']['python']</span>

    # an index is needed because of the array
    <span class="highlight">example = request_data['examples'][0]</span>

    <span class="highlight">boolean_test = request_data['boolean_test']</span>

    <span class="highlight">return '''</span>
           <span class="highlight">The language value is: {}</span>
           <span class="highlight">The framework value is: {}</span>
           <span class="highlight">The Python version is: {}</span>
           <span class="highlight">The item at index 0 in the example list is: {}</span>
           <span class="highlight">The boolean value is: {}'''.format(language, framework, python_version, example, boolean_test)</span>
</code></pre>
<p>Note como os elementos que não estão no nível superior são acessados. O <code>['version']['python']</code> é usado porque você está entrando em um objeto aninhado. E o <code>['examples'][0]</code> é usado para acessar o índice 0 na matriz de exemplos.</p>

<p>Se o objeto JSON enviado com a solicitação não tiver uma chave que seja acessada na sua função de visualização, então a solicitação falhará. Se não quiser que ela falhe quando uma chave não existir, será necessário verificar se a chave existe antes de tentar acessá-la.</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python"># GET requests will be blocked
@app.route('/json-example', methods=['POST'])
def json_example():
    request_data = request.get_json()

    <span class="highlight">language = None</span>
    <span class="highlight">framework = None</span>
    <span class="highlight">python_version = None</span>
    <span class="highlight">example = None</span>
    <span class="highlight">boolean_test = None</span>

    <span class="highlight">if request_data:</span>
        <span class="highlight">if 'language' in request_data:</span>
            language = request_data['language']

        <span class="highlight">if 'framework' in request_data:</span>
            framework = request_data['framework']

        <span class="highlight">if 'version_info' in request_data:</span>
            <span class="highlight">if 'python' in request_data['version_info']:</span>
                python_version = request_data['version_info']['python']

        <span class="highlight">if 'examples' in request_data:</span>
            <span class="highlight">if (type(request_data['examples']) == list) and (len(request_data['examples']) &gt; 0):</span>
                example = request_data['examples'][0]

        <span class="highlight">if 'boolean_test' in request_data:</span>
            boolean_test = request_data['boolean_test']

    return '''
           The language value is: {}
           The framework value is: {}
           The Python version is: {}
           The item at index 0 in the example list is: {}
           The boolean value is: {}'''.format(language, framework, python_version, example, boolean_test)
</code></pre>
<p>Execute o aplicativo e envie a solicitação JSON de exemplo usando o Postman. Na resposta, o seguinte resultado será exibido:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
The framework value is: Flask
The Python version is: 3.9
The item at index 0 in the example list is: query
The boolean value is: false
</code></pre>
<p>Agora, você sabe como processar objetos JSON.</p>

<h2 id="conclusão">Conclusão</h2>

<p>Neste tutorial, você construiu um aplicativo Flask com três rotas que aceitam strings de consulta, dados de formulário ou objetos JSON.</p>

<p>Além disso, lembre-se de que todas as abordagens precisaram lidar com o fato recorrente de falharem graciosamente quando uma chave está faltando.</p>

<p><span class='warning'><strong>Aviso:</strong> um tópico que não foi abordado neste artigo é a limpeza das entradas de usuário. A limpeza das entradas de usuário garante que os dados lidos pelo aplicativo não façam com que algo falhe de maneira inesperada ou contorne medidas de segurança.<br></span></p>

<p>Se quiser aprender mais sobre o Flask, confira <a href="https://www.digitalocean.com/community/tags/flask">nossa página de tópico do Flask</a> para exercícios e projetos de programação.</p>
