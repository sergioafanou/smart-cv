---
title : "Comment traiter les données des requêtes entrantes dans Flask"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/processing-incoming-request-data-in-flask-fr
image: "https://sergio.afanou.com/assets/images/image-midres-31.jpg"
---

<h3 id="introduction">Introduction</h3>

<p>Les applications web doivent souvent traiter les données des requêtes entrantes fournies par les utilisateurs. Cette charge utile peut prendre la forme de chaînes de requête, de données de formulaires et d'objets JSON. <a href="https://flask.palletsprojects.com/">Flask</a>, comme tout autre framework web, vous permet d'accéder aux données d'une requête.</p>

<p>Au cours de ce tutoriel, vous allez créer une application Flask avec trois itinéraires qui acceptent soit les chaînes de requête, les données de formulaire ou les objets JSON.</p>

<h2 id="conditions-préalables">Conditions préalables</h2>

<p>Pour suivre ce tutoriel, vous aurez besoin de :</p>

<ul>
<li>Pour ce projet, <a href="https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-python-3">Python devra être installé dans un environnement local</a>.</li>
<li>Ce projet utilisera <a href="https://pypi.org/project/pipenv/">Pipenv</a>, un outil prêt à l'emploi qui permet d'apporter le meilleur de tous les mondes du packaging au monde Python. Il exploite Pipfile, pip et <a href="https://virtualenv.pypa.io/en/latest/">virtualenv</a> à l'aide d'une seule commande.</li>
<li>Vous devrez télécharger et installer un outil comme <a href="https://www.getpostman.com/">Postman</a> pour tester les terminaux de l'API.</li>
</ul>

<p>Ce tutoriel a été vérifié avec Pipenv v2020.11.15, Python v3.9.0 et Flask v1.1.2.</p>

<h2 id="créer-le-projet">Créer le projet</h2>

<p>Afin de pouvoir découvrir les différentes façons d'utiliser des requêtes, vous devrez créer une application Flask. Même si l'application donnée en exemple utilise une structure simplifiée pour les fonctions et les itinéraires de visualisation, vous pouvez appliquer tout ce que vous apprendrez au cours de ce tutoriel à toute méthode qui permet d'organiser vos affichages sous forme, par exemple, d'un affichage par catégories, de plans d'action ou d'une extension comme Flask-Via.</p>

<p>Vous devrez tout d'abord créer un répertoire de projet. Ouvrez votre terminal et exécutez la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir <span class="highlight">flask_request_example</span>
</li></ul></code></pre>
<p>Ensuite, naviguez vers le nouveau répertoire :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd <span class="highlight">flask_request_example</span>
</li></ul></code></pre>
<p>Ensuite, installez Flask. Ouvrez votre terminal et exécutez la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">pipenv install Flask
</li></ul></code></pre>
<p>La commande <code>pipenv</code> créera un virtualenv pour ce projet, un Pipfile, installera <code>flask</code> et un Pipfile.lock.</p>

<p>Pour activer le virtualenv du projet, exécutez la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">pipenv shell
</li></ul></code></pre>
<p>Pour accéder aux données entrantes dans Flask, vous devez utiliser l'objet <code>request</code>. L'objet <code>request</code> contient toutes les données entrantes de la requête, qui incluent, entre autres, le typemime, le référent, l'adresse IP, les données brutes, la méthode HTTP et les en-têtes.</p>

<p>Bien que toutes les informations que contient l'objet <code>request</code> soient utiles, pour les besoins de cet article, vous allez vous concentrer sur les données qui sont normalement fournies directement par l'appelant du terminal.</p>

<p>Pour accéder à l'objet de requête dans Flask, vous devez l'importer à partir de la bibliothèque Flask :</p>
<pre class="code-pre "><code class="code-highlight language-python">from flask import request
</code></pre>
<p>Vous aurez alors la possibilité de l'utiliser dans l'une de vos fonctions d'affichage.</p>

<p>Utilisez votre éditeur de code pour créer un fichier <code>app.py</code>. Importez <code>Flask</code> et l'objet <code>request</code>. Configurez également des itinéraires pour <code>query-example</code>, <code>form-example</code> et <code>json-example</code>:</p>
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
<p>Ensuite, ouvrez votre terminal et démarrez l'application avec la commande suivante :</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="(flask_request_example)">python app.py
</li></ul></code></pre>
<p>L'application démarrera sur le port 5000. Vous pourrez alors visualiser chaque itinéraire dans votre navigateur en utilisant les liens suivants :</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example (or localhost:5000/query-example)
http://127.0.0.1:5000/form-example (or localhost:5000/form-example)
http://127.0.0.1:5000/json-example (or localhost:5000/json-example)
</code></pre>
<p>Le code établit trois itinéraires. En visualisant chaque itinéraire, les messages <code>« Requy String Example »</code>, <code>« Form Data example »</code> et <code>« JSON Object example »</code> s'afficheront respectivement.</p>

