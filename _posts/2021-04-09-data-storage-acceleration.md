---
title : "Data Storage Acceleration"
layout: post
tags: tutorial labnol
post_inspiration: https://gigaom.com/2021/04/08/data-storage-acceleration/
image: "https://sergio.afanou.com/assets/images/image-midres-37.jpg"
---

<p>Flash memory is now the standard for storing active data in the data center. NVMe and NVMe over Fiber (NVMe-oF) are on the rise and ensuring that storage is no longer the bottleneck in the infrastructure. On paper, CPUs now can access data as fast as they can and networks can be used at their full potential, opening the door to better performance and quicker results. In reality, everything is fast but also inefficient. Vendors solve their problems without addressing the bigger picture, resulting in increasing complexity and cost.</p>
<p>Over the last few months I’ve written several times about hardware acceleration and resources like GPUs, FPGAs, smart NICS, DPUs (Network Data Processing Units), and more. The point of these solutions is to improve overall system efficiency by offloading demanding tasks that consume CPU cycles and hamper application performance. This specialized hardware can significantly impact several kinds of operations.</p>
<h3>Two Approaches to Storage Acceleration</h3>
<p>The goal always is to achieve more and go faster. It is about scale, and it becomes more and more important because many applications need to access larger amounts of data. Faster is usually about latency, and the less of it the better.</p>
<p>Optimizing access to data improves efficiency of the entire stack, making it possible to do more with less compute resources. The advantages impact all the primary metrics of modern infrastructure design: power efficiency, compute density, physical footprint, and so on. In fact, for most users it is not about the best performance but about a well-balanced architecture and better TCO.</p>
<p>There are a couple of interesting and innovative approaches to storage acceleration that I didn’t mention in my previous articles: computational storage and storage processors.</p>
<p>Computational storage is a simple, yet powerful, concept. Instead of bringing data to the CPU, it brings the CPU to the data. In practice, this involves a flash memory device—an SSD—that has both RAM and CPU on board. This device can be programmed to act as a standard SSD, but data that lands in the device can be processed in-situ. This enables applications to offload many simple operations directly to the storage hardware—data doesn’t need to be moved to be analyzed and every new flash-memory device added to a server adds more computational resources that can work in parallel.</p>
<p>There are several applications that can benefit from this approach, such as image recognition or video analysis. You write the data once to the flash device and it is automatically indexed and catalogued depending on the content without ever touching the server’s CPU. To get a glimpse of the technology I’m talking about, check out the website of companies like <a href="https://www.ngdsystems.com" target="_blank" rel="noopener">NGD Systems</a>.</p>
<p>Another approach is to use an accelerator specifically designed to lighten the burden on the CPU. In this case, the storage accelerator vendor provides APIs or device drivers to replace what is usually done with standard libraries or other software components. Storage operations are performed on specialized hardware much more quickly than on a general-purpose CPU.</p>
<p>The advantage of this approach is that you don’t need to tune applications to support this model, but rather simply make minor changes to the application stack. A common example is the replacement of part of a database structure to accelerate indexes, queries, data storage, and information retrieval. A commercial example of this type of product would be <a href="https://pliops.com" target="_blank" rel="noopener">Pliops</a>. Here a video from a recent Storage Field Day event that provides an idea of the potential of this solution:</p>
<p><iframe loading="lazy" title="Flash Storage Unleashed: Pliops Storage Processors for the Data Centric Era" width="640" height="360" src="https://www.youtube.com/embed/C4ZXZsaW_AE?feature=oembed" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></p>
<h3>Closing the Circle</h3>
<p>Data center acceleration is a hot topic, and for good reason as it improves both infrastructure efficiency and TCO. Depending on use case and the size of the infrastructure, there are several approaches available. The two examples I presented in this post are just the first that came to mind. What’s really interesting is that everybody, no matter the size of the organization, has many of the same challenges, including rapid data growth and the need to support new data-hungry applications.</p>
<p>Accelerators are quickly entering every data center, even the smaller ones, and solutions have already been validated by major vendors <a href="https://blogs.vmware.com/vsphere/2020/09/announcing-project-monterey-redefining-hybrid-cloud-architecture.html" target="_blank" rel="noopener">like VMware</a>. Every IT organization should look seriously at this new field and plan accordingly to keep their infrastructure as efficient and responsive as possible.</p>