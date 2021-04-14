---
title : "Captain Take 5 – Nuno do Carmo"
layout: post
tags: tutorial labnol
post_inspiration: https://www.docker.com/blog/captain-take-5-nuno-do-carmo/
image: "https://sergio.afanou.com/assets/images/image-midres-22.jpg"
---


<p><em>Docker Captains are select members of the community that are both experts in their field and are passionate about sharing their Docker knowledge with others. “Docker Captains Take 5” is a regular blog series where we get a closer look at our Captains and ask them the same broad set of questions ranging from what their best Docker tip is to whether they prefer cats or dogs (personally, we like </em><a href="https://twitter.com/Docker/status/1105128250488090624"><em>whales</em></a><em> and </em><a href="https://twitter.com/Docker/status/1326270346820014086"><em>turtles</em></a><em> over here). Today, we’re interviewing </em><a href="https://www.docker.com/captains/nuno-do-carmo"><em>Nuno do Carmo</em></a><em> who has been a Docker Captain since 2019. He is a Sr System Analyst for a pharmaceutical company based in Switzerland and he is based in Montreux.</em></p>



<p></p>



<figure class="wp-block-image size-large"><img data-attachment-id="27962" data-permalink="https://www.docker.com/blog/captain-take-5-nuno-do-carmo/docaotake5/" data-orig-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/docaotake5.jpg?fit=400%2C400&amp;ssl=1" data-orig-size="400,400" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="docaotake5" data-image-description="" data-medium-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/docaotake5.jpg?fit=300%2C300&amp;ssl=1" data-large-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/docaotake5.jpg?fit=400%2C400&amp;ssl=1" loading="lazy" width="400" height="400" src="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/docaotake5.jpg?resize=400%2C400&#038;ssl=1" alt="" class="wp-image-27962" srcset="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/docaotake5.jpg?w=400&amp;ssl=1 400w, https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/docaotake5.jpg?resize=300%2C300&amp;ssl=1 300w, https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/docaotake5.jpg?resize=150%2C150&amp;ssl=1 150w" sizes="(max-width: 400px) 100vw, 400px" data-recalc-dims="1" /></figure>



<h3>How/when did you first discover Docker?</h3>



<p>Back in 2015, I was hanging with friends and we would meet once a week to check on technologies and we found out a training on Pluralsight, given by a certain Nigel Poulton, and we decided to “temporarily” download it, **cough**.</p>



<p>Both the training method from Nigel and the technology of Docker were an instant hit for us. We started to learn as hobbyists and fast forward, I guess I took it more at heart than my friends, haha.</p>



<h3>What is your favorite Docker command?</h3>



<p><code>`docker run`</code>, everything starts with a <code>`docker run`</code>. That first time that we launch “something”, we don’t know exactly what, it’s not VM and yet an instance with another OS is running.</p>



<p>Also, the power of simplicity from <code>`docker run`</code>, if the container image, network or volume do not exist, it creates it for us. We can start small from a <code>`docker run -it alpine`</code> to a way more complex command with ports, secrets, privileges.</p>



<p>Special mention to <code>`docker app`</code> and the whole CNAB community as it was the first time I contributed back to a project which used Docker (read below)</p>



<h3>What is your top tip you think other people don’t know for working with Docker?</h3>



<p>For the ones who know me, I’m a huge fan(boy) of Windows Subsystem for Linux (WSL) and while I do use Docker Desktop for Windows, I keep on reminding people who ask for help, that Docker on WSL2 can be installed like we would do in a native Linux OS.</p>



<p>Therefore we can have Docker Desktop running, let’s say with Windows Containers, and the Docker WSL2 running in parallel.</p>



<p>And special mention (yes, again) to<code> `docker context` </code>that makes it even easier to manage the whole setup.</p>



<h3>What’s the coolest Docker demo you have done/seen?</h3>



<p>Done, without hesitation, is the Docker to WSL2 distribution, which since then I have remixed a lot of times.</p>



<p>The demo consists in running a Docker container with almost any Linux based OS, export it as a compressed file and re-import it into WSL2 as a new distro.</p>



<p>I could automate it with a CNAB tool, called Porter.sh, and the community was so surprised (as it was not a use case initially intended for), that I got my first KubeCon invite to showcase it during a Day0 CNAB event led by another amazing Captain, Scott Coulton.</p>



<p>A massive thank you to the CNAB team and especially Carolyn Van Slyck, Ralf Squillace and Jeremy Rickard.</p>



