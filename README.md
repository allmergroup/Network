# Envoy

> A physical-first social game. You connect only by meeting someone in person — and a sepia world map colours into watercolour as your dispatches grow.

**Status:** Experiment in progress (v1.0 draft — concept stage, no application code yet).
**Owner:** Allmer Group / Foundry Exchange. *Separate from Simon Allmer Entertainment.*

📄 **Start here:** open [`index.html`](index.html) in any browser, or view it live via GitHub Pages. It's a self-contained pitch + technical brief styled in the product's 1950s travel-diary aesthetic. No build step, no dependencies — it opens offline.

---

## The one idea

In an age of AI-generated everything, **human presence is one of the few things that cannot be automated, synthesised, or faked.** Envoy turns that into a game. No remote adds, no usernames to search, no follower counts — the only way to add someone is to stand next to them and both agree.

## The core loop

**Meet → Envoice → Stamp → Colour.** You meet a person in real life, both phones confirm a proximity handshake — an *Envoice* is filed for both parties — a vintage passport stamp animates onto your diary, and that person's home country bursts from sepia fog into watercolour on your globe. Your Dispatches are the *doorway* to a real conversation, not the end of it.

### Language system
| Moment | Term |
|---|---|
| The app | **Envoy** |
| The mutual proximity handshake | **Envoice** |
| Notification on connection | *"You got an Envoice"* |
| Your full collection of connections | **Dispatches** |
| Passport tab stat | *47 Dispatches · 23 countries* |
| Nomad mode active state | *On dispatch* |

---

## The plan (for humans and future AI agents)

This section is the working brief. **Read it before adding code or changing direction.** It records what is decided, what is open, and what is explicitly off the table — so nobody re-litigates settled calls or builds against a fantasy.

### How to treat this project
- It is an **experiment**, not a billion-dollar bet. If it grows, it grows; if it dies, that's fine. Optimise for learning, not scale.
- **Seeding strategy:** win **one place completely first** (a single campus or scene with enough density that first meetings actually happen) before thinking about the world. Proximity networks die without local density.

### Decisions locked in
- **Privacy is paramount.** The proximity radar shows only *coarse, neighbourhood-level density* — **never** a stranger's location, and never "person X is 250m away." Precise location exists *only* between mutual connections, opt-in and revocable. The radar is the single most safety-sensitive surface; ship it opt-in.
- **No remote adding.** Mutual, simultaneous, in-person consent is the only path to an Envoice. The friction is the point.
- **Nomad mode** exists for people without a fixed home. Switching *into* Nomad (*On dispatch*) is one tap; changing a fixed Home Base stays deliberately hard.
- **No Instagram / social lock-in.** Cut. Importing a curated online feed works against the "presence can't be faked" thesis. Envoy stands alone.
- **Aesthetic:** 1950s travel diary — aged paper, typewriter type, passport stamps, a 3D watercolour globe. This is core to the product, not decoration.

### Technical direction (see the appendix in `index.html` for detail)
- ⚠️ **Do NOT plan around Apple NameDrop / "Share My Profile."** It is a system feature with **no public third-party API.** Any spec that depends on it is wrong.
- **Handshake (the Envoice mechanic):** `MultipeerConnectivity` (core exchange) + `Nearby Interaction`/UWB (physical-proximity proof & anti-spoof), with `Core Bluetooth` as fallback. Cross-platform / web-honest fallback = **mutual QR + co-located GPS + timestamp.**
- **Anti-spoofing:** `App Attest` / `DeviceCheck` (iOS) and `Play Integrity` (Android), plus a server-side matching session nonce + consistent coarse location at Envoice time.
- **Home Base:** inferred via `Core Location` visit/significant-location monitoring over ~2 weeks.
- **Backend:** a live social graph with realtime presence **is** server-side — there is no serverless version of that. But avoid running infrastructure: use a **BaaS** (Supabase — Postgres + Realtime + Auth + Edge Functions — or Firebase). Push via APNs/FCM.
- **Globe & radar:** `globe.gl` / `three.js` for the watercolour globe; `MapLibre` for the coarse density radar.

### Platform verdict
**Native, iOS-first** (Swift/SwiftUI), or React Native / Flutter with native modules for Bluetooth, UWB and attestation. **A PWA cannot be the product** — Web Bluetooth and Web NFC do not exist in iOS Safari, web apps can't do background location, and there's no real hardware attestation in a browser. A PWA *is* the right tool for the marketing page and an optional read-only globe viewer. If web is non-negotiable for a v1, switch the Envoice mechanic to **mutual QR + GPS on purpose** — a product decision, not a hidden workaround.

### Open questions (not yet decided)
- Final client stack: native Swift vs. React Native / Flutter.
- Exact granularity and opt-in flow for the proximity radar.
- Whether v1 is native or a deliberately-scoped QR-based web prototype.
- Which first campus/scene to seed.
- GitHub repo rename from `Network` to `Envoy` (pending).

---

## Repo contents
| File | What it is |
|------|------------|
| `index.html` | Self-contained pitch + technical brief, and the GitHub Pages homepage. Share this with anyone who can help build it. |
| `README.md` | This file — the working brief and plan of record. |

## Contributing
This is an early experiment. If you're picking it up: read the plan above, keep the locked-in decisions, and update the **Open questions** and **Decisions locked in** sections as choices get made — so this file stays the single source of truth.
