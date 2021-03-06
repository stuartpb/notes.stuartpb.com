# Wiring thoughts for my necklace

## Crimping and Casing

The basic electrical core assembly is going to be two four-shells and one three-shell with crimped-together lines interconnecting them, an LED plug coming out, and two single lines coming in.

All this will be attahed in a case that goes around one of the battery holders.

## Diagram

Here's the pinout and crimpout

- Three-shell (to Buck Converter):
  - Position 1 (Vin Pin)
    - cable-crimp to Battery Power In (terminating female crimp)
  - Position 2 (Vout Pin)
    - short-crimp to Four-Shell 1 line and Four-Shell 1, Position 2
    - cable-crimp to LED Power
  - Position 3 (Ground Pin)
    - short-crimp to Four-Shell 2, Position 4
    - double-cable-crimp to Battery Ground In Single Female and LED Ground
- Four-shell 1 (to ESP-01 Edge Pins), left to right with antenna up:
  - Positions 1, 2, and 3 (Power, Reset, and Chip Select):
    - short-crimp to shared 3-shell Vout line
  - Position 4 (UART Transmit): Empty
- Four-shell 2 (to ESP-01 Inner Pins), left to right with antenna up:
  - Position 1 (UART Receive): Empty
  - Position 2 (GPIO 0):
    - short-crimp to shared 3-shell Vout line
  - Position 3 (GPIO 2):
    - cable-crimp to LED Data
  - Position 4 (ground):
    - short-crimp to shared 3-shell Ground line
- Single-shells for both Battery Line (cabled into Buck Converter Vin and Ground)

## Earlier writing to arrive here

The ground could be handled with a pin that is crimped to a wire crimped to a socket (with the power-in cable, which is crimped to the socket for the buck converter) for battery-power-in - crimped in with the wire to the three-pin plug to the strip.

The power is crimped from the buck converter to the wire plug as well.

The three power-side plugs on the ESP are tied together (ie. soldered) as a line, as are the outside ground-side plugs (remember, the ground crimp uses the double-wire into the buck converter for its outbound connection). The data pin from the ESP, however, *is* crimped, to the data line of the LED plug.

The battery chambers will be connected to each other by a ring or whatever (update: pipe cleaners), but they'll also be riding the cables from the LED strips (update, nope, just the pipe cleaners)

## Loose idea for alternative power supply

a dupont-crimped-for-power-lines hacked USB-A (or, hell, maybe even C, or a B port - side thought, is it possible to maybe desolder the Lightning cable in my vest battery to replace it with USB-C for power? Could it work both ways?)

## notes on jig for ESP-01 and buck converter

moved to [here](5jptj-v5s87-0daze-ey3e7-2ajcm)
