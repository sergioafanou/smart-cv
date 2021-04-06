---
title : "Compiling Containers – Dockerfiles, LLVM and BuildKit"
layout: post
tags: tutorial labnol
post_inspiration: https://www.docker.com/blog/compiling-containers-dockerfiles-llvm-and-buildkit/
image: "https://sergio.afanou.com/assets/images/image-midres-7.jpg"
---


<p>Today we’re featuring a blog from Adam Gordon Bell at <a href="https://earthly.dev/">Earthly</a> who <a href="https://blog.earthly.dev/compiling-containers-dockerfiles-llvm-and-buildkit/">writes</a> about how BuildKit, a technology developed by Docker and the community, works and how to write a simple frontend. Earthly uses BuildKit in their product.</p>



<figure class="wp-block-image size-large"><img data-attachment-id="27802" data-permalink="https://www.docker.com/blog/compiling-containers-dockerfiles-llvm-and-buildkit/1-6/" data-orig-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1.png?fit=1576%2C988&amp;ssl=1" data-orig-size="1576,988" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="1" data-image-description="" data-medium-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1.png?fit=479%2C300&amp;ssl=1" data-large-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1.png?fit=1110%2C696&amp;ssl=1" loading="lazy" width="1110" height="696" src="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1.png?resize=1110%2C696&#038;ssl=1" alt="" class="wp-image-27802" srcset="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1.png?resize=1110%2C696&amp;ssl=1 1110w, https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1.png?resize=479%2C300&amp;ssl=1 479w, https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1.png?resize=1536%2C963&amp;ssl=1 1536w, https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1.png?w=1576&amp;ssl=1 1576w" sizes="(max-width: 1000px) 100vw, 1000px" data-recalc-dims="1" /></figure>



<h2>Introduction</h2>



<p>How are containers made? Usually, from a series of statements like `RUN`, `FROM`, and `COPY`, which are put into a Dockerfile and built.&nbsp; But how are those commands turned into a container image and then a running container?&nbsp; We can build up an intuition for how this works by understanding the phases involved and creating a container image ourselves. We will create an image programmatically and then develop a trivial syntactic frontend and use it to build an image.</p>



<h2>On `docker build`</h2>



<p>We can create container images in several ways. We can use Buildpacks, we can use build tools like Bazel or sbt, but by far, the most common way images are built is using `docker build` with a Dockerfile.&nbsp; The familiar base images Alpine, Ubuntu, and Debian are all created this way.&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</p>



<p>Here is an example Dockerfile:</p>



<pre class="wp-block-code"><code>FROM alpine
COPY README.md README.md
RUN echo "standard docker build" > /built.txt"</code></pre>



<p>We will be using variations on this Dockerfile throughout this tutorial.&nbsp;</p>



<p>We can build it like this:</p>



<div class="wp-block-group"><div class="wp-block-group__inner-container">
<div class="wp-block-group"><div class="wp-block-group__inner-container">
<pre class="wp-block-code"><code>docker build . -t test</code></pre>
</div></div>
</div></div>



<p>But what is happening when you call `docker build`? To understand that, we will need a little background.</p>



<h2>Background</h2>



<p>&nbsp;A docker image is made up of layers. Those layers form an immutable filesystem.&nbsp; A container image also has some descriptive data, such as the start-up command, the ports to expose, and volumes to mount. When you `docker run` an image, it starts up inside a container runtime.</p>



<p>&nbsp;I like to think about images and containers by analogy. If an image is like an executable, then a container is like a process. You can run multiple containers from one image, and a running image isn&#8217;t an image at all but a container.</p>



