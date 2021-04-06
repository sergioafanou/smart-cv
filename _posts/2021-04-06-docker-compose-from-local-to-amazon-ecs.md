---
title : "Docker Compose: From Local to Amazon ECS"
layout: post
tags: tutorial labnol
post_inspiration: https://www.docker.com/blog/docker-compose-from-local-to-amazon-ecs/
image: "https://sergio.afanou.com/assets/images/image-midres-6.jpg"
---


<p>By using cloud platforms, we can take advantage of different resource configurations and compute capacities. However, deploying containerized applications on cloud platforms is proving to be quite challenging, especially for new users who have no expertise on how to use that platform. As each platform may provide specific APIs, orchestrating the deployment of a containerized application can become a hassle.</p>



<p><a href="https://docs.docker.com/compose/">Docker Compose</a> is a very popular tool used to manage containerized applications deployed on Docker hosts. Its popularity is maybe due to the simplicity on how to define an application and its components in a Compose file and the compact commands to manage its deployment.</p>



<p>Since cloud platforms for containers have emerged, being able to deploy a Compose application on them is a most-wanted feature by many developers that use Docker Compose for their local development.</p>



<p>In this blog post, we discuss how to use Docker Compose to deploy containerized applications to Amazon ECS. We aim to show how the transition from deploying to a local Docker environment to deploying to Amazon ECS is effortless, the application being managed in the same way for both environments.</p>



<h2><strong>Requirements</strong></h2>



<p>In order to exercise the examples in this blogpost, the following tools need to be installed locally:</p>



<ul><li>Windows and MacOS: install <a href="https://www.docker.com/products/docker-desktop">Docker Desktop</a></li><li>Linux: install Docker Engine and <a href="https://github.com/docker/compose-cli/blob/main/INSTALL.md">Compose CLI</a></li><li>To deploy to Amazon ECS: <a href="https://aws.amazon.com/account/">an AWS account</a></li></ul>



<p>For deploying a Compose file to Amazon ECS, we rely on the new Docker Compose implementation embedded into the <a href="https://docs.docker.com/cloud/ecs-integration/">Docker CLI</a> binary. Therefore, we are going to run docker compose commands instead of docker-compose. For local deployments, both implementations of Docker Compose should work. If you find a missing feature that you use, report it on the <a href="https://github.com/docker/compose-cli/issues">issue tracker</a>.</p>



<p>Throughout this blogpost, we discuss how to:</p>



<ol><li><strong>Build and ship a Compose Application.</strong> We exercise how to run an application defined in a Compose file locally and how to build and ship its images to Docker Hub to make them accessible from anywhere.</li><li><strong>Create an ECS context to target Amazon ECS.</strong>&nbsp;&nbsp;&nbsp;</li><li><strong>Run the Compose application on Amazon ECS.</strong>&nbsp;</li></ol>



<ol><li><strong>Build and Ship a Compose application</strong></li></ol>



<p>Let us take an example application with the following structure:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ tree myproject/<br>myproject/<br>├── backend<br>│   ├── Dockerfile<br>│   ├── main.py<br>│   └── requirements.txt<br>├── compose.yaml<br>└── frontend<br>    ├── Dockerfile<br>    └── nginx.conf<br><br>2 directories, 6 files</code></td></tr></tbody></table></figure>



<p>The content of the files can be found <a href="https://github.com/aiordache/demos/tree/master/ecsblog-demo">here</a>. The Compose file define only 2 services as follows:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ cat compose.yaml<br>services:<br>frontend:<br>  build: frontend<br>  ports:<br>    - 80:80<br>  depends_on:<br>    - backend<br>backend:<br>  build: backend</code></td></tr></tbody></table></figure>



<p>Deploying this file locally on a Docker engine is quite straightforward:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ docker compose up -d<br>[+] Running 3/3<br>⠿ Network "myproject_default"     Created                     0.5s<br>⠿ Container myproject_backend_1   Started                     0.7s<br>⠿ Container myproject_frontend_1  Started                     1.4s</code></td></tr></tbody></table></figure>



<p>Check the application is running locally:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ docker ps<br>CONTAINER ID   IMAGE                COMMAND                    CREATED         STATUS         PORTS                NAMES<br>eec2dd88fd67   myproject_frontend   "/docker-entrypoint...."   4 seconds ago   Up 3 seconds   0.0.0.0:80->80/tcp   myproject_frontend_1<br>2c64e62b933b   myproject_backend    "python3 /app/main.py"     4 seconds ago   Up 3 seconds                        myproject_backend_1</code></td></tr></tbody></table></figure>



