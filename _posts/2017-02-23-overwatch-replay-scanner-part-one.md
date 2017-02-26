---
layout: post
title: "Overwatch Replay Scanner - Part One"
date: 2017-02-23 19:46:00 -0800
categories: projects overwatch
---

I don't often get ideas for personal projects that interest me, but the other day I had just that. I've been playing a decent amount of Overwatch lately and I often browse the [/r/Overwatch][reddit-overwatch] subreddit. While watching various highlights and Plays of the Game (Play of the Games?) a thought struck me: "With all these clips being uploaded about Overwatch, surely I must have appeared in a few of them as a teammate or opponent." I figured that there would be no way to tell if you appeared in someone else's highlight without making a mental note to check the killfeed or nameplates of every highlight you come across.

But that got me thinking... most of the Overwatch plays I've seen have been posted to /r/Overwatch, but most of them were hosted on [Gfycat][gfycat]. Furthermore, the plays all seemed to share the same tag of [#Overwatch][gfycat-overwatch], including plays that I had uploaded but not explicitly tagged. A plan started to come together: I could scan Gfycat for all clips that include the tag  #Overwatch, use some computer vision to parse out all player names, and then index these plays on some website that could be open to the Overwatch community.

## Ok, but how do you get the player names?
About three months ago, /u/ewaller, a user on /r/Overwatch, [posted][reddit-streamhive] a link to a [website][streamhive] he created that scans [Twitch.tv][twitch] gameplay streams and uses computer vision to determine:

1. What hero the user is playing
2. What competitive ranking the user is playing at

The website allows the user to filter gameplay streams by these two conditions so that they can find new streamers to watch. This service helps streamers get more views and for viewers to find less popular streamers playing at specific rankings.

/u/ewaller was able to do this by periodically scanning twitch streams for the section of the Overwatch UI that shows the current character and the competitive ranking symbols of the player. I figured the same process could be used to find at least some of the players in a GIF by scanning certain portions of the UI for their names.

## So what's the plan?
Basically the whole project can be divided into the following steps:

1. Frequently poll Gfycat for new clips with the #Overwatch tag.
2. Scan the webm file for useful frames (those containing player names).
3. Preprocess the frames with [OpenCV][opencv], cropping the names and adding contrast.
4. Process the names using [Tesseract][tesseract].
5. Update a database with the new information.
6. Website polls database to display results.

Some of these steps are easier than others, but overall the project should prove to be a challenge.

## Is that everything?
Well not exactly. There are a few things that I have to take into account to make this work, and a few issues that I can't avoid:

#### Player names aren't unique
One of the biggest issues with this method is that player names in Overwatch are not unique. Your account name consists of a your chosen name, e.g. **LeeroyJenkins**, but Blizzard appends a number, e.g. #1234 to your account name to uniquely identify you among all the other 'LeeroyJenkins'es. This unique number is not displayed anywhere on the UI during gameplay, so it is impossible to obtain. This will result in all players who share a name to be grouped together as the same player.

### There are a lot of Overwatch gifs
Gfycat will have to be polled fairly consistently to keep up with all the Overwatch gifs that are uploaded. Furthermore, we need to make sure that we don't waste resources processing GIFs that we've already processed. I'll need to dig into the Gfycat API more to see how robust the search mechanism is.

### Colorblind mode
This shouldn't be much of an issue, but will need to be accounted for during the pre-processing phase. Webm frames will be converted to black and white before processing, but my test set should include various colorblind clips to be safe.

### Score screen and Tab menu
Because the replays are often from one user's perspective, they sometimes contain the TAB menu or the after-game scoreboard. If we can detect these screens, we should be able to receive the names of everyone in the match on both teams, regardless of who dies or actually appears in the clip. This screen doesn't appear in all clips though, so it shouldn't be relied on.

## Conclusion
I plan on using the next few days to research the individual parts of this project and to make sure I haven't missed anything that might block progress. Overall, however, I believe that this should be doable in my spare time.


[reddit-overwatch]: https://www.reddit.com/r/Overwatch
[gfycat]: https://www.gfycat.com/
[gfycat-overwatch]: https://gfycat.com/tag/Overwatch
[reddit-streamhive]: https://redd.it/5dn7pi
[streamhive]: https://streamhive.com/filters/overwatch-streams
[twitch]: https://twitch.tv/
[opencv]: http://opencv.org
[tesseract]: https://github.com/tesseract-ocr
