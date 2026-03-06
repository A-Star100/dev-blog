---
title: "SRB2Kart's runtime update"
excerpt: ""
coverImage: "https://wiki.srb2.org/w/images/a/ac/Srb2kart_title.png"
date: "2026-03-05"
author:
  name: Anirudh Sevugan
  picture: "/assets/blog/authors/anirudh.png"
ogImage:
  url: "https://wiki.srb2.org/w/images/a/ac/Srb2kart_title.png"
---

If you know me, I've always liked Sonic Robo Blast 2, and a fork of it, Sonic Robo Blast 2 Kart.
Recently, a PR I made for `flathub/org.srb2.SRB2Kart` got accepted! It updated the freedesktop runtime from 23.08 to 25.08.
I was honestly pretty surprised! So in this article I'm gonna tell you what happened.

## Why did I update the runtime?
The game was perfectly fine with the old 23.08 runtime; but it was EOL.
The freedesktop runtime is crucial for every Flatpak because it includes several versions of tools such as CMake.
And since SRB2Kart builds using a Makefile with a compiler included with the runtime, as well as several other libraries,
this is why having an EOL runtime isn't too great.

## What happened while updating it?
A lot of things happened, and a lot of hacks had to be used. But all of it is pretty simple once you look into it a bit deeper.

### discord-rpc
This library is used in SRB2Kart and its forks (such as Ring Racers, SRB2Kart Saturn, Galaxy, Neptune, etc) for Discord rich presence features.
So if you're playing the game on Discord, SRB2Kart'll be like `hey Discord! I'm here!` via IPC (inter-process communication) and Discord will respond back,
all natively on every platform (Windows, macOS, and Linux).

But unfortunately... it's deprecated. A lot of games still use it but it hasn't been updated in ages.
And the only solution? Updating to Discord's proprietary SDK that not only comes loaded with new features, it's not too great to use in open-source projects.
AND the compiler HATED discord-rpc with the runtime update.

When trying my first test build, CMake whined because `discord-rpc` was designed for very old CMake versions.
Which means we gotta add
```yaml
- -DCMAKE_POLICY_VERSION_MINIMUM=3.5
```
to the config-opts for the `discord-rpc` module.

