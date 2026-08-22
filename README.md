# Cropshot

A screenshot utility for Wayland displays.

Freezing captures the screen first, so you crop a still that cannot change.
Selecting takes the pointer and the keyboard away from whatever you were
pointing at, and much of what is worth capturing exists only while it holds
them.

## Examples

```
cropshot                       # a region, named for the time it was taken
cropshot --freeze              # a region, chosen on a still of the screen
cropshot --screen --cursor     # every output, pointer included
cropshot shot.png              # named
cropshot --clipboard           # copy instead of saving
cropshot shot.png --clipboard  # both
cropshot -                     # PNG on stdout
cropshot --delay 3             # select now, capture three seconds later
```

## Where captures go

With no `FILE` the capture is named for the time it was taken and saved to
`$XDG_SCREENSHOTS_DIR`, read from the environment or from `user-dirs.dirs`, or
to `$HOME` when neither sets it. `--directory` overrides that for one run.

The path is printed on stdout, and an existing file of that name is replaced
without asking.

The clipboard is never written unless `--clipboard` asks for it.

## Delay

The wait always comes immediately before the capture, which puts it in a
different place in each mode:

- Cropping: select, wait, capture. The region can fill while you wait.
- Cropping with `--freeze`: wait, capture, select. Time to open what you
  want photographed before the picture is taken.
- Whole screen: wait, capture.

## Requires

| Needs | For |
| --- | --- |
| `grim` | every capture |
| Pillow | every capture |
| `slurp` | selecting a region |
| `wl-clipboard` | `--clipboard` |
| sway, and `imv` or `feh` | `--freeze` |

Every mode except `--freeze` works on any wlroots compositor. `--freeze` needs
sway. A live selection asks `grim` for the selected rectangle alone, so nothing
outside it is captured even for a moment.

Freezing is sway-specific. The viewer is fullscreened through `swaymsg` and its
rectangle checked against the output before a coordinate is trusted, because a
viewer's own fullscreen request can be quietly ignored — which leaves the still
tiled, and every selection landing somewhere other than where it looks. When
that check fails cropshot refuses to crop, because a crop from the wrong
coordinates looks exactly like a successful screenshot.

The full frame lives in `/dev/shm` and is removed on every exit path, including
a cancelled selection. Only the crop is written anywhere.
