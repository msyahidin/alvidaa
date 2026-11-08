# Start Here — for end users

> You own a Hisense **VIDAA** TV, you're tired of its ad-filled home screen,
> and you want to know: *what can I actually do, and what do I have to do?*
> This page answers that in plain language. The rest of the repo is a build
> log written for the author and his AI agent — useful, but not written for
> you. Read this first.

## What this project is, in one line

A way to **take your own TV back**: replace its sponsored home screen with a
clean one (clock, your HDMI inputs, your real apps), and — if you go
deeper — gain full root control of the TV you paid for. Everything runs on
your own LAN, through the TV's own APIs, and every step is designed to be
undone.

## What's in it for you — pick your goal

| You want… | You need… | Effort | Reversible? |
| --- | --- | --- | --- |
| **A clean home screen, no ads** | A small always-on computer (Raspberry Pi, old laptop) on the same Wi-Fi | ~30–60 min | Yes, fully |
| **To see & block what the TV phones home** | Root (below) | Hours, hands-on | Yes |
| **Full ownership — modify the TV itself** | Root + comfort with a terminal | A weekend | Yes, with care |

Most people want **row 1**. Start there.

---

## Before anything: find your TV's model & firmware

This matters because the deep stuff (root) is **specific to one exact
model + firmware build**. The clean-launcher part is more forgiving, but
you still want to know what you have.

On the TV: **Settings → Support → System Info** (wording varies). Write down:

- **Model** (e.g. `50A51HUV`)
- **Firmware** (e.g. `09.01O.P0813`)

Then check the compatibility table: [TESTED-MODELS.md](research/TESTED-MODELS.md).

- **Your exact build is listed, launcher "working"** → Lane 1 below will work.
- **Your build is listed, root "achieved"** → Lane 2 is possible for you.
- **Not listed** → the launcher may still work (worth trying, it's reversible);
  root is a research exercise, not a recipe — don't expect copy-paste.

> Honest expectation: this was built and fully verified on a **`50A51HUV`**.
> Other models in the table are community reports at varying confidence.
> If your TV isn't there, you're a tester, not a tourist.

---

## Lane 1 — The clean launcher (no root, start here)

**What you get:** your TV boots to *your* home screen — a clock, your HDMI
inputs showing live signal, your actual apps. No sponsored tiles, no feed,
nothing to enshittify. You can still reach the stock VIDAA home with the
HOME button if you ever want it.

**What you need:**
- Your TV and a small always-on computer on the **same network**.
  A Raspberry Pi is ideal — don't use a laptop that sleeps, or your home
  screen dies when it does.
- ~30–60 minutes.

**The shape of it** (full commands in the main [README](../README.md) Quickstart):

1. Extract the 2024 client certificate (one-time, from the VIDAA phone app).
2. **Pair** — the TV shows a 4-digit code, you type it in.
3. **Serve** — run `vidaa_server.py` on the Pi.
4. **On the TV**, open the TV browser to your Pi's address and tap
   **"+ INSTALL AS APP"**. Restart the TV. Open "Home".

**To undo it:** it's a self-installed app and an external server. Stop the
server, remove the app — your TV is exactly as it was. Nothing in the
firmware was touched.

**Tune it** by editing `server/config.json` (read live): hide apps you don't
use, rename tiles, set `"autoSwitch": true` to jump straight to your active
HDMI input on boot. Production setup: [DEPLOY.md](launcher/DEPLOY.md).

---

## Lane 2 — Root (only if you want to go all the way)

**What you get:** a root shell on the TV at every boot, reached over SSH
with a key. From there you can inspect the TV's telemetry, block it at the
system level, and modify the native launcher directly instead of overlaying
it.

**The honest truth about this lane — read it:**

- **This repo does *not* hand you a one-command exploit.** On purpose. It
  documents *why* the root path works — the architecture, the firmware
  property it rests on — but it ships **no working payload** and **no
  packaged exploit chain**. (The reasoning is in [DISCLOSURE.md](DISCLOSURE.md):
  publishing the payload is what lets the vendor close the window for
  everyone.)
- So if your mental model is "download, run, done" — that's **Lane 1 only**.
  Lane 2 means reading the stage-by-stage guide, understanding each step,
  and doing it yourself.
- It is **model-specific**. The reference chain is for `50A51HUV`
  (MTK9602 / armv7). Different SoC = different partitions, different tools.
  The guide teaches the *method*, not constants to paste.

**Where it's documented:** the full chain, stage by stage, lives in
[docs/models/50A51HUV/09.01O.P0813/](models/50A51HUV/09.01O.P0813/README.md).
Read [SECURITY.md](../SECURITY.md) first.

---

## The rules (they protect your TV)

This project has non-negotiable ground rules, and they exist so you don't
brick a TV:

- **Only on a TV you own.** LAN-local. No DRM circumvention.
- **Reversibility is the design law.** Every change has a documented way to
  undo it. If a guide has no rollback, it's incomplete — don't run it.
- **Verified boot stays green.** No flashing partitions, no writing to
  read-only areas. This is why the method is safe.
- **One server per TV.** The paired identity is exclusive; two servers fight
  over the connection.

---

## Still stuck? A 20-second decision

- *I just want the ads gone and my inputs on screen* → **Lane 1.** Go to the
  [README Quickstart](../README.md#quickstart--the-launcher).
- *I want to control the whole TV and I'm comfortable in a terminal* →
  read [the model guide](models/50A51HUV/09.01O.P0813/README.md), method first.
- *My model isn't in the table* → try Lane 1 (reversible, low risk); treat
  Lane 2 as research. Consider [reporting what you find](research/TESTED-MODELS.md).