<p>Seen, again without hesitation, it’s the demo from Jessie Frazelle called “Willy Wonka of Containers” (<a href="https://youtu.be/GsLZz8cZCzc">https://youtu.be/GsLZz8cZCzc</a>).</p>



<p>This demo had such a huge impact on me and since then, 6 years later, I finally will be able to “mimic” it 100% in WSL2. That’s how much “in the future”, this demo was and Jessie is just incredible.</p>



<h3>What have you worked on in the past six months that you&#8217;re particularly proud of?</h3>



<p>Being a hobbyist (read: I do not work with Docker in my daily job), I can definitively say that I’m proud to have helped community members as a Captain and overall fan.</p>



<p>As for the technical part, I’m hard at work bringing rootless containers to WSL2 where, once again, I adapt the existing work of the great Akihiro Suda.</p>



<h3>What do you anticipate will be Docker&#8217;s biggest announcement this year?</h3>



<p>Docker Desktop for Linux, what else?</p>



<h3>What do you think is going to be Docker&#8217;s biggest challenge this year?</h3>



<p>These past years, Docker Inc (the company), needed to find its place in a Cloud Native world led by Kubernetes and associated projects.</p>



<p>I really think Scott Johnston has a very good vision, refocusing on what Docker does and speaks to best: the Developers.</p>



<p>Still, the road is not an easy one and this year I think Docker will be cementing its “new” position within the Cloud Native ecosystem.</p>



<h3>What are some personal goals for the next year with respect to the Docker community?</h3>



<p>Being a “new generation” of Captain, it’s always hard for me to believe I am in the same group as Legends that motivate me, still to this day, to use and enjoy Docker.</p>



<p>Being the “WSL Corsair”, I found my niche, and I simply would love to see more adoption of Docker Desktop for Windows with WSL2 backend.</p>



<p>Another point is to keep helping the Docker Windows containers side too. It’s there and my own impression is that it lacks some momentum right now. So bringing back the fire with the help of the community will be lots of fun.</p>



<h3>What talk would you most love to see at DockerCon 2021?</h3>



<p>Docker Desktop for Linux, what else? (bis)</p>



<p>But also, a lot of community members coming together and having fun, and maybe, why not, someone doing a crazy demo and motivating at least 1 person to become a Captain too in the future.</p>



<h3>Looking to the distant future, what is the technology that you&#8217;re most excited about and that you think holds a lot of promise?</h3>



<p>WSL2, and that’s not just the fanboy talking right now. Having the possibility to have a mix of two worlds, not colliding but merging, it’s just mind blowing.</p>



<p>In terms of hardware, even if ARM devices have existed for a long time, the Apple M1 really shook the world and the speed at which Software makers are porting their applications really opens a new ecosystem.</p>



<h3>Rapid fire questions&#8230;</h3>



<h4>What new skill have you mastered during the pandemic?</h4>



<p>Cooking new recipes</p>



<h4>Cats or Dogs?</h4>



<p>4 cats at home&#8230;</p>



<h4>Salty, sour or sweet?</h4>



<p>Salty</p>



<h4>Beach or mountains?</h4>



<p>I’m Portuguese living in Switzerland: both</p>



<h4>Your most often used emoji?</h4>



<p> <img src="https://s.w.org/images/core/emoji/13.0.1/72x72/1f601.png" alt="������" class="wp-smiley" style="height: 1em; max-height: 1em;" /> and <img src="https://s.w.org/images/core/emoji/13.0.1/72x72/1f3f4-200d-2620-fe0f.png" alt="������‍☠️" class="wp-smiley" style="height: 1em; max-height: 1em;" /></p>



<h3><em>DockerCon Live 2021</em></h3>



<p><em>Join us for </em><a href="https://dockr.ly/2PSJ7vn"><em>DockerCon LIVE 2021</em></a><em> on Thursday, May 27. DockerCon Live is a free, one day virtual event that is a unique experience for developers and development teams who are building the next generation of modern applications. If you want to learn about how to go from code to cloud fast and how to solve your development challenges, DockerCon 2021 offers engaging live content to help you build, share and run your applications. Register today at </em><a href="https://dockr.ly/2PSJ7vn"><em>https://dockr.ly/2PSJ7vn</em></a></p>
<p>The post <a rel="nofollow" href="https://www.docker.com/blog/captain-take-5-nuno-do-carmo/">Captain Take 5 &#8211; Nuno do Carmo</a> appeared first on <a rel="nofollow" href="https://www.docker.com/blog">Docker Blog</a>.</p>
