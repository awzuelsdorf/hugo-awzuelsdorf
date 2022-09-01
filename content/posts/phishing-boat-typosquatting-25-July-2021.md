---
title: "Phishing Boat"
date: 2021-07-25T00:00:00-04:00
draft: true
---

***Overview***

This article will describe Phishing Boat, a [Chrome](https://chrome.google.com/webstore/detail/phishing-boat/ljaiihgfejfaggbjfildfnjdckomlfop) and [Firefox](https://addons.mozilla.org/en-US/firefox/addon/phishing-boat/) extension I made a few years ago. Even though this is an older extension, there is still active development and features that can add value in terms of increased security or usability are being added. One of those features, which addresses the use of "typosquatting" domains in phishing attempts, is described here.</p>

***Background: Why Use Phishing Boat?***

Phishing Boat was created to address some of the limitations in other methods of detecting phishing emails (reading links' URLs carefully before clicking on them, website scanning, and blacklists/whitelists).

***Phishing Boat: Yet Another Step in the Right Direction***

Phishing Boat attempts to combat phishing emails by observing which websites one has visited from his/her computer over a period of time. After this "learning period" has passed, a popup window will appear when one clicks the link to a website that one has not visited since the extension was installed. It will show the name of the website's domain and ask for confirmation before continuing. If one clicks "OK", then the browser will follow the link like normal. If one clicks "Cancel", then the browser will remain on the current page. This way, the phishing attempt is foiled since the user does not actually visit the site behind the phishing attempt. Since most people don't visit more than a few dozen sites regularly, these alerts tend to fire very infrequently (in my own experience, I usually only see alerts once or twice a week). This solves the problem that the traditional method has with the mental burden of reading each URL, and it avoids issues with scan latency or heuristics based on site age. In this way, it is another step in the right direction, though it still has some limitations that will be detailed later.

***The New Feature***

The new feature I have released is one that will provide more amplifiying information in popup messages to the user depending upon how closely the domain of an unfamiliar URL is to the other sites that user has visited. Namely, if the domain in the link is within[^1] two letters of a domain that the user has already visited, then the popup message displayed to the user will display both the link's domain and the previously-visited, similar domain. The message will ask the user to confirm the decision to go to the new domain, knowing that it is very similar to another domain and is more likely to be a phishing domain. The messages previously displayed when the user visited an unfamiliar domain will still be displayed if the new domain is different and not similar to any previously-seen domains.
			
***Limitations/Future Work***

While I use Phishing Boat virtually every day and it has helped me avoid cleverly disguised links for over four years now, it has some disadvantages. First, it is a browser extension, so it cannot be used on most mobile devices currently. Bringing Phishing Boat to a mobile platform is part of my future plans for this application, but there are pros and cons to making a browser app that can support this type of functionality or making an app that can somehow integrate with existing browser apps, and I am still working through those pros and cons.</p><p>Additionally, it is possible that one could visit a phishing site within the learning period mentioned previously, in which case one would not receive a popup asking for confirmation and one could fall victim to a phishing attack. For a while, Phishing Boat did not have a learning period. While this fixed the issue of people potentially not receiving confirmation messages, this resulted in a deluge of messages during the first few days of using Phishing Boat. This was not a good experience and was seen as a flaw since it would discourage many people from using this application more than a learning period would. It is a trade-off and it seems like the "right" trade to make, since it effectively means that the user will be in a condition no worse than the state he/she was in before getting Phishing Boat, and it greatly decreases the likelihood of him/her quitting on Phishing Boat early and resort to less effective methods at combating phishing attempts.</p><p>One additional feature I can think of adding is to use Amazon Web Information Services' Alexa service to automatically update a list of the most popular websites on the web, which would act as a whitelist for Phishing Boat to avoid flagging websites that the user may not have visited during the learning period but are very unlikely to be phishing websites.

[^1]: a domain is "within two letters" of a different domain if the Levenshtein distance between the two domains is less than two.
