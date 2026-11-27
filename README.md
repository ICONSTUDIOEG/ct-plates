# Platō · بلاتوه

**Study plates by Cinematic Tutor · لوحات دراسية من سينماتيك توتر**

*Platō* plays on the film studio stage (بلاتوه, the word every Egyptian film student knows) and on *plate*, the printed study sheet each lesson is built as.

Interactive lessons in sound and cinema, by ICON Studio for the Sound Engineering Department, Higher Institute of Cinema (Academy of Arts, Egypt).

Each plate teaches one technical idea by letting the student operate it. Every plate is a live 3D model the student can turn, cut open and take apart, driven by a real physics simulation they can hear. It looks like a printed 1960s studio engineering sheet, in two inks, Arabic first with English glosses.

**Live:** https://plato.iconstudio.tech

| Plate | Subject | Link |
|---|---|---|
| 01 | The microphone · الميكروفون | https://plato.iconstudio.tech/plate01/ |

---

## Plate 01 · The microphone

A microphone decides what it hears before it records anything. The student learns this by doing.

### What is on the sheet

- **Six study microphones**, each with its own 3D body, pickup pattern and sound:
  - measurement omni
  - handheld dynamic (cardioid)
  - large-diaphragm condenser (all five patterns on a knob)
  - small-diaphragm pencil (hypercardioid)
  - ribbon (figure-8)
  - shotgun (supercardioid capsule behind an interference tube)
- **A stage** with three draggable sources: a sung voice, a guitar, and air-conditioner noise. The student hears the *mic output*, never the dry sources.
- **Four views:** Studio, Cutaway (the diaphragms move with the simulated pressure), Sound field (true-metre wavefronts and the 3D pickup lobe), Exploded (every part labelled).
- **Four live instruments:** VU meter with true 300 ms ballistics, polar plot, frequency response, chart recorder.
- **Four live drawings**, printed like diazo blueprints from the running model: Front, Side, Plan, Section A-A.
- **Try your voice** («جرّب صوتك»): the student's own microphone becomes the voice source, and the meters read their real voice.
- **Six lesson tasks** that tick only when the student actually does them. Progress is kept in their browser.

### Controls

| Key | Action |
|---|---|
| `1` `2` `3` `4` | Studio · Cutaway · Sound field · Exploded |
| `M` / `Shift+M` | Next / previous microphone |
| `P` / `Shift+P` | Step the pattern knob (large condenser only) |
| `S` | Slow motion (1/25) |
| `Space` | Sound on / off |
| Drag a source | Move it on the stage |
| Drag the angle ring | Turn the microphone |
| Drag elsewhere · scroll | Orbit · zoom |

### Facts and models

Everything on the sheet is either **physics** or **modelled**, and the sheet labels the modelled values «تقديري».

- **Physics** (textbook laws and published standards):
  - first-order polar law `P(θ) = A + B·cos θ`, with the dual-diaphragm blend front + k·rear
  - c = 343 m/s
  - inverse-square law (−6.02 dB per doubling of distance)
  - proximity effect `|1 + 1/(j·k·r)|`
  - 48 V phantom power (IEC 61938)
  - VU ballistics: 99% in 300 ms
- **Modelled:**
  - null depth (about −25 dB)
  - each mic's voicing (corners, presence peaks)
  - shotgun tube length and its envelope
  - display sizes, source timbres, levels
- The source project's physics check runs **56 assertions** on the formulas and on the real sample-by-sample audio chain.
- All microphones are generic study models. None copies a real product.

---

## Using the plates

### Open

Any current desktop or phone browser. No install, no login, no internet after the page has loaded (fonts and code are inside the file). Sound starts after the first click inside the plate, a browser rule.

### Microphone access

"Try your voice" needs a secure page: **HTTPS**, `localhost`, or the file opened from disk. On `plato.iconstudio.tech` the browser asks for permission once. Where it is refused, the plate says so and carries on with the synthesised voice.

### Embed in another site or app

```html
<iframe
  src="https://plato.iconstudio.tech/plate01/?host=https://your-app.example"
  allow="microphone; autoplay"
  style="width:100%; aspect-ratio:16/10; border:0"
  title="Plate 01 · The microphone"></iframe>
```

The plate keeps its full sheet layout from about 900 px wide, and switches to a vertical scroll below that.

### Drive it from the Cinematic Tutor app

Plates talk to their host with `postMessage`. The host can switch mics and views, move sources, show narration lines, and read state. The plate reports every narration line (so Mr. Aly can speak it), every teaching event, and the student's task progress.

- Full API and URL parameters: [`plate01/INTEGRATION.md`](plate01/INTEGRATION.md)
- Working reference host (Mr. Aly guides the lesson in nine steps): [`plate01/host-demo.html`](plate01/host-demo.html), live at https://plato.iconstudio.tech/plate01/host-demo.html

---

## Repository layout

```
index.html                  plates index (plato.iconstudio.tech)
plate01/index.html          Plate 01, the whole plate in one self-contained file (~1.3 MB)
plate01/host-demo.html      Mr. Aly reference host
plate01/INTEGRATION.md      integration guide for the Cinematic Tutor app
deploy/docker-compose.yml   server deployment
```

This repo holds the **built** plates only. The source (simulation, 3D models, plate framework, tests, build tools) is a separate project. Its bundler, `tools/bundle.mjs`, produces `plate01/index.html`.

## Deployment

`plato.iconstudio.tech` runs on the ICON Studio VPS as the Docker project **`plates`**. The old address `plates.iconstudio.tech` permanently redirects to it.


- An `nginx:alpine` container downloads this repo's files at a **pinned commit** on every start and serves them.
- It sits behind the VPS's existing Traefik proxy (network `n8n_default`, resolver `mytlschallenge`), which provides HTTPS automatically.
- The headers allow the microphone (`Permissions-Policy: microphone=(self)`) and disable stale caching.
- DNS: an `A` record `plates` → the VPS.

### Publish a new version

1. Commit and push the new files to `main`.
2. In `deploy/docker-compose.yml`, set `REV` to the new commit hash and push.
3. Redeploy the `plates` project on the VPS with that compose file. The container fetches the new files on start.
4. Check `https://plato.iconstudio.tech/plate01/` loads, and that the served file matches the commit.

### Remove

Delete the `plates` Docker project and the `plates` DNS record. Nothing else on the server depends on them.

### Add a plate

Add `plateNN/` with its built `index.html`, list it in the root `index.html` and in the `for f in …` line of the compose file, then publish as above.

---

ICON Studio · Cairo