<figure class="wp-block-image size-large"><img data-attachment-id="27810" data-permalink="https://www.docker.com/blog/compiling-containers-dockerfiles-llvm-and-buildkit/1-2-2/" data-orig-file="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1-2.png?fit=1600%2C419&amp;ssl=1" data-orig-size="1600,419" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="1-2" data-image-description="" data-medium-file="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1-2.png?fit=730%2C191&amp;ssl=1" data-large-file="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1-2.png?fit=1110%2C291&amp;ssl=1" loading="lazy" width="1110" height="291" src="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1-2.png?resize=1110%2C291&#038;ssl=1" alt="" class="wp-image-27810" srcset="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1-2.png?resize=1110%2C291&amp;ssl=1 1110w, https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1-2.png?resize=730%2C191&amp;ssl=1 730w, https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1-2.png?resize=1536%2C402&amp;ssl=1 1536w, https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/1-2.png?w=1600&amp;ssl=1 1600w" sizes="(max-width: 1000px) 100vw, 1000px" data-recalc-dims="1" /></figure>



<p>Continuing our analogy, <a href="https://github.com/moby/buildkit">BuildKit</a> is a compiler, just like <a href="https://en.wikipedia.org/wiki/LLVM">LLVM</a>.&nbsp; But whereas a compiler takes source code and libraries and produces an executable, BuildKit takes a Dockerfile and a file path and creates a container image.</p>



<figure class="wp-block-image size-large"><img data-attachment-id="27811" data-permalink="https://www.docker.com/blog/compiling-containers-dockerfiles-llvm-and-buildkit/3-5/" data-orig-file="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/3.png?fit=1182%2C930&amp;ssl=1" data-orig-size="1182,930" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="3" data-image-description="" data-medium-file="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/3.png?fit=381%2C300&amp;ssl=1" data-large-file="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/3.png?fit=1110%2C873&amp;ssl=1" loading="lazy" width="1182" height="930" src="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/3.png?resize=1182%2C930&#038;ssl=1" alt="" class="wp-image-27811" srcset="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/3.png?resize=1110%2C873&amp;ssl=1 1110w, https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/3.png?resize=381%2C300&amp;ssl=1 381w, https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/3.png?w=1182&amp;ssl=1 1182w" sizes="(max-width: 1000px) 100vw, 1000px" data-recalc-dims="1" /></figure>



<p>Docker build uses BuildKit, to turn a Dockerfile into a docker image, OCI image, or another image format.&nbsp; In this walk-through, we will primarily use BuildKit directly.</p>



<figure class="wp-block-image size-large"><img data-attachment-id="27812" data-permalink="https://www.docker.com/blog/compiling-containers-dockerfiles-llvm-and-buildkit/4-5/" data-orig-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/4.png?fit=853%2C611&amp;ssl=1" data-orig-size="853,611" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="4" data-image-description="" data-medium-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/4.png?fit=419%2C300&amp;ssl=1" data-large-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/4.png?fit=853%2C611&amp;ssl=1" loading="lazy" width="853" height="611" src="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/4.png?resize=853%2C611&#038;ssl=1" alt="" class="wp-image-27812" srcset="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/4.png?w=853&amp;ssl=1 853w, https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/4.png?resize=419%2C300&amp;ssl=1 419w" sizes="(max-width: 853px) 100vw, 853px" data-recalc-dims="1" /></figure>



<p>This <a href="https://blog.earthly.dev/what-is-buildkit-and-what-can-i-do-with-it/">primer on using BuildKit</a> supplies some helpful background on using BuildKit, `buildkitd`, and `buildctl` via the command-line. However, the only prerequisite for today is running `brew install buildkit` or the appropriate OS <a href="https://github.com/moby/buildkit#quick-start">equivalent</a> steps.</p>



<h2>How Do Compilers Work?</h2>



<p>A traditional compiler takes code in a high-level language and lowers it to a lower-level language. In most conventional ahead-of-time compilers, the final target is machine code. Machine code is a low-level programming language that your CPU understands.</p>



<p><img src="https://s.w.org/images/core/emoji/13.0.1/72x72/2139.png" alt="ℹ" class="wp-smiley" style="height: 1em; max-height: 1em;" /> Fun Fact: Machine Code VS. Assembly</p>



