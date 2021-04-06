---
title : "Apple Reminds Developers About Upcoming App Tracking Transparency Enforcement in iOS 14.5"
layout: post
tags: tutorial labnol
post_inspiration: https://www.macrumors.com/2021/04/05/app-tracking-transparency-developer-reminder/
image: "https://sergio.afanou.com/assets/images/image-midres-8.jpg"
---

Apple <a href="https://developer.apple.com/news/?id=8h0btjq7">today reminded developers</a> that its App Tracking Transparency rules will be enforced starting with the launch of iOS 14.5, iPadOS 14.5, and tvOS 14.5.
<br/>

<br/>
<img src="https://images.macrumors.com/article-new/2021/01/nba-tracking-prompt.jpg" alt="" width="1600" height="878" class="aligncenter size-full wp-image-780733" />
<br/>
When these updates are released, developers will need to get express permission to access the IDFA or advertising identifier on a device to track users across apps and websites for ad targeting purposes.<blockquote>Make sure your apps are ready for iOS 14.5, iPadOS 14.5, and tvOS 14.5. With the upcoming public release, all apps must use the AppTrackingTransparency framework to request the user's permission to track them or to access their device's advertising identifier. Unless you receive permission from the user to enable tracking, the device's advertising identifier value will be all zeros and you may not track them.
<br/>

<br/>
When submitting your app for review, any other form of tracking -- for example, by name or email address -- must be declared in the product page's <a href="https://www.macrumors.com/guide/app-store/">App Store</a> Privacy Information section and be performed only if permission is granted through AppTrackingTransparency. You'll also need to include a purpose string in the system prompt to explain why you'd like to track the user, per &zwnj;App Store&zwnj; Review Guideline 5.1.2(i). These requirements apply to all apps starting with the public release of iOS 14.5, iPadOS 14.5, and tvOS 14.5.
<br/>

<br/>
As a reminder, collecting device and usage data with the intent of deriving a unique representation of a user, or fingerprinting, continues to be a violation of the <a href="https://developer.apple.com/terms/">Apple Developer Program License Agreement</a>.</blockquote>Apple clarifies that developers are not allowed to use specific device data with the intent of fingerprinting a user to replace the IDFA, which is something that <a href="https://www.macrumors.com/2021/03/18/app-tracking-transparency-chinese-tech-companies/">Chinese app developers</a> and mobile measurement companies <a href="https://www.macrumors.com/2021/04/01/apple-rejecting-app-updates-tracking-transparency/">have already been doing</a>.
<br/>

<br/>
Apple earlier in March warned app developers not to use alternate methods to collect user data for tracking purposes, and last week, rejected several app updates from developers using an SDK from mobile measurement company Adjust, which used data like software version and charge level to keep track of users.
<br/>

<br/>
All of the framework for App Tracking Transparency is already in place and some developers have already begun asking users for IDFA access permission, but it will be a requirement for all apps that use the IDFA when iOS 14.5 and its sister updates are released.
<br/>

<br/>
We don't yet know when iOS 14.5 will see a release, but in an interview with Kara Swisher that came out this morning, Apple CEO <a href="https://www.macrumors.com/guide/tim-cook/">Tim Cook</a> said that iOS 14.5 will be coming in "just a few weeks."<div class="linkback">Tags: <a href="https://www.macrumors.com/guide/apple-developer-program/">Apple Developer Program</a>, <a href="https://www.macrumors.com/guide/app-tracking-transparency/">App Tracking Transparency</a></div><br/>This article, &quot;<a href="https://www.macrumors.com/2021/04/05/app-tracking-transparency-developer-reminder/">Apple Reminds Developers About Upcoming App Tracking Transparency Enforcement in iOS 14.5</a>&quot; first appeared on <a href="https://www.macrumors.com">MacRumors.com</a><br/><br/><a href="https://forums.macrumors.com/threads/apple-reminds-developers-about-upcoming-app-tracking-transparency-enforcement-in-ios-14-5.2290710/">Discuss this article</a> in our forums<br/><br/><div class="feedflare">
<a href="http://feeds.macrumors.com/~ff/MacRumors-All?a=n8pN0emkq1o:DnGsb4o64To:6W8y8wAjSf4"><img src="http://feeds.feedburner.com/~ff/MacRumors-All?d=6W8y8wAjSf4" border="0"></img></a> <a href="http://feeds.macrumors.com/~ff/MacRumors-All?a=n8pN0emkq1o:DnGsb4o64To:qj6IDK7rITs"><img src="http://feeds.feedburner.com/~ff/MacRumors-All?d=qj6IDK7rITs" border="0"></img></a>
</div><img src="http://feeds.feedburner.com/~r/MacRumors-All/~4/n8pN0emkq1o" height="1" width="1" alt=""/>