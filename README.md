# lamiera

*superficie suonabile vibrante*

A handpan you play in the browser, with the computer keyboard or by touch. One HTML file, no dependencies, no install.

**▶ Play it: [phonk.github.io/lamiera](https://phonk.github.io/lamiera/)**

![lamiera](screenshot.png)

## Play

Open [phonk.github.io/lamiera](https://phonk.github.io/lamiera/), or `index.html` in any browser. Press any key or tap the pan to wake up the audio.

### Keyboard

The home row is one continuous line of tones. They climb in a zig-zag from the middle of the keyboard outwards, the way the tone fields climb around a real handpan.

| key | A | S | D | F | **G** | H | J | K | L |
|---|---|---|---|---|---|---|---|---|---|
| tone | 8 | 6 | 4 | 2 | **1** | 3 | 5 | 7 | 9 |
| edge | Z | X | C | V | B | N | M | , | . |

- **space** plays the ding, the low note in the centre.
- The **home row** strikes the centre of a tone field: full fundamental.
- The **row below** strikes the same tone on its edge: more octave, twelfth and steel.
- **Shift** + key plays an accent.

On each pad the amber name is the note; the solid box is its centre key and the dashed box its edge key.

### Touch

The whole surface plays. Where you strike changes the sound:

- the centre of a pad gives the fundamental, the edge gives the harmonics;
- just outside a pad you hit its edge; further away the note fades into a knock on the steel;
- the more finger you press down, the harder the strike (where the browser reports contact size or pressure).

### Modifiers

Laid out like the top row of the keyboard, one group per hand.

| left hand | | right hand | |
|---|---|---|---|
| **Q** | octave down (hold) | **U** | octave up (hold) |
| **W** | soft (hold) | **I** | long tail (hold) |
| **E** | roll (hold): repeated strikes on the held tones, or on the ding | **O** | damp all ringing notes |
| **R** | next scale | **P** | grit, a stepped lo-fi distortion (toggle) |

### Controls

- **dynamics**: base strike strength for keyboard and mouse
- **roll**: density of the roll
- **brightness**: how much twelfth and metal comes out
- **reverb**: amount of the long (12 s) reverb

## Record

- **audio** records the output as a stereo 16-bit WAV, keeping the screen awake while it runs; **save .wav** downloads it.
- **midi** records your strikes. **play** replays them with the current scale, **save .mid** exports a standard MIDI file (120 bpm).

Inside the claude.ai viewer files are offered through its download prompt, packed in a .zip.

## MIDI

Press **enable** and pick the ports (Chrome, Edge or Firefox on desktop; Web MIDI is blocked inside the claude.ai viewer, so use the GitHub Pages version).

- **in**: notes strike the pad closest in pitch, velocity sets the strength; CC64 (sustain) holds the long tail, CC1 (mod wheel) sets the brightness.
- **out**: every strike sends its note on channel 1, with its velocity.

## Scales

Each scale is tuned in just intonation on its own ding.

| scale | ding | tones |
|---|---|---|
| D Kurd | D | A B♭ C D E F G A C |
| C♯ Kurd | C♯ | G♯ A B C♯ D♯ E F♯ G♯ B |
| D Celtic | D | A C D E F G A C D |
| D Amara | D | A C D E F A C D E |
| D Integral | D | A B♭ C D E F A C D |
| D Hijaz | D | A B♭ C♯ D E F G A C |
| F Pygmy | F | G A♭ C E♭ F G A♭ C E♭ |
| F♯ Pygmy | F♯ | G♯ A C♯ E F♯ G♯ A C♯ E |
| F Equinox | F | A♭ C D♭ E♭ F G A♭ C E♭ |
| C Aegean | C | E G B C E F♯ G B C |
| C Major | C | G A B C D E F G A |

## Sound

Plain JavaScript and the Web Audio API, no WebAssembly and no libraries.

Each strike runs through a **modal resonator bank** in an AudioWorklet: one set of resonators per tone field, each mode a complex one-pole resonator (a decaying sinusoid whose amplitude is exactly its input). The modes and their behaviour were measured from recordings of four different instruments: handpans and a Hang, about 80 single strikes, finger and mallet, analysed for partials, levels, bloom and decay.

| what was measured | real instruments (range, typical) | lamiera |
|---|---|---|
| octave | 1.975–2.01 × f, −4…−21 dB (typical −8…−13), peak 30–340 ms after the strike | two split modes at 1.992 and 2.006 × f that beat, about −8 dB, peak around 60–125 ms |
| twelfth | 2.98–3.01 × f, −7…−28 dB (typical −15) | 2.998 × f, about −11 dB, stronger towards the edge and on hard strikes |
| fourth harmonic | −13…−40 dB, often absent | about −23 dB |
| decay | fundamental T60 2–6 s, nearly independent of pitch; octave and twelfth ring 1.3–2.1 × longer | T60 = 5.5 s × (196 Hz / f)^0.2, octave × 1.8, twelfth × 1.9 |
| sympathy | the other notes ring at −18…−34 dB | every strike leaks into the other fields, more where their partials coincide: about −28 dB |
| attack | high noise burst −28…−31 dB, falling 20 dB in 30–50 ms | about −31 dB |

Sources: [luntal/HandPan](https://github.com/luntal/HandPan) (Apache-2.0), [arash-mohtat/hangdrum_py](https://github.com/arash-mohtat/hangdrum_py) (CC0, finger and mallet recordings), [jakobabesser/hang-buddy](https://github.com/jakobabesser/hang-buddy), [wutlu/hang](https://github.com/wutlu/hang) (GPL-3.0), [musicHK/air-handpan](https://github.com/musicHK/air-handpan). The recordings were only measured; none of them is included here.

- **Bloom.** The octave and twelfth are not only struck directly: they are driven by the square of the fundamental (and by fundamental × octave), a quadratic coupling like the one in the steel shell. Energy flows from the fundamental into the octave over the first few hundred milliseconds, more on harder strikes.
- **Exciter.** The finger is a Gaussian contact force pulse (σ 0.1–0.36 ms, a smooth spectrum with no gaps): harder strikes are narrower and reach higher modes. A short burst of lowpassed noise, different every time, adds the skin of the finger.
- **Strike position.** The fundamental mode is strongest at the centre of a field, the octave modes have a nodal line through it and peak towards the edge, the twelfth rises near the edge.
- **Reverb.** A convolution with a synthetic 12 s impulse response that darkens as it fades.

On screen, every strike sends rings across the steel for as long as the note lasts, and the rim of the pan trembles in amber with the actual output level.

## Origin

Lamiera started as a patch for the Shbobo Shnth, a handpan whose keyboard borrowed the binary touch pads of Lorre-Mill's Keyed Mosstone, and grew into this browser instrument.
