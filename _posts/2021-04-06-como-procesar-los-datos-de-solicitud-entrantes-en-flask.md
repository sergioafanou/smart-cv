---
title : "Cómo procesar los datos de solicitud entrantes en Flask"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/processing-incoming-request-data-in-flask-es
image: "https://sergio.afanou.com/assets/images/image-midres-34.jpg"
---

<h3 id="introducción">Introducción</h3>

<p>Las aplicaciones web requieren con frecuencia el procesamiento de los datos de las solicitudes entrantes de los usuarios. Esta carga útil puede tener la forma de cadenas de consulta, datos de formulario y objetos JSON. <a href="https://flask.palletsprojects.com/">Flask</a>, como cualquier otro marco web, le permite acceder a los datos de la solicitud.</p>

<p>En este tutorial, se creará una aplicación Flask con tres rutas que aceptan cadenas de consulta, datos de formulario u objetos JSON.</p>

<h2 id="requisitos-previos">Requisitos previos</h2>

<p>Para completar este tutorial, necesitará lo siguiente:</p>

<ul>
<li>Este proyecto requerirá que <a href="https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-python-3">Python se instale en un entorno local</a>.</li>
<li>Este proyecto usará <a href="https://pypi.org/project/pipenv/">Pipenv</a>, una herramienta preparada para la producción que pretende traer lo mejor de todos los mundos de empaquetado al mundo de Python. Aprovecha Pipfile, pip, y <a href="https://virtualenv.pypa.io/en/latest/">virtualenv</a> en un solo comando.</li>
<li>Se requerirá descargar e instalar una herramienta como <a href="https://www.getpostman.com/">Postman</a> para probar los puntos finales de API.</li>
</ul>

<p>Este tutorial se verificó con Pipenv v2020.11.15, Python v3.9.0 y Flask v1.1.2.</p>

<h2 id="configuración-del-proyecto">Configuración del proyecto</h2>

<p>Para demostrar las diferentes formas de usar solicitudes, deberá crear una aplicación Flask. Aunque la aplicación de ejemplo utiliza una estructura simplificada para las funciones y las rutas de vista, lo que se aprende en este tutorial puede aplicarse a cualquier método de organización de las vistas, como las vistas basadas en clases, los planos o una extensión como Flask-Via.</p>

<p>Primero, deberá crear un directorio de proyecto. Abra su terminal y ejecute el siguiente comando:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir <span class="highlight">flask_request_example</span>
</li></ul></code></pre>
<p>Luego, diríjase al nuevo directorio:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd <span class="highlight">flask_request_example</span>
</li></ul></code></pre>
<p>Luego, instale Flask. Abra su terminal y ejecute el siguiente comando:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">pipenv install Flask
</li></ul></code></pre>
<p>El comando <code>pipenv</code> creará un virtualenv para este proyecto, un Pipfile, para instalar <code>flask</code> y un Pipfile.lock.</p>

<p>Para activar el virtualenv del proyecto, ejecute el siguiente comando:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">pipenv shell
</li></ul></code></pre>
<p>Para acceder a los datos entrantes en Flask, tiene que usar el objeto <code>request</code>. El objeto <code>request</code> contiene todos los datos entrantes de la solicitud, que incluye el mimetype, recomendante, dirección IP, datos sin procesar, HTTP y encabezados, entre otras cosas.</p>

<p>Aunque toda la información que contiene el objeto de <code>solicitud</code> puede ser útil, para los propósitos de este artículo, se centrará en los datos que normalmente son suministrados directamente por la persona que llama al punto final.</p>

<p>Para obtener acceso al objeto de solicitud en Flask, deberá importarlo desde la biblioteca Flask:</p>
<pre class="code-pre "><code class="code-highlight language-python">from flask import request
</code></pre>
<p>Luego, podrá usarlo en cualquiera de sus funciones de vista.</p>

<p>Utilice su editor de código para crear un archivo <code>app.py</code>. Importe <code>Flask</code> y el objeto <code>request</code>. Y también establezca rutas para <code>query-example</code>, <code>form-example</code>, y <code>json-example</code>:</p>
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
<p>Luego, abra su terminal e inicie la aplicación con el siguiente comando:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="(flask_request_example)">python app.py
</li></ul></code></pre>
<p>La aplicación se iniciará en el puerto 5000, por lo que puede ver cada ruta en su navegador con los siguientes enlaces:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example (or localhost:5000/query-example)
http://127.0.0.1:5000/form-example (or localhost:5000/form-example)
http://127.0.0.1:5000/json-example (or localhost:5000/json-example)
</code></pre>
<p>El código establece tres rutas y visitar cada una de ellas mostrará los mensajes de <code>"Query String Example"</code>, <code>"Form Data Example"</code> y <code>"JSON Object Example"</code>, respectivamente.</p>

