---
title : "WhatsApp flaw lets attackers suspend your account using your phone number"
layout: post
tags: tutorial labnol
post_inspiration: https://www.androidauthority.com/whatsapp-account-suspension-flaw-1217262/
image: "https://sergio.afanou.com/assets/images/image-midres-49.jpg"
---

<p><html><body><img class="size-large wp-image-1086321 noname aa-img" title="WhatsApp by Facebook stock photo 8" src="https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-1200x675.jpg" alt="WhatsApp by Facebook stock photo 8" width="1200" height="675" data-attachment-id="1086321" srcset="https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-1200x675.jpg 1200w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-300x170.jpg 300w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-768x432.jpg 768w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-16x9.jpg 16w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-32x18.jpg 32w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-28x16.jpg 28w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-56x32.jpg 56w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-64x36.jpg 64w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-712x400.jpg 712w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-1000x563.jpg 1000w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-792x446.jpg 792w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-1280x720.jpg 1280w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-840x472.jpg 840w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-1340x754.jpg 1340w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-770x433.jpg 770w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-356x200.jpg 356w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8-675x380.jpg 675w, https://cdn57.androidauthority.net/wp-content/uploads/2020/02/WhatsApp-by-Facebook-stock-photo-8.jpg 1920w" sizes="(max-width: 1200px) 100vw, 1200px" /></p>
<div class="aa-img-source-credit"></div>
<div class="aa_tldr_text">
<ul>
<li>Researchers have found a WhatsApp flaw that lets attackers suspend your account.</li>
<li>They just have to email support after multiple two-factor authentication attempts using your phone number.</li>
<li>There&#8217;s no indication WhatsApp has a fix in the works.</li>
</ul>
</div><hr>
<p>You&#8217;ll want to be on guard if you get an unexpected <a href="https://www.androidauthority.com/how-to-use-whatsapp-1097088/">WhatsApp</a> two-factor authentication attempt  — someone might be trying to shut down your account. <a href="https://www.forbes.com/sites/zakdoffman/2021/04/10/shock-new-warning-for-millions-of-whatsapp-users-on-apple-iphone-and-google-android-phones/?sh=5b9aa7417585"><em>Forbes</em></a> reports (via <a href="https://www.androidpolice.com/2021/04/12/your-whatsapp-account-can-be-suspended-by-anyone-who-has-your-phone-number/"><em>Android Police</em></a>) that security researchers Luis Márquez Carpintero and Ernesto Canales Pereña have discovered a flaw letting attackers suspend your account if they have your phone number.</p>
<p>The perpetrator initially requests and incorrectly guesses multiple two-factor SMS codes to have WhatsApp lock out sign-ins on their device for 12 hours. After that, they register a new email address and email the support team asking to deactivate the number due to a lost or stolen account. As WhatsApp automatically disables the number without verifying the authenticity of the request, you could find yourself locked out with no input required on your part.</p>
<p>While you can theoretically get back to your WhatsApp account after that 12-hour window expires, the attackers can try to permanently lock you out by repeating the code requests two more times and waiting until that third period to email the company. If they do that, you&#8217;re asked to wait &#8220;-1 seconds&#8221; and have no choice but to ask WhatsApp for help recovering your account.</p>
<p style="text-align: center;"><strong>See also:</strong> <a href="https://www.androidauthority.com/whatsapp-vs-telegram-vs-signal-1195902/">WhatsApp vs Telegram vs Signal: Which app should you use?</a></p>
<p>WhatsApp didn&#8217;t discuss a potential solution to the account flaw in a statement to <em>Forbes</em>. Instead, it recommended that users provide an email address with two-factor authentication to help support reps if you ever run into this &#8220;unlikely problem.&#8221; Anyone attempting an attack like this would be violating terms of service, a company spokesperson added.</p>
<p>It&#8217;s true that you probably won&#8217;t see many attacks like this. Intruders are typically interested in hijacking accounts rather than disabling them, and you&#8217;ll know that something is wrong during that first string of SMS code requests. You should reach out to WhatsApp support immediately if you notice this activity.</p>
<p>There may be instances where someone wants to cause grief, though, and WhatsApp makes it easy to find a phone number&#8217;s owner by searching for it. More importantly, it raises questions about WhatsApp account security. The Facebook-owned service could theoretically stop this by relying on trusted devices rather than phone numbers, and it could manually verify deactivation requests to catch suspicious activity.</p>
<p>Until that changes, your best bet is simply to keep an eye on your text messages and act quickly.</p>
</body></html></p>