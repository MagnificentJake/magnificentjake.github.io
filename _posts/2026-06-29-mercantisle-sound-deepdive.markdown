---
layout: post
title:  "Mercantisle: Audio Deep Dive"
date:   2026-06-28 13:16:00 +0000
tags: projects mercantisle
excerpt_separator: <!--excerpt-->
---

<div class="embed-center post-hero"><img src="/assets/img/banner-mercantisle-deep-dive.jpg"></div>
Hi folks!

If you've been over to see my [game audio projects]({% link gameaudio.md %}), you'll have seen that a few months ago we shipped version 1.0 of [Mercantisle][mercantisle]! Having been responsible for all the sound on that game (sans music), and having had a whale of a time working on the sound design, I thought it might be cool to break down some of the stuff I did and peel back the curtain on it. Hopefully you also agree that might be cool, because it's my site and I'm calling the shots around here so that's what we're doing.

As I say, I did everything auditory on Mercantisle with the exception of music, which Forbidden Oak had already commissioned the talented [Helena Ricci][helena] for. This meant I was defining the auditory vision for the game, identifying and designing all the sounds it needed, implementing both the sounds themselves and the overall audio systems into the game, and orchestrating the final mix. As you can imagine this added up to quite a lot of work, and working on all of this in the evenings and weekends alongside my PhD kept me pretty busy!

Rather than go through every single thing I did on Mercantisle, I think we'll instead focus on my favourite bits and the stuff that's most interesting or unique about the audio. The world does not need another article explaining that you can make gore sounds from vegetables, but perhaps it could do with one that talks about what you can do with the impulse response of a carrot.
<!--excerpt-->

### Background, Audio Vision, and Sonic Pillars
<div class="embed-center"><img src="/assets/img/mercantisle-steamgrab.jpg"><div class="caption">Look at that barren arctic wasteland, ripe for capitalist exploitation!</div></div>
Mercantisle, for those who don't know, is a colony sim with an extensive free market simulation. Rather than control an entire colony, you control one character within the colony, and influence its development by contributing to the local economy, which is a pretty interesting angle. The game is set on an island...collection? I'm not sure if it counts as an archipelago. A group of islands? A herd of islands? There's a lot of islands, and you have a colony on one of them, basically, and you can sail between different islands to trade and such. 

Google suggests what I'm describing is in fact an archipelago.

Anyways this archipelago covers multiple <span class="bounce-text"><strong>biomes</strong></span> -- when I came onto the project we only had wetlands, arctic, and jungle, but we also added the **badlands** in 1.0 (what a cool word). All these biomes are inhabited by peaceful, kind-eyed plant-animal hybrids and <span class="shake-text"><strong><em>giant man-eating insects oh god</em></strong></span>. The game also has a strong sense of humour about it, what with all the puns it's riddled with and the presence of moustache-twirling capitalists who encourage you to do things like bottle up the air and sell it back to your neighbours. Admittedly, that's feeling like an increasingly less far-fetched vision of the future these past few years.

The first thing I did when I came on board the project was to familiarise myself with the game, and these three aspects of the game stood out to me as central pillars which the sound design needed to support, the sort of **sonic pillars** that I needed to ensure were in place and informed my approach to the audio. 
- **Setting.** The biomes needed to feel significantly different, and the island aspect of the setting needed to be emphasised. I wanted the player to be able to close their eyes and be able to clearly hear the sound of the sea, and know exactly which biome they were currently in. In practice, building this pillar meant ensuring there were unique ambient elements in each biome, but also things like incorporating watery layers into the UI sounds.
- **Inhabitants.** Given how unique the creature and insect designs were, I wanted those visual designs to be well supported sonically. A central aspect of building out this pillar was ensuring the creatures and insects sounded plausible, which meant a lot of time spent thinking about how an antelope might sound different if it were...made out of carrot, for instance.
- **Tone.** The sound design should have some humour and playfulness to it to match the tone of the rest of the game, wherever possible. It needed to sound 'Dr. Seuss', was the touchstone I had in mind. The kids are all saying "whimsy" right now, right? Whimsy whimsy whimsy whimsy[^1]. This was probably the most important of the three pillars, as it bled into so much of how I designed the sounds -- I was often drawing much more from cartoon sound than the real world.

