# The Sound Bike

A short visual explainer for the Dublin bike disco crew: how the sound gets down a
20–100 bike parade, and who gets to press skip.

**Live site:** https://itsslimetime.github.io/bike-disco-sound/

Single static page, no build step, no dependencies. `index.html` is the whole thing.
Diagram-led and deliberately brief — roughly 1,200 words.

## The rig

Today: iPhone → **Bluetooth** → SoundBoks → minijack cable → SKAA transmitter → speakers,
with a second SKAA unit in **receiver mode** relaying to a further cluster.

Conor's proposal — wire the phone straight into the SKAA transmitter — is the right call.
It deletes the only variable-latency hop in the chain (Bluetooth wanders 120–200 ms; SKAA
is a fixed 36 ms) along with a battery, a pairing and a thing that can be switched off.

Notes:

- **SOUNDBOKS 4, Mix and Go have SKAA built in** and can join a cluster without their own
  transmitter.
- One transmitter feeds **up to 4 receivers**, held within **40 µs** of each other, out to
  about **45 m**.
- Range extends by **relay**, not just by adding transmitters: a SKAA unit in receiver mode
  feeds a speaker by cable, and that speaker starts a fresh cluster. Each relay adds 36 ms,
  which is less than the sound-travel time between clusters that far apart — so it's
  inaudible.
- **Raise the aerials.** 2.4 GHz is absorbed by water and a crowd is mostly water. A 200 g
  transmitter on a short mast beats lifting a 16 kg speaker, and buys more range than
  anything else cheap.

## Who controls the music

**Spotify Connect is same-account only** — no tier or setting changes that. **Spotify Jam**
is the cross-account route: the sound bike hosts, everyone else keeps their own Spotify.

- **Guest controls are OFF by default** (Jam details → Guest settings). Very likely why the
  first attempt felt broken: guests could queue but never skip.
- **Every admin needs their own Premium.** A Free account can join in person and add songs,
  but can't host, can't join by link from a distance, and reportedly can't skip. On a parade
  every join is a distant one.
- **No shared network is needed.** Jam runs over ordinary mobile data through Spotify's
  servers. Tailscale wouldn't help regardless — it doesn't carry multicast/mDNS
  ([#11134](https://github.com/tailscale/tailscale/issues/11134)).

## The one rule

**Only one device makes a sound.** In a Jam every phone streams its own copy; that's fine
until two of them drive speakers, at which point two clocks drift apart — the reported 3–7
seconds that sank the first attempt. Speakers on one SKAA transmitter physically cannot
drift. Extra phones can.

Fallbacks on the page: one shared **Bike Disco** account with Connect (bulletproof, but
everyone signs out of their own Spotify), and a three-button web remote on the Spotify Web
API (admins need no account at all, but it needs a small backend).

## Local preview

```sh
python3 -m http.server 8000   # then open http://localhost:8000
```

## Sources

Spotify [Jam](https://support.spotify.com/us/article/jam/) and
[Connect](https://support.spotify.com/us/article/spotify-connect/) documentation;
[SOUNDBOKS on SKAA](https://soundboks.com/blogs/in-tune/what-is-skaa-technology-and-how-does-it-make-your-soundboks-even-better);
[SKAA](https://skaa.com/pages/transmitters) published specifications.
