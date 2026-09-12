# AirPlay module — licensing position for this fork

This fork carries the `:airplay` module from upstream PR #170
(`starbrightlab/immortal#170`, by @rudysev), which vendors
[jqssun/android-airplay-server](https://github.com/jqssun/android-airplay-server)
and, beneath it, [UxPlay](https://github.com/FDH2/UxPlay).

## The conflict

Immortal is **MIT**. The AirPlay native stack is **GPL-3.0**, and not by accident:

| Component | Licence |
| --- | --- |
| UxPlay `lib/playfair/` (FairPlay handshake) | **GPL-3.0** |
| UxPlay `lib/crypto.c` | **GPL-3.0** |
| UxPlay `lib/` (rest) | LGPL-2.1+ |
| FFmpeg | LGPL-2.1+ |
| libplist | LGPL-2.1 |
| llhttp, SRP | MIT |

The GPL-3.0 parts are the FairPlay handshake. They are **not optional** — without
them there is no AirPlay receiver. So **any APK bundling `:airplay` is GPL-3.0 as a
whole**, regardless of Immortal's own MIT licence.

There is no permissive alternative to swap in. Every open AirPlay receiver
(UxPlay, RPiPlay, jqssun's Android port) descends from the same reverse-engineered
`playfair` lineage. `openairplay/airplay2-receiver` carries *no licence at all*,
which grants nothing. Apple's official route is MFi hardware certification, which
is not open to a software-only Android app.

## What this fork does

**Personal use only. Do not distribute a build containing `:airplay`.**

GPL obligations attach to *distribution*, not to use. Building this branch and
running it on your own Portal triggers nothing. The line is publishing a combined
binary — cutting a GitHub release, or serving it through Immortal's self-update.

Concretely, for this fork:

- Do not attach an `:airplay` APK to a release on this repository.
- Do not point `version.json` at a build containing `:airplay`.
- Keep it on a feature branch; do not merge to `main` if `main` is ever used to cut
  releases.

Upstream's own tripwire says the same thing, in `airplay/build.gradle.kts`:

> DO NOT cut an Immortal release containing :airplay until that is resolved.
> Dev/debug builds only.

It is a comment, not a build failure — nothing stops a release being cut by mistake.

## If distribution is ever wanted

Two clean options, both real:

1. **Relicense this fork's releases as GPL-3.0.** MIT is one-way compatible with the
   GPL, so Immortal's own code can be absorbed into a GPL-3.0 whole. MIT notices must
   be preserved. This changes terms only for this fork, never upstream.

2. **Ship AirPlay as a separate APK.** Keep Immortal MIT; distribute the GPL-3.0
   receiver as its own application, talking to the launcher at arm's length (intents
   or localhost) and listed in the App Store catalog. Two aggregated programs rather
   than one combined work — the structure the FSF treats as separate. This is the
   option that preserves upstream's licence, and is likely why #170 has not landed.

This file is a working note, not legal advice.