<h2 id="utiliser-les-arguments-de-requête">Utiliser les arguments de requête</h2>

<p>On utilise souvent les arguments URL que vous ajoutez à une chaîne de requête pour transmettre des données à une application web. Alors qu'en navigant sur le Web, vous rencontrerez probablement une chaîne de requête auparavant.</p>

<p>Une chaîne de requête ressemble à ce qui suit :</p>
<pre class="code-pre "><code>example.com?arg1=value1&amp;arg2=value2
</code></pre>
<p>La chaîne de requête commence après le caractère du point d'interrogation (<code>?</code>) :</p>
<pre class="code-pre "><code>example.com?<span class="highlight">arg1=value1&amp;arg2=value2</span>
</code></pre>
<p>Et intègre des paires de valeurs clés séparées par un caractère de perluète (<code>&amp;</code>) :</p>
<pre class="code-pre "><code>example.com?<span class="highlight">arg1=value1</span>&amp;<span class="highlight">arg2=value2</span>
</code></pre>
<p>Pour chaque pair, la clé est suivie du caractère égal (<code>=</code>), puis de la valeur.</p>
<pre class="code-pre "><code>arg1 : value1
arg2 : value2
</code></pre>
<p>Les chaînes de requête vous seront utiles pour transmettre les données sur lesquelles l'utilisateur n'a pas besoin d'agir. Vous pouvez générer une chaîne de requête quelque part dans votre application et l'ajouter à une URL afin que, lorsqu'un utilisateur soumet une requête, les données soient automatiquement transmises pour elles. Une chaîne de requête peut également être générée par les formulaires qui ont pour méthode GET.</p>

<p>Ajoutons une chaîne de requête à l'itinéraire <code>query-example</code>. Dans cet exemple hypothétique, vous allez renseigner le nom d'un langage de programmation qui s'affichera à l'écran. Créez une clé de <code>« langage »</code> et une valeur de <code>« Python »</code> :</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example<span class="highlight">?language=Python</span>
</code></pre>
<p>Si vous exécutez l'application et que vous naviguez vers cette URL, vous verrez qu'elle affiche toujours le message <code>« Request String Example »</code>.</p>

<p>Vous devrez alors programmer la partie qui gère les arguments de la requête. Ce code lira le contenu de la clé <code>language</code> en utilisant soit <code>request.args.get('language')</code> ou <code>request.args['language']</code>.</p>

<p>En appelant <code>request.args.get('language')</code>, l'exécution de l'application continuera si la clé <code>language</code> n'existe pas dans l'URL. Le cas échéant, la méthode affichira le résultat <code>None</code>.</p>

<p>En appelant <code>request.args['language']</code>, l'application renverra une erreur 400 si la clé de la <code>langue</code> n'existe pas dans l'URL.</p>

<p>Lorsque vous travaillez avec des chaînes de requête, nous vous recommandons d'utiliser <code>request.args.get()</code> pour empêcher l'application d'entrer en échec.</p>

<p>Lisons la clé <code>language</code> et affichons-la comme la sortie.</p>

<p>Modifiez l'itinéraire <code>query-example</code> dans <code>app.py</code> en utilisant le code suivant :</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python">@app.route('/query-example')
def query_example():
    # if key doesn't exist, returns None
    <span class="highlight">language = request.args.get('language')</span>

    <span class="highlight">return '''&lt;h1&gt;The language value is: {}&lt;/h1&gt;'''.format(language)</span>
</code></pre>
<p>Ensuite, exécutez l'application et naviguez jusqu'à l'URL :</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python
</code></pre>
<p>Le navigateur devrait afficher le message suivant :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
</code></pre>
<p>L'argument provenant de l'URL est affecté à la variable <code>language</code> et ensuite renvoyé au navigateur.</p>

