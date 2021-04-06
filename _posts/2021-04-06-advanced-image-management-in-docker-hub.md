---
title : "Advanced Image Management in Docker Hub"
layout: post
tags: tutorial labnol
post_inspiration: https://www.docker.com/blog/advanced-image-management-in-docker-hub/
image: "https://sergio.afanou.com/assets/images/image-midres-7.jpg"
---


<p>We are excited to announce the latest feature for Docker Pro and Team users, our new Advanced Image Management Dashboard available on Docker Hub. The new dashboard provides developers with a new level of access to all of the content you have stored in Docker Hub providing you with more fine grained control over removing old content and exploring old versions of pushed images.&nbsp;</p>



<figure class="wp-block-image"><img src="https://lh5.googleusercontent.com/bNo7maa5d14SyFmNH9tLTERYqU6i9y2G9lUN1qcVYYmbcrKbj_4wCCWVcs-fTdZ9_ar1vCsYtksW3lOF1UEh_dyrpL8s5e5PgU1mQtzm9gZfjmk7OUaM8yYxzSJGCYVPbAo-u326" alt=""/></figure>



<p>Historically in Docker Hub we have had visibility into the latest version of a tag that a user has pushed, but what has been very hard to see or even understand is what happened to all of those old things that you pushed. When you push an image to Docker Hub you are pushing a manifest, a list of all of the layers of your image, and the layers themselves.</p>



<div class="wp-block-image"><figure class="aligncenter"><img src="https://lh5.googleusercontent.com/AZcGVy39ZckgIOHKICWGndAXa_4zNJIpC9NJ2cqKYb1d1NmowN0U2e_yIU_MTQ8BglTPmZK-5w-xcUw3zmcnCH6KzIVoRKHG5tFSepTX8g82MvwqlEpwzAUuxx_LnBHFvH5aWImE" alt=""/></figure></div>



<p>When you are updating an existing tag, only the new layers will be pushed along with the new manifest which references these layers. This new manifest will be given the tag you specify when you push, such as bengotch/simplewhale:latest. But this does not mean that all of those old manifests which point at the previous layers that made up your image are removed from Hub. These are still here, there is just no way to easily see them or to manage that content. You can in fact still use and reference these using the digest of the manifest if you know it. You can kind of think of this like your commit history (the old digests) to a particular branch (your tag) of your repo (your image repo!). </p>



<figure class="wp-block-image size-large"><img data-attachment-id="27788" data-permalink="https://www.docker.com/blog/advanced-image-management-in-docker-hub/image-4/" data-orig-file="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/image.png?fit=1102%2C588&amp;ssl=1" data-orig-size="1102,588" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="image" data-image-description="" data-medium-file="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/image.png?fit=562%2C300&amp;ssl=1" data-large-file="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/image.png?fit=1102%2C588&amp;ssl=1" loading="lazy" width="1102" height="588" src="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/image.png?resize=1102%2C588&#038;ssl=1" alt="" class="wp-image-27788" srcset="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/image.png?w=1102&amp;ssl=1 1102w, https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/03/image.png?resize=562%2C300&amp;ssl=1 562w" sizes="(max-width: 1000px) 100vw, 1000px" data-recalc-dims="1" /></figure>



<p>This means you can have hundreds of old versions of images which your systems can still be pulling by hash rather than by the tag and you may be unaware which old versions are still in use. Along with this the only way until now to remove these old versions was to delete the entire repo and start again!</p>



<p>With the release of the image management dashboard we have provided a new GUI with all of this information available to you including whether those currently ‘untagged old manifests’ are still ‘active’ (have been pulled in the last month) or whether they are inactive. This combined with the new bulk delete for these objects and current tags provides you a more powerful tool for batch managing your content in Docker Hub.&nbsp;</p>



<p>To get started you will find a new banner on your repos page if you have inactive images:</p>



<figure class="wp-block-image"><img src="https://lh5.googleusercontent.com/5s7SoA-Gs8SbUdzXPweUTtZ8_TPsWQl5dXWHyzQul1yjIORZykn5ombOprQkcVP1oGFET-C9ml633S70EPj8G-ZVw18JY-k8ASanCPKTxhMZI4RVV3JJxZqma6PZRGXPQs9cfHVI" alt=""/></figure>



<p>This will tell you how many images you have, tagged or old, which have not been pushed or pulled to in the last month. By clicking view you can go through to the new Advanced Image Management Dashboard to check out all your content, from here you can see what the tags of certain manifests used to be and use the multi-selector option to bulk delete these.&nbsp;</p>



<p>For a full product tour check out our overview video of the feature below.</p>



<figure class="wp-block-embed-youtube wp-block-embed is-type-video is-provider-youtube wp-embed-aspect-16-9 wp-has-aspect-ratio"><div class="wp-block-embed__wrapper">
<iframe class='youtube-player' width='640' height='360' src='https://www.youtube.com/embed/luH1Z8IFGy4?version=3&#038;rel=1&#038;showsearch=0&#038;showinfo=1&#038;iv_load_policy=1&#038;fs=1&#038;hl=en-US&#038;autohide=2&#038;wmode=transparent' allowfullscreen='true' style='border:0;' sandbox='allow-scripts allow-same-origin allow-popups allow-presentation'></iframe>
</div></figure>



<p></p>



<p>We hope that you are excited for the first step of us providing greater insight into your content on Docker Hub, if you want to get started exploring your content then all users can see how many inactive images they have and Pro &amp; Team users can see which tags these used to be associated with, what the hashes of these are and start removing these today. To find out more about becoming a Pro or Team user check out&nbsp;<a href="https://www.docker.com/pricing">this page</a>.</p>
<p>The post <a rel="nofollow" href="https://www.docker.com/blog/advanced-image-management-in-docker-hub/">Advanced Image Management in Docker Hub</a> appeared first on <a rel="nofollow" href="https://www.docker.com/blog">Docker Blog</a>.</p>
