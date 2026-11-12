[![Codacy Badge](https://app.codacy.com/project/badge/Grade/7979951772c14c6d83c411553f76e1e8)](https://www.codacy.com/gh/Openvario/variod?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=Openvario/variod&amp;utm_campaign=Badge_Grade)

# variod
Daemon for autonomous  e-vario

## Volume and mute commands

variod receives commands from the flight computer (XCSoar/OpenSoar) as `$POV,C,...` sentences on the
same connection it forwards the sensor data on. All sentences carry the usual NMEA checksum; a
sentence with a wrong checksum is ignored as a whole.

| Sentence | Effect |
| --- | --- |
| `$POV,C,VU` | Volume up by 10 percent. Also unmutes, because a pilot asking for "louder" expects to hear something. |
| `$POV,C,VD` | Volume down by 10 percent. Also unmutes. |
| `$POV,C,VM` | Toggle mute. |
| `$POV,C,VOL,<percent>` | Set the volume to an absolute value between 0 and 100. Values outside that range are clamped, decimals are accepted, and a missing or non-numeric value is ignored rather than read as 0. The mute state is not changed, so a sender can restore its stored volume after a reconnect without cancelling a mute the pilot has chosen. |
| `$POV,C,MUT,1` / `$POV,C,MUT,0` | Mute or unmute. Unlike `VM` the command describes the target state, so it can be repeated safely by a sender that does not know the current state. Any other value is ignored. |
| `$POV,?,VOL` | variod answers with `$POV,C,VOL,<percent>`, the current volume rounded to whole percent. |
| `$POV,?,MUT` | variod answers with `$POV,C,MUT,<0 or 1>`. |

The percentage scales the amplitude of the tone linearly, exactly as the relative `VU`/`VD` steps
always did. Versions of variod without these commands ignore `VOL` and `MUT` silently, so a flight
computer may send them unconditionally.

Note that variod unmutes itself whenever the connection to the flight computer is (re)established;
a flight computer that wants to keep the vario silent has to send `MUT,1` again after connecting.
