---
date: '2025-11-24T00:45:00-05:00'
draft: true
title: 'I modded my Xbox 360'
# weight: 1
# aliases: ["/first"]
# tags: ["first"]
author: "aciidz"
# author: ["Me", "You"] # multiple authors
#showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
#description: "Desc Text."
#canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
#UseHugoToc: false
cover:
    image: "/2025-11-24-i-modded-my-xbox-360/LeTombeau.png" # image path/url
    alt: "*Le Tombeau*" # alt text
    caption: "*Le Tombeau*" # display caption under cover
    relative: false # when using page bundles set this to true
    #hidden: true # only hide on current single page
    hiddenInList: false # control whether hidden /posts
    hiddenInSingle: false # hides on the post itself
#editPost:
#    URL: "https://github.com/<path_to_repo>/content"
#    Text: "Suggest Changes" # edit text
#    appendFilePath: true # to append file path to Edit link
---


No, this isn't a post about BadUpdate. I actually did this about 4-5 days before BadUpdate dropped!

### Some backstory:

I first got an Xbox 360 (Trinity) in 2014 to play with friends from school, since I was the only PC gamer there. In 2018, I put the console away in a closet. In June 2019, I powered it on (and the console was accidentally upside down when I did this, but I'm about 99.9% certain that was irrelevant to what ended up happening), and I saw this: ![Dead 360](/2025-11-24-i-modded-my-xbox-360/Dead360.jpeg)

I don't remember what the secondary error code ended up being, but the end result was that I got obsessed with buying parts or not working/cheap 360's on eBay in hopes of finding one with an ancient dashboard. I did end up getting a Falcon on NXE, but it was newer than 7371, so couldn't JTAG it (sadge). Pretty sure it's also prior to the fixed GPU revision, so it will fail eventually. Regardless, with the advent of BadUpdate, old dashes really aren't that special anymore since you can persistently downgrade the dash through it, so who cares.

One of the consoles I got in late 2019 (from a for parts/not working ebay sale) was a Falcon (with a fixed GPU) that was actually console banned. The console originally didn't power on because the front usb ports were destroyed and the pins were shorting to the casing of the ports. The console also had a goofy ahh LED mod: ![Picture of the stupid LED mod](/2025-11-24-i-modded-my-xbox-360/EpicLEDmod.jpg)

Anyways, The Man Himself told me to, and I quote, "RGH that bitch", so that's what I was gonna do, since there was literally nothing to lose at that point, given it was already console banned: ![Josh (Octal450) telling me to RGH the console in the Xbox Homebrew server](/2025-11-24-i-modded-my-xbox-360/RGHit.png)

However, despite being an electrical engineering student at the time (vocational high school), I didn't have a soldering iron at home, nor any of the other tools necessary. My life also began to take a nosedive at the end of 2019, and got progressively worse (and then completely spiraled out of control) once COVID lockdowns hit, which culminated in me dropping out of my EE degree two weeks into the first semester in fall 2020, so I pretty much entirely lost any interest in fooling around with consoles at that point. However, this was still a bucket list goal of sorts, so it remained in the back of my mind.

### So, I finally did it in February.

Why did I do it then? Because I wanted to play the leaked Black Ops 2 DLC5 & Origins pre-alpha builds. Xenia wasn't (isn't?) mature enough yet, and I didn't feel like waiting around for that to happen either. I'm also quite a bit more motivated (and confident) now as a person, and I've realized if I want something to happen, I have to actually do it, instead of just thinking about doing it (lol), and these BO2 builds were the perfect catalyst, so yeah.

I was a bit worried about finding glitch chips; The Xilinx CPLD used on basically all glitch chips went EOL last year, so the market has dried up somewhat rapidly. Of course, RGH3 exists, but it's unreliable on phat consoles and I just want something that works, without any bullshit or asterisks. Fortunately, I was able to buy 5 glitch chips on Aliexpress (I bought a mix of Matrix and Ace V3's). I'm not sure I'll ever use the other ones (feel free to call me an asshole for hoarding these), but who knows. I used a Matrix for this project.

Anyways, I took my TS100 that I bought in 2020 (recommended by Louis Rossmann) out of the packaging for the first time and flashed IronOS onto it as I'd originally planned back then. It seems the meta for cheap soldering irons has shifted quite a bit since then, but this worked completely fine for my goals here. I did end up buying a different tip as the one that I had with the iron was simply too big/not ideal for the precise soldering work I had to do.