I set a rule early on in the sound design process to only work with source material I recorded or synthesised myself on this project. This was partially in an effort to grow my own sound library, partially to aid cohesion across the soundscape, and partially because I like to work within creative constraints where I can. When I'm sat in front of a blank Reaper project and have the entire world at my fingertips/earholes, it can be difficult to know where to start. I find that defining a box in which I have to work helps with getting some...I would say "paint on the canvas" but I suppose "waveforms on the timeline" is more accurate but less sexy.

Of course if I had really needed to use source material from libraries for a certain sound I would have bent this rule (and my occasional use of orchestral VSTs probably counts as bending it!), but I never felt the need to and had a lot of fun getting creative with the equipment, props, and resources I had, which are relatively meager in the grand scheme of things! My source recordings for this project were made using a TASCAM DR-40X portable recorder, an sE Electronics sE7, and my Focusrite 2i4. On the technical side I was working in Reaper, using a mish-mash of free plugins plus some stuff from FabFilter, SoundToys, and Kilohearts.

Mercantisle is built in Godot, so I was working with the native Godot audio toolset for this project which is admittedly a little thin, in much the same way that Unity's was for a long time. I considered using FMOD or Wwise for this, but as Forbidden Oak don't have any audio people, I didn't want to saddle them with a confusing piece of software for any development that occurs beyond me. This did make my work more complex -- any time I needed to tweak a level or adjust an EQ I had to do it in code which slowed down iteration, but again, constraints can breed creativity.

By 1.0 I had made more than 200 audio files for the game, so like I say, we're only going to look at a handful of the most interesting sounds and systems. On with the show.

### Biomes and Ambience
As I mentioned, Mercantisle features four different island biomes -- wetlands, arctic, jungle, and badlands (desert, in other words). These all have unique ambient elements, although all consist of a similar structure: a wind and sea wash bed, with intermittent wave crashes and foliage rustles, and any biome-specific elements.

The wind and sea wash bed was synthesised using Kilohearts PhasePlant, in a patch I created myself. I defined macros for the windiness and overall temperature of the biome, which shape the patch subtly. I love pointing out to people that you can hear the temperature of water[^2], so I implemented that behaviour in the synth patch. The windiness control adjusts how often and intensely the wind howls, and each biome's ambient bed is set at different temperatures and windiness levels. Arctic is low temperature and windy, the jungle is warm and less windy, etc. This results in ambient beds that sound cohesive, but also vary appropriately between biomes. The patch effectively boils down to two white noise sources -- wind noise and waves noise -- which are filtered to sound like...wind and waves. There's an LFO which drives the rise and fall of the waves, and a random noise generator which drives the resonance of a bandpass filter on the wind noise, resulting in a howlier wind. The temperature and windiness controls affect things thuslywise:
- **Windiness.** This control adjusts the overall level of sea wash and wind noise, the intensity of wind howls, and the pace at which the sea wash rises and falls.
- **Temperature.** This really just modifies three filters: the lowpass filter cutoff on the sea wash (warmer = lower cutoff = creamier waves), the center frequency of a very slight (2dB) bell boost on the sea wash (warmer = lower center frequency = again, creamier waves), and the center frequency of the bandpass filter which drives the wind howls (warmer = lower center frequency = warmer sounding wind).

<div class="embed-center"><img src="/assets/img/mercantisle-amb-bed-patch.png"><div class="caption">I realise this doesn't necessarily explain how any of this works, but at least you know <em>roughly</em> how it worked!</div></div>

<div class="embed-center">
<audio controls>
  <source src="/assets/audio/mercantisle-arctic-windwash.ogg" type="audio/ogg">
  Your browser does not support the audio element.
</audio>
<div class="caption">Here's an excerpt of the cold, windy, arctic ambient bed.</div>
</div>

<div class="embed-center">
<audio controls>
  <source src="/assets/audio/mercantisle-badlands-windwash.ogg" type="audio/ogg">
  Your browser does not support the audio element.
</audio>
<div class="caption">And here's an excerpt of the hot, differently windy badlands ambient bed. I was aiming for a sort of spaghetti western vibe, not that those tend to involve the sea. Same patch, just varying those macro controls.</div>
</div>

Each biome also has its own ambient elements. The wetlands feature birdsong that I recorded out my back window a few years ago. The arctic biomes feature these crystalline 'shimmers' that I made from hand bell samples with some judicious pitch shifting and reverb. The jungle features intermittent frogs (which I recorded on a visit to the zoo in Amsterdam), crickets (which I recorded on a trip to South Korea), and cicadas which are synthesised in PhasePlant. 