<p><em>Machine code is written in binary. This makes it hard for a human to understand.&nbsp; Assembly code is a plain-text representation of machine code that is designed to be somewhat human-readable. There is generally a 1-1 mapping between instructions the machine understands (in machine code) and the OpCodes in Assembly</em></p>



<p>Compiling the classic C &#8220;Hello, World&#8221; into x86 assembly code using the Clang frontend for LLVM looks like this:</p>



<figure class="wp-block-image size-large"><img data-attachment-id="27813" data-permalink="https://www.docker.com/blog/compiling-containers-dockerfiles-llvm-and-buildkit/5-5/" data-orig-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/5.png?fit=512%2C166&amp;ssl=1" data-orig-size="512,166" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="5" data-image-description="" data-medium-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/5.png?fit=512%2C166&amp;ssl=1" data-large-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/5.png?fit=512%2C166&amp;ssl=1" loading="lazy" width="512" height="166" src="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/5.png?resize=512%2C166&#038;ssl=1" alt="" class="wp-image-27813" data-recalc-dims="1"/></figure>



<p>Creating an image from a dockerfile works a similar way:</p>



<figure class="wp-block-image size-large"><img data-attachment-id="27814" data-permalink="https://www.docker.com/blog/compiling-containers-dockerfiles-llvm-and-buildkit/6-2/" data-orig-file="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/6.png?fit=512%2C151&amp;ssl=1" data-orig-size="512,151" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="6" data-image-description="" data-medium-file="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/6.png?fit=512%2C151&amp;ssl=1" data-large-file="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/6.png?fit=512%2C151&amp;ssl=1" loading="lazy" width="512" height="151" src="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/6.png?resize=512%2C151&#038;ssl=1" alt="" class="wp-image-27814" data-recalc-dims="1"/></figure>



<p>BuildKit is passed the Dockerfile and the build context, which is the present working directory in the above diagram. In simplified terms, each line in the dockerfile is turned into a layer in the resulting image.&nbsp; One significant way image building differs from compiling is this build context.&nbsp; A compiler&#8217;s input is limited to source code, whereas `docker build` takes a reference to the host filesystem as an input and uses it to perform actions such as `COPY`.</p>



<h2>There Is a Catch</h2>



<p>The earlier diagram of compiling &#8220;Hello, World&#8221; in a single step missed a vital detail. Computer hardware is not a singular thing. If every compiler were a hand-coded mapping from a high-level language to x86 machine code, then moving to the Apple M1 processor would be quite challenging because it has a different instruction set.&nbsp;&nbsp;</p>



<p>Compiler authors have overcome this challenge by splitting compilation into phases.&nbsp; The traditional phases are the frontend, the backend, and the middle. The middle phase is sometimes called the optimizer, and it deals primarily with an internal representation (IR).</p>



<figure class="wp-block-image size-large"><img data-attachment-id="27815" data-permalink="https://www.docker.com/blog/compiling-containers-dockerfiles-llvm-and-buildkit/7-3/" data-orig-file="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/7.png?fit=512%2C127&amp;ssl=1" data-orig-size="512,127" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="7" data-image-description="" data-medium-file="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/7.png?fit=512%2C127&amp;ssl=1" data-large-file="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/7.png?fit=512%2C127&amp;ssl=1" loading="lazy" width="512" height="127" src="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/7.png?resize=512%2C127&#038;ssl=1" alt="" class="wp-image-27815" data-recalc-dims="1"/></figure>



<p>This staged approach means you don&#8217;t need a new compiler for each new machine architecture. Instead, you just need a new backend. Here is an example of what that looks like in <a href="https://llvm.org/">LLVM</a>:</p>



<figure class="wp-block-image size-large"><img data-attachment-id="27816" data-permalink="https://www.docker.com/blog/compiling-containers-dockerfiles-llvm-and-buildkit/attachment/9/" data-orig-file="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/9.png?fit=512%2C277&amp;ssl=1" data-orig-size="512,277" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="9" data-image-description="" data-medium-file="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/9.png?fit=512%2C277&amp;ssl=1" data-large-file="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/9.png?fit=512%2C277&amp;ssl=1" loading="lazy" width="512" height="277" src="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/9.png?resize=512%2C277&#038;ssl=1" alt="" class="wp-image-27816" data-recalc-dims="1"/></figure>