<h2 id="uso-de-argumentos-query">Uso de argumentos Query</h2>

<p>Los argumentos de URL que se añaden a una cadena de consulta son una forma común de pasar los datos a una aplicación web. Al navegar por la web, es probable que haya encontrado alguna vez una cadena de consulta.</p>

<p>Una cadena de consulta se parece a lo siguiente:</p>
<pre class="code-pre "><code>example.com?arg1=value1&amp;arg2=value2
</code></pre>
<p>La cadena de consulta comienza después del carácter de signo de interrogación (<code>?</code>) :</p>
<pre class="code-pre "><code>example.com?<span class="highlight">arg1=value1&amp;arg2=value2</span>
</code></pre>
<p>Y tiene pares clave-valor separados por un carácter de &ldquo;y&rdquo; comercial (<code>&amp;</code>):</p>
<pre class="code-pre "><code>example.com?<span class="highlight">arg1=value1</span>&amp;<span class="highlight">arg2=value2</span>
</code></pre>
<p>Para cada par, la clave va seguida por un signo de igualdad (<code>=</code>) y, a continuación, el valor.</p>
<pre class="code-pre "><code>arg1 : value1
arg2 : value2
</code></pre>
<p>Las cadenas de consulta son útiles para pasar datos que no requieren que el usuario realice ninguna acción. Puede generar una cadena de consulta en algún lugar de su aplicación y añadirla a una URL para que cuando un usuario realice una solicitud, los datos se pasen automáticamente para ellos. Una cadena de consulta también puede generarse a través de formularios que tienen GET como método.</p>

<p>Vamos a añadir una cadena de consulta a la ruta <code>query-example</code>. En este ejemplo hipotético, se proporcionará el nombre de un lenguaje de programación que se mostrará en la pantalla. Cree una clave de <code>"language"</code> y un valor de <code>"Python"</code>:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example<span class="highlight">?language=Python</span>
</code></pre>
<p>Si ejecuta la aplicación y navega a esa URL, verá que sigue mostrando un mensaje de <code>"Query String Example"</code>.</p>

<p>Tendrá que programar la parte que maneja los argumentos de la consulta. Este código leerá la clave de <code>language</code> usando <code>request.args.get('language')</code> o <code>request.args['language']</code>.</p>

<p>Al invocar <code>request.args.get('language')</code>, la aplicación continuará ejecutándose si la clave de <code>language</code> no existe en la URL. En ese caso, el resultado del método será <code>None</code>.</p>

<p>Al invocar <code>request.args['language']</code>, la aplicación mostrará un error 400 si la clave de <code>language</code> no existe en la URL.</p>

<p>Cuando se trata de cadenas de consulta, se recomienda usar <code>request.args.get()</code> para evitar que la aplicación falle.</p>

<p>Vamos a leer la clave <code>language</code> y mostrarla como resultado.</p>

<p>Modifique la ruta <code>query-example</code> en <code>app.py</code> con el siguiente código:</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python">@app.route('/query-example')
def query_example():
    # if key doesn't exist, returns None
    <span class="highlight">language = request.args.get('language')</span>

    <span class="highlight">return '''&lt;h1&gt;The language value is: {}&lt;/h1&gt;'''.format(language)</span>
</code></pre>
<p>Luego, ejecute la aplicación y diríjase a la URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python
</code></pre>
<p>El navegador debe mostrar el siguiente mensaje:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
</code></pre>
<p>El argumento de la URL se asigna a la variable <code>language</code> y luego se devuelve al navegador.</p>

