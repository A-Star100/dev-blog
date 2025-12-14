---
title: "Custom Android ROMs - Breathing new life into old devices"
excerpt: ""
coverImage: "/assets/blog/customandroidrom/lineage.avif"
date: "2025-12-14"
author:
  name: Anirudh Sevugan
  picture: "/assets/blog/authors/anirudh.png"
ogImage:
  url: "/assets/blog/customandroidrom/lineage.avif"
---

Every device dies sooner or later in terms of updates.
For example, the Samsung Galaxy S21 just recently became an EOL device (doesn't support Android 16). But that doesn't mean you have to stay EOL unless you buy a new phone.
Custom Android ROMs breathe new life into older Android devices.

For example, my international Samsung Galaxy S9+ was originally stuck on Android 10, but now I upgraded it to Android 13 (despite it not being officially supported) and it works great! I could even upgrade it to Android 16 if I wanted to (though the custom ROM for that is more unstable due to it being unofficial).
And, my Samsung Galaxy S3 (US variant, AT&T-locked) is now running Android 7 compared to Android 4.4.2 (which means it can run modern apps now!), and it is no longer locked to AT&T anymore! (I can use it with any carrier now... but it is far too old anyway).

And this is all thanks to [LineageOS](http://lineageos.org/), as well as [TWRP](https://twrp.me/about/). LineageOS is a fork of [AOSP (Android Open Source Project)](https://source.android.com/) with extra features and a wide array of compatible devices. Build exist for devices like the Samsung Galaxy S3, all the way to some of the newest phones on the market.
LineageOS can also improve performance on some devices (in tests, my S9+ performs better than with the stock firmware when running the same apps now), and with official builds, you get the best compatibility. For example, the **Bixby** button on my S9+ isn't redundant, it actually does something even on a custom OS like Lineage! (though it doesn't actually open Bixby anymore; Phew, I hated Bixby).

[GrapheneOS](https://grapheneos.org/) also exists, and it is much more secure (even more secure than Samsung and Google's consumer skins of Android), with advanced attack mitigation and other security features. But it is only officially supported on Google Pixels.
But a custom ROM isn't always right for you. So let's dive into the pros and cons!

## Pros
- Lets an officially EOL device benefit from features and improvements in newer versions of Android, including security patches, UI updates, and more!

- Lets an older device use newer apps and newer Android libraries and toolkits.

- Can increase performance due to not being as bloaty as most stock firmware, for example: Samsung's OneUI (which is good but sort of bloaty).

- Allows for ***deeeeep*** customization, and is easier to root than with stock firmware.

- Keeps budget hardware running on newer versions of Android.

- Better/same battery life in most cases.

## Cons
- Can brick (render temporarily/permanently unusable) your device if the custom ROM flashing procedure is done incorrectly or if the wrong ROM is used.

- Requires a custom recovery image, ROM, and technical knowledge, and in certain cases, formatting all the data on your phone.

- Harder to update than with stock firmware (it is still possible to update inside Android; but for major version leaps a new custom ROM is normally required).

- Requires an unlocked bootloader, **which is not unlockable at all on certain phones, for example, US variants of newer Samsung phones**, **and on Samsung phones, unlocking the bootloader will disable security features and permanently disable Samsung Knox; which will remain unusable even if you go back to stock firmware**.

- Some apps will **refuse to run on modded and/or rooted phones due to quirks with integrity checks, for example banking apps, so keep your modded devices as a backup if you plan to use banking apps often**.

For most people, I'd say that **custom ROMs are not worth it**, but for people who are willing to take the risks, and want to upgrade their EOL device, go ahead! You'll learn a lot!
Anyway, that's all for now. See you soon! Oh, and Merry Christmas and Happy New Year in advance!