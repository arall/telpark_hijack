# What is this about?

Telpark is a parking meter App, available in more than 60 Spanish cities.

Some time ago, I've discovered a **Cross Site Request Forgery** vulnerability in the account setting, that allows **full account hijacking**, by only opening a link, with a currently open session on the app website.
I've reported this issue the **31 May 2016** and up to this day it remains unfixed.

Today, 7 October 2016, I'm releasing proof of concept of this attack.

# How does the attack work?

A **Cross Site Request Forgery** (CSRF) is a type of attack that occurs when a malicious website, email, blog, instant message, or program causes a user's web browser to perform an unwanted action on a trusted site at which the user is currently authenticated.

### The vulnerability

The `account` page is not protected with any CSRF token. Performing an unwanted `POST` request targeting the account settings page, will allow the attacker to change the account email.
Once the email is changed, the attacker will be able to reset the account password.

### Bypassing the 'protection'
The only 'protection' is an incremental ID that is sent over the URL.
For example, if we just logged in, accessing `https://app.telpark.com/es/myaccount` will redirect us to `https://app.telpark.com/es/my_profile?5`. If we access the setting page again, the ID will be 6, and so on.
To bypass that, the attack might perform multiple asynchronous requests, trying to guess the current ID.

### Attack flow
This is an examples of a possible attack flow:
- The victim logs in using the website https://app.telpark.com/.
- The attacker creates a malicious website with the exploit, where he will leverage 'email change' vector.
- The attacker sends the website link to the victim.
- The victim opens the link and the exploit starts in the background. At this point victim's email address is changed to the attacker's address.
- The attacker receives an email on his account, with new address confirmation.
- The attacker can now start the password reset process for the account using his own email address.
- The attacker sets the new password and logs in. The account is now completely under his control. That allows attacker to use victim's payment methods for parking cars, seeing sensitive information such as license plates, parking history and locations.
- The victim will not be able to log in, change nor remove the payment method, since the account is no longer under victim's control.

### Proof of concept video
[![Video](http://i.imgur.com/xcm97BR.png)](http://webm.land/media/tmp/b99c7a25-9de3-4bd5-b85f-3e7d36c53309.webm)
