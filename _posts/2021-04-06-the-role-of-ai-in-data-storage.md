---
title : "The Role of AI in Data Storage"
layout: post
tags: tutorial labnol
post_inspiration: https://gigaom.com/2021/02/19/the-role-of-ai-in-data-storage/
image: "https://sergio.afanou.com/assets/images/image-midres-53.jpg"
---

<p>In the past, data storage was one of the most conservative areas of IT. Storage system administrators were typically depicted as grumpy people with limited or no interest in change. Most of the time they wanted to keep it simple. “If it ain’t broke, don’t fix it” was their mantra. It’s hard to blame them.I was a storage sys admin once; solid data protection was not easily obtained, resources were limited and needed to be carefully managed, and the amount of data was less.</p>
<p>But things radically changed in the last few years. Flash memory, scale-out systems, and the cloud all changed paradigms and now we have plenty of resources to play with. All modern systems can be considered very mature when it comes to basic functionality (this is why we have a table stakes section in our report—to address the features you can take for granted).</p>
<p>The challenge now is that there are two other major changes to storage:</p>
<ul>
<li>There are less storage admins and more full-stack engineers. IT organizations tend to push more on hiring a jack of all trades than specialized and skilled admins now. They look for infrastructure simplification and to the standardization of as many processes as they can. Can you blame them for it?</li>
<li>Organizations need to increase the TB/Sysadmin ratio to keep total cost of ownership (TCO) at bay. We need more automation everywhere to keep things running.</li>
</ul>
<p>These two aspects are connected, of course. More data to manage shouldn’t result in a linear increase in costs or the infrastructure will quickly become too expensive, to the point of undermining any business initiative.</p>
<p><strong>Evolution of Analytics and AI</strong><br />
All modern infrastructure can collect much more data than previously possible. Sensor data contains rafts of information about what is happening in the system. Many data points enable the user to get a real-time view of any potential issue.</p>
<p>A few years back, companies like Nimble Storage started to build on this data collection and created user-friendly big data systems to compare sensor data collected in your system with those from other users. At that time this was a major differentiator and, still today, I think HPE (now HPE InfoSight) acquired them for this reason. Many vendors started to do the same and built their analytics systems. Now every vendor in the market offers a solution that features visibility into your system.</p>
<p>The information collected by the analytics system can be helpful in many situations, including capacity planning, support, and day-to-day operations. After a while, we also started to see active advising from these analytics platforms. By consolidating data in the cloud and comparing different systems behaviors, they were able to suggest configurations or tweaks that could improve system utilization, performance, and reliability.</p>
<p>Things evolved quickly, and ML and AI have started to surface. Of course, with all this data collected, it was just a matter of time before predictive analytics platforms began to emerge.</p>
<p><strong>Automation and Autopilots</strong><br />
Now, thanks to these new AI-based approaches, anomalies are easier to spot before they escalate into full-blown issues. And the system can help anticipate the necessary action to solve potential problems. This improves system support and pre-emptive maintenance, limiting service disruption.</p>
<p>Many users are still skeptical about ML and AI (some of the conservatism of old sysadmins is still with us), but they use these AI platforms to visualize the problem, make the necessary changes, and keep the system at maximum efficiency. In other cases, automation is becoming more common and, after a while, the sys admins automate some of these repetitive tasks. This frees up some of their time so it can be better spent performing other activities.</p>
<p>Storage on autopilot is not something that many systems can provide—and I’m pretty sure that many sysadmins are not ready for it yet. But it will become a strong option eventually, and storage will become more autonomous in all aspects. Call it AIOps if you want. This, of course, will free additional time that will be used for other activities and dramatically increase that sysadmin/PB ratio that I mentioned earlier.</p>
<p>An example of AI-driven analytics and how it can be of help in storage management can be found on a couple of videos recorded not a long ago at Storage Field Day 21, where Tintri was demoing the latest AI-based features implemented for its systems. The videos are worth a watch and can give you an idea of the potential of this type of system.</p>
<p><iframe title="Tintri Anomaly Detection Demonstration" width="500" height="281" src="https://www.youtube.com/embed/hrATdtjZ8GE?feature=oembed" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></p>
<p><iframe title="Tintri Analytics Advisor Demonstration" width="500" height="281" src="https://www.youtube.com/embed/uJW5C-w8NZk?feature=oembed" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></p>
<p><strong>Closing the Circle</strong><br />
AI-powered infrastructure is slowly becoming a reality as more vendors focus their efforts on building this kind of solution to enable their customers to do more in less time. From this point of view, we are slowly entering the AIOps era with more autonomous infrastructure components that can take care of themselves.</p>
<p>Unfortunately, we are still at the beginning and there is a lot to do to make these systems completely reliable and secure. And there is also the matter of winning the hearts and minds of the user base—that will take some time.</p>
<p>Last but not least, to be honest, I would love to see a single AIOPs system that can manage all my infrastructure, instead of separate AIs focused on a single vendor’s gear or, worse, a single system. It’s wishful thinking at the moment, I know, and will probably remain so for a long time. But we can always hope.</p>