<h2>Intermediate Representations</h2>



<p>This multiple backend approach allows LLVM to target ARM, X86, and many other machine architectures using LLVM Intermediate Representation (IR) as a standard protocol.&nbsp; LLVM IR is a human-readable programming language that backends need to be able to take as input. To create a new backend, you need to write a translator from LLVM IR to your target machine code. That translation is the primary job of each backend.</p>



<p>Once you have this IR, you have a protocol that various phases of the compiler can use as an interface, and you can build not just many backends but many frontends as well. LLVM has frontends for numerous languages, including C++, Julia, Objective-C, Rust, and Swift.&nbsp;&nbsp;</p>



<figure class="wp-block-image size-large"><img data-attachment-id="27817" data-permalink="https://www.docker.com/blog/compiling-containers-dockerfiles-llvm-and-buildkit/attachment/10/" data-orig-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/10.png?fit=512%2C267&amp;ssl=1" data-orig-size="512,267" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="10" data-image-description="" data-medium-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/10.png?fit=512%2C267&amp;ssl=1" data-large-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/10.png?fit=512%2C267&amp;ssl=1" loading="lazy" width="512" height="267" src="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/10.png?resize=512%2C267&#038;ssl=1" alt="" class="wp-image-27817" srcset="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/10.png?w=512&amp;ssl=1 512w, https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/10.png?resize=285%2C150&amp;ssl=1 285w" sizes="(max-width: 512px) 100vw, 512px" data-recalc-dims="1" /></figure>



<p>If you can write a translation from your language to LLVM IR, LLVM can translate that IR into machine code for all the backends it supports. This translation function is the primary job of a compiler frontend.</p>



<p>In practice, there is much more to it than that. Frontends need to tokenize and parse input files, and they need to return pleasant errors. Backends often have target-specific optimizations to perform and heuristics to apply. But for this tutorial, the critical point is that having a standard representation ends up being a bridge that connects many front ends with many backends. This shared interface removes the need to create a compiler for every combination of language and machine architecture. It is a simple but very empowering trick!</p>



<h2>BuildKit</h2>



<p>Images, unlike executables, have their own isolated filesystem. Nevertheless, the task of building an image looks very similar to compiling an executable. They can have varying syntax (dockerfile1.0, dockerfile1.2), and the result must target several machine architectures (arm64 vs. x86_64).</p>



<p><em>&#8220;LLB is to Dockerfile what LLVM IR is to C&#8221; </em>&#8211; <a href="https://github.com/moby/buildkit/blob/master/README.md">BuildKit Readme</a></p>



<p>This similarity was not lost on the BuildKit creators.&nbsp; BuildKit has its own intermediate representation, LLB.&nbsp; And where LLVM IR has things like function calls and garbage-collection strategies, LLB has mounting filesystems and executing statements.</p>



<figure class="wp-block-image size-large"><img data-attachment-id="27818" data-permalink="https://www.docker.com/blog/compiling-containers-dockerfiles-llvm-and-buildkit/attachment/11/" data-orig-file="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/11.png?fit=1576%2C988&amp;ssl=1" data-orig-size="1576,988" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="11" data-image-description="" data-medium-file="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/11.png?fit=479%2C300&amp;ssl=1" data-large-file="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/11.png?fit=1110%2C696&amp;ssl=1" loading="lazy" width="1110" height="696" src="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/11.png?resize=1110%2C696&#038;ssl=1" alt="" class="wp-image-27818" srcset="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/11.png?resize=1110%2C696&amp;ssl=1 1110w, https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/11.png?resize=479%2C300&amp;ssl=1 479w, https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/11.png?resize=1536%2C963&amp;ssl=1 1536w, https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/11.png?w=1576&amp;ssl=1 1576w" sizes="(max-width: 1000px) 100vw, 1000px" data-recalc-dims="1" /></figure>



