---
title : "Get Email Alerts When COVID-19 Vaccines Become Available Near You"
layout: post
categories: [  ]
tags: tutorial labnol
post_inspiration: https://www.labnol.org/covid19-vaccine-tracker-210501
image: "https://sergio.afanou.com/assets/images/image-midres-13.jpg"
---

<p>India is currently in the midst of a second wave of the Coronavirus and this one is far more devastating than what we have seen last year. The country is reporting close to <a href="https://docs.google.com/spreadsheets/d/1swdjquWqq5tjMm9tpxMa-9C8rjCyWVWHs-ODdAXfWDw/edit?usp=sharing" target="_blank" rel="nofollow noopener noreferrer">400,000+ new cases</a> every day but the actual count of daily infections could be much higher.</p><p>The COVID-19 vaccination program in India was earlier available to people above 45 years of age but starting today (May 1), anyone above the age of 18 years is eligible for Covid-19 vaccination.</p><p><span class="gatsby-resp-image-wrapper" style="display:block;margin-left:auto;margin-right:auto;max-width:720px;">
      <a class="gatsby-resp-image-link" href="/static/5a1c0194b1899123d57d405113666b7a/823ba/232729.png" style="display:block;" target="_blank" rel="noopener">
    <span class="gatsby-resp-image-background-image" style="display:block;"></span>
  <img class="gatsby-resp-image-image" alt="Vaccine Availability" title="Vaccine Availability" src="/static/5a1c0194b1899123d57d405113666b7a/16546/232729.png" style="width:100%;height:100%;margin:0;vertical-align:middle;">
  </a>
    </span></p><h2 id="covid-19-vaccines-near-me">
<a href="#covid-19-vaccines-near-me" class="anchor before"><svg height="16" version="1.1" viewbox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>COVID-19 Vaccines Near Me</h2><p>The government’s official website - <a href="https://www.cowin.gov.in/home" target="_blank" rel="nofollow noopener noreferrer">cowin.gov.in</a> - has a useful search section that allows you see the nearby vaccination centers in your city or zip code. You’ll also know how many vaccine doses are available at a specific center and the earliest date when the next batch of vaccine stocks are due.</p><p>Based on this public data, I have developed an open-source vaccine tracker that will monitor the vaccine availability near you and will send email alerts as stocks become available. The source code of the project is available on <a href="https://github.com/labnol/covid19-vaccine-tracker" target="_blank" rel="nofollow noopener noreferrer">Github</a>.</p><h3 id="build-your-own-covid-19-vaccine-tracker">
<a href="#build-your-own-covid-19-vaccine-tracker" class="anchor before"><svg height="16" version="1.1" viewbox="0 0 16 16" width="16"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Build your own Covid-19 Vaccine Tracker</h3><p><strong>Step 1:</strong> To get started, <a href="https://docs.google.com/spreadsheets/d/1NPBFLOvNpqGMKUzPEMKRczmV9nNer6Ysv8pEPhOsuPI/copy" target="_blank" rel="nofollow noopener noreferrer">click here</a> to make a copy of the Vaccine Tracker Google Sheet in your Google Drive.</p><p><strong>Step 2:</strong> Click the Vaccine Tracker menu (near the Help menu) and choose Enable as shown in the screenshot.</p><p><span class="gatsby-resp-image-wrapper" style="display:block;margin-left:auto;margin-right:auto;max-width:720px;">
      <a class="gatsby-resp-image-link" href="/static/c9d7009e37328e0db75c9cbd9a907564/357da/172221.png" style="display:block;" target="_blank" rel="noopener">
    <span class="gatsby-resp-image-background-image" style="display:block;"></span>
  <img class="gatsby-resp-image-image" alt="Vaccine Tracker Google Sheet" title="Vaccine Tracker Google Sheet" src="/static/c9d7009e37328e0db75c9cbd9a907564/16546/172221.png" style="width:100%;height:100%;margin:0;vertical-align:middle;">
  </a>
    </span></p><p><strong>Step 3:</strong> You may see an authorization window. If you get an “unverified app” message, click the Advanced link and choose “Go to Vaccine Alerts”. The app is 100% safe and <a href="https://github.com/labnol/covid19-vaccine-tracker" target="_blank" rel="nofollow noopener noreferrer">open-source</a>.</p><p><strong>Step 4:</strong> Go to Step 2 now and choose the <code class="language-text">Enable</code> menu again to launch the tracker. Enter one more pin codes, the email address where you wish to receive the alerts and the age group for which you need to monitor vaccine availability.</p><p>Click the <code class="language-text">Create Email Alert</code> button and your system is up and running. Google Sheets will run this monitor every day and send an email at 8 am indicating the availability of vaccines in your specified areas.</p><p>Here’s a copy of the email sent by the vaccine tracker.</p><p><span class="gatsby-resp-image-wrapper" style="display:block;margin-left:auto;margin-right:auto;max-width:720px;">
      <a class="gatsby-resp-image-link" href="/static/f8a136bc9efd97eded105b8e345f6fc5/16546/email.png" style="display:block;" target="_blank" rel="noopener">
    <span class="gatsby-resp-image-background-image" style="padding-bottom:75%;display:block;"></span>
  <img class="gatsby-resp-image-image" alt="Email Alert - Vaccine Tracker" title="Email Alert - Vaccine Tracker" src="/static/f8a136bc9efd97eded105b8e345f6fc5/16546/email.png" style="width:100%;height:100%;margin:0;vertical-align:middle;">
  </a>
    </span></p><p>And if you ever wish to stop Google Sheets from tracking vaccine availability, go to the same sheet and choose <code class="language-text">Disable</code> from the menu.</p>