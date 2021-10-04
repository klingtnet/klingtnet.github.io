```json
{
  "author": "Andreas Linz",
  "title": "AMD Ryzen random reboots under Linux when in idle",
  "description": "This post describes how to prevent random reboots for AMD Ryzen based Linux systems in idle state that use XMP memory profiles.",
  "created_at": "2021-10-02"
}
```

# AMD Ryzen random reboots under Linux when in idle

At the end of 2019 I decided to replace my, now 10 years old, trusty Intel i7 920 machine.
The main motivation was that the i7 was not powerful enough to drive a bunch of CPU hungry software synth, and I always wanted to have a machine that was able to handle any amount of audio plugins I throw at it.
It was still fast enough for some casual gaming or web browsing so I gave it to a friend who was looking for PC at that time.  Otherwise I would have had to throw it away, which would have made me feel a little guilty.
Anyways, AMD Ryzen was all the buzz at that time so I decided to go for that and ordered all the necessary parts.

Here are the relevant specs for reference:

- CPU: AMD Ryzen 3700X
- Mainboard: ASUS ROG STRIX B450-I GAMING
- Memory: 32GB Corsair DDR4 DRAM 3200MHz (part no. `CMK32GX4M2B3200C16`) [^1]
- OS: Arch Linux kernel 5.14.8-arch1-1

Now to the actual topic of this post that is the random reboots that the Ryzen PC showed when running in idle for some time.
I ran a stress test over night and there was no spontaneous reboot so I could rule out a faulty power supply.
Checking `journalctl` for errors did not reveal anything suspicious [^2], in fact the logs just ended before the reboot without any error message.

There is a [Ryzen page on Gentoo's wiki][gentoo] that recommends to set two kernel parameters:

- `idle=nomwait`
- and `processor.max_cstate=5`

The latter one disables certain processor [c-states][c-state] which are basically sleep levels a processor can reach when idling, where C0 is the active state and Cn is the _lowest level of activity_ or _power consumption_.  Disabling c-states is actually wasting energy and thus not very desirable.  I then set both kernel parameters and tried the latest kernel and the LTS variant, but spontaneous reboots still occured.  For a moment I ran out of ideas and suspected that either my Ryzen CPU was defective.

A couple of weeks passed and after some UEFI update I had to reset BIOS settings to _optimized defaults_.  With default settings no random reboots happened anymore!  Problem solved?  Not yet.  In default settings memory is not running at full speed and Ryzen CPUs scale quite well with memory bandwidth.  Enabling the [XMP memory profile][xmp] is, except the a different boot order, the only thing I change from defaults.  This made me curious and after some googling I stumbled over a [linustechtips forum thread][ltt] where someone suggested to disable memory _power down mode_.  Because BIOS settings are often undocumented I cannot exactly say what this settings does, except from the obviously disabling some memory power saving feature.  Anyways, this setting was located under `AI Tweaker -> Memory Timing Controls` which is kind of hidden.

To make a long story short, disabling memory _power down mode_ made the random reboots disappear, even when if the memory XMP profile is activated.

[gentoo]: https://wiki.gentoo.org/wiki/Ryzen#Random_reboots_with_mce_events
[c-state]: https://www.thomas-krenn.com/en/wiki/Processor_P-states_and_C-states#C-states
[ltt]: https://linustechtips.com/topic/1298348-ryzen-reboot-at-idle-solved-rma/
[xmp]: https://www.intel.com/content/www/us/en/gaming/extreme-memory-profile-xmp.html

[^1]: dmidecode to the rescue, god knows which RAM I had installed: `$ sudo dmidecode --type memory | grep -i part`
[^2]: `$ journalctl --priority 0..3 --boot=-1`