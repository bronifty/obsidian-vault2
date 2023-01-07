---
author: "Simon Plenderleith"
handle: "@simonplend"
source: "https://twitter.com/simonplend/status/1590654397167865857"
date: "November 10, 2022 5:35 AM"
fetched: "November 24, 2022 8:18 PM"
likes: 1
retweets: 0
replies: 1
---
![simonplend](https://pbs.twimg.com/profile_images/1302907139757924353/3a0F6OAr_normal.jpg)
Simon Plenderleith ([@simonplend](https://twitter.com/simonplend)) - November 10, 2022 5:35 AM

Having fun planning for this afternoon's live stream...

[twitter.com/platformatic/s…](https://twitter.com/platformatic/status/1590032624545734656)

> ![platformatic](https://pbs.twimg.com/profile_images/1519874059613343746/n1t5upyx_normal.jpg)
> Platformatic ([@platformatic](https://twitter.com/platformatic)) - November 8, 2022 12:24 PM
> 
> 
> Join [@matteocollina](https://twitter.com/matteocollina) and [@simonplend](https://twitter.com/simonplend) on Twitch as they show you how to build your own Twitter alternative with Platformatic DB! 🐦
> 
> ⏰ When? 16:00 UTC on Thu 10th November
> 
> 🔔 Follow Matteo on Twitch for an automatic reminder when the stream goes live.
> 
> [twitch.tv/matteocollina/…](https://www.twitch.tv/matteocollina/schedule)
> 
> 
> [Tweet link](https://twitter.com/platformatic/status/1590032624545734656)

[Tweet link](https://twitter.com/simonplend/status/1590654397167865857)

---

![simonplend](https://pbs.twimg.com/profile_images/1302907139757924353/3a0F6OAr_normal.jpg)
Simon Plenderleith ([@simonplend](https://twitter.com/simonplend)) - November 10, 2022 5:35 AM

I've written up some requirements, like a good Product Manager!

[github.com/platformatic/4…](https://github.com/platformatic/420chars/pull/2) [pic.twitter.com/ILdGqi8RWX](https://twitter.com/simonplend/status/1590654400837521409/photo/1)

![Screenshot of a list of requirements:  420chars  Build your own Twitter alternative with Platformatic. Requirements      Feature: Create account         Users can create an account (integrate with Auth0)     Feature: Create post         Logged in users can create posts     Feature: Profiles         Users have a public profile page         Profile page shows their username and photo (use Gravatar or Unavatar)         Profile pages shows their posts (reverse chronological order)     Feature: Following         Logged in users can follow other users     Feature: Feed         Logged in users have a feed that shows their posts and the posts of people that they follow         Posts in reverse chronological order     Feature: Likes         Logged in users can like other users posts     Feature: Automatically updating feed         New posts are automatically displayed in user’s feeds ](https://pbs.twimg.com/media/FhMjTdLWQAESOix.jpg)

[Tweet link](https://twitter.com/simonplend/status/1590654400837521409)

---

![simonplend](https://pbs.twimg.com/profile_images/1302907139757924353/3a0F6OAr_normal.jpg)
Simon Plenderleith ([@simonplend](https://twitter.com/simonplend)) - November 10, 2022 5:35 AM

I've also put my architecture hat on and created a basic DB schema diagram. [pic.twitter.com/ouAadXjd7N](https://twitter.com/simonplend/status/1590654410622849025/photo/1)

![Screenshot of a database schema diagram: 420chars schema, table: users, table: posts](https://pbs.twimg.com/media/FhMjl_3WYAAsgBK.png)

[Tweet link](https://twitter.com/simonplend/status/1590654410622849025)

---

![simonplend](https://pbs.twimg.com/profile_images/1302907139757924353/3a0F6OAr_normal.jpg)
Simon Plenderleith ([@simonplend](https://twitter.com/simonplend)) - November 10, 2022 5:37 AM

Now I need to figure out how the integration between our frontend (which we'll build with Astro) and Auth0 will work.

I'd like it to be server-side based, but it's looking like we'll need to use the Auth0 React SDK.

[Tweet link](https://twitter.com/simonplend/status/1590654883677761539)

---

![simonplend](https://pbs.twimg.com/profile_images/1302907139757924353/3a0F6OAr_normal.jpg)
Simon Plenderleith ([@simonplend](https://twitter.com/simonplend)) - November 10, 2022 10:15 AM

The Auth0 React SDK works great! Thanks to [@MarcoPiraccini](https://twitter.com/MarcoPiraccini) for helping me get it configured 🙌

[auth0.com/docs/quickstar…](https://auth0.com/docs/quickstart/spa/react/)

[Tweet link](https://twitter.com/simonplend/status/1590724955141943296)