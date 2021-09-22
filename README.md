# The Mindy Python's Confined Circus
A LEGO robotic MIDI band

## Intro
Almost 2 year working at home. Lots of noise... mainly from neighbors rebuilding their apartmens but also from
the kids while they were also attending classes from home.

Not easy being an introv'd engineer. You get crazy with the noise and the lack of your own "space".

You find yourself thinking on playing bagpipes on the roof for the falcons... but you know zero about music...

... hey, but you have Google and LEGO and a linux laptop.

So I ordered a bagpiper pratice chanter and started Tuxie McPython, a LEGO bagpiper.

Though, still working on it. But meanwhile I learned a lot with it and applied most of the findings on other LEGO
music instruments. All of them MIDI based so they can play together, like a band. A crazy python-based robotic
band, much like the Toa Mata Band.

### The instruments

All instruments / robotic players use LEGO hubs running Pybricks firmware. That means I can use City Hub (2 ports),
Technic Control+ Hub (4 ports) or Robot Inventor / SPIKE Hub (6 ports). There is now a new SPIKE Essentials Hub
(2 ports only) but I see no benefits on using one for my purposes (the best, for me, is the Technic Control+
Hub with 4 ports and a reasonable price at Bricklink). I could also use BOOST Move Hub (2 ports and 2 extra
internal motors) but it lacks enough resources for a full Pybricks image so for now I am avoiding using to assure
all my hubs and code are compatible.

The LEGI hubs don't need complex code - they just receive short messages from the "coordinator" and move their
motors according to it. Most of the instruments just expect a single character like 'A' meaning that they should
move the motor attached to 'Port A' whenever an 'A' is received. However some instruments (like the Bass) expect
a note instead (like 'E') and that will initiate a combination of actions on the several finger motors in order to
produce somethink that sounds like an 'E'.

### The coordinator

For each instrument there is a python 'coordinator' script running on my linux laptop. It just uses the
'pybricksdev' libraty to communicate with the LEGO hubs, very simple. Each coordinator receive some kind of
short command from the MIDI logic and passes it to the hub - for instance the Bass coordinator receives an 'E'
and sends an 'E' to the Bass hub, just that. But since some instruments need more than one hub, some
coordinators receive a note MIDI number (like '60' for the central C) and have to decide what to send to each
of the instruments hub (for the Glockenspiel, a '60' would mean to send 'A' for the first hub in order to move
the hammer over the 'C' bar).

### The MIDI logic

This is the ugly part but also where linux shines: a mix of ALSA utils and a bash script that parses a MIDI
stream and forwards the right events to the Coordinators.

The ALSA utls are 'aplaymidi', 'aseqdump' and 'aconnect'.

I use 'aplaymidi' to play a midi file, sending it to a MIDI port. If there is a synth listening to that port
it will play the song but I use 'aseqdump' to listen to that midi stream and some bash commands to parse the
events and send the relevant ones to the Coordinators.

I also use 'QMidiRoute' to ease this taks because most instruments don't have a predefined MIDI channel
(except for Channel 10, reserved for percussion) so songs musics might use a Bass on channel 2 and others on
channel 3 or 7 or whatever... some songs even switch instruments while the music is playing so channel 3 can
use a fingered bass at first and then a fretless bass.

'QMidiRoute' allows to define routing rules so I can use a rule for a 'Queen' song and another rule for a
'Depeche Mode' song without changing my scripts. And it also allows to transpose some notes which is great
when my instrument cannot cope with all the notes used on a song.

### The extras

The Glockenspiel and the Percussion Kit have plenty of volume but the Bass can't cope with them so I use
a contact mic and a portable guitar amplifier with it. The sound is good but on some songs the notes could
be better, more differentiated and with less mechanical noise (the faster the song, the more these problems
are noticed) so I'm now also using a Raspberry Pi with a PiSound board and PatchboxOS so I can use some
virtual pedals (like a pre-amp, a compressor, a loudness and a low-pass filter).

The PiSound was bought because of it's low-latency MIDI interface for another project and will probably
be used later with my LEGO Laser Harp if I find the time to build a robotic Je4n-Michel.
