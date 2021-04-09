---
title : "Changing How Updates Work with Docker Desktop 3.3"
layout: post
tags: tutorial labnol
post_inspiration: https://www.docker.com/blog/changing-how-updates-work-with-docker-desktop-3-3/
image: "https://sergio.afanou.com/assets/images/image-midres-27.jpg"
---


<p>Today we are pleased to announce the release of Docker Desktop 3.3.</p>



<p>We’ve been listening to your feedback on our <a href="https://github.com/docker/roadmap">Public Roadmap</a> and we are consistently asked for three things: smaller downloads, more flexible installation options, and more frequent feature releases, bug fixes, and security updates.</p>



<p>We also heard from our community that the smaller updates are appreciated, requiring immediate installation is not convenient, and automatic background downloads are problematic for developers on constrained or metered bandwidth.</p>



<p>We’ve heard you and are changing how updates to Docker Desktop work, while still maintaining the ability to provide you with smaller, faster updates. We are also providing additional flexibility to developers with <strong>Pro or Team subscriptions</strong>.</p>



<h2>Flexibility for Updates&nbsp;</h2>



<p>With Docker Desktop 3.3, when a new update to Docker Desktop is available, it will no longer be automatically downloaded and installed on your next restart.<em> </em><strong><em>You can now choose when to start the download and installation process.</em></strong></p>



<figure class="wp-block-image size-large"><img data-attachment-id="27951" data-permalink="https://www.docker.com/blog/changing-how-updates-work-with-docker-desktop-3-3/screen-shot-2021-04-08-at-11-18-15-am/" data-orig-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/Screen-Shot-2021-04-08-at-11.18.15-AM.jpeg?fit=1113%2C365&amp;ssl=1" data-orig-size="1113,365" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="Screen-Shot-2021-04-08-at-11.18.15-AM" data-image-description="" data-medium-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/Screen-Shot-2021-04-08-at-11.18.15-AM.jpeg?fit=730%2C239&amp;ssl=1" data-large-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/Screen-Shot-2021-04-08-at-11.18.15-AM.jpeg?fit=1110%2C364&amp;ssl=1" loading="lazy" width="1110" height="364" src="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/Screen-Shot-2021-04-08-at-11.18.15-AM.jpeg?resize=1110%2C364&#038;ssl=1" alt="" class="wp-image-27951" srcset="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/Screen-Shot-2021-04-08-at-11.18.15-AM.jpeg?resize=1110%2C364&amp;ssl=1 1110w, https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/Screen-Shot-2021-04-08-at-11.18.15-AM.jpeg?resize=730%2C239&amp;ssl=1 730w, https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/Screen-Shot-2021-04-08-at-11.18.15-AM.jpeg?w=1113&amp;ssl=1 1113w" sizes="(max-width: 1000px) 100vw, 1000px" data-recalc-dims="1" /></figure>



<p>To encourage developers to stay up to date, we have built in increasingly persistent reminders after an update has become available.</p>



<p>If you use Docker Desktop at work you may need to skip a specific update. For this reason, <strong>Pro or Team subscription </strong>developers can skip notifications for a particular update when a reminder appears.</p>



<figure class="wp-block-image size-large"><img data-attachment-id="27949" data-permalink="https://www.docker.com/blog/changing-how-updates-work-with-docker-desktop-3-3/skipthisupdate/" data-orig-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/SkipThisUpdate.png?fit=480%2C251&amp;ssl=1" data-orig-size="480,251" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="SkipThisUpdate" data-image-description="" data-medium-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/SkipThisUpdate.png?fit=480%2C251&amp;ssl=1" data-large-file="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/SkipThisUpdate.png?fit=480%2C251&amp;ssl=1" loading="lazy" width="480" height="251" src="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/SkipThisUpdate.png?resize=480%2C251&#038;ssl=1" alt="" class="wp-image-27949" srcset="https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/SkipThisUpdate.png?w=480&amp;ssl=1 480w, https://i2.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/SkipThisUpdate.png?resize=285%2C150&amp;ssl=1 285w" sizes="(max-width: 480px) 100vw, 480px" data-recalc-dims="1" /></figure>



<p>Finally, developers in larger organizations, who don’t have administrative access to install updates to Docker Desktop, or are only allowed to upgrade to IT-approved versions, there is now an option in the Settings menu to opt out of notifications altogether for Docker Desktop updates if your Docker ID is part of a <strong>Team subscription</strong>.</p>



<figure class="wp-block-image size-large"><img data-attachment-id="27950" data-permalink="https://www.docker.com/blog/changing-how-updates-work-with-docker-desktop-3-3/teamupgrade/" data-orig-file="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/TeamUpgrade.png?fit=1082%2C169&amp;ssl=1" data-orig-size="1082,169" data-comments-opened="0" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;,&quot;orientation&quot;:&quot;0&quot;}" data-image-title="TeamUpgrade" data-image-description="" data-medium-file="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/TeamUpgrade.png?fit=730%2C114&amp;ssl=1" data-large-file="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/TeamUpgrade.png?fit=1082%2C169&amp;ssl=1" loading="lazy" width="1082" height="169" src="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/TeamUpgrade.png?resize=1082%2C169&#038;ssl=1" alt="" class="wp-image-27950" srcset="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/TeamUpgrade.png?w=1082&amp;ssl=1 1082w, https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/04/TeamUpgrade.png?resize=730%2C114&amp;ssl=1 730w" sizes="(max-width: 1000px) 100vw, 1000px" data-recalc-dims="1" /></figure>



<p>It’s your positive feedback that helps us continue to improve the Docker experience. We truly appreciate it. Please keep that feedback coming by raising tickets on our <a href="https://github.com/docker/roadmap">Public Roadmap</a>.</p>



<p>See the release notes for <a href="https://docs.docker.com/docker-for-mac/release-notes/">Docker Desktop for Mac</a> and <a href="https://docs.docker.com/docker-for-windows/release-notes/">Docker Desktop for Windows</a> for the complete set of changes in Docker Desktop 3.3 including the latest Compose release, update to Linux Kernel 5.10, and several other bug fixes and improvements you’ve been waiting for.</p>



<p>And check out our <a href="https://docs.docker.com/docker-for-mac/apple-m1/">Tech Preview</a> page for the latest updates on support for Apple Silicon (there’s an RC3!).<br></p>



<p>Interested in learning more about what else is included with a <strong>Pro or Team subscription </strong>in addition to more flexible update options? Check out our <a href="https://www.docker.com/pricing">pricing page</a> for a detailed breakdown.</p>
<p>The post <a rel="nofollow" href="https://www.docker.com/blog/changing-how-updates-work-with-docker-desktop-3-3/">Changing How Updates Work with Docker Desktop 3.3</a> appeared first on <a rel="nofollow" href="https://www.docker.com/blog">Docker Blog</a>.</p>