<p>Query the frontend:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ curl localhost:80<br><br>          ##         .<br>    ## ## ##        ==<br>## ## ## ## ##    ===<br>/"""""""""""""""""\___/ ===<br>{                       /  ===-<br>\______ O           __/<br>\    \         __/<br>  \____\_______/<br><br>Hello from Docker!</code></td></tr></tbody></table></figure>



<p>To remove the application:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ docker compose down<br>[+] Running 3/3<br>⠿ Container myproject_frontend_1  Removed                                                 0.5s<br>⠿ Container myproject_backend_1   Removed                                                10.3s<br>⠿ Network "myproject_default"     Removed                                                 0.4s</code></td></tr></tbody></table></figure>



<p>In order to deploy this application on ECS, we need to have the images for the application frontend and backend stored in a public image registry such as <a href="https://hub.docker.com/">Docker Hub</a>. This enables the images to be pulled from anywhere.</p>



<p>To upload the images to Docker Hub, we can set the image names in the compose file as follows:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ cat compose.yamlservices:<br>frontend:<br>  image: myhubuser/starter-front<br>  build: frontend<br>  ports:<br>    - 80:80<br>  depends_on:<br>    - backend<br>backend:<br>  image: myhubuser/starter-back<br>  build: backend</code></td></tr></tbody></table></figure>



<p>Build the images with Docker Compose:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ docker compose build<br>[+] Building 1.2s (16/16) FINISHED                                                                                                                                 <br>=> [myhubuser/starter-front internal] load build definition from Dockerfile            0.0s<br>=> => transferring dockerfile: 31B                                                     0.0s<br>=> [myhubuser/starter-back internal] load build definition from Dockerfile 0.0s<br>...</code></td></tr></tbody></table></figure>



<p>In the build output we can notice the image has been named and tagged according to the image field from the Compose file.</p>



<p>Before pushing the images to Docker Hub, check to be logged in:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ docker login<br>...<br>Login Succeeded</code></td></tr></tbody></table></figure>