<p>Pour ajouter d'autres paramètres de chaîne de requête, vous pouvez ajouter les perluètes et de nouvelles paires clé-valeur à la fin de l'URL. Créez une clé <code>« framework »</code> et une valeur <code>« Flask »</code> :</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python<span class="highlight">&amp;framework=Flask</span>
</code></pre>
<p>Et si vous en voulez plus, continuez à ajouter des perluettes et des paires clé-valeur. Créez une clé <code>« website »</code> et une valeur « <code>DigitalOcean »</code> :</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python&amp;framework=Flask<span class="highlight">&amp;website=DigitalOcean</span>
</code></pre>
<p>Pour accéder à ces valeurs, vous devrez à nouveau utiliser soit <code>request.args.get()</code> ou <code>request.args[]</code>. Utilisons-les toutes les deux pour voir ce qu'il se passe lorsqu'une clé est manquante. Modifiez l'itinéraire <code>query_example</code> pour attribuer la valeur des résultats à des variables, puis affichez-les :</p>
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
<p>Ensuite, exécutez l'application et naviguez jusqu'à l'URL :</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python&amp;framework=Flask&amp;website=DigitalOcean
</code></pre>
<p>Le navigateur devrait afficher le message suivant :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
The framework value is: Flask
The website value is: DigitalOcean
</code></pre>
<p>Supprimez la clé <code>language</code> de l'URL :</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?framework=Flask&amp;website=DigitalOcean
</code></pre>
<p>Le navigateur devrait afficher le message suivant accompagné de <code>None</code> si aucune valeur <code>language</code> n'est fournie :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: None
The framework value is: Flask
The website value is: DigitalOcean
</code></pre>
<p>Supprimez la clé <code>framework</code> de l'URL :</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python&amp;website=DigitalOcean
</code></pre>
<p>Le navigateur devrait rencontrer une erreur, étant donné qu'il s'attend à trouver une valeur pour <code>framework</code> :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>werkzeug.exceptions.BadRequestKeyError
werkzeug.exceptions.BadRequestKeyError: 400 Bad Request: The browser (or proxy) sent a request that this server could not understand.
KeyError: 'framework'
</code></pre>
<p>Vous comprenez maintenant de quelle manière sont traitées les chaînes de requête. Passons au prochain type de données entrantes.</p>

<h2 id="utiliser-les-données-de-formulaire">Utiliser les données de formulaire</h2>

<p>Les données des formulaires proviennent d'un formulaire qui a été envoyé en tant que requête POST à un itinéraire. Par conséquent, au lieu de voir les données dans l'URL (sauf si le formulaire est soumis avec une requête GET), les données du formulaire seront transmises à l'application en coulisses. Même si vous ne pouvez pas facilement voir les données du formulaire qui sont transmises, votre application est tout de même en capacité de les lire.</p>

<p>Pour le démontrer, modifiez l'itinéraire <code>form-example</code> dans <code>app.py</code> pour accepter les requêtes à la fois GET et POST. Le formulaire suivant est renvoyé :</p>
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
<p>Ensuite, exécutez l'application et naviguez jusqu'à l'URL :</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/form-example
</code></pre>
<p>Le navigateur devrait afficher un formulaire avec deux champs de saisie (un pour <code>language</code> et un pour <code>framework</code>) et un bouton Submit.</p>

<p>Le plus important à savoir sur ce formulaire, c'est qu'il effectue une requête POST sur le même itinéraire qui a généré le formulaire. Les clés qui seront lues dans l'application proviennent toutes des attributs <code>name</code> qui se trouvent sur nos entrées de formulaire. Dans ce cas, <code>langage</code> et <code>framework</code> correspondent aux noms des entrées. Vous y aurez donc accès dans l'application.</p>

<p>Dans la fonction d'affichage, vous devrez alors vérifier si la méthode de requête est GET ou POST. S'il s'agit d'une requête GET, vous pouvez afficher le formulaire. Dans le cas contraire, s’il s'agit d'une requête POST, vous devrez alors traiter les données entrantes.</p>

<p>Modifiez l'itinéraire <code>form-example</code> dans <code>app.py</code> en utilisant le code suivant :</p>
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
<p>Ensuite, exécutez l'application et naviguez jusqu'à l'URL :</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/form-example
</code></pre>
<p>Renseignez le champ <code>language</code> avec la valeur <code>Python</code> et le champ <code>framework</code> avec la valeur <code>Flask</code>. Ensuite, appuyez sur <strong>Submit</strong>.</p>

<p>Le navigateur devrait afficher le message suivant :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
The framework value is: Flask
</code></pre>
<p>Vous comprenez maintenant de quelle manière sont traitées les chaînes de requête. Passons au prochain type de données entrantes.</p>

<h2 id="utiliser-des-données-json">Utiliser des données JSON</h2>

<p>Les données JSON sont généralement construites par un processus qui appelle l'itinéraire.</p>

<p>Voici à quoi ressemble un exemple d'objet JSON :</p>
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
<p>Cette structure peut permettre la transmission de données bien plus complexes par opposition aux chaînes de requête et aux données de formulaires. Dans l'exemple, vous voyez des objets JSON imbriqués et un tableau d'éléments. Flask peut gérer ce format de données.</p>

<p>Modifiez l'itinéraire <code>form-example</code> dans <code>app.py</code> pour accepter les requêtes POST et ignorer les autres requêtes comme GET :</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python">@app.route('/json-example'<span class="highlight">, methods=['POST']</span>)
def json_example():
    return 'JSON Object Example'
