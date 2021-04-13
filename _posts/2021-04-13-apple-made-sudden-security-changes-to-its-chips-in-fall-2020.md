---
title : "Apple Made Sudden Security Changes to its Chips in Fall 2020"
layout: post
tags: tutorial labnol
post_inspiration: https://www.macrumors.com/2021/04/12/apple-made-security-changes-to-chips-in-fall-2020/
image: "https://sergio.afanou.com/assets/images/image-midres-43.jpg"
---

Apple made unusual mid-production hardware changes to the A12, A13, and S5 processors in its devices in the fall of 2020 to update the Secure Storage Component, according to Apple Support documents.
<br/>

<br/>
<img src="https://images.macrumors.com/article-new/2019/10/a13-bionic-mockup.jpg" alt="" width="800" height="533" class="aligncenter size-full wp-image-714134" />
<br/>
According to an <a href="https://support.apple.com/guide/security/secure-enclave-sec59b0b31ff/web">Apple Support page</a>, spotted by Twitter user <a href="https://twitter.com/pandrewhk/status/1381260920635027459?s=21">Andrew Pantyukhin</a>, Apple changed the Secure Enclave in a number of products in the fall of 2020:<blockquote>Note: A12, A13, S4, and S5 products first released in Fall 2020 have a 2nd-generation Secure Storage Component; while earlier products based on these SoCs have 1st-generation Secure Storage Component.</blockquote>The Secure Enclave is a coprocessor that is used for data protection and authentication with <a href="https://www.macrumors.com/guide/touch-id/">Touch ID</a> and Face ID. The purpose of the Secure Enclave is to handle keys and other information, such as biometrics, that are sensitive enough to not be handled by the Application Processor. This data is stored in a Secure Storage Component inside the Secure Enclave, which is the specific part that Apple changed last year.
<br/>

<br/>
The explanation in Apple's support document suggests, at minimum, that the eighth-generation entry-level <a href="https://www.macrumors.com/roundup/ipad/">iPad</a>, <a href="https://www.macrumors.com/roundup/apple-watch-se/">Apple Watch SE</a>, and <a href="https://www.macrumors.com/roundup/homepod-mini/">HomePod mini</a> have different Secure Enclaves compared to older devices with the same chip.
<br/>

<br/>
However, there are a number of discrepancies in Apple's support document. Despite Apple explaining that A13 products "first released in Fall 2020 have a 2nd-generation Secure Storage Component," there was no device with an A13 chip "first released in Fall 2020." The last device to be released with an A13 chip was the <a href="https://www.macrumors.com/roundup/iphone-se/">iPhone SE</a> in February 2020.
<br/>

<br/>
If the change was, in fact, made to all newly-manufactured devices with these chips, the affected devices would include the <a href="https://www.macrumors.com/roundup/iphone-xr/">iPhone XR</a>, <a href="https://www.macrumors.com/roundup/iphone-11/">iPhone 11</a>, &zwnj;iPhone SE&zwnj;, and fifth-generation <a href="https://www.macrumors.com/roundup/ipad-mini/">iPad mini</a>, as well as the newly-released eighth-generation &zwnj;iPad&zwnj;, &zwnj;Apple Watch SE&zwnj;, and &zwnj;HomePod mini&zwnj;.
<br/>

<br/>
<img src="https://images.macrumors.com/article-new/2021/04/a12-a13-s5-secure-enclave-change.jpg" alt="" width="1663" height="1776" class="aligncenter size-full wp-image-793495" />
<br/>
To make matters more confusing, the table listing the multiple versions of the Secure Enclave's storage component in the feature summary omits the S4 chip with a second-generation Secure Storage Component, despite the rubric claiming that such a chip exists. The Apple Watch Series 4 was the only device to contain an S4 chip, and this device was discontinued in September 2019, long before the second-generation Secure Storage Component was implemented in the fall of 2020. It is possible that part of this lack of clarity relates to the fact that the A12 and S4 chips introduced the first-generation Secure Storage Component.
<br/>

<br/>
New devices containing the A14 or S6 chip, such as the <a href="https://www.macrumors.com/roundup/iphone-12/">iPhone 12</a>, <a href="https://www.macrumors.com/roundup/iphone-12-pro/">iPhone 12 Pro</a>, fourth-generation <a href="https://www.macrumors.com/roundup/ipad-air/">iPad Air</a>, and <a href="https://www.macrumors.com/roundup/apple-watch/">Apple Watch Series 6</a>, also have the updated Secure Enclave.
<br/>

