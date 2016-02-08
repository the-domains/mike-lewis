---
inFeed: true
hasPage: true
inNav: false
inLanguage: null
starred: false
keywords: []
description: ''
datePublished: '2016-02-08T18:18:12.227Z'
dateModified: '2016-02-08T18:17:52.717Z'
title: 'The Art of Troubleshooting '
author: []
authors: []
publisher:
  name: null
  domain: null
  url: null
  favicon: null
sourcePath: _posts/2016-02-08-the-art-of-troubleshooting.md
published: true
url: the-art-of-troubleshooting/index.html
_type: Article

---
# The Art of Troubleshooting

## The Problem

Out of nowhere one website started, seemingly randomly, displaying a blank screen as the homepage. When this was first brought to my attention, I did a quick look at the HTML source and confirmed that there was nothing. No errors, no opening and closing tags, just nothing. So I'm getting a blank 200 OK response from the server. Weird.

I go ahead and ssh into the server and look at the logs. Nothing in the logs to indicate a problem. So I just restartt the web server, look at the logs again, no errors. I bring up the homepage and everything is back to normal. At this point I really don't have anything to go on so I do what you would expect: pray that it was some fluky thing that doesn't happen again.

And of course, the problem repeats a couple more times. It seems to only happen for short periods of time and then the nromal homepage comes back. When I do get a chance to actually see the problem in action I do what I can to try to build up some facts. This is what I have at this point:

* A blank response is sent from the server with a 200 OK
* The problem appears throughout the day for short periods of time
* No errors in the logs
* Only the homepage has this problem
* No code or server change has happened

## Pouring Over Logs

Give the facts that I have, I need to start putting together some concrete possible reasons for this problem. I start by trying to narrow the problem down by eliminating parts of the technology stack. I am starting with:

* Application code (Django app)
* Web server (uWSGI)
* Proxy (Nginx)
* Server OS (Ubuntu)

I'm pretty sure that I can eliminate one of these right away. As I said, I am very confident that the code has not changed since this problem started. Not only that, but I cannot think of a reason that the Django app would ever just deliver an empty response without an error code. If one or more of the modules was fouling things up then I would expect an error in the logs and a partially rendered page, not just a blank page. So let's get rid of Application code for now.

So onto uWSGI. At this point I really have no option but to identify time periods when I know this is happening and pour over the uWSGI logs looking for the right timestamps. To help myself with this, I download the log file I am working with and remove all requests_except_requests for the homepage. This is only happening for the homepage so I don't need any of the other mess. I quickly find out that this isn't getting me anywhere. I see a single request for the homepage in the time interval that I'm interested in and all I can tell is that it is a 200 OK, there are no other requests.

OK, onto the Nginx logs. I repeat my procedure from uWSGI here. Now I'm seeing a lot more requests for the homepage in the time interval, this makes more sense. Unfortunately, I'm not getting anywhere with this either. All I see is 200 responses and no real indication of what is going wrong.

I would say that it took me about another hour of just staring at the logs before I really started making progress. I was thinking out the whole chain of request. OK, he client's request goes to the Nginx server, if the requested page is not cached already then Nginx send it to uWSGI which gets the response from Django and sends it to Nginx for caching and back to the user. So now I know why I was only seeing one request in a timeframe in the uWSGI logs: the response was being cached. OK, so why would an empty response be cached by Nginx? I really have no idea, but Nginx is going to cache any 200 response so I get why it is doing that.