<div class="embed-center">
<audio controls>
  <source src="/assets/audio/mercantisle-jungle-frogs.ogg" type="audio/ogg">
  Your browser does not support the audio element.
</audio>
<div class="caption">The frogs in question were teeny tiny. Super cool. Love a frog.</div>
</div>

The badlands feature intermittent rumbles which are the heavily distorted and manipulated sound of me playing with the empty packet of M&Ms that I stress-ate in the days leading up to 1.0. These are somewhere between an earthquake and thunder, which I felt fit nicely with the badlands -- this biome is where you can drill for liquid magic, and I thought these ambiguous rumbles captured the chaotic potential of that resource nicely.

<div class="embed-center">
<audio controls>
  <source src="/assets/audio/mercantisle-badlands-rumble.ogg" type="audio/ogg">
  Your browser does not support the audio element.
</audio>
<div class="caption">Yes, this sounds like a place we should be drilling for magic!</div>
</div>

### Creatures
This was probably the most significant chunk of work in terms of sound design, as the game features a bunch of different creatures and I wanted the sound to do justice to the excellent visual designs. I approached creatures in two categories -- the plant-animal hybrids, and the giant insects.

The first of the animals I tackled properly was the carrolope, as the game's mascot. 
<div class="embed-center"><img src="/assets/img/mercantisle-carrolope.png" style="height: 25em;"><div class="caption">You take one look at this fella and know he has to sound quite unique...</div></div>
These charming lads got some unique footsteps produced with a bag of carrots and a dish of garden soil, as well as some intermittent vocalisations. I performed the vocalisations myself, and played around with the pitch and processing on them, but they still didn't feel 'carrot-y' enough. Considering that these animals are antelopes hybridized with carrots, it occurred to me that I could hybridize these vocalisations with carrots also. 

This is how I found myself recording the impulse response of a carrot at 11PM.

Normally we use convolution for reverb, applying an impulse response of a specific environment with a dry sound to make an approximation of how that sound would behave in that environment. However, the process is fundamentally just a way to impart the tonal characteristics or frequency response of one recording to another, which I figured was perfect for adding some carrot to these vocalisations. In an ideal world I guess I would have used a vibration speaker to send a sine sweep through a carrot and then deconvolved it to create an IR, but I didn't have that kit on hand so I settled for taping a contact mic to one carrot, and hitting it with a second carrot. I don't know how closely this *actually* captures the resonances of a carrot, but it added an extra *something-something* that got the vocalisations to somewhere I was happy with. I used the same process for the avocadodo also (but with a couple of avocados, natch).

<div class="embed-center">
<img src="/assets/img/mercantisle-carrot-ir.jpg" style="height:50em;">
<audio controls>
  <source src="/assets/audio/mercantisle-carrolope-bark.ogg" type="audio/ogg">
  Your browser does not support the audio element.
</audio>
<div class="caption">This is one of the sillier things I've ever done for a sound project.</div>
</div>

The giant bugs, although there's more of them than the plant-animal hybrids, were a bit more straightforward. There were three main groups of bugs, which I separated based on their locomotion -- gastropods, legged insects, and wormy-leechy-slithery fellas. To use the technical term. Gastropods had slithery movement loops, based on recordings of me getting physical with a bowl of Hartley's jelly. 

Legged insects had individual footsteps which I manually synchronised with their animation sets, with the exception of the <span class="shake-text"><strong>COLOSSUS</strong></span>, the giant centipede which is quite capable of wrecking your day. The colossus' movement sounds are just a constant loop which is a mixture of me drumming my fingers on my desk, and some quite saturated recordings of me rustling dirt[^3] to create the sensation of the ground being torn up by the weight of this thing. Wormy-leechy-slithery fellas were based on a cartoonish slither sound made by a wet finger on a glass. Some of the beetles were also able to fly, which was accomplished through the power of <span class="bounce-text"><span class="rainbow-text"><strong><em>KAZOO</em></strong></span></span>, which I made sure to have on hand early in the process because I was quite sure I'd be needing a kazoo at some point in this project.

<div class="embed-center">
<audio controls>
  <source src="/assets/audio/mercantisle-worm-movement.ogg" type="audio/ogg">
  Your browser does not support the audio element.
</audio>
<div class="caption">I was quite wary of whether this worm movement was too cartoonish for the game, but it worked quite well in context.</div>
</div>

