# lcd screen puttering

Messed around with a screenshot in GIMP because I was too lazy to visit the path first and see that the matrix is already a default

Oh well, whatever

## local workspace on stushiba

making a zachtronics/shenzhen-io/lcd-screens directory under workspaces

## Writing an install script

had a bit of confusion around `$XDG_DATA_HOME` being undefined (just me forgetting that Bash defaults come from `-`, not `:`)

`env | grep ^XDG` I notice that XDG_DATA_HOME is not defined, but:

```
XDG_DATA_DIRS=/home/stuart/.local/share/flatpak/exports/share/:/var/lib/flatpak/exports/share/:/usr/local/share/:/usr/share/
```

lol

## side thought

You know what'd be cool? If you could just refresh the LCD list once the end is reached or a file goes missing? idk

## another way-out side thought re: .local

[split off to a separate file](xdcbk-xxjpm-4ta24-qtt46-r64fd)