</code></pre>
<p>Contrairement au navigateur web qui sert à interroger les chaînes et les données de formulaire, pour les besoins de cet article, afin d'envoyer un objet JSON, vous utiliserez <a href="https://www.getpostman.com/">Postman</a> pour envoyer des requêtes personnalisées aux URL.</p>

<p><span class='remarque'><strong>Remarque</strong> : si vous avez besoin d'aide pour naviguer sur l'interface Postman pour les requêtes, consultez <a href="https://learning.postman.com/docs/postman/sending-api-requests/requests/">la documentation officielle</a>.<br></span></p>

<p>Dans Postman, ajoutez l'URL et configurez le type sur <strong>POST</strong>. Sur l'onglet body, changez la valeur sur <strong>raw</strong> et sélectionnez <strong>JSON</strong> dans la liste déroulante.</p>

<p>Ces paramètres sont nécessaires pour permettre à Postman d'envoyer les données JSON correctement. Par conséquent, votre application Flask comprendra qu'elle reçoit JSON :</p>
<pre class="code-pre "><code>POST http://127.0.0.1:5000/json-example
Body
raw JSON
</code></pre>
<p>Ensuite, copiez l'exemple JSON précédent dans la saisie de texte.</p>

<p>Envoyez la requête. Vous devriez recevoir la réponse <code>« JSON Object Example »</code>. Il s'agit d'une approche assez étrange, mais il faudra vous y attendre, car il reste encore à écrire le code qui permet de gérer la réponse des données JSON.</p>

<p>Pour lire les données, vous devez comprendre de quelle manière Flask traduit les données JSON en structures de données Python :</p>

<ul>
<li>Tout ce qui est un objet est converti en un dict. Python <code>{"key" : "value"}</code>, dans JSON cela correspond à <code>somedict['key']</code> qui renvoie une valeur dans Python.</li>
<li>Un tableau dans JSON est converti en une liste dans Python. Étant donné que la syntaxe est la même, voici un exemple de liste : <code>[1,2,3,4,5]</code></li>
<li>Les valeurs entre crochets dans l'objet JSON deviennent des chaînes dans Python.</li>
<li>Les valeurs booléennes <code>true</code> et <code>false</code> deviennent <code>True</code> et <code>False</code> dans Python.</li>
<li>Enfin, les numéros sans parenthèse se transforment en numéros dans Python.</li>
</ul>

<p>Travaillons maintenant sur le code afin de pouvoir lire les données JSON entrantes.</p>

<p>Tout d'abord, attribuons tout ce qui se trouve dans l'objet JSON à une variable en utilisant <code>request.get_json()</code>.</p>

<p><code>request.get_json()</code> convertit l'objet JSON en données Python. Attribuons maintenant les données des requêtes entrantes aux variables et renvoyons-les en apportant les modifications suivantes à l'itinéraire <code>json-example</code> :</p>
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
<p>Notez de quelle manière vous accédez aux éléments qui ne sont pas au niveau supérieur. <code>['version']['python']</code> est utilisé, car vous êtes en train de saisir un objet imbriqué. Et <code>['example'][0]</code> permet d'accéder à l'index 0 dans le tableau des exemples.</p>

<p>Si l'objet JSON envoyé avec la requête n'est pas accessible dans votre fonction de visualisation, cela signifie que la requête échouera. Si vous ne souhaitez pas qu'elle échoue en l'absence d'une clé, il vous faudra préalablement vérifier si la clé existe avant d'essayer d'y accéder.</p>
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
<p>Exécutez l'application et envoyez l'exemple de requête JSON en utilisant Postman. Dans la réponse, vous obtiendrez la sortie suivante :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
The framework value is: Flask
The Python version is: 3.9
The item at index 0 in the example list is: query
The boolean value is: false
</code></pre>
<p>Désormais, vous comprenez de quelle manière sont manipulés les objets JSON.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Au cours de ce tutoriel, vous avez créé une application Flask avec trois itinéraires qui acceptent soit les chaînes de requête, les données de formulaire ou les objets JSON.</p>

<p>En outre, n'oubliez pas que toutes les approches ont bien pris en compte la problématique récurrente de l'échec normal généré par l'absence d'une clé.</p>

<p><span class='warning'><strong>Avertissement :</strong> le nettoyage des entrées des utilisateurs n'a pas été abordé dans cet article. Le nettoyage des entrées des utilisateurs permet de garantir que les données lues par l'application ne génèrent pas la défaillance inattendue d'un élément ou ne contournent pas les mesures de sécurité.<br></span></p>

<p>Si vous souhaitez en savoir plus sur Flask, veuillez consulter <a href="https://www.digitalocean.com/community/tags/flask">notre page thématique Flask</a> dans laquelle vous trouverez des exercices et des projets de programmation.</p>
