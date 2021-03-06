# Pulling from the device flash with SP Flash Tool

I downloaded the Linux version of SP Flash Tool to Stuzzy from [here](https://spflashtools.com/category/linux), then got confused why it seemed to be stuck in the "ready" state when running readback

After this, I was looking into how I could pull just the bits I need from the device, rather than just doing a dump of the whole address space

## useless sleuthing: getting the scatter file

Downloaded https://www.xsfirmware.com/packard-bell-m10400-mt8167-official-stock-firmware/ which is a different device with more or less the same chipset (actually found through a clumsy clonse of that page's text without the file links)

https://forum.xda-developers.com/android/help/scatter-file-mt8167d-t3780862 asked for their device's scatter file but got none

Then I realized, oh, wait, I don't need a scatter file, I *want* to make a dump of the phone, since I'm gonna be experimenting with blowing the whole thing away.

(note that I did eventually find [scatter file info in the in recovery log](djaep-jyejg-4waps-7ygwf-4k4re))

## the useless windows seed is planted

https://www.cyanogenmods.org/forums/topic/mediatek-how-to-create-scatter-file-for-a-mediatek-phone/ describes how I might boot Stuzzy into Windows and get a layout to read from the dump with

(I do try this later, it ends up [going nowhere](phmkn-2mesm-c5a7v-zp3p8-m98fv))

There must be a simpler way to get the boot address, though, right?

## loose threads trying to find a place where this info comes

Googling some of the strings in the region I dumped trying to follow the Pac Bell scatter file turned up https://github.com/ariafan/lk_mtk which might come in handy?

Also has something to do with https://github.com/littlekernel/lk/tree/master/app/lkboot ? I'll come back to this if those ever become relevant

Also, I was looking for the string "Android Boot IMG hdr - kernel size" in use, which turned up these logs I'm not sure how to get

- https://raw.githubusercontent.com/shinewave/x20_96bd/master/x20_96bd_android_reboot.log
- https://pastebin.com/fEn2ZrKi
- https://pastebin.com/xXe4aLvW

(again, note that, while I'm still not sure where the strings I was searching for get spit out, I did eventually find [scatter file info in the in recovery log](djaep-jyejg-4waps-7ygwf-4k4re))

## adding some terminals to stuzzy

Might be on boot? from https://askubuntu.com/questions/40959/how-do-i-connect-to-tty-com-dev-ttyusb0, https://github.com/tio/tio sounds nice, so I'm gonna try that

[picocom](https://github.com/npat-efault/picocom) is also one to keep in mind, and [moserial](https://wiki.gnome.org/action/show/Apps/Moserial?action=show&redirect=moserial)

https://unix.stackexchange.com/questions/22545/how-to-connect-to-a-serial-port-as-simple-as-using-ssh also has some cool suggestions that might be better in different contexts (also, I hadn't considered using an ESP8266 with a serial connection to control WS2812s - this makes me think about one way they could potentially be set up as an ExternalService to Kubernetes like that)

## connecting with tio

```
[tio 17:42:40] tio v1.32
[tio 17:42:40] Press ctrl-t q to quit
[tio 17:42:53] Connected
READYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADYREADY
[tio 17:42:55] Disconnected
```

Okay, how can we sniff what the SP Flash Tool is doing?

https://unix.stackexchange.com/questions/12359/how-can-i-monitor-serial-port-traffic

## sniffing the wire

luckily I've already set up Wireshark

tried `sudo modprobe usbmon` and reading via Wireshark, but ctrl+F "READY" sisn't work so I'm putting that aside - https://blog.davehylands.com/capturing-usb-serial-using-wireshark/ might get me there later

Actually, looking at the `usb.capdata` filter they suggest... okay, now that I get that the serial traffic is the "Remaining capture data" block, I can look at this

Okay, so, it's definitely a binary protocol - this interface isn't the one that produces the logs I was finding. While using Wireshark might be the key to decoding the MTK flashing protocol at some point, I think I can get away with just using the tool's readback functionality to do the whole ROM dump

Anyway, I'm going to go ahead and [tackle that on Stushiba](f0bth-zz6f7-rv9fd-wxs3h-17f67) - I just copied my voyager folder in Downloads on Stuzzy to Stushiba
