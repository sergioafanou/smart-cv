---
title : "Guest Post: Calling the Docker CLI from Python with Python-on-whales"
layout: post
tags: tutorial labnol
post_inspiration: https://www.docker.com/blog/guest-post-calling-the-docker-cli-from-python-with-python-on-whales/
image: "https://sergio.afanou.com/assets/images/image-midres-15.jpg"
---


<figure class="wp-block-image size-large is-resized"><img data-attachment-id="27680" data-permalink="https://www.docker.com/blog/guest-post-calling-the-docker-cli-from-python-with-python-on-whales/pythonwhale/" data-orig-file="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/pythonwhale.png?fit=1333%2C1303&amp;ssl=1" data-orig-size="1333,1303" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="pythonwhale" data-image-description="" data-medium-file="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/pythonwhale.png?fit=307%2C300&amp;ssl=1" data-large-file="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/pythonwhale.png?fit=1048%2C1024&amp;ssl=1" loading="lazy" src="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/pythonwhale.png?resize=373%2C364&#038;ssl=1" alt="" class="wp-image-27680" width="373" height="364" srcset="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/pythonwhale.png?resize=1048%2C1024&amp;ssl=1 1048w, https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/pythonwhale.png?resize=307%2C300&amp;ssl=1 307w, https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/pythonwhale.png?w=1333&amp;ssl=1 1333w" sizes="(max-width: 373px) 100vw, 373px" data-recalc-dims="1" /><figcaption>Image: Alice Lang, alicelang-creations@outlook.fr</figcaption></figure>



<p></p>



<p><em>At Docker, we are incredibly proud of our vibrant, diverse and creative community. From time to time, we feature cool contributions from the community on our blog to highlight some of the great work our community does. The following is a guest post by Docker community member Gabriel de Marmiesse. Are you working on something awesome with Docker? Send your contributions to William Quiviger (@william) on the </em><a href="https://dockercommunity.slack.com/join/shared_invite/zt-m8y5jl0m-kFllVvYPlexvNd6qvGRNhw#/"><em>Docker Community Slack</em></a><em> and we might feature your work!&nbsp;&nbsp;&nbsp;</em></p>



<p>The most common way to call and control Docker is by using the command line.</p>



<p>With the increased usage of Docker, users want to call Docker from programming languages other than shell. One popular way to use Docker from Python has been to use <a href="https://docker-py.readthedocs.io/en/stable/">docker-py</a>. This library has had so much success that even <code>docker-compose</code> is written in Python, and leverages docker-py.</p>



<p>The goal of docker-py though is not to replicate the Docker client (written in Golang), but to talk to the Docker Engine HTTP API. The Docker client is extremely complex and is hard to duplicate in another language. Because of this, a lot of features that were in the Docker client could not be available in docker-py. Sometimes users would sometimes get frustrated because docker-py did not behave exactly like the CLI.</p>



<p>Today, we’re presenting a new project built by Gabriel de Marmiesse from the Docker community: Python-on-whales. The goal of this project is to have a 1-to-1 mapping between the Docker CLI and the Python library. We do this by communicating with the Docker CLI instead of calling directly the Docker Engine HTTP API.</p>



<figure class="wp-block-image"><img src="https://lh5.googleusercontent.com/n5eyjtJeGjV6Elmh1zD23R0hl1w90Ytk9Awce_7TFbhPKdyZ6rBTckSrR6XssKHyzEKqxHyYtIcNWVS6vuTV2vx2eCkT8BmcSEFLulaxsmU8oAOubWGuvDTRBc2fujjhTnC0cLAe" alt=""/></figure>



<p>If you need to call the Docker command line, use Python-on-whales. And if you need to call the&nbsp;Docker engine directly, use docker-py.</p>



<p>In this post, we’ll take a look at some of the features that are not available in docker-py but are available in Python-on-whales:</p>



<ul><li>Building with Docker buildx</li><li>Deploying to Swarm with docker stack</li><li>Deploying to the local Engine with Compose</li></ul>



<p>Start by downloading Python-on-whales with&nbsp;</p>



<p><code>pip install python-on-whales</code></p>



<p>and you’re ready to rock!</p>



<p></p>



<h2>Docker Buildx0</h2>



<p>Here we <a href="https://gabrieldemarmiesse.github.io/python-on-whales/sub-commands/buildx/">build a Docker image</a>. Python-on-whales uses buildx by default and gives you the output in real time.</p>



<pre class="wp-block-code"><code>>>> from python_on_whales import docker
>>> my_image = docker.build(".", tags="some_name")
&#91;+] Building 1.6s (17/17) FINISHED
 => &#91;internal] load build definition from Dockerfile                       0.0s
 => => transferring dockerfile: 32B                                        0.0s
 => &#91;internal] load .dockerignore                                          0.0s
 => => transferring context: 2B                                            0.0s
 => &#91;internal] load metadata for docker.io/library/python:3.6              1.4s
 => &#91;python_dependencies 1/5] FROM docker.io/library/python:3.6@sha256:293 0.0s
 => &#91;internal] load build context                                          0.1s
 => => transferring context: 72.86kB                                       0.0s
 => CACHED &#91;python_dependencies 2/5] RUN pip install typeguard pydantic re 0.0s
 => CACHED &#91;python_dependencies 3/5] COPY tests/test-requirements.txt /tmp 0.0s
 => CACHED &#91;python_dependencies 4/5] COPY requirements.txt /tmp/           0.0s
 => CACHED &#91;python_dependencies 5/5] RUN pip install -r /tmp/test-requirem 0.0s
 => CACHED &#91;tests_ubuntu_install_without_buildx 1/7] RUN apt-get update &amp;&amp; 0.0s
 => CACHED &#91;tests_ubuntu_install_without_buildx 2/7] RUN curl -fsSL https: 0.0s
 => CACHED &#91;tests_ubuntu_install_without_buildx 3/7] RUN add-apt-repositor 0.0s
 => CACHED &#91;tests_ubuntu_install_without_buildx 4/7] RUN  apt-get update &amp; 0.0s
 => CACHED &#91;tests_ubuntu_install_without_buildx 5/7] WORKDIR /python-on-wh 0.0s
 => CACHED &#91;tests_ubuntu_install_without_buildx 6/7] COPY . .              0.0s
 => CACHED &#91;tests_ubuntu_install_without_buildx 7/7] RUN pip install -e .  0.0s
 => exporting to image                                                     0.1s
 => => exporting layers                                                    0.0s
 => => writing image sha256:e1c2382d515b097ebdac4ed189012ca3b34ab6be65ba0c 0.0s
 => => naming to docker.io/library/some_image_name
