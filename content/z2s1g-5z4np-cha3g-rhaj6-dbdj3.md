# Base-5 Extended-Bacon Character Encoding

This is a weird proposal for a practical-use "UTF-5"

## Core idea

Each character is 5 bits. Two codes are "shift characters", and two are "run codes" which allow for any Unicode code point to be expressed using the last two bits (when multiple shift/run codes are contiguous).

## Base

```
   quintet          quintet         quintet         quintet
   00000   Reserved 01000   h       10000   p       11000   x
   00001   a        01001   i       10001   q       11001   y
   00010   b        01010   j       10010   r       11010   z
   00011   c        01011   k       10011   s       11011   <space>
   00100   d        01100   l       10100   t       11100   <shift 0>
   00101   e        01101   m       10101   u       11101   <shift 1>
   00110   f        01110   n       10110   v       11110   <run 0>
   00111   g        01111   o       10111   w       11111   <run 1>
```

## Shift 0 (caps)

```
   quintet          quintet         quintet         quintet
   00000   Reserved 01000   H       10000   P       11000   X
   00001   A        01001   I       10001   Q       11001   Y
   00010   B        01010   J       10010   R       11010   Z
   00011   C        01011   K       10011   S       11011   .
   00100   D        01100   L       10100   T       11100   <shift 0>
   00101   E        01101   M       10101   U       11101   <shift 1>
   00110   F        01110   N       10110   V       11110   <run 0>
   00111   G        01111   O       10111   W       11111   <run 1>
```

## Shift 1 (punc)

```
   quintet          quintet         quintet         quintet
   00000   Reserved 01000   8       10000   ?       11000   "
   00001   1        01001   9       10001   !       11001   '
   00010   2        01010   0       10010   (       11010   -
   00011   3        01011   /       10011   )       11011   ,
   00100   4        01100   +       10100   :       11100   <shift 0>
   00101   5        01101   =       10101   ;       11101   <shift 1>
   00110   6        01110   *       10110   %       11110   <run 0>
   00111   7        01111   &       10111   @       11111   <run 1>
```

## Shifting into Unicode

shift 0 advances the same amount as the last shift
shift 1 doubles the advance amount
this allows for large gap crossing in code points

or maybe just shift

## More coherent shift idea

The first "shift" character represents the first bit of the little-endian sequence noting how many bits will be expressed in the "successor": the next "run" characters represent bits until the next shift character which represents the end of the number.

Might want a rule stating that all codes are expected to have an odd number of bits, so they always end with a run character (specifying the last bit), which ensures a shift character will never terminate the sequence (which could cause it to get conflated with the next character).

The thing I wanted, though, was to have it so a code point that's like `1000000001101` would be representable by expressing the `1101`, then that much higher bit via a shorter series of "run of zero" characters - but, in practice, I'm not sure that's superior to just "use the last two bits of the character to a pre-established length".

Maybe "shift 0" and "shift 1" can be used to distinguish between "here's a new code point" and "here's a new bit spec at position X within this code point"?

## honestly?

why not just have a run of multiple characters above 27 in a row is...

okay, so I'm thinking, and I'm thinking maybe 11111 should be the "return modal" code? like there can be a "sticky bit"? it would be a problem for self-sync, though, where EB normally can only have that problem for one character because the shifts need to be repeated

anyway, if you wanted to copy UTF-8's model, you'd make it so a run of the last 4 code points constructs a code point...

ah, but you have to know how many code points it's going to take first. so maybe at the beginning every "flip" extends the bits, and a flip of the other bit signals that we've started

hmm, maybe you do a "shift for 3 bits at the 15th position, then 5 at the 3rd position" or something to shift into the context with multiple "extended shift sequences"