<p>Para añadir más parámetros de cadena de consulta, puede añadir ampersands y los nuevos pares clave-valor al final de la URL. Cree una clave de <code>"framework"</code> y un valor de <code>"Flask"</code>:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python<span class="highlight">&amp;framework=Flask</span>
</code></pre>
<p>Y si desea más, continúe añadiendo ampersands y pares clave-valor. Cree una clave de <code>"website"</code> y un valor de <code>"DigitalOcean"</code>:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python&amp;framework=Flask<span class="highlight">&amp;website=DigitalOcean</span>
</code></pre>
<p>Para obtener acceso a esos valores, seguirá usando <code>request.args.get()</code> o <code>request.args[]</code>. Vamos a usar ambos para demostrar lo que sucede cuando falta una clave. Modifique la ruta <code>query_example</code> para asignar el valor de los resultados a variables y luego mostrarlos:</p>
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
<p>Luego, ejecute la aplicación y diríjase a la URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python&amp;framework=Flask&amp;website=DigitalOcean
</code></pre>
<p>El navegador debe mostrar el siguiente mensaje:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
The framework value is: Flask
The website value is: DigitalOcean
</code></pre>
<p>Elimine la clave de <code>language</code> de la URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?framework=Flask&amp;website=DigitalOcean
</code></pre>
<p>El navegador debe mostrar el siguiente mensaje con <code>None</code> cuando no se proporciona un valor para <code>language</code>:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: None
The framework value is: Flask
The website value is: DigitalOcean
</code></pre>
<p>Elimine la clave de <code>framework</code> de la URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python&amp;website=DigitalOcean
</code></pre>
<p>El navegador debería encontrar un error porque está esperando un valor para <code>framework</code>:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>werkzeug.exceptions.BadRequestKeyError
werkzeug.exceptions.BadRequestKeyError: 400 Bad Request: The browser (or proxy) sent a request that this server could not understand.
KeyError: 'framework'
</code></pre>
<p>Ahora, ya comprende cómo se manejan las cadenas de consulta. Continuemos con el siguiente tipo de datos entrantes.</p>

<h2 id="uso-de-datos-de-formulario">Uso de datos de formulario</h2>

<p>Los datos de formulario provienen de un formulario que ha sido enviado como solicitud POST a una ruta. Por lo tanto, en lugar de ver los datos en la URL (excepto en los casos en que el formulario se envía con una solicitud GET), los datos del formulario pasarán a la aplicación entre bastidores. Aunque no se pueden ver fácilmente los datos del formulario que se pasan, su aplicación puede leerlos.</p>

<p>Para demostrarlo, modifique la ruta de <code>form-example</code> en <code>app.py</code> para aceptar las solicitudes GET y POST, y devolver un formulario:</p>
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
<p>Luego, ejecute la aplicación y diríjase a la URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/form-example
</code></pre>
<p>El navegador debe mostrar un formulario con dos campos de entrada, uno para <code>language</code> y otro para <code>framework</code>, así como para un botón de envío.</p>

<p>Lo más importante que hay que saber sobre este formulario es que realiza una solicitud POST a la misma ruta que generó el formulario. Las claves que se leerán en la aplicación provienen de los atributos de <code>nombre</code> de las entradas de nuestro formulario. En este caso, <code>language</code> y <code>framework</code> son los nombres de las entradas, por lo que tendrá acceso a ellos en la aplicación.</p>

<p>Dentro de la función de vista, deberá verificar si el método de solicitud es GET o POST. Si es una solicitud GET, puede mostrar el formulario. De lo contrario, si se trata de una solicitud POST, entonces querrá procesar los datos entrantes.</p>

<p>Modifique la ruta de <code>form-example</code> en <code>app.py</code> con el siguiente código:</p>
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
<p>Luego, ejecute la aplicación y diríjase a la URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/form-example
</code></pre>
<p>Complete el campo de <code>language</code> con el valor de <code>Python</code> y el campo de <code>framework</code> con el valor de <code>Flask</code>. Luego, presione <strong>Submit</strong> (Enviar).</p>

<p>El navegador debe mostrar el siguiente mensaje:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
The framework value is: Flask
</code></pre>
<p>Ahora, entiende cómo manejar los datos del formulario. Continuaremos con el siguiente tipo de datos entrantes.</p>

<h2 id="uso-de-datos-json">Uso de datos JSON</h2>

<p>Los datos JSON generalmente se construye a través de un proceso que invoca la ruta.</p>

<p>Un ejemplo de objeto JSON tiene el siguiente aspecto:</p>
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
<p>Esta estructura puede permitir que se pasen datos mucho más complicados en lugar de las cadenas de consulta y los datos del formulario. En el ejemplo, se ve los objetos JSON anidados y una matriz de elementos. Flask puede gestionar este formato de datos.</p>

<p>Modifique la ruta de <code>form-example</code> en <code>app.py</code> para aceptar las solicitudes POST e ignore otras solicitudes como GET:</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python">@app.route('/json-example'<span class="highlight">, methods=['POST']</span>)
def json_example():
    return 'JSON Object Example'