I started with reading the NAND (& then flashing XeLL onto it). At first I tried using the flux paste I bought 5 years ago to prepare the points I needed to solder to, but it was making a mess and didn't seem to be very effective. So, I bought a flux syringe and used that for the rest of the project (which, in hindsight, I should've done from the start).

I used an xFlasher for reading/writing the NAND - this may be the only console I ever RGH, so it's kind of a waste... but I didn't want any potential headaches from picoflasher jank, I just wanted it to work. So I was willing to throw away the money in the interest of making that happen. Anyways, I got this done without any issues.

I then moved onto soldering the glitch chip points. The only one I really had a lot of trouble with was R8C2 (I think this is for RST?): ![image](/2025-11-24-i-modded-my-xbox-360/R8C2.jpg)

...So I ended up using the alternate point on the bottom of the board. It seems most tutorials/other people's consoles don't have a capacitor here, so getting at this point was a lot harder than it should have been, and I simply didn't have the patience required to make it happen. I was also afraid I'd end up breaking shit if I kept trying, so, yeah.

#### If I had to do it again (or if I do it again): 

1. I'd use different colored wire for the different points, just for the sake of making it easier to know what is what towards the end of the process.
2. I'd get wire strippers that I can actually use for wire of this gauge. I was cutting the glitch chip wires to length, then trying to manually strip them, and hoping that I didn't accidentally cut the wire completely in the process. If I did cut the wire, I had to hope I hadn't cut it too short & could try stripping it again, otherwise I'd have to start over. It was the most nervewracking bullshit ever, and made things a lot scarier/annoying than they needed to be.

### Pics/final result:

![Topside RGH](/2025-11-24-i-modded-my-xbox-360/RGHtopside.jpeg)
![Bottomside RGH](/2025-11-24-i-modded-my-xbox-360/RGHbottomside.jpeg)

Happy to finally have this done. :)

All things considered, given that I haven't soldered since, at the latest, March 2020? I'm proud of my work. Seems to consistently boot first try, I can't remember if I tried any timing files besides what you're recommended to start with.

I also installed a 1TB PNY CS900 SSD a few days after RGHing it. It's kinda wild how simple things like signing in/out of profiles is so much faster. There's also no more absurd pop-in on GTA V like there was with the 60GB hard drive that came with the console, lol. Looking forward to EatonZ's TRIM support patch, whenever that releases.

### Future TODOs:

* Perhaps I'll buy a replacement hard drive shell at some point, since the one that came with the console was destroyed when I got it. The console's outer shell is also pretty worn, but I honestly don't really care. It doesn't need to look pretty. I got bigger fish to fry in my life right now lmao. 
* However, it would be nice to replace the front USB ports with ones that aren't broken. It's mildly obnoxious having to use a USB hub off the rear port to be able to plug anything in, lol.
* I think it'd be neat to have a dual NAND system in the future? But I dunno. It's November at the time of writing this particular part of the post, and the novelty of the modded system kind of wore off after the first month or two (but my life also got very busy and changed dramatically this year, so I kinda haven't really been playing video games very much in general). Anyways, not sure I'd get much out of a dual NAND system, and I don't think I really need one either. I've got a working Slim (Trinity) that I picked up for $20 earlier in the year which I can use on the stock dashboard; that's good enough for me honestly.

### Conclusion

In classic fashion, I have procrastinated on writing about this for several months. Remember, I modded the console at the end of February. I created this markdown file and wrote the first few paragraphs on April 18th. It is now the end of June at the time of writing this paragraph, and I probably won't end up finishing this for a while after (Hello from November 2025 when I'm now finally finishing and publishing this!!). Woe is me (or, woe is my brain).

Regardless, I'm so happy that I got around to doing this, even if it took me ~8 years since I'd first thought about doing it (back in like 2017... though I remember wishing I had a modded 360 back in like 2015 lol). 

Also so thankful that BadUpdate didn't release any sooner than it did - otherwise I may have never felt it was worth it to go through with hardmodding the system.

Huge shoutout to everyone involved in the 360 modding scene and those who have kept it alive or pushed it forward over the years. Really awesome stuff!

As always, thanks for reading! ❤️