<p><a href="https://github.com/moby/buildkit/blob/ebd98bcbe600c662a72ce9725417540f277be4d6/solver/pb/ops.proto">LLB</a> is defined as a protocol buffer, and this means that BuildKit frontends can make GRPC requests against buildkitd to build a container directly.</p>



<figure class="wp-block-image size-large"><img data-attachment-id="27819" data-permalink="https://www.docker.com/blog/compiling-containers-dockerfiles-llvm-and-buildkit/attachment/12/" data-orig-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/12.png?fit=512%2C159&amp;ssl=1" data-orig-size="512,159" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="12" data-image-description="" data-medium-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/12.png?fit=512%2C159&amp;ssl=1" data-large-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/12.png?fit=512%2C159&amp;ssl=1" loading="lazy" width="512" height="159" src="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/12.png?resize=512%2C159&#038;ssl=1" alt="" class="wp-image-27819" data-recalc-dims="1"/></figure>



<h2>Programmatically Making An Image</h2>



<p>Alright, enough background.&nbsp; Let&#8217;s programmatically generate the LLB for an image and then build an image.&nbsp;&nbsp;</p>



<p><img src="https://s.w.org/images/core/emoji/13.0.1/72x72/2139.png" alt="ℹ" class="wp-smiley" style="height: 1em; max-height: 1em;" /> Using Go</p>



<p><em>In this example, we will be using Go which lets us leverage existing BuildKit libraries, but it&#8217;s possible to accomplish this in any language with Protocol Buffer support.</em></p>



<p>Import LLB definitions:</p>



<pre class="wp-block-code"><code>import (
	"github.com/moby/buildkit/client/llb"
)
</code></pre>



<p>Create LLB for an Alpine image:</p>