<br/>
Although the change took place in the fall of 2020, the support document detailing the alteration was published in February 2021. The full <a href="https://manuals.info.apple.com/MANUALS/1000/MA1902/en_US/apple-platform-security-guide.pdf">PDF version of Apple's Platform Security Guide</a> reveals the difference between the first and second-generation Secure Storage Component: <blockquote>The 2nd-generation Secure Storage Component adds counter lockboxes. Each counter lockbox stores a 128-bit salt, a 128-bit passcode verifier, an 8-bit counter, and an 8-bit maximum attempt value. Access to the counter lockboxes is through an encrypted and authenticated protocol.
<br/>

<br/>
Counter lockboxes hold the entropy needed to unlock passcode-protected user data. To access the user data, the paired Secure Enclave must derive the correct passcode entropy value from the user's passcode and the Secure Enclave's UID. The user's passcode can't be learned using unlock attempts sent from a source other than the paired Secure Enclave. If the passcode attempt limit is exceeded (for example, 10 attempts on <a href="https://www.macrumors.com/guide/iphone/">iPhone</a>), the passcode-protected data is erased completely by the Secure Storage Component.</blockquote>
<br/>

<br/>
This appears to be a countermeasure against password-cracking devices, such as <a href="https://www.macrumors.com/2018/04/12/graykey-iphone-unlocking-box-adoption/">GrayKey</a>, which attempt to break into iPhones by guessing the passcode an infinite number of times, using exploits that allow for infinite incorrect password attempts.
<br/>

<br/>
The change appears to have been significant enough for Apple to justify an entire "second-generation" version of the Secure Enclave's storage. It is certainly unusual for Apple to change a component in its chips mid-way through production, but Apple likely deemed the security upgrade important enough to roll it out to all relevant new devices from the fall onwards, rather than just devices with the latest A14 and S6 chips. <div class="linkback">Related Roundups: <a href="https://www.macrumors.com/roundup/ipad-mini/">iPad mini 5</a>, <a href="https://www.macrumors.com/roundup/iphone-se/">iPhone SE 2020</a>, <a href="https://www.macrumors.com/roundup/ipad/">iPad</a>, <a href="https://www.macrumors.com/roundup/iphone-xr/">iPhone XR</a>, <a href="https://www.macrumors.com/roundup/iphone-11/">iPhone 11</a>, <a href="https://www.macrumors.com/roundup/apple-watch-se/">Apple Watch SE</a>, <a href="https://www.macrumors.com/roundup/homepod-mini/">HomePod mini</a></div><div class="linkback">Tag: <a href="https://www.macrumors.com/guide/security/">security</a></div><div class="linkback">Buyer's Guide: <a href="https://buyersguide.macrumors.com/#iPad_Mini">iPad Mini (Don't Buy)</a>, <a href="https://buyersguide.macrumors.com/#iPhone_SE">iPhone SE (Caution)</a>, <a href="https://buyersguide.macrumors.com/#iPad">iPad (Neutral)</a>, <a href="https://buyersguide.macrumors.com/#Apple_Watch_SE">Apple Watch SE (Buy Now)</a>, <a href="https://buyersguide.macrumors.com/#Homepod-Mini">HomePod Mini (Buy Now)</a></div><br/>This article, &quot;<a href="https://www.macrumors.com/2021/04/12/apple-made-security-changes-to-chips-in-fall-2020/">Apple Made Sudden Security Changes to its Chips in Fall 2020</a>&quot; first appeared on <a href="https://www.macrumors.com">MacRumors.com</a><br/><br/><a href="https://forums.macrumors.com/threads/apple-made-sudden-security-changes-to-its-chips-in-fall-2020.2291443/">Discuss this article</a> in our forums<br/><br/><div class="feedflare">
<a href="http://feeds.macrumors.com/~ff/MacRumors-All?a=XpsIAKMw1T8:-yfbBGtwtYk:6W8y8wAjSf4"><img src="http://feeds.feedburner.com/~ff/MacRumors-All?d=6W8y8wAjSf4" border="0"></img></a> <a href="http://feeds.macrumors.com/~ff/MacRumors-All?a=XpsIAKMw1T8:-yfbBGtwtYk:qj6IDK7rITs"><img src="http://feeds.feedburner.com/~ff/MacRumors-All?d=qj6IDK7rITs" border="0"></img></a>
</div><img src="http://feeds.feedburner.com/~r/MacRumors-All/~4/XpsIAKMw1T8" height="1" width="1" alt=""/>