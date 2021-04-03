---
title : Apple Now Rejecting App Updates That Defy iOS 14.5 App Tracking Transparency Rules
layout: post
tags: tutorial labnol
post_inspiration: https://www.macrumors.com/2021/04/01/apple-rejecting-app-updates-tracking-transparency/
image: "https://sergio.afanou.com/assets/images/image-midres-30.jpg"
---

Apple has begun rejecting app updates that do not comply with the App Tracking Transparency rules that the company is enforcing starting with iOS 14.5, according to a new report from <em><a href="https://www.forbes.com/sites/johnkoetsier/2021/04/01/apple-rejecting-apps-with-fingerprinting-enabled-as-ios-14-privacy-enforcement-starts/?sh=7590a93a3d19">Forbes</a></em>.
<br/>

<br/>
<img src="https://images.macrumors.com/article-new/2021/01/nba-tracking-prompt.jpg" alt="" width="1600" height="878" class="aligncenter size-full wp-image-780733" />
<br/>
Apps must ask for permission to access the advertising identifier or IDFA of a user's <a href="https://www.macrumors.com/guide/iphone/">iPhone</a> in order to track them across apps for ad targeting purposes, a rule that apps will need to comply with when iOS 14.5 launches. The rule also prevents apps from using other workaround methods for tracking users, which is getting some developers into trouble already.
<br/>

<br/>
Several apps have been rejected so far, with <em>Forbes</em> listing Heetch, Radish Fiction, InnoGames, and more. Developers seeing app rejections are getting the following message: "Your app uses algorithmically converted device and usage data to create a unique identifier in order to track the user," with the message also listing the data that's being collected.
<br/>

<br/>
<img src="https://images.macrumors.com/article-new/2021/04/apple-app-transparency-tracking-rejection.jpg" alt="" width="960" height="761" class="aligncenter size-full wp-image-792024" />
<br/>
Mobile marketing analyst Eric Seufert said that an SDK from mobile measurement company Adjust is at fault because of the data that it collects for device fingerprinting. Adjust, which is installed in more than 50,000 apps, says that it "maximizes the impact" of mobile marketing.
<br/>

<br/>
<div class="center-wrap"><blockquote class="twitter-tweet"><p lang="en" dir="ltr">Per a number of developers, Apple has begun rejecting app updates that include the Adjust SDK related to its collection of data used for device fingerprinting.</p>&mdash; Eric Seufert (@eric_seufert) <a href="https://twitter.com/eric_seufert/status/1377596078179123202?ref_src=twsrc%5Etfw">April 1, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script></div>
<br/>
Apple is blocking apps that are using fingerprinting techniques to collect data for the purpose of building a profile of a user that allows the user to be tracked even without an advertising identifier. Data collection uses metrics like software version, time since last update, time since last restart, charge level, battery status, and more to identify individual users.
<br/>

<br/>
It is Apple's position that if a customer has declined the usage of the IDFA for ad tracking, that user has also <a href="https://developer.apple.com/app-store/user-privacy-and-data-use/">declined other tracking methods</a>. Apple's <a href="https://www.macrumors.com/guide/app-store/">App Store</a> rules say that app developers cannot collect data from a device for the purpose of identifying it, and developers are responsible for all tracking code in their apps, including any third-party SDKs they're using.
<br/>

<br/>
Adjust has now updated its SDK to remove code that accesses data like CPU type, phone memory, charging status, and battery level, so apps that were rejected for using Adjust may be able to have their updates greenlit after installing the new Adjust SDK.
<br/>

<br/>
There's still no word on when Apple plans to release iOS 14.5, but we've had six betas so far and the software is set to be available to the public sometime in the spring. With the App Tracking Transparency rules starting to be enforced for updates, it is possible that Apple is preparing for the software's launch, so we could perhaps see it debut in the near future.<div class="linkback">Tag: <a href="https://www.macrumors.com/guide/app-tracking-transparency/">App Tracking Transparency</a></div><br/>This article, &quot;<a href="https://www.macrumors.com/2021/04/01/apple-rejecting-app-updates-tracking-transparency/">Apple Now Rejecting App Updates That Defy iOS 14.5 App Tracking Transparency Rules</a>&quot; first appeared on <a href="https://www.macrumors.com">MacRumors.com</a><br/><br/><a href="https://forums.macrumors.com/threads/apple-now-rejecting-app-updates-that-defy-ios-14-5-app-tracking-transparency-rules.2290351/">Discuss this article</a> in our forums<br/><br/><div class="feedflare">
<a href="http://feeds.macrumors.com/~ff/MacRumors-All?a=M-v1HRbvr3s:6sX85zLfsqI:6W8y8wAjSf4"><img src="http://feeds.feedburner.com/~ff/MacRumors-All?d=6W8y8wAjSf4" border="0"></img></a> <a href="http://feeds.macrumors.com/~ff/MacRumors-All?a=M-v1HRbvr3s:6sX85zLfsqI:qj6IDK7rITs"><img src="http://feeds.feedburner.com/~ff/MacRumors-All?d=qj6IDK7rITs" border="0"></img></a>
</div><img src="http://feeds.feedburner.com/~r/MacRumors-All/~4/M-v1HRbvr3s" height="1" width="1" alt=""/>