<pre class="wp-block-code"><code><code>func createLLBState() llb.State {   
 return llb.Image("docker.io/library/alpine").        
   File(llb.Copy(llb.Local("context"), "README.md", "README.md")).       
   Run(llb.Args(&#91;]string{"/bin/sh", "-c", "echo \"programmatically built\" > /built.txt"})).      
Root()  
}</code></code></pre>



<p>We are accomplishing the equivalent of a `FROM` by using `llb.Image`. Then, we copy a file from the local file system into the image using `File` and `Copy`.&nbsp; Finally, we `RUN` a command to echo some text to a file.&nbsp; LLB has many more operations, but you can recreate many standard images with these three building blocks.</p>



<p>The final thing we need to do is turn this into protocol-buffer and emit it to standard out:</p>



<div class="wp-block-group"><div class="wp-block-group__inner-container">
<div class="wp-block-group"><div class="wp-block-group__inner-container">
<pre class="wp-block-code"><code>func main() {

	dt, err := createLLBState().Marshal(context.TODO(), llb.LinuxAmd64)
	if err != nil {
		panic(err)
	}
	llb.WriteTo(dt, os.Stdout)
}</code></pre>
</div></div>
</div></div>



<p>Let&#8217;s look at the what this generates using the `dump-llb` option of buildctl:</p>



<div class="wp-block-group"><div class="wp-block-group__inner-container">
<div class="wp-block-group"><div class="wp-block-group__inner-container">
<div class="wp-block-group"><div class="wp-block-group__inner-container">
<pre class="wp-block-code"><code> go run ./writellb/writellb.go | 
 buildctl debug dump-llb | 
 jq .</code></pre>



<p></p>
</div></div>
</div></div>
</div></div>



<p>We get this JSON formatted LLB:</p>



<pre class="wp-block-code"><code>{
  "Op": {
    "Op": {
      "source": {
        "identifier": "local://context",
        "attrs": {
          "local.unique": "s43w96rwjsm9tf1zlxvn6nezg"
        }
      }
    },
    "constraints": {}
  },
  "Digest": "sha256:c3ca71edeaa161bafed7f3dbdeeab9a5ab34587f569fd71c0a89b4d1e40d77f6",
  "OpMetadata": {
    "caps": {
      "source.local": true,
      "source.local.unique": true
    }
  }
}
{
  "Op": {
    "Op": {
      "source": {
        "identifier": "docker-image://docker.io/library/alpine:latest"
      }
    },
    "platform": {
      "Architecture": "amd64",
      "OS": "linux"
    },
    "constraints": {}
  },
  "Digest": "sha256:665ba8b2cdc0cb0200e2a42a6b3c0f8f684089f4cd1b81494fbb9805879120f7",
  "OpMetadata": {
    "caps": {
      "source.image": true
    }
  }
}
{
  "Op": {
    "inputs": &#91;
      {
        "digest": "sha256:665ba8b2cdc0cb0200e2a42a6b3c0f8f684089f4cd1b81494fbb9805879120f7",
        "index": 0
      },
      {
        "digest": "sha256:c3ca71edeaa161bafed7f3dbdeeab9a5ab34587f569fd71c0a89b4d1e40d77f6",
        "index": 0
      }
    ],
    "Op": {
      "file": {
        "actions": &#91;
          {
            "input": 0,
            "secondaryInput": 1,
            "output": 0,
            "Action": {
              "copy": {
                "src": "/README.md",
                "dest": "/README.md",
                "mode": -1,
                "timestamp": -1
              }
            }
          }
        ]
      }
    },
    "platform": {
      "Architecture": "amd64",
      "OS": "linux"
    },
    "constraints": {}
  },
  "Digest": "sha256:ba425dda86f06cf10ee66d85beda9d500adcce2336b047e072c1f0d403334cf6",
  "OpMetadata": {
    "caps": {
      "file.base": true
    }
  }
}
{
  "Op": {
    "inputs": &#91;
      {
        "digest": "sha256:ba425dda86f06cf10ee66d85beda9d500adcce2336b047e072c1f0d403334cf6",
        "index": 0
      }
    ],
    "Op": {
      "exec": {
        "meta": {
          "args": &#91;
            "/bin/sh",
            "-c",
            "echo "programmatically built" > /built.txt"
          ],
          "cwd": "/"
        },
        "mounts": &#91;
          {
            "input": 0,
            "dest": "/",
            "output": 0
          }
        ]
      }
    },
    "platform": {
      "Architecture": "amd64",
      "OS": "linux"
    },
    "constraints": {}
  },
  "Digest": "sha256:d2d18486652288fdb3516460bd6d1c2a90103d93d507a9b63ddd4a846a0fca2b",
  "OpMetadata": {
    "caps": {
      "exec.meta.base": true,
      "exec.mount.bind": true
    }
  }
}
{
  "Op": {
    "inputs": &#91;
      {
        "digest": "sha256:d2d18486652288fdb3516460bd6d1c2a90103d93d507a9b63ddd4a846a0fca2b",
        "index": 0
      }
    ],
    "Op": null
  },
  "Digest": "sha256:fda9d405d3c557e2bd79413628a435da0000e75b9305e52789dd71001a91c704",
  "OpMetadata": {
    "caps": {
      "constraints": true,
      "platform": true
    }
  }
}</code></pre>



<p>Looking through the output, we can see how our code maps to LLB.</p>



<p>Here is our `Copy` as part of a FileOp:</p>



