---
title : SQL Transaction Processing Test Shows an Edge for Azure
layout: post
tags: tutorial labnol
post_inspiration: https://gigaom.com/2021/03/24/sql-transaction-processing-test-shows-an-edge-for-azure/
image: "https://sergio.afanou.com/assets/images/image-midres-24.jpg"
---

<p>Data volume and complexity has soared in recent years, and so too have transaction volumes.</p>
<p>In this environment, the performance of transactional databases is key to business success. But depending on the application’s scale and the chosen cloud, some database solutions can be prone to delays.</p>
<p>In the past most organizations turned to on-premises hardware for databases, but today many IT leaders are turning to cloud-based solutions. Infrastructure-as-a-Service (IaaS) deployments are on the upswing, enabling a cloud provider to manage the infrastructure while organizations install, configure, and manage their own software.</p>
<p>In a recent report titled <em><a href="https://gigaom.com/report/sql-transaction-processing-price-performance-testing-2/" target="_blank" rel="noopener">SQL Transaction Processing, Price-Performance Testing</a></em>, GigaOm analysts <a href="https://gigaom.com/analyst/mcknight-william/" target="_blank" rel="noopener">William McKnight</a> and <a href="https://gigaom.com/analyst/jake-dolezal/" target="_blank" rel="noopener">Jake Dolezal</a> examined two IaaS cloud database offerings:</p>
<ul>
<li>Microsoft SQL Server on Amazon Web Services (AWS) Elastic Cloud Compute (EC2) instances</li>
<li>Microsoft SQL Server Microsoft on Azure Virtual Machines (VM)</li>
</ul>
<p>Both are installations of Microsoft SQL Server and were tested using the Red Hat Enterprise Linux OS.</p>
<p>“For anyone wanting to run Microsoft SQL Server and having a choice about the platform, this report should impact their decision about the platform to choose,” says McKnight.</p>
<p>In the report, he notes that the results are valuable to all operational functions of an organization, including human resource management, financial supply chain management, sales and distribution, and financial accounting. The underlying data for many of these departments is in (you guessed it) SQL Server.</p>
<p>The testing found that Microsoft SQL Server on Microsoft Azure VMs showed three times better performance on RHEL 8.2 than Microsoft SQL Server on AWS EC2 in the tested configurations, which include similar maximum IOPS. In terms of price-performance, when comparing both on-demand and pay-as-you-go rates, Microsoft SQL Server on Azure VMs was found to be up to 68% better than AWS EC2.</p>
<p><em>Figure 2. Price-Performance Comparison</em></p>
<p><img loading="lazy" class="aligncenter size-full wp-image-997722" src="https://storage.googleapis.com/stateless-gigaom-com/2021/03/0d151f57-image4.png" alt="" width="4423" height="2488" /></p>
<p>McKnight says some elements of the findings were unexpected.</p>
<p>“This performance difference is seemingly due to enabling local cache on the Azure disks and the additional IOPS on the Azure disks, compared to the AWS gp2 volumes of the same size on Microsoft SQL Server on Azure Virtual Machines. We knew this would make a difference, but were surprised by how much,” he says.</p>
<p>The researchers write that the results provide enough information for readers to reproduce the test in their own environments.</p>
