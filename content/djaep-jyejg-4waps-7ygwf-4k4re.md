# Voyager Pro Scatter File

Rebooting into recovery (via the button in MTK Droid Tool, though I probably could have done it via adb on stushiba had I thought to try it) lets me look at /cache/recovery/last_log via "View recovery logs", where the list of buildprops(? stuff like ro.product.manufacturer, which is listed as "Alco", contra the ro.product.brand which is RCA) is followed by stuff like "Opening update package...", and then this after "Update package verification took 0.5 s" - I'm copying this by hand:

```
====== Scatter File:
preloader 0x0
pgpt 0x0
proinfo 0x80000
nvram 0x380000
protect1 0x880000
protect2 0x1280000
persist 0x1c80000
seccfg 0x4c80000
lk 0x4cc0000
lk2 0x4d20000
boot 0x4d80000
recovery 0x5d80000
para 0x5d80000
logo 0x6e00000
odmdtbo 0x7600000
vendor 0x8600000
expdb 0x21600000
frp 0x22000000
tee1 0x22100000
tee2 0x22600000
kb 0x22b00000
dkb 0x22d00000
metadata 0x22f00000
system 0x25000000
cache 0x6b800000
userdata 0x72800000
```

note that the sizes of some of these partitions are also listed at [output from fastboot getinfo all](cpnw8-mvw3r-ye9b5-5130t-27dys), to save you some math

I'm realizing now that this is, ironically, probably in the system image I dumped - not just as metadata, but this log itself (NOTE: it's GPT-formatted, so yes)

Going on to [manipulating the dumps on stushiba](hehse-b5bbn-r4awm-p6xg0-1pj4c)
