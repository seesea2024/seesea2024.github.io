---
title: OpenID and OAuth
categories: [iteye,openid,oauth]
layout: post
excerpt_separator: <!--more-->
---
OpenID and OAuth <!--more-->

From http://cakebaker.42dh.com/2008/04/01/openid-versus-oauth-from-the-users-perspective/

In this article I want to show the differences between OpenID and its younger cousin OAuth by providing for each a typical user scenario.

First the scenario for OpenID: User wants to access his account on example.com. example.com (the “Relying Party” in OpenID lingo) asks the user for his OpenID. User enters his OpenID. example.com redirects the user to his OpenID provider. User authenticates himself to the OpenID provider. OpenID provider redirects the user back to example.com. example.com allows the user to access his account 

And now the scenario for OAuth: User is on example.com and wants to import his contacts from mycontacts.com. example.com (the “Consumer” in OAuth lingo) redirects the user to mycontacts.com (the “Service Provider”). User authenticates himself to mycontacts.com (which can happen by using OpenID) mycontacts.com asks the user whether he wants to authorize example.com to access his contacts. User makes his choice mycontacts.com redirects the user back to example.com. example.com retrieves the contacts from mycontacts.com. example.com informs the user that the import was successful.

From those scenarios we can see that OpenID is about authentication (i.e. I can identify myself with an url) whereas OAuth is about authorization (i.e. I can grant permission to access my data on some website to another website, without providing this website the authentication information for the original website).
