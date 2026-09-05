# The Sound Bike

A scrollytelling explainer for the Dublin bike disco crew: how to run one playlist
across a parade of 20–100 cyclists, and let 2–5 people control it from anywhere
in the line.

**Live site:** https://itsslimetime.github.io/bike-disco-sound/

Single static page. No build step, no dependencies. `index.html` is the whole thing.

## The short version

Everyone keeps their own Spotify account. That rules out Spotify Connect and makes
**Spotify Jam** the answer.

**Spotify Connect is same-account only.** There is no tier or setting that lets one
account control another account's playback. Family members in the same room can't do it
without logging out and back in as the same account.

**Spotify Jam is the cross-account mechanism.** Guests keep their own accounts and can
play, pause, skip and queue — but **guest controls are OFF by default** (Jam details →
Guest settings). That's very likely why the first attempt felt broken.

**Tailscale isn't needed, and wouldn't work anyway.**

1. Jam and Connect both route through Spotify's servers, not your LAN. No shared network
   is involved at any point.
2. A tailnet couldn't substitute for a LAN regardless — local discovery is multicast/mDNS,
   which Tailscale does not carry (open issues
   [#11134](https://github.com/tailscale/tailscale/issues/11134),
   [#1013](https://github.com/tailscale/tailscale/issues/1013)).

**Jam's earlier failure was misdiagnosed.** In a Jam every participant's phone decodes its
own stream; several phones each feeding a speaker drift by a reported 3–7 seconds. That's a
"too many devices making sound" problem, not a connectivity fault — and fixing the count is
exactly what makes Jam viable.

**The architecture** — two planes that never touch:

| | Carries | Over | Needs internet |
|---|---|---|---|
| Control plane | play / pause / skip / queue | Spotify's servers (a Jam) | Yes, both ends |
| Audio plane | the actual sound | SKAA, 2.4 GHz radio | No |

One phone cabled to the SKAA transmitter hosts the Jam and is the only device on the parade
allowed to make a sound. Everyone else joins on their own account, sets their volume to zero,
and acts purely as a remote.

Fallbacks documented on the page: a shared **Bike Disco** account with Connect (bulletproof,
but everyone logs out of their own Spotify), and a DIY web remote on the Spotify Web API
(admins need no account at all, but it needs a small backend).

## Gotchas the page covers

- **Guest controls are off by default.** Jam details → Guest settings → allow guests to
  control playback. Without it guests can queue but never skip.
- **Joining in person is free; joining remotely needs Premium.** Get everyone into the Jam
  at the meeting point, phones held together, before rolling out.
- **If the host leaves the Jam or closes Spotify, the Jam ends for everyone.** Music keeps
  playing; every remote goes dead until it's restarted and the link reshared.
- Download the playlist offline on the disco phone — playback then survives coverage
  blackspots (remote control doesn't, but the music never stops).
- Shared volume control doesn't apply to wired or Bluetooth outputs. Do volume at the
  transmitter.
- **SKAA reaches ~45 m and drives at most 4 receivers per transmitter.** A 150–200 m parade
  cannot be covered from the front — ride the sound bike in the *middle* and the same range
  covers ~90 m. More transmitters add speakers, not distance.

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