<p>Push the images:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ docker compose push<br>[+] Running 0/16<br>⠧ Pushing Pushing frontend: f009a503aca1 Pushing              [===========================================...                                        2.7s<br>...</code></td></tr></tbody></table></figure>



<p>The images should be stored now in Docker Hub.</p>



<ol start="2"><li><strong>Create an ECS Docker Context</strong></li></ol>



<p>To make Docker Compose target the Amazon ECS platform, we need first to create a <a href="https://docs.docker.com/engine/context/working-with-contexts/">Docker context</a> of the <a href="https://docs.docker.com/cloud/ecs-integration/#create-aws-context">ECS type</a>. A docker context is a mechanism that allows redirecting commands to different Docker hosts or cloud platforms.&nbsp;</p>



<p>We assume at this point that we have AWS credentials set up in the local environment for authenticating with the ECS platform.&nbsp;</p>



<p>To create an ECS context run the following command:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ docker context create ecs myecscontext<br>? Create a Docker context using:  [Use arrows to move, type to filter]<br>  An existing AWS profile<br>  AWS secret and token credentials<br>> AWS environment variables</code></td></tr></tbody></table></figure>



<p>Based on the familiarity with the AWS credentials setup and the AWS tools use, we are prompted to choose between 3 context setups. To skip&nbsp; the details of&nbsp; AWS credential setup, we choose the option of using environment variables.</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ docker context create ecs myecscontext<br>? Create a Docker context using: AWS environment variables<br>Successfully created ecs context "myecscontext"</code></td></tr></tbody></table></figure>



<p>This requires to have the <strong>AWS_ACCESS_KEY</strong> and <strong>AWS_SECRET_KEY</strong> set in the local environment when running Docker commands that target Amazon ECS.</p>



<p>The current context in use is marked by&nbsp; * in the output of context listing:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ docker context lsNAME                TYPE      DESCRIPTION                                   DOCKER ENDPOINT       default *           moby      Current DOCKER_HOST based configuration       unix:///var/run/docker.sockmyecscontext        ecs       credentials read from environment                                                           </code></td></tr></tbody></table></figure>



<p>To make all subsequent commands target Amazon ECS, make the newly created ECS context the one in use by running:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ docker context use myecscontext<br>myecscontext<br><br><br>$ docker context ls<br>NAME                TYPE         DESCRIPTION                               DOCKER ENDPOINT              <br>default             moby         Current DOCKER_HOST based configuration   unix:///var/run/docker.sock<br>myecscontext *      ecs          credentials read from environment                                                             </code></td></tr></tbody></table></figure>



<ol start="3"><li><strong> Run the Compose application on Amazon ECS</strong></li></ol>



<p>An alternative to having it as the context in use is to set the context flag for all commands targeting ECS.</p>



<p><strong>WARNING: </strong>Check in advance the cost that the ECS deployment may incur for 2 ECS services, load balancing (ALB), cloud map (DNS resolution) etc. </p>



<p>For the following commands, we keep ECS context as the current context in use. Before running commands on ECS, make sure the Amazon account credentials grant access to manage resources for the application as detailed in the <a href="https://docs.docker.com/cloud/ecs-integration/#requirements">documentation</a>.</p>



<p>&nbsp;We can now run a command to check we can successfully access ECS.</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ AWS_ACCESS_KEY="*****" AWS_SECRET_KEY="******" docker compose ls<br>NAME                                STATUS               </code></td></tr></tbody></table></figure>



<p>Export the AWS credentials to avoid setting them for every command.</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ export AWS_ACCESS_KEY="*****"<br>$ export AWS_SECRET_KEY="******"</code></td></tr></tbody></table></figure>



<p>The deploy the sample application to ECS, we can run the same command as in the local deployment:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ docker compose up<br>WARNING services.build: unsupported attribute       <br>WARNING services.build: unsupported attribute       <br>[+] Running 18/18<br>⠿ myproject                      CreateComplete                                     206.0s<br>⠿ FrontendTCP80TargetGroup       CreateComplete                                       0.0s<br>⠿ CloudMap                       CreateComplete                                      46.0s<br>⠿ FrontendTaskExecutionRole      CreateComplete                                      19.0s<br>⠿ Cluster                        CreateComplete                                       5.0s<br>⠿ DefaultNetwork                 CreateComplete                                       5.0s<br>⠿ BackendTaskExecutionRole       CreateComplete                                      19.0s<br>⠿ LogGroup                       CreateComplete                                       1.0s<br>⠿ LoadBalancer                   CreateComplete                                     122.0s<br>⠿ Default80Ingress               CreateComplete                                       1.0s<br>⠿ DefaultNetworkIngress          CreateComplete                                       0.0s<br>⠿ BackendTaskDefinition          CreateComplete                                       2.0s<br>⠿ FrontendTaskDefinition         CreateComplete                                       3.0s<br>⠿ FrontendServiceDiscoveryEntry  CreateComplete                                       1.0s<br>⠿ BackendServiceDiscoveryEntry   CreateComplete                                       2.0s<br>⠿ BackendService                 CreateComplete                                      65.0s<br>⠿ FrontendTCP80Listener          CreateComplete                                       3.0s<br>⠿ FrontendService                CreateComplete                                      66.0s</code></td></tr></tbody></table></figure>



<p>Docker Compose converts the Compose file to a <a href="https://aws.amazon.com/cloudformation/">CloudFormation</a> template defining a set of AWS resources. Details on the resource mapping can be found in the <a href="https://docs.docker.com/cloud/ecs-architecture/">documentation</a>. To review the CloudFormation template generated, we can run the command:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ docker compose convert<br>WARNING services.build: unsupported attribute       <br>WARNING services.build: unsupported attribute       <br>AWSTemplateFormatVersion: 2010-09-09<br>Resources:<br>  BackendService:<br>    Properties:<br>      Cluster:<br>        Fn::GetAtt:<br>        - Cluster<br>        - Arn<br>      DeploymentConfiguration:<br>        MaximumPercent: 200<br>        MinimumHealthyPercent: 100...</code></td></tr></tbody></table></figure>



<p>To check the state of the services, we can run the command:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ docker compose ps<br>NAME                                              SERVICE             STATUS              PORTS<br>task/myproject/8c142dea1282499c83050b4d3e689566   backend             Running            <br>task/myproject/a608f6df616e4345b92a3d596991652d   frontend            Running             mypro-LoadB-1ROWIHLNOG5RZ-1172432386.eu-west-3.elb.amazonaws.com:80->80/http</code></td></tr></tbody></table></figure>



<p>Similarly to the local run, we can query the frontend of the application:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ curl mypro-LoadB-1ROWIHLNOG5RZ-1172432386.eu-west-3.elb.amazonaws.com:80<br><br>          ##         .<br>    ## ## ##        ==<br>## ## ## ## ##    ===<br>/"""""""""""""""""\___/ ===<br>{                       /  ===-<br>\______ O           __/<br>\    \         __/<br>  \____\_______/<br><br>Hello from Docker!</code></td></tr></tbody></table></figure>



<p>We can retrieve logs from the ECS containers by running the compose logs command:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ docker compose logs<br>backend  |  * Serving Flask app "main" (lazy loading)<br>backend  |  * Environment: production<br>backend  |    WARNING: This is a development server. Do not use it in a production deployment.<br>backend  |    Use a production WSGI server instead.<br>...<br>frontend  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh<br>frontend  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh<br>frontend  | /docker-entrypoint.sh: Configuration complete; ready for start up<br>frontend  | 172.31.22.98 - - [02/Mar/2021:08:35:27 +0000] "GET / HTTP/1.1" 200 212 "-" "ELB-HealthChecker/2.0" "-"<br>backend   | 172.31.0.11 - - [02/Mar/2021 08:35:27] "GET / HTTP/1.0" 200 -<br>backend   | 172.31.0.11 - - [02/Mar/2021 08:35:57] "GET / HTTP/1.0" 200 -<br>frontend  | 172.31.22.98 - - [02/Mar/2021:08:35:57 +0000] "GET / HTTP/1.1" 200 212 "-" "curl/7.75.0" "94.239.119.152"<br>frontend  | 172.31.22.98 - - [02/Mar/2021:08:35:57 +0000] "GET / HTTP/1.1" 200 212 "-" "ELB-HealthChecker/2.0" "-"</code></td></tr></tbody></table></figure>



<p>To terminate the Compose application and release AWS resources, run:</p>



<figure class="wp-block-table"><table><tbody><tr><td><code>$ docker compose down<br>[+] Running 2/4<br>⠴ myproject              DeleteInProgress User Initiated                                        8.5s<br>⠿ DefaultNetworkIngress  DeleteComplete                                                         1.0s<br>⠿ Default80Ingress       DeleteComplete                                                         1.0s<br>⠴ FrontendService        DeleteInProgress                                                       7.5s...</code></td></tr></tbody></table></figure>



<p>The Docker documentation provides several <a href="https://docs.docker.com/cloud/ecs-compose-examples/">examples</a> of Compose files, supported features, details on how to deploy and how to <a href="https://docs.docker.com/cloud/ecs-integration/#rolling-update">update</a> a Compose application running in ECS, etc.</p>



<p>The following features are discussed in detail:</p>



<ul><li>use of <a href="https://docs.docker.com/cloud/ecs-integration/#private-docker-images">private images</a></li><li><a href="https://docs.docker.com/cloud/ecs-integration/#private-docker-images">service discovery</a></li><li><a href="https://docs.docker.com/cloud/ecs-integration/#volumes">volumes</a> and <a href="https://docs.docker.com/cloud/ecs-integration/#secrets">secrets</a> definition</li><li>AWS-specific service properties for <a href="https://docs.docker.com/cloud/ecs-integration/#auto-scaling">auto-scaling</a>, <a href="https://docs.docker.com/cloud/ecs-integration/#iam-roles">IAM roles</a> and <a href="https://docs.docker.com/cloud/ecs-integration/#adjusting-load-balancer-http-healthcheck-configuration">load balancing</a></li><li>use of existing<a href="https://docs.docker.com/cloud/ecs-integration/#using-existing-aws-network-resources"> AWS resources</a></li></ul>



<h2><strong>Summary</strong></h2>



<p>We have covered the transition from local deployment of a Compose application to the deployment on Amazon ECS. We have used a minimal generic example for demonstrating how to use the Docker Compose cloud-capability. For a better understanding on how to update the Compose file and use specific AWS features, the documentation provides much more details.</p>



<h2><strong>Resources:</strong></h2>



<ul><li>Docker Compose embedded in the Docker CLI<ul><li><a href="https://github.com/docker/compose-cli/blob/main/INSTALL.md">https://github.com/docker/compose-cli/blob/main/INSTALL.md</a></li></ul></li><li>Compose to ECS support<ul><li> <a href="https://docs.docker.com/cloud/ecs-integration/">https://docs.docker.com/cloud/ecs-integration/</a></li></ul></li><li>ECS-specific Compose examples:<ul><li><a href="https://docs.docker.com/cloud/ecs-compose-examples/">https://docs.docker.com/cloud/ecs-compose-examples/</a></li></ul></li><li>Deploying Docker containers to ECS:<ul><li><a href="https://docs.docker.com/cloud/ecs-integration/">https://docs.docker.com/cloud/ecs-integration/</a></li></ul></li><li>Sample used to demonstrate Compose commands:<ul><li><a href="https://github.com/aiordache/demos/tree/master/ecsblog-demo">https://github.com/aiordache/demos/tree/master/ecsblog-demo</a></li></ul></li></ul>
<p>The post <a rel="nofollow" href="https://www.docker.com/blog/docker-compose-from-local-to-amazon-ecs/">Docker Compose: From Local to Amazon ECS</a> appeared first on <a rel="nofollow" href="https://www.docker.com/blog">Docker Blog</a>.</p>