</code></pre>
<p>A diferencia del navegador web que se utiliza para las cadenas de consulta y los datos de formulario, para los fines de este artículo, para enviar un objeto JSON, se utilizará <a href="https://www.getpostman.com/">Postman</a> para enviar solicitudes personalizadas a las URL.</p>

<p><span class='note'><strong>Nota</strong>: Si necesita ayuda para navegar por la interfaz de Postman para las solicitudes, consulte <a href="https://learning.postman.com/docs/postman/sending-api-requests/requests/">la documentación oficial</a>.<br></span></p>

<p>En Postman, añada la URL y cambie el tipo a <strong>POST</strong>. En la pestaña de cuerpo, cambie a <strong>raw</strong> y seleccione <strong>JSON</strong> en el menú desplegable.</p>

<p>Estos ajustes son necesarios para que Postman pueda enviar datos JSON correctamente, y para que su aplicación Flask comprenda que está recibiendo JSON:</p>
<pre class="code-pre "><code>POST http://127.0.0.1:5000/json-example
Body
raw JSON
</code></pre>
<p>Luego, copie el ejemplo JSON anterior en la entrada de texto.</p>

<p>Envíe la solicitud y debería obtener <code>"JSON Object Example"</code> como respuesta. Eso es bastante decepcionante, pero es de esperar porque el código para gestionar la respuesta de los datos JSON aún no se ha escrito.</p>

<p>Para leer los datos, debe entender cómo Flask traduce los datos JSON en las estructuras de datos de Python:</p>

<ul>
<li>Cualquier cosa que sea un objeto se convierte en una dictadura de Python. <code>{"key" : "value"}</code> en JSON corresponde a <code>somedict['key']</code>, que devuelve un valor en Python.</li>
<li>Una matriz en JSON se convierte en una lista en Python. Dado que la sintaxis es la misma, aquí hay un ejemplo de una lista: <code>[1,2,3,4,5]</code>.</li>
<li>Los valores dentro de las comillas en el objeto JSON se convierten en cadenas en Python.</li>
<li>Los booleanos <code>true</code> y <code>false</code> se convierten en <code>True</code> y <code>False</code> en Python.</li>
<li>Por último, los números sin comillas se convierten en números en Python.</li>
</ul>

<p>Ahora vamos a trabajar en el código para leer los datos JSON entrantes.</p>

<p>Primero, vamos a asignar todo desde el objeto JSON a una variable usando <code>request.get_json()</code>.</p>

<p><code>request.get_json()</code> convierte el objeto JSON en datos de Python. Asignemos los datos de la solicitud entrante a las variables y devolvámoslos haciendo los siguientes cambios en la ruta <code>json-example</code>:</p>
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
<p>Tenga en cuenta cómo se accede a los elementos que no están en el nivel superior. Se utiliza <code>['version']['python']</code> porque se está entrando en un objeto anidado. Y <code>['examples'][0]</code> se utiliza para acceder al índice 0 en la matriz de ejemplos.</p>

<p>Si el objeto JSON enviado con la solicitud no tiene una clave a la que se accede en su función de vista, entonces la solicitud fallará. Si no quiere que la solicitud falle cuando no existe una clave, tendrá que verificar si la clave existe antes de intentar ingresarla.</p>
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
<p>Ejecute la aplicación y envíe la solicitud JSON de ejemplo usando Postman. En la respuesta, obtendrá el siguiente resultado:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
The framework value is: Flask
The Python version is: 3.9
The item at index 0 in the example list is: query
The boolean value is: false
</code></pre>
<p>Ahora comprende cómo se manejan los objetos JSON.</p>

<h2 id="conclusión">Conclusión</h2>

<p>En este artículo, se creó una aplicación Flask con tres rutas que aceptan cadenas de consulta, datos de formulario u objetos JSON.</p>

<p>Además, recuerde que todos los enfoques tuvieron que abordar la consideración recurrente de fallar con gracia cuando falta una clave.</p>

<p><span class='warning'><strong>Advertencia:</strong> Un tema que no se cubrió en este artículo fue cómo desinfectar la entrada de los usuarios. La sanitización de la entrada de lo usuario garantizaría que los datos leídos por la aplicación no causen un fallo inesperado o que se salten las medidas de seguridad.<br></span></p>

<p>Si desea obtener más información sobre Flask, consulte <a href="https://www.digitalocean.com/community/tags/flask">nuestra página del tema Flask</a> para consultar ejercicios y proyectos de programación.</p>