</code></pre>



<h2>Docker Stacks</h2>



<p>Here we deploy a simple <a href="https://swarmpit.io/">Swarmpit</a> stack on a local Swarm. You get a Stack object that has several methods: <code>remove(), services(), ps().</code></p>



<pre class="wp-block-code"><code>>>> from python_on_whales import docker
>>> docker.swarm.init()
>>> swarmpit_stack = docker.stack.deploy("swarmpit", compose_files=&#91;"./docker-compose.yml"])
Creating network swarmpit_net
Creating service swarmpit_influxdb
Creating service swarmpit_agent
Creating service swarmpit_app
Creating service swarmpit_db
>>> swarmpit_stack.services()
&#91;&lt;python_on_whales.components.service.Service object at 0x7f9be5058d60>,
&lt;python_on_whales.components.service.Service object at 0x7f9be506d0d0>,
&lt;python_on_whales.components.service.Service object at 0x7f9be506d400>,
&lt;python_on_whales.components.service.Service object at 0x7f9be506d730>]
>>> swarmpit_stack.remove()
</code></pre>



<h2>Docker Compose</h2>



<p>Here we show how we can run a <a href="https://gabrieldemarmiesse.github.io/python-on-whales/sub-commands/compose/">Docker Compose application with Python-on-whales</a>. Note that, behind the scenes, it uses the <a href="https://github.com/docker/compose-cli">new version of Compose</a> written in Golang. This version of Compose is still experimental. Take appropriate precautions.</p>



<pre class="wp-block-code"><code>$ git clone https://github.com/dockersamples/example-voting-app.git
$ cd example-voting-app
$ python
>>> from python_on_whales import docker
>>> docker.compose.up(detach=True)
Network "example-voting-app_back-tier"  Creating
Network "example-voting-app_back-tier"  Created
Network "example-voting-app_front-tier"  Creating
Network "example-voting-app_front-tier"  Created
example-voting-app_redis_1  Creating
example-voting-app_db_1  Creating
example-voting-app_db_1  Created
example-voting-app_result_1  Creating
example-voting-app_redis_1  Created
example-voting-app_worker_1  Creating
example-voting-app_vote_1  Creating
example-voting-app_worker_1  Created
example-voting-app_result_1  Created
example-voting-app_vote_1  Created
>>> for container in docker.compose.ps():
...     print(container.name, container.state.status)
example-voting-app_vote_1 running
example-voting-app_worker_1 running
example-voting-app_result_1 running
example-voting-app_redis_1 running
example-voting-app_db_1 running
>>> docker.compose.down()
>>> print(docker.compose.ps())
&#91;]
</code></pre>



<h2>Bonus section: Docker objects attributes as Python attributes</h2>



<p>All information that you can access with <code>docker inspect</code> is available as Python attributes:</p>



<pre class="wp-block-code"><code>>>> from python_on_whales import docker
>>> my_container = docker.run("ubuntu", &#91;"sleep", "infinity"], detach=True)
>>> my_container.state.started_at
datetime.datetime(2021, 2, 18, 13, 55, 44, 358235, tzinfo=datetime.timezone.utc)
>>> my_container.state.running
True
>>> my_container.kill()
>>> my_container.remove()

>>> my_image = docker.image.inspect("ubuntu")
>>> print(my_image.config.cmd)
&#91;'/bin/bash']
</code></pre>



<h2>What&#8217;s next for Python-on-whales ?</h2>



<p>We&#8217;re currently improving the integration of Python-on-whales with the <a href="https://github.com/docker/compose-cli">new Compose</a> in the Docker CLI (currently beta).</p>



<p>You can consider that Python-on-whales is in beta. Some small API changes are still possible.&nbsp;</p>



<p>We encourage the community to try it out and give feedback <a href="https://github.com/gabrieldemarmiesse/python-on-whales/issues">in the issues</a>!</p>



<h2>To learn more about Python-on-whales:</h2>



<ul><li><a href="https://gabrieldemarmiesse.github.io/python-on-whales/">Documentation</a></li><li><a href="https://github.com/gabrieldemarmiesse/python-on-whales">Github repository</a></li></ul>
<p>The post <a rel="nofollow" href="https://www.docker.com/blog/guest-post-calling-the-docker-cli-from-python-with-python-on-whales/">Guest Post: Calling the Docker CLI from Python with Python-on-whales</a> appeared first on <a rel="nofollow" href="https://www.docker.com/blog">Docker Blog</a>.</p>
