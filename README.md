# MeshRoute

**Split-tunnel mesh router for Android** — connect your phone to a WAN-less
MANET radio (Silvus StreamCaster, Doodle Labs Mesh Rider, etc.) over USB-C
ethernet while LTE/5G or Wi-Fi keeps carrying your internet. The dual-network
behavior desktops have always had, on a Samsung in your pocket.

Functionally equivalent to the simultaneous dual-connection (MANET + LTE)
feature of the Samsung Galaxy **Tactical Edition** models — but on standard
consumer and enterprise hardware.

No root. No system modification. Works on managed/Knox devices.

<p align="center">
  <img src="docs/screenshot.png" width="360" alt="MeshRoute main screen">
</p>

## The problem

Android routes all traffic over one "default network." Plug in a mesh radio
that has no internet and one of two bad things happens: Android keeps
LTE/Wi-Fi as default and packets to mesh hosts go out the wrong interface and
die, or the ethernet grabs the default slot and your internet dies. There is
no user-facing way to add a static route.

## What MeshRoute does

MeshRoute registers a VPN whose tunnel claims **only the mesh subnet**. Traffic
to mesh hosts is forwarded out the ethernet interface; everything else never
touches the tunnel and rides your normal internet connection untouched — for
every app on the phone, with no per-app configuration.

- **Auto subnet** — reads the subnet from the ethernet link when it attaches.
  Plug in any radio and it routes; no CIDRs to type (manual override available).
- **Follow ethernet** — connects on plug, disconnects on unplug, arms itself at
  boot and works with the app closed. It behaves like a cable, not an app.
- **Find Devices** — scans the mesh subnet and lists every live node with
  latency; tap one to set it as your monitored host.
- **Test Mesh** — one-tap field diagnostic: link state, ping, TCP probe, and a
  plain-language verdict ("mesh link healthy" / "nothing answering — check
  radio power and IP").
- **Watchdog** — pings your mesh host every 30 s and fires a notification the
  moment the mesh goes silent.
- **Home-screen widget** — one-tap start/stop with live status.
- TCP, UDP, and ICMP (ping) forwarding via userspace relays bound to the
  ethernet network. MSS clamping and upload backpressure built in.

## What it deliberately does not touch

- **Multicast (224.0.0.0/4)** — left native so interface-aware apps (e.g. ATAK
  multicast SA) keep working directly on the ethernet interface.
- **IPv6** — bypasses the tunnel entirely.
- **Your default network** — internet routing is never modified.

## Install

1. Grab the APK from [Releases](../../releases) and sideload it
   (`adb install MeshRoute-*.apk` or open it on the phone).
2. Open the app once and tap **START**, accepting the VPN prompt (one time
   only). From then on it follows the cable automatically.
3. Recommended: set the app's battery usage to **Unrestricted** so aggressive
   battery management can't kill the background service.

## Notes

- Requires Android 10+. Built for Samsung devices (XCover, S-series); should
  work on any modern Android with USB-C ethernet support.
- Idle battery use is negligible — the standby watcher is event-driven, and
  the packet loop sleeps unless traffic is actually flowing.
- The status bar's ethernet icon is drawn by the OS and reflects link state,
  not routing; the app's **Internet** row shows where your traffic really goes.
- Works with DHCP or static ethernet configurations. No gateway or DNS is
  needed on the mesh link — the tunnel only routes the on-link subnet.