<pre class="wp-block-code"><code>    "Action": {
              "copy": {
                "src": "/README.md",
                "dest": "/README.md",
                "mode": -1,
                "timestamp": -1
              }</code></pre>



<p>Here is mapping our build context for use in our `COPY` command:</p>



<pre class="wp-block-code"><code>  "Op": {
      "source": {
        "identifier": "local://context",
        "attrs": {
          "local.unique": "s43w96rwjsm9tf1zlxvn6nezg"
        }
      }</code></pre>



<p>Similarly, the output contains LLB that corresponds to our&nbsp; <code>`RUN`</code> and <code>`FROM` </code>commands.&nbsp;</p>



<h2>Building Our LLB</h2>



<p>To build our image, we must first start `buildkitd`:</p>



<pre class="wp-block-code"><code>docker run --rm --privileged -d --name buildkit moby/buildkit
export BUILDKIT_HOST=docker-container://buildkit</code></pre>



<p>To build our image, we must first start `buildkitd`:</p>



<pre class="wp-block-code"><code>go run ./writellb/writellb.go | 
buildctl build 
--local context=. 
--output type=image,name=docker.io/agbell/test,push=true</code></pre>



<p>The output flag lets us specify what backend we want BuildKit to use.&nbsp; We will ask it to build an OCI image and push it to docker.io.&nbsp;</p>



<p><img src="https://s.w.org/images/core/emoji/13.0.1/72x72/2139.png" alt="ℹ" class="wp-smiley" style="height: 1em; max-height: 1em;" /> Real-World Usage</p>



<p><em>In the real-world tool, we might want to programmatically make sure `buildkitd` is running and send the RPC request directly to it, as well as provide friendly error messages. For tutorial purposes, we will skip all that.</em></p>



<p>We can run it like this:</p>



<pre class="wp-block-code"><code>> docker run -it --pull always agbell/test:latest /bin/sh</code></pre>



<p>And we can then see the results of our programmatic `COPY` and `RUN` commands:</p>



<pre class="wp-block-code"><code>/ # cat built.txt 
programmatically built
/ # ls README.md
README.md</code></pre>



<p>There we go! The <a href="https://github.com/agbell/compiling-containers/blob/main/writellb/writellb.go">full code example</a> can be a great starting place for your own programmatic docker image building.</p>



<h2>A True Frontend for BuildKit</h2>



<p>A true compiler front end does more than just emit hardcoded IR.&nbsp; A proper frontend takes in files, tokenizes them, parses them, generates a syntax tree, and then lowers that syntax tree into the internal representation. <a href="https://matt-rickard.com/building-a-new-dockerfile-frontend/">Mockerfiles</a> are an example of such a frontend:</p>



<pre class="wp-block-code"><code><code>#syntax=r2d4/mocker
apiVersion: v1alpha1
images:- name: demo
  from: ubuntu:16.04
  package:
    install:
    - curl
    - git
    - gcc
</code></code></pre>



<p>And because Docker build supports the `#syntax` command we can even build a Mockerfiles directly with `docker build`.&nbsp;</p>



<pre class="wp-block-code"><code>docker build -f mockerfile.yaml</code></pre>



<p>To support the #syntax command, all that is needed is to put the frontend in a docker image that accepts a gRPC request in the correct format, publish that image somewhere.&nbsp; At that point, anyone can use your frontend `docker build` by just using `#syntax=yourimagename`.</p>



<h2>Building Our Own Example Frontend for `docker build`</h2>



<p>Building a tokenizer and a parser as a gRPC service is beyond the scope of this article. But we can get our feet wet by extracting and modifying an existing frontend. The standard <a href="https://github.com/moby/buildkit/tree/master/frontend/dockerfile">dockerfile frontend</a> is easy to disentangle from the moby project. I&#8217;ve pulled the relevant parts out into a <a href="https://github.com/agbell/compiling-containers/tree/main/ickfile">stand-alone repo</a>. Let&#8217;s make some trivial modifications to it and test it out.</p>



<p>So far, we&#8217;ve only used the docker commands `FROM`, `RUN` and `COPY`.&nbsp; At a surface level, with its capitalized commands, Dockerfile syntax looks a lot like the programming language <a href="https://blog.earthly.dev/intercal-yaml-and-other-horrible-programming-languages/">INTERCAL</a>. Let change these commands to their INTERCAL equivalent and develop our own Ickfile format.</p>



<figure class="wp-block-table"><table><tbody><tr><td><strong>Dockerfile</strong></td><td><strong>Ickfile</strong></td></tr><tr><td>FROM</td><td>COME FROM</td></tr><tr><td>RUN</td><td>PLEASE</td></tr><tr><td>COPY</td><td>STASH</td></tr></tbody></table></figure>



<p>The modules in the dockerfile frontend split the parsing of the input file into several discrete steps, with execution flowing this way:</p>



<figure class="wp-block-image size-large"><img data-attachment-id="27820" data-permalink="https://www.docker.com/blog/compiling-containers-dockerfiles-llvm-and-buildkit/attachment/13/" data-orig-file="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/13.png?fit=512%2C127&amp;ssl=1" data-orig-size="512,127" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="13" data-image-description="" data-medium-file="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/13.png?fit=512%2C127&amp;ssl=1" data-large-file="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/13.png?fit=512%2C127&amp;ssl=1" loading="lazy" width="512" height="127" src="https://i0.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/13.png?resize=512%2C127&#038;ssl=1" alt="" class="wp-image-27820" data-recalc-dims="1"/></figure>



<p>For this tutorial, we are only going to make trivial changes to the frontend.&nbsp; We will leave all the stages intact and focus on customizing the existing commands to our tastes.&nbsp; To do this, all we need to do is change `command.go`:</p>


<pre class="wp-block-code"><code>package command

// Define constants for the command strings
const (
	Copy        = "stash"
	Run         = "please"
	From        = "come_from"
	...
)
</code></pre>


<p>And we can then see results of our `STASH` and `PLEASE` commands:</p>



<pre class="wp-block-code"><code>/ # cat built.txt 
custom frontend built
/ # ls README.md
README.md</code></pre>



<p>I&#8217;ve pushed this image to dockerhub.&nbsp; Anyone can start building images using our `ickfile` format by adding `#syntax=agbell/ick` to an existing Dockerfile. No manual installation is required!</p>



<p><img src="https://s.w.org/images/core/emoji/13.0.1/72x72/2139.png" alt="ℹ" class="wp-smiley" style="height: 1em; max-height: 1em;" /> Enabling BuildKit</p>



<p><em>BuildKit is enabled by default on Docker Desktop. It is not enabled by default in the current version of Docker for Linux (`version 20.10.5`). To instruct `docker build` to use BuildKit set the following environment variable `DOCKER_BUILDKIT=1` or </em><a href="https://docs.docker.com/develop/develop-images/build_enhancements/#to-enable-buildkit-builds"><em>change the Engine config</em></a><em>.</em></p>



<h2>Conclusion</h2>



<p>We have learned that a three-phased structure borrowed from compilers powers building images, that an intermediate representation called LLB is the key to that structure.&nbsp; Empowered by the knowledge, we have produced two frontends for building images.&nbsp;&nbsp;</p>



<p>This deep dive on frontends still leaves much to explore.&nbsp; If you want to learn more, I suggest looking into BuildKit workers.&nbsp; Workers do the actual building and are the secret behind `docker buildx`, and <a href="https://docs.docker.com/buildx/working-with-buildx/">multi-archtecture builds</a>. `docker build` also has support for remote workers and cache mounts, both of which can lead to faster builds.</p>



<p><a href="http://earthly.dev/">Earthly</a> uses BuildKit internally for its repeatable build syntax. Without it, our containerized Makefile-like syntax would not be possible. If you want a saner CI process, then <a href="http://earthly.dev/">you should check it out</a>.</p>



<p>There is also much more to explore about how modern compilers work. Modern compilers often have many stages and more than one intermediate representation, and they are often able to do very sophisticated optimizations.</p>
<p>The post <a rel="nofollow" href="https://www.docker.com/blog/compiling-containers-dockerfiles-llvm-and-buildkit/">Compiling Containers &#8211; Dockerfiles, LLVM and BuildKit</a> appeared first on <a rel="nofollow" href="https://www.docker.com/blog">Docker Blog</a>.</p>
