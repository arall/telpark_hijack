# What is this about?

Telpark is a parking meter App, available in more than 60 cities in Spain.

Some time ago, I've discovered a **Cross Site Request Forgery** vulnerability in the account setting, that allows **full account hijacking**, by only opening a link, with a currently open session on the app website.
I've reported this issue the **31 May 2016** and still not fixed.

Today, 7 October 2016, I'm releasing this attack proof of concept.

# Who does the attack work?

A **Cross Site Request Forgery** (CSRF) is a type of attack that occurs when a malicious web site, email, blog, instant message, or program causes a user's web browser to perform an unwanted action on a trusted site for which the user is currently authenticated.

### The vulnerability

The `account` page is not protected with any CSRF token. Performing an unwanted `POST` request targeting the account settings page, will allow the attacker to change the account email.
Once the email is changed, the attacker will be able to reset the account password.

### Bypassing the 'protection'
The only 'protection' is an incremental ID that is send over the URL.
For example, if we just logged in, accessing `https://app.telpark.com/es/myaccount` will redirect us to `https://app.telpark.com/es/my_profile?5`. If we access the setting page again, the ID will be 6, and so on.
To bypass that, the attack will perform multiple asynchronous requests, trying to guess the current ID.

### Attack flow
This is one of many examples of exploitability:
- The victim logs in using the website https://app.telpark.com/.
- The attacker creates a malicious website with the exploit in the background, targeting the email change to his own email.
- The attacker sends the website link to the victim.
- The victim opens the link and the exploit starts. At this point his account email is changed to the attacker one.
- The attacker start now the password reset for the victim account using his email.
- The attacker sets the new password and logs in. The whole account is now on his possession, allowing to use victim's payment methods for parking attacker's cars, seeing sensible information such as license plates, parking history and locations.
- The victim will not be able to remove the payment method, since the account is no longer under his / her domain.


### Proof of concept video
[![Video](http://webm.land/media/tmp/b569677b-82e0-42fc-a7ca-1d93fb50f6d9.webm)](http://webm.land/media/tmp/b569677b-82e0-42fc-a7ca-1d93fb50f6d9.webm)
