# more gdm wackiness

following from my [previous problems](xk5v6-vsgh8-rna1x-br7jz-gtf8b)

thought the Night Light Slider wasn't working - turns out [the defaults changed](https://github.com/kiyui/gnome-shell-night-light-slider-extension/commit/7223e214ac3b8915b36cfd726b0160372e16a225), even though the old ones were perfectly fine (2500 is darker than I'd like, and 6500 is pretty much the break-even point)

I went ahead and set the old defaults

MPV has no window chrome on wayland

full screen apps seem to be *slightly* transparent? I was seeing my Steam window under a video, it looked like I had screen burn-in

## package changes

rar a `yay -Syu`

uninstalled lib32-gst-plugins-bad because it had an ownership conflict with something else