But AFTER this another problem arised...
As mentioned in a [GitHub issue](https://github.com/flathub/org.srb2.SRB2Kart/issues/60) in the repo (for the failure to update to the 24.08 runtime), a stacktrace is given that says
```shell
At global scope:
cc1plus: note: unrecognized command-line option ‘-Wno-global-constructors’ may have been intended to silence earlier diagnostics
cc1plus: note: unrecognized command-line option ‘-Wno-exit-time-destructors’ may have been intended to silence earlier diagnostics
cc1plus: note: unrecognized command-line option ‘-Wno-covered-switch-default’ may have been intended to silence earlier diagnostics
cc1plus: note: unrecognized command-line option ‘-Wno-c++98-compat-pedantic’ may have been intended to silence earlier diagnostics
cc1plus: note: unrecognized command-line option ‘-Wno-c++98-compat’ may have been intended to silence earlier diagnostics
ninja: build stopped: subcommand failed.
```
The key here is the `unrecognized command-line option` note and the `-Wno-` prefix, which tells us it's a `Wno-error` for a CL option
that was removed because of the runtime update (because it *used* to work with the old runtime)
that can be filtered out in the build-options. Nice!
So I added 
```yaml
    build-options:
      cflags: "-O3 -Wno-error"
      cxxflags: "-O3 -Wno-error"
```
under the `discord-rpc` module.
The reason we can do this is because the compiler sees CLI options it doesn't know exists; as they were removed with the runtime update.
But even if it doesn't recognize the options, the build will still *work* if we remove them.
The only reason this was stopping us is because the compiler wanted to make sure we weren't just going to do this stupidly and make a bunch of huge mistakes.
And thankfully, I don't think I did.

### rapidjson
This library is ALSO used in SRB2Kart (and its forks).
It is a really really fast JSON parser. I *think* it's used for the API for the SRB2Kart Master Server???

You see, the way online play works in SRB2Kart is there is a master server that contains info about active game servers (that were public (NOT LAN-only) and had advertising enabled in-game).
Its API returns back active servers and their IP addresses once it receives a GET request to the right endpoint (a good tool for examining APIs is [Postman](https://www.postman.com/), so when you play SRB2Kart you can use the server browser (which fetches data from the master server)
and then shows a list of valid servers, instead of searching the world for each one (which would take INSANELY long...).
SRB2Kart will take it from here and connect to a server you choose.
If a server isn't on the master server (it is LAN-only or advertising on the MS was disabled) you can also use its IP address or a valid domain for it.

But I digress, I should probably review the source code to make sure.
*Anyway*, `rapidjson` had the same CMake compatibility issue as `discord-rpc` (another `- -DCMAKE_POLICY_VERSION_MINIMUM=3.5`)
but after that it had some weird `GenericStringRef` error.

In the same [GitHub issue](https://github.com/flathub/org.srb2.SRB2Kart/issues/60), this was ALSO present.
```shell
/app/include/rapidjson/document.h: In member function ‘rapidjson::GenericStringRef<CharType>& rapidjson::GenericStringRef<CharType>::operator=(const rapidjson::GenericStringRef<CharType>&)’:
/app/include/rapidjson/document.h:319:82: error: assignment of read-only member ‘rapidjson::GenericStringRef<CharType>::length’
  319 |     GenericStringRef& operator=(const GenericStringRef& rhs) { s = rhs.s; length = rhs.length; }
      |                                                                           ~~~~~~~^~~~~~~~~~~~
```
Apparently this was a bug in the `rapidjson` library itself that was patched in an update...
but unfortunately the last GitHub release for `rapidjson` was `v1.1.0` (released in 2016) which included the bug.
So what would I do?
Well, Git is a platform for source code, and the [rapidjson](https://github.com/Tencent/rapidjson) repo had MUCH newer source available,
that WHACKED the terrible bug out.

So the solution was simply to update the `rapidjson` version used.
```yaml
    sources:
      - type: archive
        # latest commit as of 2026-02
        url: https://github.com/Tencent/rapidjson/archive/24b5e7a8b27f42fa16b96fc70aade9106cf7102f.tar.gz
        sha256: 2d2601a82d2d3b7e143a3c8d43ef616671391034bc46891a9816b79cf2d3e7a8
```
I purposely decided to keep the rapidjson version frozen because this one worked pretty well with Kart, and I didn't want to use the latest commit
so breaking changes would be included in production builds of SRB2Kart.

### srb2kart
The SRB2Kart module ALSO had some build problems, regarding the C standard.
A C standard is basically a set of rules the compiler and linters will follow.
Back with the old runtime (freedesktop 23.08) the default C standard was much older and compatible with SRB2Kart's source code.
But with the NEW runtime (25.08), it updated the C standard to C23, which was far newer and had direct conflicts with SRB2 and SRB2Kart's source.
And C23 had a crucial difference with the older standards.
It set the `register` keyword to a reserved one.... now THIS was bad because many older games
would use register practically EVERYWHERE as a hack to define variables.
But if it is reserved it can only be used in a controlled manner, so when I did the NEXT test build for Kart,
the compiler ABSOLUTELY HATED the BLATANT usage of register...

But by setting the C standard back to C17, everything worked fine.
```yaml
        CFLAGS: "-O3 -std=gnu17 -I/app/include"
        CXXFLAGS: "-O3 -std=gnu++17 -I/app/include"
```

However, extra logs were added for fastmix.cpp, and by that I mean a whopping 1000+ lines...
But this was just because of another hack being used.
In the past, devs used to use register (for fastmix they were used in places C23 liked)
for int variables to ENSURE they were set instantly and to not waste valuable CPU cycles computing when exactly to add it.
They were just force-added.
But in newer C standards, this hack was no longer needed; so the compiler would throw warnings for each and EVERY instance of `register`
(a HUGE amount...), and the game would never use the register keyword (instead just set an `int`) and the hack would be useless.

So in a new PR I made (not the merged one; a different one), I added 
```yaml
    build-options:
      cxxflags: "-O3 -Wno-register"
```
for `libmodplug`. If you're wondering why SDL2 is recompiled in the yaml, this is why.
SDL2 in the freedesktop runtime and its mixer lib does NOT come with modplug support enabled.
But if you recompile it with modplug options, you get support.

### libgme
Libgme (thankfully) didn't cause any problems. It is what is used for music playback in Kart.
SDL2-mixer supplements it (I think, oughta double check).
But v0.6.3 was being used with an older `bitbucket.org` link instead of the proper upstream one on GitHub.
So I updated the version. But the cleanup section in the module was causing issues.
```shell
    cleanup:
       - /include
       - /lib/*.so # this is what we're talkin about
       - /lib/pkgconfig
```
The `- /lib/*.so` step would end up cleaning the very binaries compiled for SRB2Kart... quite literally.
So the game would fail to even launch and end up without a mixer.
So as a quick fix I just hinted out this whole section and it worked again.
I *guess* v0.6.4 included some breaking changes or somethin I dunno.
To remedy the `/lib/pkgconfig` and other cleanup steps being thrown out the window; on my last commit before the PR was merged
I added a top-level cleanup:
```yaml
cleanup:
  - "/include"
  - "/lib/cmake"
  - "/lib/pkgconfig"
  - "/share/man"
  - "*.la"
  - "*.a"
```
Unfortunately, I added `/share/man`, so descriptions of each command would unnecessarily be cleaned up.
They DO regenerate on Flathub's CI (I think) but aren't gonna be used by many workflows on the CI anyway, and since the maintainers
were fine with it I decided to leave it as is and only clean this up in a new PR I made.

And then, it got merged... my contributions were now included in [SRB2Kart on Flathub](https://flathub.org/apps/details/org.srb2.SRB2Kart).
I was pretty glad honestly :D
If you play the game on Linux, you've now got an insider look at what happened on the scene!!!

## So, what's the new PR for?
It's not crucial, but it is for a library migration
You see, SRB2Kart works on Steam Decks.
And Steam Decks utilize a little feature called *Gamescope* for games.
It's basically a middleman between the game and the OS, that adds extra features (like AMDX-graphics-something support for the TRUE GAMERS).
But SRB2Kart doesn't take advantage of that... (wawaawaaaaaaaaa...).

But the library used for gamescope support (`com.valvesoftware.Steam.Utility.gamescope`) was deprecated recently.
While this is fine for now, it means it'll get NO security updates... or any updates really.
It's officially been migrated to `org.freedesktop.Platform.VulkanLayer.gamescope`, which is actually SUPPORTED.
So I made this [new PR](https://github.com/flathub/org.srb2.SRB2Kart/pull/72/) just for gamescope.
Once it gets merged, I'll see ya then!
