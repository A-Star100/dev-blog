---
title: "My broken SRB2Kart ports"
excerpt: ""
coverImage: "/assets/blog/srb2kartsnap/cool_tails.png"
date: "2026-03-17"
author:
  name: Anirudh Sevugan
  picture: "/assets/blog/authors/anirudh.png"
ogImage:
  url: "/assets/blog/srb2kartsnap/cool_tails.png"
---

Well, I tried to do the almost-impossible.
Get SRB2Kart running on `i386`, `armhf`, and `riscv64`.... a BUNCH of arches.
First off, you might be thinking, what's RISC-V? I mean, it sounds kinda weird.
Well, it's a relatively new architecture that stands for *Reduced Instruction Set Computer Five*.
No, literally, that's the name, not kidding.
Yeah, it's got kind of a weird name, but the arch is a standout because there are no royalty fees, and it's pretty low power too
so it competes against ARM nicely :D
Normally, with x86 and ARM chips, people who manafacture them have to pay a fee to the guys who invented the arch.
These are royalty fees, and they can be pretty expensive sometimes.
This is also what happened with Android vs Windows Mobile (yeah, Microsoft made phones at one point; they sucked though).
Microsoft charged $5-15 for every phone running Windows Mobile, and considering that hundreds of phones
can be manafactured every day, this wasn't exactly looking too good for Microsoft...
But Android had no royalty fees, was able to compete with iOS, and was just way better than Windows Mobile ever was.
So you can guess what every manafacturer chose to do. They chose Android obviously.
*Some* guys chose Windows Mobile, but they kinda treated it as a side thing.
There was a good amount of people that liked Windows Mobile, but it was just, bad compared to Android and iOS because it felt really dated...
I mean, nowadays when you use your phone, it's got a touchscreen and a digital keyboard. Well this was the era when phones had PHYSICAL keyboards. With real keys!!
And back in the 2000s, it was considered normal for a phone to have a keyboard!... with all the letters shrouded up and curved and good screen real-estate
being virtually non-existent compared to nowadays. Until the iPhone launched, pretty much every phone was like this.
And after the iPhone launched, Google created Android and manafacturers used it in their hardware, which followed the iPhone's design,
and had no physical keyboard (the earliest models had a keyboard and sometimes both keyboard and multi-touch, but after 2010 this got pretty much phased out).

### About Snaps
SRB2Kart has always had a great [Flatpak](https://flathub.org/en/apps/org.srb2.SRB2Kart) available, but never a Snap.
The big yada-yada with Flatpaks and Snaps is that they're both packaging formats that apps can be released in.
But Flatpaks are completely open, whereas Snaps are partially controlled by Canonical with the Snap Store.
Unlike Flatpaks, which can be on any repo, but the most popular one is Flathub, so it's considered the central store for them.
Some people like Flatpaks, others Snaps. The main difference between them being that Flatpaks use a runtime system
(the Freedesktop runtime is central to all Flatpaks as it provides tools such as CMake, Python, Node.js; pretty much everything you can think of),
and additional runtimes (like the gnome one) are available as well.
But Snaps don't use runtimes, they use packages. For example, to get SDL2 in a Snap, you use libSDL2 as a package.
But for Flatpaks, you oughta either add it as a module (if you want to rebuild it from source for extra features like modplug)
or use the one in the runtime.
Another key difference is that Flatpaks use your host's filesystem, but are heavily sandboxed (by default anyway),
so the most access they can get when sandboxed to their own isolated folder in a place like
`.var/apps/com.CoolAppsHere.MyVeryAwesomeApp`
and nothing else.
Snaps are also similar, but their path is more like
`/home/snap/...` (yeah I forgot the rest... awkward)
and it is separate for each version of the app released most of the time,
but data is manually migrated between each revision and Snaps can also
use a `.common/[app-id-here]` folder to store their assets in one universal place,
but where an app chooses to store its stuff sorta varies.
And since SRB2 already had a [community Snap](https://snapcraft.io/srb2)
I decided to use that as a basis for my [SRB2Kart](https://snapcraft.io/srb2kart) and [Ring Racers](https://snapcraft.io/ringracers) snaps.

### The porting process
I started by using nice CI/CD (thanks to GitHub Actions) for the builds.
CI/CD (Continuous Integration/Continuous Development) lets you run builds automatically on servers somewhere else out there
when you make changes to the code in your Git repository (basically a place to store your code).
And I decided to use QEMU (an emulator that could emulate TONS of different arches on TONS of different hardware)
for the build process.
Now, I'm not gonna delve into exactly what happened because that would take ages. If I told you what happened every second,
it would go on for about 3 days (which is how long I tried to do this for)
AND before that I tried to make a RISC-V port of a totally different app...
that didn't go too well either.
(to be continued, the GitHub repo for the Snap is [here](https://github.com/A-Star100/SRB2Kart-Snap/))
