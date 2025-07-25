# A Trail of Insecurities - osint

## Challenge

These interns are going to be the end of me. How are we supposed to champion digital governance when they can't stop leaking things? First it was Ken’s offhand post on Bluesky that he made me sign up to... and who knows what else they’ve scattered online?

Find out what they’ve let slip... before someone less responsible does.

## Solve

The first thing we looked for was Ken's Bluesky account which was found successfully [here](https://bsky.app/profile/kengoodman-cu.bsky.social). 

On the page we can see a post with some employee screens visible and find our first part of the flag:

img

In the image we can also see a profile with a matching picture on the screen. Looks like Ken's [Github...](https://github.com/kengoodman-cu).

On the Github account we check the only repository's commit history and stumble across another part of the flag:

commit.png

`_y0ur_s3cr3ts`

Additionally, we can check the repository and find a draft `index.html` file containing this link:

https://www.canva.com/design/DAGqSzVswNM/n3okI37oRKhG8sjTsvSGTQ/watch?embed&autoplay=1&loop=1

The link directs us to a video showing a number of symbols, e.g:

flag images

Doing a reverse Google search leads to [this](https://en.wikipedia.org/wiki/International_maritime_signal_flags) Wikipedia page on international maritime signal flags. We have the last part of the flag!

Replacing the flags with their character correspondents we get:

`_sd6f4shf7}`

Assembling all the parts we have:

`cysea{s3cur3_y0ur_s3cr3ts_sd6f4shf7}`