<div class="embed-center">
<audio controls>
  <source src="/assets/audio/mercantisle-insect-flight.ogg" type="audio/ogg">
  Your browser does not support the audio element.
</audio>
<div class="caption">Oh yeah, shred that funky kazoo.</div>
</div>

### ~~Shamelessly Stealing~~ *Taking Inspiration* From Don't Starve
In the implementation realm, one audio system I'd like to put a spotlight on is the biome-based mix that I implemented. As I mentioned each biome has their own specific ambient elements, but I wanted to also have the biome shape the sounds that are common throughout the game -- for example the sounds made by villagers and the UI feedback sounds. I accomplished this by implementing my take on [Don't Starve](https://www.klei.com/games/dont-starve)'s seasonal audio system, which I haven't seen much discussion of but think is super cool.

In Don't Starve, winter is communicated aurally by low-passing some amount of the audio, making everything sound a bit more muffled and wintry. To me this evokes a thick blanket of snow over everything, and everything requiring a bit more effort. I believe in summer the opposite is true, and the sound world is filtered to sound thin and hot.

Mercantisle features effectively the same system, but biome-based rather than season-based. Compared to the wetlands biome (which I considered the default or baseline biome when approaching the sound), sounds in the arctic biome are low-passed to sound heavy and muffled. In the hot biomes, the jungle and badlands, sounds are high-passed at 200Hz to sound thin and hot. Again, my touchstone for this was a sort of spaghetti western sound. I think you could take this approach further and I would have liked to have devised a way for the jungle and badlands to sound different -- hot and wet compared to hot and dry -- but the audio tools in Godot are, uh, lean and didn't make it especially easy to iterate on.

The final system results in each biome having a broader sonic identity than just the ambient elements within it, and while it's quite audible when you know to listen for it, I think it's subtle enough that most players won't notice exactly what's going on but things will just sound more appropriate to the environment, which is exactly the goal I had.

<div class="embed-center">
<audio controls>
  <source src="/assets/audio/mercantisle-build.ogg" type="audio/ogg">
  Your browser does not support the audio element.
</audio>
<div class="caption">Here's what opening the build menu sounds like in the wetlands.</div>
</div>

<div class="embed-center">
<audio controls>
  <source src="/assets/audio/mercantisle-arctic-build.ogg" type="audio/ogg">
  Your browser does not support the audio element.
</audio>
<div class="caption">And here's the build menu in the arctic.</div>
</div>

<div class="embed-center">
<audio controls>
  <source src="/assets/audio/mercantisle-jungle-build.ogg" type="audio/ogg">
  Your browser does not support the audio element.
</audio>
<div class="caption">And finally in a hot biome.</div>
</div>

### Conclusion
I had a really great time working on Mercantisle, and it was a lot of fun to dig into a game project after a few years of focusing on my PhD work. Of course these projects are never truly complete, there's always more I could have added, but you have to set the pencils down at some point and I'm pleased with how the final result turned out.

This was my first time working with Godot which was an interesting experience. As an open-source project it's extremely impressive, but at the moment the audio tools leave a little to be desired, in the same way that Unity's did for a long time. As I mentioned earlier, any time I needed to iterate on the mix it involved finding the relevant code file and adjusting hardcoded values in there, which slowed down iteration. Wwise or FMOD would certainly have made that aspect a lot smoother.

I also didn't get a chance to incorporate either my slide whistle or my Acme siren, which I was sure would have a place in the sound design. Perhaps in a future Mercantisle update or future game project I'll get the chance to use them!

Anyways, hopefully there was something in here that you found interesting. If you read this article and thought "wow, this guy seems like just the capable, handsome, charming, virile sound designer I'm in need of for my next project" then do [get in touch]({% link contact.md %})! My rates are very reasonable and can potentially include ice cream.

<br>
<span class="dark-text">Semper sonorus,</span>

<span class="dark-text">— Jake</span>


<br>
<hr>
<br>

[^1]: *King* of SEO!
[^2]: You, as a person of impeccable hearing and elite-level bathing, can tell when the shower is warm enough to get in because it starts to sound...creamy? That's the best word I've found to describe it. [Here's a research paper that talks about it.](https://doi.org/10.1111/joss.12052)
[^3]: Bless my partner for putting up with a giant lasagne dish of dirt being sat by my desk for the best part of two months.

[mercantisle]: https://store.steampowered.com/app/2909270/Mercantisle/
[helena]: https://helenaricci.com

