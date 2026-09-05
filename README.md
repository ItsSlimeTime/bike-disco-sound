# The Sound Bike

A scrollytelling explainer for the Dublin bike disco crew: how to run one playlist
across a parade of 20–100 cyclists, and let 2–5 people control it from anywhere
in the line.

**Live site:** https://itsslimetime.github.io/bike-disco-sound/

Single static page. No build step, no dependencies. `index.html` is the whole thing.

## The short version

**Tailscale isn't needed, and wouldn't work anyway.**

1. **Spotify Connect does not require a shared network.** Two phones signed into the
   same Spotify account can control each other through Spotify's servers — different
   carriers, different networks, no Wi-Fi involved.
2. **A tailnet couldn't substitute for a LAN regardless.** Local device discovery runs
   on multicast/mDNS, which Tailscale does not carry (open issues
   [#11134](https://github.com/tailscale/tailscale/issues/11134),
   [#1013](https://github.com/tailscale/tailscale/issues/1013)).

**Jam's failure was misdiagnosed.** In a remote Jam every participant's phone decodes
its own stream; several phones feeding several speakers drift by a reported 3–7 seconds.
That is a "too many devices making sound" problem, not a connectivity problem.

**The architecture that works** — two planes that never touch:

| | Carries | Over | Needs internet |
|---|---|---|---|
| Control plane | play / pause / skip / volume | Spotify's servers | Yes, both ends |
| Audio plane | the actual sound | SKAA, 2.4 GHz radio | No |

One shared Spotify Premium account. One phone cabled to the SKAA transmitter — the only
device on the parade allowed to make a sound. Everyone else is a remote.

## Gotchas the page covers

- Admin phones: **Settings → Apps and devices → "Show local devices only" must be OFF.**
  Left on, the disco phone stays invisible because it isn't on your Wi-Fi. This is the
  single most likely reason a first attempt fails.
- Download the playlist offline on the disco phone — playback then survives coverage
  blackspots (remote control doesn't, but the music never stops).
- One account plays on one device at a time. An admin pressing play without selecting
  DISCO PHONE first steals the music. Hence the golden rule: read the green bar.
- Spotify Connect drops idle devices after roughly 10 minutes.
- **SKAA reaches ~45 m and drives at most 4 receivers per transmitter.** A 150–200 m
  parade cannot be covered from the front — ride the sound bike in the *middle* and the
  same range covers ~90 m. More transmitters add speakers, not distance.

## Local preview

```sh
python3 -m http.server 8000   # then open http://localhost:8000
```

## Sources

Spotify [Connect](https://support.spotify.com/us/article/spotify-connect/) and
[Jam](https://support.spotify.com/us/article/jam/) documentation; Spotify Community
threads on [cross-network control](https://community.spotify.com/t5/Other-Podcasts-Partners-etc/Devices-can-be-controlled-from-any-network/td-p/4428681)
and [Jam drift](https://community.spotify.com/t5/Live-Ideas/Add-a-Manual-Audio-Delay-Sync-Offset-Slider-for-Spotify-Jam/idi-p/7353774);
[SKAA](https://skaa.com/pages/transmitters) published specifications.
